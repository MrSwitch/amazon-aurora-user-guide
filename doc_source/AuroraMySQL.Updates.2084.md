# Aurora MySQL database engine updates 2022\-01\-06 \(version 2\.08\.4\)<a name="AuroraMySQL.Updates.2084"></a>

**Version:** 2\.08\.4

Aurora MySQL 2\.08\.4 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\.

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\.

**Note**  
For information on how to upgrade your Aurora MySQL database cluster, see [Upgrading the minor version or patch level of an Aurora MySQL DB cluster](AuroraMySQL.Updates.Patching.md)\.

## Improvements<a name="AuroraMySQL.Updates.2084.Improvements"></a>

 **Security fixes and general improvements:** 
+ Fixed security issues related to Aurora MySQL integration with other AWS Services such as Amazon S3, Amazon ML, and AWS Lambda\.
+ Fixed an issue where the value of a TIMESTAMP column of an existing row is updated to the latest timestamp when all of the following conditions are satisfied: 1\. a trigger exists for the table; 2\. an INSERT is performed on the table that has an ON DUPLICATE KEY UPDATE clause; 3\. the inserted row can cause a duplicate value violation in a UNIQUE index or PRIMARY KEY; and 4\. one or more columns are of TIMESTAMP data type and have a default value of CURRENT\_TIMESTAMP\.
+ Fixed an issue which, in rare conditions, causes a writer instance to restart when an in\-memory data\-integrity check fails\.
+ Fixed an issue with parallel query which could cause the database to restart when executing SQL statements with a LIMIT clause\.

## Comparison with Aurora MySQL version 1<a name="AuroraMySQL.Updates.2084.Compare56"></a>

The following Amazon Aurora MySQL features are supported in Aurora MySQL Version 1 \(compatible with MySQL 5\.6\), but these features are currently not supported in Aurora MySQL Version 2 \(compatible with MySQL 5\.7\)\.
+ Asynchronous key prefetch \(AKP\)\. For more information, see [Optimizing Amazon Aurora indexed join queries with asynchronous key prefetch](AuroraMySQL.BestPractices.md#Aurora.BestPractices.AKP)\.
+ Hash joins\. For more information, see [Optimizing large Aurora MySQL join queries with hash joins](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\.
+ Native functions for synchronously invoking AWS Lambda functions\. For more information, see [Invoking a Lambda function with an Aurora MySQL native function](AuroraMySQL.Integrating.Lambda.md#AuroraMySQL.Integrating.NativeLambda)\.
+ Scan batching\. For more information, see [Aurora MySQL database engine updates 2017\-12\-11 \(version 1\.16\) \(deprecated\)](AuroraMySQL.Updates.20171211.md)\.
+ Migrating data from MySQL using an Amazon S3 bucket\. For more information, see [Migrating data from MySQL by using an Amazon S3 bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3)\.

## MySQL 5\.7 compatibility<a name="AuroraMySQL.Updates.2084.Compatibility"></a>

This Aurora MySQL version is wire\-compatible with MySQL 5\.7 and includes features such as JSON support, spatial indexes, and generated columns\. Aurora MySQL uses a native implementation of spatial indexing using z\-order curves to deliver >20x better write performance and >10x better read performance than MySQL 5\.7 for spatial datasets\.

This Aurora MySQL version does not currently support the following MySQL 5\.7 features:
+ Group replication plugin
+ Increased page size
+ InnoDB buffer pool loading at startup
+ InnoDB full\-text parser plugin
+ Multisource replication
+ Online buffer pool resizing
+ Password validation plugin
+ Query rewrite plugins
+ Replication filtering
+ The `CREATE TABLESPACE` SQL statement