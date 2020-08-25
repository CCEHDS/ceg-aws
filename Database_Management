# RDS Backup & Restore

Requires S3 bucket with access policy linked to db.  As root user under Master:
```exec msdb.dbo.rds_backup_database
@source_db_name='analysis_ceg',
@s3_arn_to_backup_to='arn:aws:s3:::eastlondondb/bak_files/analysis_ceg.bak',
@overwrite_s3_backup_file=1;```

                @source_db_name           : Source database name to create a full backup of.
                @s3_arn_to_backup_to      : S3 key ARN to store the backup file at.
                @kms_master_key_arn       : KMS customer master key ARN to encrypt the backup file with.
                @overwrite_s3_backup_file : Indicates whether to overwrite the specified file in S3 or not, if one exists. 1=overwrite 0=no overwrite
                @type                     : Type of backup to create. Valid options are: FULL and DIFFERENTIAL. Defaults to FULL.
                @number_of_files          : (Multifile backups only) Number of files to create for this backup.

RDS cannot overwrite db, therefore need to drop db and restore. As root user under Master:
```exec msdb.dbo.rds_restore_database
@restore_db_name='eldb2019',
@s3_arn_to_restore_from='arn:aws:s3:::eastlondondb/bak_files/eldb2019_PREP.bak'```

Output provides <task_id> that can be used to check progress:
``exec msdb.dbo.rds_task_status @task_id= <task_id>``
 
Restore will take a few minutes. Once complete Refresh

https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Procedural.Importing.html

#RDS Rename DB

As root user under Master:
``EXEC rdsadmin.dbo.rds_modify_db_name N'snomed_ct_2920', N'snomedct_2920';``

needs to be only login so check using
``SELECT *
FROM sys.dm_exec_sessions``

#SQL Server Database Space Management

Assess database Info
``SELECT
name
,file_id
,file_guid
,physical_name
,size/128.0 FileSizeInMB
,size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0 AS EmptySpaceInMB
FROM sys.database_files;``

Or quick space info
``EXEC sp_spaceused``

Shrink database with 1% free space (approx. 4mins)
``DBCC SHRINKDATABASE (eldb2020_DEV, 1);``

Shrink specifically database (.mdf) or log file (.ldf) to 1MB
``DBCC SHRINKFILE ('2019_EL_log',1);``
