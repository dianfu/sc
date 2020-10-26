# Blink 3.6.2

This topic lists the major features and bug fixes in Blink 3.6.2.

## Major features

-   Optimizes the auto scaling feature so that scale-down operations are triggered for jobs with no traffic. This reduces resource consumption.
-   Optimizes resource rescaling.
-   Optimizes data parsing of the Log Service connector. The FastLogGroup method is used to parse data.
-   Reduces the memory consumed to store vertex topology information for jobs that involve a large amount of data.

## Major bug fixes

-   Fixes the bug that Blink cannot consume the accumulated data in a Tablestore source table.
-   Fixes the bug that NullPointException occurs when the cache parameter is set to ALL for a dimension table.
-   Fixes the bug that a MaxCompute sink cannot submit data to a dynamic partition.

