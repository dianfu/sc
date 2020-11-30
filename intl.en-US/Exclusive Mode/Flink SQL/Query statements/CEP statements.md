---
keyword: [complex event processing, CEP]
---

# CEP statements

MATCH\_RECOGNIZE is a complex event processing \(CEP\) statement that identifies events from input data streams based on the specified rules and generates output events based on the specified method.

## Syntax

```
SELECT [ ALL | DISTINCT ]
{ * | projectItem [, projectItem ]* }
FROM tableExpression
[MATCH_RECOGNIZE (
[PARTITION BY {partitionItem [, partitionItem]*}]
[ORDER BY {orderItem [, orderItem]*}]
[MEASURES {measureItem AS col [, measureItem AS col]*}]
[ONE ROW PER MATCH|ALL ROWS PER MATCH|ONE ROW PER MATCH WITH TIMEOUT ROWS|ALL ROWS PER MATCH WITH TIMEOUT ROWS]
[AFTER MATCH SKIP]
PATTERN (patternVariable[quantifier] [ patternVariable[quantifier]]*) WITHIN intervalExpression
DEFINE {patternVariable AS patternDefinationExpression [, patternVariable AS patternDefinationExpression]*}
)]; 
```

|Clause|Description|
|------|-----------|
|PARTITION BY|Optional. You can use the clause to divide the rows of an input table into partitions based on one or more partition key columns.|
|ORDER BY|Optional. You can use the clause to sort the rows of a partition based on one or more columns. If you use multiple columns to sort the rows, specify `EVENT TIME` or `PROCESS TIME` as the first column.|
|MEASURES|You can use the clause to define the output events that are generated based on the matched input events.|
|ONE ROW PER MATCH|If you use the clause, only one output event is generated for each match.|
|ONE ROW PER MATCH WITH TIMEOUT ROWS|If you use the clause, an output event is generated for each match or time-out. The time-out period is specified by the WITHIN clause in PATTERN. **Note:** Blink V3.6.0 and later do not support the ONE ROW PER MATCH WITH TIMEOUT ROWS clause due to Calcite upgrades. |
|ALL ROWS PER MATCH|If you use the clause, an output event is generated for each input event upon each match.|
|ALL ROWS PER MATCH WITH TIMEOUT ROWS|If you use the clause, an output event is generated for each input event upon each match or time-out. The time-out period is specified by the WITHIN clause in PATTERN. **Note:** Blink V3.6.0 and later do not support the ALL ROWS PER MATCH WITH TIMEOUT ROWS clause due to Calcite upgrades. |
|\[ONE ROW PER MATCH\|ALL ROWS PER MATCH\|ONE ROW PER MATCH WITH TIMEOUT ROWS\|ALL ROWS PER MATCH WITH TIMEOUT ROWS\]|Optional. By default, `ONE ROW PER MATCH` is used.|
|AFTER MATCH SKIP TO NEXT ROW|If you use the clause, the next match starts to be performed from the event that follows the first event in the sequence of matched events.|
|AFTER MATCH SKIP PAST LAST ROW|If you use the clause, the next match starts to be performed from the event that follows the last event in the sequence of matched events.|
|AFTER MATCH SKIP TO FIRST patternItem|If you use the clause, the next match starts to be performed from the first event that corresponds to patternItem in the sequence of matched events.|
|AFTER MATCH SKIP TO LAST patternItem|If you use the clause, the next match starts to be performed from the last event that corresponds to patternItem in the sequence of matched events.|
|PATTERN|You can use the clause to specify the rule that the sequence of events to be identified must meet. You must enclose the rule in parentheses `()`. The rule is specified by a set of custom patternVariable variables. **Note:**

-   If two patternVariable variables are separated with a space, no events exist between the events that correspond to the patternVariable variables.
-   If two patternVariable variables are separated with an arrow sign \(-\>\), other events may exist between the events that correspond to the patternVariable variables.
-   Blink V3.6.0 and later do not support the PATTERN clause due to Calcite upgrades. |

## Parameter description

