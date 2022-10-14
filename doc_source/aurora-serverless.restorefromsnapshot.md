# Restoring an Aurora Serverless v1 DB cluster<a name="aurora-serverless.restorefromsnapshot"></a>

 You can configure an Aurora Serverless v1 DB cluster when you restore a provisioned DB cluster snapshot with the AWS Management Console, the AWS CLI, or the RDS API\. 

 When you restore a snapshot to an Aurora Serverless v1 DB cluster, you can set the following specific values: 
+  **Minimum Aurora capacity unit** – Aurora Serverless v1 can reduce capacity down to this capacity unit\. 
+  **Maximum Aurora capacity unit** – Aurora Serverless v1 can increase capacity up to this capacity unit\. 
+  **Timeout action** – The action to take when a capacity modification times out because it can't find a scaling point\. Aurora Serverless v1 DB cluster can force your DB cluster to the new capacity settings if set the **Force scaling the capacity to the specified values\.\.\. ** option\. Or, it can roll back the capacity change to cancel it if you don't choose the option\. For more information, see [Timeout action for capacity changes](aurora-serverless-v1.how-it-works.md#aurora-serverless.how-it-works.timeout-action)\. 
+  **Pause after inactivity** – The amount of time with no database traffic to scale to zero processing capacity\. When database traffic resumes, Aurora automatically resumes processing capacity and scales to handle the traffic\. 

 For general information about restoring a DB cluster from a snapshot, see [Restoring from a DB cluster snapshot](aurora-restore-snapshot.md)\. 

## Console<a name="aurora-serverless.restorefromsnapshot.console"></a>

 You can restore a DB cluster snapshot to an Aurora DB cluster with the AWS Management Console\. 

**To restore a DB cluster snapshot to an Aurora DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the upper\-right corner of the AWS Management Console, choose the AWS Region that hosts your source DB cluster\. 

1.  In the navigation pane, choose **Snapshots**, and choose the DB cluster snapshot that you want to restore\. 

1.  For **Actions**, choose **Restore Snapshot**\. 

1.  On the **Restore DB Cluster** page, choose **Serverless** for **Capacity type**\.   
![\[Restore Aurora Serverless v1 DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-restore.png)

1.  In the **DB cluster identifier** field, type the name for your restored DB cluster, and complete the other fields\. 

1.  In the **Capacity settings** section, modify the scaling configuration\.   
![\[Setting minimum and maximum capacity for an Aurora Serverless v1 DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-capacity.png)

1.  Choose **Restore DB Cluster**\. 

 To connect to an Aurora Serverless v1 DB cluster, use the database endpoint\. For details, see the instructions in [Connecting to an Amazon Aurora DB cluster](Aurora.Connecting.md)\. 

**Note**  
 If you encounter the following error message, your account requires additional permissions:   
 `Unable to create the resource. Verify that you have permission to create service linked role. Otherwise wait and try again later.`   
 For more information, see [Using service\-linked roles for Amazon Aurora](UsingWithRDS.IAM.ServiceLinkedRoles.md)\. 

## AWS CLI<a name="aurora-serverless.restorefromsnapshot.cli"></a>

You can configure an Aurora Serverless DB cluster when you restore a provisioned DB cluster snapshot with the AWS Management Console, the AWS CLI, or the RDS API\.

When you restore a snapshot to an Aurora Serverless DB cluster, you can set the following specific values:
+ **Minimum Aurora capacity unit** – Aurora Serverless can reduce capacity down to this capacity unit\.
+ **Maximum Aurora capacity unit** – Aurora Serverless can increase capacity up to this capacity unit\.
+ **Timeout action** – The action to take when a capacity modification times out because it can't find a scaling point\. Aurora Serverless v1 DB cluster can force your DB cluster to the new capacity settings if set the **Force scaling the capacity to the specified values\.\.\. ** option\. Or, it can roll back the capacity change to cancel it if you don't choose the option\. For more information, see [Timeout action for capacity changes](aurora-serverless-v1.how-it-works.md#aurora-serverless.how-it-works.timeout-action)\.
+ **Pause after inactivity** – The amount of time with no database traffic to scale to zero processing capacity\. When database traffic resumes, Aurora automatically resumes processing capacity and scales to handle the traffic\.

**Note**  
The version of the DB cluster snapshot must be compatible with Aurora Serverless v1\. For the list of supported versions, see [Aurora Serverless v1](Concepts.Aurora_Fea_Regions_DB-eng.Feature.ServerlessV1.md)\.

 To restore a snapshot to an Aurora Serverless v1 cluster with MySQL 5\.7 compatibility, include the following additional parameters: 
+  `--engine aurora-mysql` 
+  `--engine-version 5.7` 

 The `--engine` and `--engine-version` parameters let you create a MySQL 5\.7\-compatible Aurora Serverless v1 cluster from a MySQL 5\.6\-compatible Aurora or Aurora Serverless v1 snapshot\. The following example restores a snapshot from a MySQL 5\.6\-compatible cluster named *mydbclustersnapshot* to a MySQL 5\.7\-compatible Aurora Serverless v1 cluster named *mynewdbcluster*\. 

For Linux, macOS, or Unix:

```
aws rds restore-db-cluster-from-snapshot \
    --db-cluster-identifier mynewdbcluster \
    --snapshot-identifier mydbclustersnapshot \
    --engine-mode serverless \
    --engine aurora-mysql \
    --engine-version 5.7
```

For Windows:

```
aws rds restore-db-cluster-from-snapshot ^
    --db-instance-identifier mynewdbcluster ^
    --db-snapshot-identifier mydbclustersnapshot ^
    --engine aurora-mysql ^
    --engine-version 5.7
```

 You can optionally specify the `--scaling-configuration` option to configure the minimum capacity, maximum capacity, and automatic pause when there are no connections\. Valid capacity values include the following: 
+  Aurora MySQL: `1`, `2`, `4`, `8`, `16`, `32`, `64`, `128`, and `256`\. 
+  Aurora PostgreSQL: `2`, `4`, `8`, `16`, `32`, `64`, `192`, and `384`\. 

 In the following example, you restore from a previously created DB cluster snapshot named *mydbclustersnapshot* to a new DB cluster named *mynewdbcluster*\. You set the `--scaling-configuration` so that the new Aurora Serverless v1 DB cluster can scale from 8 ACUs to 64 ACUs \(Aurora capacity units\) as needed to process the workload\. After processing completes and after 1000 seconds with no connections to support, the cluster shuts down until connection requests prompt it to restart\. 

For Linux, macOS, or Unix:

```
aws rds restore-db-cluster-from-snapshot \
    --db-cluster-identifier mynewdbcluster \
    --snapshot-identifier mydbclustersnapshot \
    --engine-mode serverless --scaling-configuration MinCapacity=8,MaxCapacity=64,TimeoutAction='ForceApplyCapacityChange',SecondsUntilAutoPause=1000,AutoPause=true
```

For Windows:

```
aws rds restore-db-cluster-from-snapshot ^
    --db-instance-identifier mynewdbcluster ^
    --db-snapshot-identifier mydbclustersnapshot ^
    --engine-mode serverless --scaling-configuration MinCapacity=8,MaxCapacity=64,TimeoutAction='ForceApplyCapacityChange',SecondsUntilAutoPause=1000,AutoPause=true
```

## RDS API<a name="aurora-serverless.restorefromsnapshot.api"></a>

 To configure an Aurora Serverless v1 DB cluster when you restore from a DB cluster using the RDS API, run the [RestoreDBClusterFromSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterFromSnapshot.html) operation and specify `serverless` for the `EngineMode` parameter\. 

 You can optionally specify the `ScalingConfiguration` parameter to configure the minimum capacity, maximum capacity, and automatic pause when there are no connections\. Valid capacity values include the following: 
+  Aurora MySQL: `1`, `2`, `4`, `8`, `16`, `32`, `64`, `128`, and `256`\. 
+  Aurora PostgreSQL: `2`, `4`, `8`, `16`, `32`, `64`, `192`, and `384`\. 