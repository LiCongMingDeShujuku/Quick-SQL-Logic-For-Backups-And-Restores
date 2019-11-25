![CLEVER DATA GIT REPO](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/0-clever-data-github.png "李聪明的数据库")

# POST TITLE
#### SECONDARY TITLE
**发布-日期: 2016年12月12日 (评论)**

![#](images/##############?raw=true "#")

## Contents

- [中文](#中文)
- [English](#English)
- [SQL Logic](#Logic)
- [Build Info](#Build-Info)
- [Author](#Author)
- [License](#License) 


## 中文
下面是一些创建一些备份、进行一些还原的SQL逻辑（logic）。这些不是什么新鲜或者疯狂的东西。只是一些能让人快速轻松地投入自动化逻辑（logic）的东西。当然，这些并不难找到，但它在这里依然会有它的作用。


## English
Here’s some quick SQL Logic to create some backups, and do some restores. Nothing new, or crazy here. Just something quick and easy for people to throw into their automation logic. Sure; these are not hard to find, but it’s helpful none the less to have it out there.

---
## Logic
```SQL
use master;
set nocount on
 
/********************************/
/********  ABOUT BACKUPS ********/
/********************************/
关于备份
 
--backup single database (overwrite backup file if exists (format), verify backup (checksum)
--备份单个数据库（如果存在则覆盖备份文件（格式），验证备份（校验和））
backup database [MyDatabase] to disk = 'E:\SQLBACKUPS\MyDatabase.bak' with format, checksum
 
--backup all databases  (overwrite backup file if exists (format), verify backup (checksum), the question mark(?) denotes @MyDatabaseName automatically.  this script does not require any changes.  just run.
--备份所有数据库（覆盖备份文件，如果存在（格式），验证备份（校验和），问号（？），自动用@MyDatabaseName表示。此脚本不需要任何更改。只需运行。
exec master..sp_msforeachdb
'if (''?'') not in (''tempdb'')
    begin
        backup database [?] to disk = ''E:\SQLBACKUPS\?.bak'' with format, checksum;
    end'
 
--verify backup date and location (occurred today)
--验证备份日期和地点（今天发生的）
select
    'database'      = upper(bs.database_name)
,   'backed up on'  = left(bs.backup_finish_date, 19)
,   'file location' = upper(bmf.physical_device_name)
from
    msdb..backupset bs join msdb..backupmediafamily bmf on bs.media_set_id = bmf.media_set_id
where
--check backups that occurred today
--检查今天发生的备份
    bs.backup_finish_date > (select dateadd(day, datediff(day, 0, getdate()), 0))
--get full database backup history only
--仅获取完整数据库备份历史记录
    and bs.type = 'D'
order by
    bs.backup_finish_date desc
 
 
/********************************/
/********  ABOUT RESTORES *******/
/********************************/
有关还原
 
--close open sessions against a database prior to restore.
--在还原之前关闭数据库的打开会话。
declare       @close_open_sessions varchar(max)
set           @close_open_sessions = ''
select        @close_open_sessions = @close_open_sessions + 
'kill ' + cast(spid as varchar) + ';' + char(10)
from          sysprocesses where db_name(dbid) = 'MyDatabase'
exec          (@close_open_sessions)
  
--restore database.  get logical and physical names from backup file with 'restore filelistonly from disk = 'E:\SQLBACKUPS\MyDatabase.bak' or from live database before the restore with 'select name, filename from [MyDatabase]..sysfiles'
--还原数据库。使用 'restore filelistonly from disk = 'E:\SQLBACKUPS\MyDatabase.bak' 从还原文件中或者用 'select name, filename from [MyDatabase]..sysfiles' 从还原之前的实时数据库中，获取逻辑和物理名称。
restore database [MyDatabaseTest] from disk = 'E:\SQLBACKUPS\MyDatabase.bak'
with
       replace
,      recovery
,      move 'MyDatabase_Data' to 'E:\Program Files\Microsoft SQL Server\MSSQL10_50.MSSQLSERVER\MSSQL\DATA\MyDatabase_Data_01.MDF'
,      move 'MyDatabase_Log'  to 'E:\Program Files\Microsoft SQL Server\MSSQL10_50.MSSQLSERVER\MSSQL\DATA\MyDatabase_Log_01.MDF'
,      stats = 25
go


```



[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

- **李聪明的数据库 Lee's Clever Data**
- **Mike的数据库宝典 Mikes Database Collection**
- **李聪明的数据库** "Lee Songming"

[![Gist](https://img.shields.io/badge/Gist-李聪明的数据库-<COLOR>.svg)](https://gist.github.com/congmingshuju)
[![Twitter](https://img.shields.io/badge/Twitter-mike的数据库宝典-<COLOR>.svg)](https://twitter.com/mikesdatawork?lang=en)
[![Wordpress](https://img.shields.io/badge/Wordpress-mike的数据库宝典-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

---
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Lee Songming](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/1-clever-data-github.png "李聪明的数据库")

