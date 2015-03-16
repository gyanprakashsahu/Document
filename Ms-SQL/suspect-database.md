### Repair A Suspect Database In MSSQL

If you have a database in MS SQL that is tagged as (suspect) and you are unable to connect to the database. Following are the reasons for this:

1. The database could have become corrupted.
2. There is not enough space available for the SQL Server to recover the database during startup.
3. The database cannot be opened due to inaccessible files or insufficient memory or disk space.
4. The database files are being held by operating system, third party backup software etc.
5. There was an unexpected SQL Server Shutdown, power failure or a hardware failure.

#### Steps to solve the problem:
Login at your server and connect with database through management studio and execute following queries step by step:

1. Execute the below mentioned TSQL code to identify all the databases which are marked as SUSPECT.

```
USE master
SELECT NAME,STATE_DESC FROM SYS.DATABASES 
WHERE STATE_DESC='SUSPECT'
```

2.Open the latest SQL Server Error Log and check for errors logged for the database which is marked as suspect.

3.When a database is in SUSPECT mode you will not be able to get connected to the database. Hence you need to bring the database first in EMERGENCY mode to repair the database.

```
EXEC sp_resetstatus [YourDatabase];
ALTER DATABASE [YourDatabase] SET EMERGENCY
```

4.Execute the DBCC CHECKDB command which will check the logical and physical integrity of all the objects within the specified database.

```
DBCC checkdb([YourDatabase])
```

5.Next step will be to bring the user database in SINGLE_USER mode by executing the below mentioned TSQL code.

```
ALTER DATABASE [YourDatabase] SET SINGLE_USER WITH ROLLBACK IMMEDIATE

```
6.Once the database is in SINGLE_USER mode execute the below TSQL code to repair the database. When you repair your database using REPAIR_ALLOW_DATA_LOSS option of DBCC CHECKDB command there can be some loss of data. Once the database is successfully repaired using REPAIR_ALLOW_DATA_LOSS option of DBCC CHECKDB command then there is no way to go back to the previous state.

```
DBCC CheckDB ([YourDatabase], REPAIR_ALLOW_DATA_LOSS)
```

7.Finally, execute the below mentioned TSQL command to allow MULTI_USER access to the database.

```
ALTER DATABASE [YourDatabase] SET MULTI_USER
```

Your database should no longer be tagged as (suspect) and you should be able to access it.
