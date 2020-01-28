![MIKES DATA WORK GIT REPO](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_01.png "Mikes Data Work")        

# Use SQL To Find Time Between Database Create_Date And Restore_Date
**Post Date: September 18, 2015**        



## Contents    
- [About Process](##About-Process)  
- [SQL Logic](#SQL-Logic)  
- [Build Info](#Build-Info)  
- [Author](#Author)  
- [License](#License)       

## About-Process

<p>Here's some sql logic that will show you the time between when a database was created (create_date), and when it was restored (restore_date).</p>      


## SQL-Logic
```SQL
with last_restored as
(
select
databasename = sd.[name]
,   sd.[create_date]
,   sd.[compatibility_level]
,   sd.[collation_name]
,   rh.*
,   rownum = row_number() over (partition by sd.name order by rh.[restore_date] desc) from
master.sys.databases sd left outer join msdb.dbo.[restorehistory] rh on rh.[destination_database_name] = sd.name join master.sys.database_mirroring sdm on sd.database_id = sdm.database_id --where
--sd.database_id > 4
--and   sd.state_desc = 'online'
--and   sdm.mirroring_role_desc is null
--or    sdm.mirroring_role_desc != 'mirror'
)
select
'database' = upper(databasename)
,   'created_on' = replace(replace(left(create_date, 19), 'AM', 'am'), 'PM', 'pm') + ' ' + datename(dw, create_date)
--, 'restored_on' = replace(replace(left(restore_date, 19), 'AM', 'am'), 'PM', 'pm') + ' ' + datename(dw, create_date) ,    'restored_on' =
case
when restore_date is not null then replace(replace(left(restore_date, 19), 'AM', 'am'), 'PM', 'pm') + ' ' + datename(dw, create_date) else ''
end
,   'time_between' =
case
when restore_date is null then ''
else
cast(datediff(second, create_date, restore_date) / 60 / 60 / 24 / 30 / 12 as nvarchar(50)) + ' years, '
+ cast(datediff(second, create_date, restore_date) / 60 / 60 / 24 / 30 % 12 as nvarchar(50)) + ' months, '
+ cast(datediff(second, create_date, restore_date) / 60 / 60 / 24 % 30 as nvarchar(50)) + ' days, '
+ cast(datediff(second, create_date, restore_date) / 60 / 60 % 24 as nvarchar(50)) + ' hours, '
+ cast(datediff(second, create_date, restore_date) / 60 % 60 as nvarchar(50)) + ' minutes '
+ cast(datediff(second, create_date, restore_date) % 60 as nvarchar(50)) + ' seconds ' end
 
from
[last_restored]
where
[rownum] = 1
and databasename not in ('master', 'model', 'msdb', 'tempdb')
```




[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

[![Gist](https://img.shields.io/badge/Gist-MikesDataWork-<COLOR>.svg)](https://gist.github.com/mikesdatawork)
[![Twitter](https://img.shields.io/badge/Twitter-MikesDataWork-<COLOR>.svg)](https://twitter.com/mikesdatawork)
[![Wordpress](https://img.shields.io/badge/Wordpress-MikesDataWork-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

     
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Mikes Data Work](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_02.png "Mikes Data Work")

