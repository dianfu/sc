# Specification selection

This topic describes how to select specifications when you configure a Realtime Compute for Apache Flink cluster in exclusive mode. It also provides precautions during the configuration.

## Background information

A Realtime Compute for Apache Flink cluster functions as a primary/secondary distributed cluster, which consists of master and slave nodes.

-   Master nodes manage cluster resources and interactions among slave nodes, but are not used for computing.
-   Slave nodes are used for computing.

    **Note:** A slave node cannot use all of its resources for computing because the operating system and interactions with other nodes also consume resources.


## Usage notes

-   The slave node specifications determine the scaling configuration. For example, if your slave node specifications are 8 CPU cores and 32 GB memory, you can add or remove only a specific number of nodes of this configuration for each scaling operation. In this case, the number of available compute units \(CUs\) increases or decreases by the value that is calculated by using the following formula: Number of nodes added or removed × 6 CUs.
-   Three master nodes in a Realtime Compute for Apache Flink cluster support failover in case of a failure. This ensures cluster stability. If you configure a Realtime Compute for Apache Flink cluster with three master nodes, Alibaba Cloud provides you with a Service Level Agreement \(SLA\) guarantee.
-   You cannot change the number of master nodes in a Realtime Compute for Apache Flink cluster.

## Select specifications

The configuration of a Realtime Compute for Apache Flink cluster in exclusive mode can be calculated in CUs, which is the same as Realtime Compute for Apache Flink clusters in shared mode. One CU is equivalent to 1 CPU core and 4 GB memory. You can configure master and slave nodes as required based on the following computing logic. Realtime Compute for Apache Flink provides a pricing calculator to help you configure your cluster to be as cost-effective as possible.

**Note:** If you are a new user, you can determine the number of CUs based on the queries per second \(QPS\) and complexity of your business logic, and then configure the cluster. For more information, see [Billing](/intl.en-US/Exclusive Mode/Pricing/Billing.md). Processing capability of one CU in Realtime Compute for Apache Flink:

-   For simple operations such as single-stream filtering and string conversion, one CU can process 10,000 data records per second.
-   For complex operations such as operations that use a JOIN clause, GROUP BY clause, or window function, one CU can process 1,000 to 5,000 data records per second.

The following tables list the empirical CU quantities for different master and slave node specifications.

-   You must configure at least two slave nodes for a Realtime Compute for Apache Flink cluster in exclusive mode. Therefore, the minimum computing capability of this cluster is 6 CUs, which is calculated by using the following formula: 3 CUs × 2.

    |Slave node specifications|Available CUs|
    |-------------------------|-------------|
    |4 CPU cores, 16 GB memory|3|
    |8 CPU cores, 32 GB memory|6|
    |16 CPU cores, 64 GB memory|13|
    |24 CPU cores, 96 GB memory|21|
    |32 CPU cores,128 GB memory|28|
    |56 CPU cores, 224 GB memory|52|
    |64 CPU cores, 256 GB memory|60|

    **Note:** The empirical data is for reference only.

-   The master node specifications are restricted by the maximum number of CUs allowed in a cluster. The following table lists empirical CU quantities for different master node specifications.

    |Master node specifications|Maximum number of CUs allowed in a cluster|
    |--------------------------|------------------------------------------|
    |4 CPU cores, 16 GB memory|80|
    |8 CPU cores, 32 GB memory|160|
    |16 CPU cores, 64 GB memory|800|
    |24 CPU cores, 96 GB memory|More than 800|

    **Note:** The empirical data is for reference only.