-   quantifier

    The quantifier parameter specifies the number of occurrences of events that meet the patternVariable definition.

    |Value|Description|
    |-----|-----------|
    |\*|Zero or multiple times.|
    |+|One or more times.|
    |?|Zero or once.|
    |\{n\}|n times.|
    |\{n,\}|At least n times.|
    |\{n, m\}|Ranges from n times to m times.|
    |\{,m\}|At most m times.|

    By default, greedy matching is performed. For example, if the PATTERN clause is `A -> B+ -> C` and the input is `a bc1 bc2 c`, the output is `a bc1 bc2 c`. In the input, bc1 and bc2 means that the results must match B and C. To perform reluctant matching, append the quantifier with a question mark \(?\). You can use the following reluctant quantifiers:

    -   `*?`
    -   `+?`
    -   `{n}?`
    -   `{n,}?`
    -   `{n, m}?`
    -   `{,m}?`
    **Note:** Blink V3.x and later do not support \(e1 e2+\) greedy matching. You can use e1 e2+ e3 e3 as not e2 as an alternative. In the alternative method, at least one e3 entry must be included to make sure that the output data is returned as expected.

    In this case, the output that is generated for the input and the PATTERN setting in the preceding example changes to a bc1 bc2,a bc1 bc2 c.

    -   The WITHIN clause defines the maximum time span of the events that meet the specified rule in an event sequence.
    -   The format of static windows is `INTERVAL 'string' timeUnit [ TO timeUnit ]`, for example, `INTERVAL '10' SECOND, INTERVAL '45' DAY, INTERVAL '10:20' MINUTE TO SECOND, INTERVAL '10:20.10' MINUTE TO SECOND, INTERVAL '10:20' HOUR TO MINUTE, INTERVAL '1-5' YEAR TO MONTH`.
    -   The format of dynamic windows is `INTERVAL intervalExpression`, for example, `INTERVAL A.windowTime + 10`. In this example, A is the first patternVariable variable in the PATTERN clause. When you specify intervalExpression, you can use only the first patternVariable variable in the PATTERN clause. When you specify the intervalExpression parameter, you can use user defined functions \(UDFs\). The intervalExpression result indicates the window size that is measured in milliseconds. The data type of the result must be LONG.
    -   The DEFINE statement specifies the meanings of patternVariable variables in the PATTERN clause. If a patternVariable variable is not defined in the DEFINE statement, the patternVariable variable is valid for each event.
-   Functions in MEASURES and DEFINE statements

    |Function|Description|
    |--------|-----------|
    |Row Pattern Column References|This function uses the format of `patternVariable.col`. This function is used to access the specified column of an event that corresponds to a patternVariable variable.|
    |PREV|This function can be used in only the DEFINE statement. In most cases, this function is used in conjunction with the `Row Pattern Column References` function. This function is used to access the specified column of a previous event that has a specified offset. The event occurs before the events that are specified by the PATTERN clause. For example, you can use `DOWN AS DOWN.price < PREV(DOWN.price)`. In this example, `PREV(A.price)` specifies the `price` column value for the event that occurs before the current event.

**Note:**

    -   `DOWN.price` is equivalent to `PREV(DOWN.price, 0)`.
    -   `PREV(DOWN.price)` is equivalent to `PREV(DOWN.price, 1)`. |
    |FIRST or LAST|In most cases, the FIRST or the LAST function is used in conjunction with the `Row Pattern Column References` function. You can use the FIRST or the LAST function to access the event that has a specified offset in the sequence of events. The sequence of events is specified by the PATTERN clause. The following examples are used to explain the functions:     -   `FIRST(A.price, 3)` specifies the fourth event in the sequence of events that match the `pattern A`.
    -   `LAST(A.price, 3)` specifies the last but three event in the sequence of events that match the `pattern A`. |

-   Output columns

    |Function|Output column|
    |--------|-------------|
    |ONE ROW PER MATCH|The columns that are specified in `PARTITION BY` and `MEASURES` are included. The columns that are specified in `PARTITION BY` do not need to be specified in `MEASURES` again.|
    |ONE ROW PER MATCH WITH TIMEOUT ROWS|An output event is generated for each match or time-out. The time-out period is specified by the WITHIN clause in PATTERN.|

    **Note:**

    -   When you define the PATTERN clause, we recommend that you define the WITHIN clause. If the WITHIN clause is not defined, the state size may grow larger.
    -   The first column that is specified in the ORDER BY clause must be EVENT TIME or PROCESS TIME.

