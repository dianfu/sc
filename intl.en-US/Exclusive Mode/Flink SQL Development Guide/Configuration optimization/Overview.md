# Overview

After you implement the business logic, and then publish and start a Realtime Compute job, you need to optimize the job to meet performance requirements.

## Purposes

-   Jobs can start and run properly.
-   The job latency and throughput meet performance requirements.
-   Resources can be used efficiently to reduce the cost.

## Procedure

The following figure shows the procedure of job optimization.

![Procedure](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8321659951/p59016.png)

1.  Optimize the SQL code.

    SQL optimization allows you to select an appropriate SQL implementation method based on business requirements. For example, you can optimize aggregation functions, resolve data hotspot issues, optimize the TopN algorithm, use built-in functions, deduplicate data records, and avoid use of regular expressions. For more information, see [Recommended Flink SQL practices](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Configuration optimization/Recommended Flink SQL practices.md).

2.  Optimize performance by adjusting parameter settings.
    -   Adjust job parameter settings.

        Select an underlying optimization policy. For example, you can enable miniBatch to reduce state data access. For more information, see [Job parameters](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Configuration optimization/Performance optimization by manual configuration.md).

    -   Adjust parameter settings of upstream and downstream data storage.

        Optimize the read and write operations performed on the upstream and downstream storage systems. For example, you can read or write data in batches to improve the throughput. You can also configure the cache policy to improve the efficiency of joining dimension tables. For more information, see [Upstream and downstream data storage parameters](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Configuration optimization/Performance optimization by manual configuration.mdsection_q6p_kx0_qrm).

3.  Optimize resource configuration automatically.

    To simplify job optimization, Realtime Compute provides the automatic configuration optimization feature. We recommend that you use this feature for job optimization. For more information, see [Performance optimization by using auto scaling](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Configuration optimization/Optimize performance by AutoScale.md).

4.  Optimize resource configuration manually or repeat the optimization process.
    -   Optimize resource configuration manually.

        If automatic configuration optimization cannot meet your requirements, you can manually optimize the resource configuration. For more information, see [Optimize performance by manual configuration](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Configuration optimization/Performance optimization by manual configuration.md).

    -   Repeat the optimization process.

        If the optimization result cannot meet your requirements, repeat the previous steps.


