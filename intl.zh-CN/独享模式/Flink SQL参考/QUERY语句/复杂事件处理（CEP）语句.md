---
keyword: [复杂事件处理, CEP]
---

# 复杂事件处理（CEP）语句

复杂事件处理（CEP）语句MATCH\_RECOGNIZE，用于识别输入流中符合指定规则的事件，并按照指定方式输出。

## 语法

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

|参数|说明|
|--|--|
|PARTITION BY|分区的列，可选项。|
|ORDER BY|可以指定多列，但是必须以`EVENT TIME`或`PROCESS TIME`列作为排序的首列，可选项。|
|MEASURES|定义如何根据匹配成功的输入事件构造输出事件。|
|ONE ROW PER MATCH|对于每一次成功的匹配，只产生一个输出事件。|
|ONE ROW PER MATCH WITH TIMEOUT ROWS|除了匹配成功时产生的输出外，超时也会产生输出。超时时间由PATTERN语句中的WITHIN语句定义。 **说明：** Blink 3.6.0及以后版本，由于Calcite升级，不再支持ONE ROW PER MATCH WITH TIMEOUT ROWS参数。 |
|ALL ROWS PER MATCH|对于每一次成功的匹配，对应于每一个输入事件，都会产生一个输出事件。|
|ALL ROWS PER MATCH WITH TIMEOUT ROWS|除了匹配成功时产生输出外，超时也会产生输出。超时时间由PATTERN语句中的WITHIN语句定义。 **说明：** Blink 3.6.0及以后版本，由于Calcite升级，不再支持ALL ROWS PER MATCH WITH TIMEOUT ROWS参数。 |
|\[ONE ROW PER MATCH\|ALL ROWS PER MATCH\|ONE ROW PER MATCH WITH TIMEOUT ROWS\|ALL ROWS PER MATCH WITH TIMEOUT ROWS\]|可选项，默认为`ONE ROW PER MATCH`。|
|AFTER MATCH SKIP TO NEXT ROW|匹配成功后，从匹配成功的事件序列中的第一个事件的下一个事件开始进行下一次匹配。|
|AFTER MATCH SKIP PAST LAST ROW|匹配成功后，从匹配成功的事件序列中的最后一个事件的下一个事件开始进行下一次匹配。|
|AFTER MATCH SKIP TO FIRST patternItem|匹配成功后，从匹配成功的事件序列中第一个对应于patternItem的事件开始进行下一次匹配。|
|AFTER MATCH SKIP TO LAST patternItem|匹配成功后，从匹配成功的事件序列中最后一个对应于patternItem的事件开始进行下一次匹配。|
|PATTERN|定义待识别的事件序列需要满足的规则，需要定义在`()`中，由一系列自定义的patternVariable构成。 **说明：**

-   patternVariable之间如果以空格间隔，表示符合这两种patternVariable的事件中间不存在其他事件。
-   patternVariable之间如果以-\>间隔，表示符合这两种patternVariable的事件之间可以存在其它事件。
-   Blink 3.6.0及以后版本，由于Calcite升级，不再支持PATTERN参数。 |

## 参数解释

-   quantifier

    quantifier用于指定符合patternVariable定义事件的出现次数。

    |参数|说明|
    |--|--|
    |\*|0次或多次|
    |+|1次或多次|
    |?|0次或1次|
    |\{n\}|n次|
    |\{n,\}|大于等于n次|
    |\{n, m\}|大于等于n次，小于等于m次|
    |\{,m\}|小于等于m次|

    默认为贪婪匹配。例如，对于`pattern: A -> B+ -> C`，输入为`a bc1 bc2 c`（其中bc1和bc2表示既匹配B也匹配C），则输出为`a bc1 bc2 c`。可以在quantifier符号后面加？来表示非贪婪匹配。例如：

    -   `*?`
    -   `+?`
    -   `{n}?`
    -   `{n,}?`
    -   `{n, m}?`
    -   `{,m}?`
    **说明：** Blink 3.x以上版本不支持\(e1 e2+\)贪婪匹配，您可以使用e1 e2+ e3  e3 as not e2绕行方案，但请务必要使用一个e3，才能有数据输出。

    此时，对于上面例子中的PATTERN及输入，产生的输出为a bc1 bc2,a bc1 bc2 c。

    -   WITHIN定义符合规则的事件序列的最大时间跨度。
    -   静态窗口格式：`INTERVAL ‘string’ timeUnit [ TO timeUnit ]`。例如：`INTERVAL ‘10’ SECOND, INTERVAL ‘45’ DAY, INTERVAL ‘10:20’ MINUTE TO SECOND, INTERVAL ‘10:20.10’ MINUTE TO SECOND, INTERVAL ‘10:20’ HOUR TO MINUTE, INTERVAL ‘1-5’ YEAR TO MONTH`。
    -   动态窗口格式：`INTERVAL intervalExpression`。例如：`INTERVAL A.windowTime + 10`，其中A为PATTERN定义中第一个patternVariable。在intervalExpression的定义中，可以使用PATTERN定义中出现过的patternVariable。当前只能使用第一个patternVariable。intervalExpression中可以使用UDF，intervalExpression的结果必须为LONG，单位为millisecond，表示窗口的大小。
    -   DEFINE定义在PATTERN中出现的patternVariable的具体含义，如果某个patternVariable在DEFINE中没有定义，则认为对于每一个事件，该patternVariable都成立。