## Examples

-   Syntax in the example

    ```
    SELECT *
    FROM Ticker MATCH_RECOGNIZE (
    PARTITION BY symbol
    ORDER BY tstamp
    MEASURES STRT.tstamp AS start_tstamp,
    LAST(DOWN.tstamp) AS bottom_tstamp,
    LAST(UP.tstamp) AS end_tstamp
    ONE ROW PER MATCH
    AFTER MATCH SKIP TO NEXT ROW
    PATTERN (STRT DOWN+ UP+) WITHIN INTERVAL '10' SECOND
    DEFINE
    DOWN AS DOWN.price < PREV(DOWN.price),
    UP AS UP.price > PREV(UP.price)
    );
    ```

-   Test data

    |timestamp \(TIMESTAMP\)|card\_id\(VARCHAR\)|location \(VARCHAR\)|action \(VARCHAR\)|
    |-----------------------|-------------------|--------------------|------------------|
    |`2018-04-13 12:00:00`|1|Beijing|Consumption|
    |`2018-04-13 12:05:00`|1|Shanghai|Consumption|
    |`2018-04-13 12:10:00`|1|Shenzhen|Consumption|
    |`2018-04-13 12:20:00`|1|Beijing|Consumption|

-   Syntax in the test

    Each credit card is identified by a unique card ID that is specified by the card\_id parameter. If payments with a credit card are made within 10 minutes at two different locations, an alert is triggered. This helps you monitor unauthorized operations of credit cards.

    ```
    CREATE TABLE datahub_stream (
        `timestamp`               TIMESTAMP,
        card_id                   VARCHAR,
        location                  VARCHAR,
        `action`                  VARCHAR,
        WATERMARK wf FOR `timestamp` AS withOffset(`timestamp`, 1000)
    ) WITH (
        type = 'datahub'
        ...
    );
    
    CREATE TABLE rds_out (
        start_timestamp               TIMESTAMP,
        end_timestamp                 TIMESTAMP,
        card_id                       VARCHAR,
        event                         VARCHAR
    ) WITH (
        type= 'rds'
        ...
    );
    
    --Define the computational logic.
    insert into rds_out
    select 
    `start_timestamp`, 
    `end_timestamp`, 
    card_id, `event`
    from datahub_stream
    MATCH_RECOGNIZE (
        PARTITION BY card_id   --Partition the table by card ID. The data that has the same card ID is allocated to the same compute node.
        ORDER BY `timestamp`   --Sort events by time in a window.
        MEASURES               --Define how to generate output events based on the input events that are matched.
            e2.`action` as `event`,   
            e1.`timestamp` as `start_timestamp`,   --Specify the time of the first event as the start_timestamp value.
            LAST(e2.`timestamp`) as `end_timestamp` --Specify the time of the last event as the end_timestamp value.
        ONE ROW PER MATCH           --The system generates an output event for each match.
        AFTER MATCH SKIP TO NEXT ROW --The system performs the next match from the next row after each match.
        PATTERN (e1 e2+) WITHIN INTERVAL '10' MINUTE  --Define two events: e1 and e2.
        DEFINE                     --Define the meanings of patternVariable variables in the PATTERN clause.
            e1 as e1.action = 'Consumption',    --Mark the action of the e1 event as Consumption.
            e2 as e2.action = 'Consumption' and e2.location <> e1.location --Mark the action of the e2 event as Consumption. The locations of e1 and e2 are different.
    );
    ```

    **Note:** In some scenarios, some data meets the CEP condition but no outputs are returned. This occurs because only data that meets the watermark \> e2.ts condition is processed. No input data flows into the system after e2 and the watermark is always e2.ts -1000. As a result, the e2 data cannot be processed. Therefore, no outputs are returned.

-   Test result

    |start\_timestamp \(TIMESTAMP\)|end\_timestamp \(TIMESTAMP\)|card\_id \(VARCHAR\)|event \(VARCHAR\)|
    |------------------------------|----------------------------|--------------------|-----------------|
    |`2018-04-13 12:00:00.0`|`2018-04-13 12:05:00.0`|1|Consumption|
    |`2018-04-13 12:05:00.0`|`2018-04-13 12:10:00.0`|1|Consumption|


