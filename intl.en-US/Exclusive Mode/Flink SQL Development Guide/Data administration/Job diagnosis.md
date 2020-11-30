# Job diagnosis

Realtime Compute offers job diagnosis to help you troubleshoot job issues.

## Procedure

**Note:** Only jobs that are in the running state can be diagnosed.

1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).
2.  In the top navigation bar, click **Administration**.
3.  On the Administration page that appears, find the target job, move the pointer over the More icon in the **Actions** column, and click **Check**.

## Check metrics

-   **Failover**
    -   **Job failover**: Check whether the job encountered a failover within the last 30 minutes.
    -   **Application Master \(AM\) failover**: Check whether a failover is detected in AM.
-   **Blink Metric****Job latency**: Check the job latency. If a latency occurs, the nodes with backpressure are displayed.
    -   **High latency**: The latency is longer than 100 seconds and shorter than 200 seconds.
    -   **Excessively high latency**: The latency is 200 seconds or longer.
-   This tab displays the Yarn check result.
-   This tab displays the **OS** check result.

