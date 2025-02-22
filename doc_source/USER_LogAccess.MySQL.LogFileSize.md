# Overview of Aurora MySQL database logs<a name="USER_LogAccess.MySQL.LogFileSize"></a>

You can monitor the following types of Aurora MySQL log files:
+ Error log
+ Slow query log
+ General log
+ Audit log

The Aurora MySQL error log is generated by default\. You can generate the slow query and general logs by setting parameters in your DB parameter group\.

**Topics**
+ [Aurora MySQL error logs](#USER_LogAccess.MySQL.Errorlog)
+ [Aurora MySQL slow query and general logs](#USER_LogAccess.MySQL.Generallog)
+ [Aurora MySQL audit log](#ams-audit-log)
+ [Log rotation and retention for Aurora MySQL](#USER_LogAccess.AMS.LogFileSize.retention)
+ [Size limits on BLOBs written to the redo log](#USER_LogAccess.MySQL.LogFileSize.BLOBs)

## Aurora MySQL error logs<a name="USER_LogAccess.MySQL.Errorlog"></a>

Aurora MySQL writes errors in the `mysql-error.log` file\. Each log file has the hour it was generated \(in UTC\) appended to its name\. The log files also have a timestamp that helps you determine when the log entries were written\.

Aurora MySQL writes to the error log only on startup, shutdown, and when it encounters errors\. A DB instance can go hours or days without new entries being written to the error log\. If you see no recent entries, it's because the server didn't encounter an error that would result in a log entry\.

By design, the error logs are filtered so that only unexpected events such as errors are shown\. However, the error logs also contain some additional database information, for example query progress, which isn't shown\. Therefore, even without any actual errors the size of the error logs might increase because of ongoing database activities\.

Aurora MySQL writes `mysql-error.log` to disk every 5 minutes\. It appends the contents of the log to `mysql-error-running.log`\.

Aurora MySQL rotates the `mysql-error-running.log` file every hour\. It removes the audit, general, slow query, and SDK logs after either 24 hours or when 15% of storage has been consumed\.

**Note**  
The log retention period is different between Amazon RDS and Aurora\.

## Aurora MySQL slow query and general logs<a name="USER_LogAccess.MySQL.Generallog"></a>

You can write the Aurora MySQL slow query log and the general log to a file or a database table\. To do so, set parameters in your DB parameter group\. For information about creating and modifying a DB parameter group, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\. You must set these parameters before you can view the slow query log or general log in the Amazon RDS console or by using the Amazon RDS API, Amazon RDS CLI, or AWS SDKs\.

You can control Aurora MySQL logging by using the parameters in this list:
+ `slow_query_log`: To create the slow query log, set to 1\. The default is 0\.
+ `general_log`: To create the general log, set to 1\. The default is 0\.
+ `long_query_time`: To prevent fast\-running queries from being logged in the slow query log, specify a value for the shortest query runtime to be logged, in seconds\. The default is 10 seconds; the minimum is 0\. If log\_output = FILE, you can specify a floating point value that goes to microsecond resolution\. If log\_output = TABLE, you must specify an integer value with second resolution\. Only queries whose runtime exceeds the `long_query_time` value are logged\. For example, setting `long_query_time` to 0\.1 prevents any query that runs for less than 100 milliseconds from being logged\.
+ `log_queries_not_using_indexes`: To log all queries that do not use an index to the slow query log, set to 1\. Queries that don't use an index are logged even if their runtime is less than the value of the `long_query_time` parameter\. The default is 0\.
+ `log_output option`: You can specify one of the following options for the `log_output` parameter\. 
  + **TABLE** – Write general queries to the `mysql.general_log` table, and slow queries to the `mysql.slow_log` table\.
  + **FILE** – Write both general and slow query logs to the file system\.
  + **NONE** – Disable logging\.

  For Aurora MySQL 5\.6, the default for `log_output` is `TABLE`\. For Aurora MySQL 5\.7, the default for `log_output` is `FILE`\.

For more information about the slow query and general logs, go to the following topics in the MySQL documentation:
+ [The slow query log](https://dev.mysql.com/doc/refman/8.0/en/slow-query-log.html)
+ [The general query log](https://dev.mysql.com/doc/refman/8.0/en/query-log.html)

## Aurora MySQL audit log<a name="ams-audit-log"></a>

Audit logging for Aurora MySQL is called Advanced Auditing\. To turn on Advanced Auditing, you set certain DB cluster parameters\. For more information, see [Using Advanced Auditing with an Amazon Aurora MySQL DB cluster](AuroraMySQL.Auditing.md)\.

## Log rotation and retention for Aurora MySQL<a name="USER_LogAccess.AMS.LogFileSize.retention"></a>

When logging is enabled, Amazon Aurora rotates or deletes log files at regular intervals\. This measure is a precaution to reduce the possibility of a large log file either blocking database use or affecting performance\. Aurora MySQL handles rotation and deletion as follows:
+ The Aurora MySQL error log file sizes are constrained to no more than 15 percent of the local storage for a DB instance\. To maintain this threshold, logs are automatically rotated every hour\. Aurora MySQL removes logs after 30 days or when 15% of disk space is reached\. If the combined log file size exceeds the threshold after removing old log files, then the oldest log files are deleted until the log file size no longer exceeds the threshold\.
+ When `FILE` logging is enabled, general log and slow query log files are examined every hour and log files more than 24 hours old are deleted\. In some cases, the remaining combined log file size after the deletion might exceed the threshold of 15 percent of a DB instance's local space\. In these cases, the oldest log files are deleted until the log file size no longer exceeds the threshold\.
+ When `TABLE` logging is enabled, log tables aren't rotated or deleted\. Log tables are truncated when the size of all logs combined is too large\. You can subscribe to the `low_free_storage` event to be notified when log tables should be manually rotated or deleted to free up space\. For more information, see [Working with Amazon RDS event notification](USER_Events.md)\.

  You can rotate the `mysql.general_log` table manually by calling the `mysql.rds_rotate_general_log` procedure\. You can rotate the `mysql.slow_log` table by calling the `mysql.rds_rotate_slow_log` procedure\.

  When you rotate log tables manually, the current log table is copied to a backup log table and the entries in the current log table are removed\. If the backup log table already exists, then it is deleted before the current log table is copied to the backup\. You can query the backup log table if needed\. The backup log table for the `mysql.general_log` table is named `mysql.general_log_backup`\. The backup log table for the `mysql.slow_log` table is named `mysql.slow_log_backup`\.
+ The Aurora MySQL audit logs are rotated when the file size reaches 100 MB, and removed after 24 hours\.

To work with the logs from the Amazon RDS console, Amazon RDS API, Amazon RDS CLI, or AWS SDKs, set the `log_output` parameter to FILE\. Like the Aurora MySQL error log, these log files are rotated hourly\. The log files that were generated during the previous 24 hours are retained\. Note that the retention period is different between Amazon RDS and Aurora\.

## Size limits on BLOBs written to the redo log<a name="USER_LogAccess.MySQL.LogFileSize.BLOBs"></a>

For Aurora MySQL, there is a size limit on BLOBs written to the redo log\. To account for this limit, check the `innodb_log_file_size` parameter for your Aurora MySQL DB instance\. Make sure that it is 10 times larger than the largest BLOB data size found in your tables plus the length of other variable length fields \(`VARCHAR`, `VARBINARY`, `TEXT`\) in these tables\. For information about how to set parameter values, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\. For information about the redo log BLOB size limit, see [Changes in MySQL 5\.6\.20](http://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-20.html) in the MySQL documentation\.