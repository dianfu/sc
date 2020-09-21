# Blink-3.6.8

This topic lists the major improvements and bug fixes in Blink 3.6.8.

## Major improvements

-   Improves the performance of writing data to Time Series Database \(TSDB\).
-   Improves the performance of writing data to AnalyticDB for PostgreSQL.
-   Sets the connection timeout period of Tablestore to 20s.

## Major bug fixes

-   Fixes the bug in which a failed job cannot resume during auto scaling.
-   Fixes the bug that causes the NullPointerException error in a result table of AnalyticDB for MySQL V2.0.
-   Fixes the bug that causes the ArrayIndexOutOfBoundsException error in jobs that use a RetracRank operator.
-   Fixes the bug that causes data exceptions when a job that uses a RetracRank operator constantly receives the same retract message.
-   Fixes the bug in which data is lost when you use Tunnel Service to read incremental data from a Tablestore source table after a full read.