-   MEASURES和DEFINE语句函数

    |函数|说明|
    |--|--|
    |Row Pattern Column References|形式为`patternVariable.col`。表示访问patternVariable所对应的事件的指定的列。|
    |PREV|只能用在DEFINE语句中，通常与`Row Pattern Column References`合用。用于访问指定的PATTERN所对应的事件之前，偏移指定的offset所对应的事件的指定的列。 例如，`DOWN AS DOWN.price < PREV(DOWN.price)`，`PREV(A.price)`表示当前事件的前一个事件的`price`列的值。

**说明：**

    -   `DOWN.price`等价于`PREV(DOWN.price, 0)`。
    -   `PREV(DOWN.price)`等价于`PREV(DOWN.price, 1)`。 |
    |FIRST、LAST|一般与`Row Pattern Column References`合用，用于访问指定的PATTERN所对应的事件序列中的指定偏移位置的事件。例如：     -   `FIRST(A.price, 3)`表示`PATTERN A`所对应的事件序列中的第4个事件。
    -   `LAST(A.price, 3)`表示`PATTERN A`所对应的事件序列中的倒数第4个事件。 |

-   输出列

    |函数|输出列|
    |--|---|
    |ONE ROW PER MATCH|包括`PARTITION BY`中指定的列及`MEASURES`中定义的列。对于`PARTITION BY`中已经指定的列，在`MEASURES`中无需重复定义。|
    |ONE ROW PER MATCH WITH TIMEOUT ROWS|除匹配成功的时候产生输出外，超时的时候也会产生输出，超时时间由PATTERN语句中的WITHIN语句定义。|

    **说明：**

    -   当定义PATTERN时，最好也定义WITHIN，否则可能会造成STATE越来越大。
    -   ORDER BY中定义的首列必须为EVENT TIME列或者PROCESS TIME列。

## 示例

-   示例语法

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

-   测试数据

    |timestamp（TIMESTAMP）|card\_id\(VARCHAR\)|location（VARCHAR）|action（VARCHAR）|
    |--------------------|-------------------|-----------------|---------------|
    |`2018-04-13 12:00:00`|1|Beijing|Consumption|
    |`2018-04-13 12:05:00`|1|Shanghai|Consumption|
    |`2018-04-13 12:10:00`|1|Shenzhen|Consumption|
    |`2018-04-13 12:20:00`|1|Beijing|Consumption|

-   测试语法

    当相同的card\_id在十分钟内，在两个不同的location发生刷卡现象，就会触发报警机制，以便监测信用卡盗刷等现象。

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
    
    --定义计算逻辑。
    insert into rds_out
    select 
    `start_timestamp`, 
    `end_timestamp`, 
    card_id, `event`
    from datahub_stream
    MATCH_RECOGNIZE (
        PARTITION BY card_id   --按card_id分区，将相同卡号的数据分发到同一个计算节点。
        ORDER BY `timestamp`   --在窗口内，对事件时间进行排序。
        MEASURES               --定义如何根据匹配成功的输入事件构造输出事件。
            e2.`action` as `event`,   
            e1.`timestamp` as `start_timestamp`,   --第一次的事件时间为start_timestamp。
            LAST(e2.`timestamp`) as `end_timestamp` --最新的事件时间为end_timestamp。
        ONE ROW PER MATCH           --匹配成功输出一条。
        AFTER MATCH SKIP TO NEXT ROW --匹配后跳转到下一行。
        PATTERN (e1 e2+) WITHIN INTERVAL '10' MINUTE  --定义两个事件，e1和e2。
        DEFINE                     --定义在PATTERN中出现的patternVariable的具体含义。
            e1 as e1.action = 'Consumption',    --事件一的action标记为Consumption。
            e2 as e2.action = 'Consumption' and e2.location <> e1.location --事件二的action标记为Consumption，且事件一和事件二的location不一致。
    );
    ```

    **说明：** 如果存在满足CEP条件的数据，但没有结果输出是因为只有Watermark \> e2.ts的数据才会被处理，但由于e2后面已经没有数据了，导致Watermark一直是e2.ts -1000，所以e2的数据一直没有被处理，从而导致没有结果输出。

-   测试结果

    |start\_timestamp（TIMESTAMP）|end\_timestamp（TIMESTAMP）|card\_id（VARCHAR）|event（VARCHAR）|
    |---------------------------|-------------------------|-----------------|--------------|
    |`2018-04-13 12:00:00.0`|`2018-04-13 12:05:00.0`|1|Consumption|
    |`2018-04-13 12:05:00.0`|`2018-04-13 12:10:00.0`|1|Consumption|


