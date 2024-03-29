### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-04-28 | Alfred Jiang | - |

### 方案名称
Unix时间戳介绍(Unix timestamp)

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
时间戳 \ Unix timestamp

### 需求场景
1. 服务器与客户端进行时间传送时

### 参考链接
1. [关于Unix时间戳(Unix timestamp)](http://unixtime.51240.com/)

### 详细内容

时间戳是自 1970 年 1 月 1 日（00:00:00 GMT）以来的秒数。它也被称为 Unix 时间戳（Unix Timestamp）。

　　Unix时间戳(Unix timestamp)，或称Unix时间(Unix time)、POSIX时间(POSIX time)，是一种时间表示方式，定义为从格林威治时间1970年01月01日00时00分00秒起至现在的总秒数。Unix时间戳不仅被使用在Unix系统、类Unix系统中，也在许多其他操作系统中被广泛采用。

##### 如何在不同编程语言中获取现在的Unix时间戳(Unix timestamp)？

| 语言  | 代码  |
|:-------------: |:---------------|
| Java | time |
| JavaScript | Math.round(new Date().getTime()/1000)<br/>getTime()返回数值的单位是毫秒 |
| Microsoft .NET / C# | epoch = (DateTime.Now.ToUniversalTime().Ticks - 621355968000000000) / 10000000 |
| MySQL | SELECT unix_timestamp(now()) |
| Perl | time |
| PHP | time() |
| PostgreSQL | SELECT extract(epoch FROM now()) |
| Python | 先 import time 然后 time.time() |
| Ruby | 获取Unix时间戳：Time.now 或 Time.new<br/>显示Unix时间戳：Time.now.to_i |
| SQL Server | SELECT DATEDIFF(s, '1970-01-01 00:00:00', GETUTCDATE()) |
| Unix / Linux | date +%s |
| VBScript / ASP | DateDiff("s", "01/01/1970 00:00:00", Now()) |
| 其他操作系统<br/>(如果Perl被安装在系统中) | 命令行状态：perl -e "print time" |
| Objective-C | [[NSDate new] timeIntervalSince1970]; |
| Swift | NSDate().timeIntervalSince1970 |

##### 如何在不同编程语言中实现Unix时间戳(Unix timestamp) → 普通时间？

| 语言  | 代码  |
|:-------------: |:---------------|
| Java | String date = new java.text.SimpleDateFormat("dd/MM/yyyy HH:mm:ss").format(new java.util.Date(Unix timestamp * 1000)) |
| JavaScript | 先 var unixTimestamp = new Date(Unix timestamp * 1000) 然后	commonTime = unixTimestamp.toLocaleString() |
| Linux | date -d @Unix timestamp |
| MySQL | from_unixtime(Unix timestamp) |
| Perl | 先 my $time = Unix timestamp 然后	my ($sec, $min, $hour, $day, $month, $year) = (localtime($time))[0,1,2,3,4,5,6] |
| PHP | date('r', Unix timestamp) |
| PostgreSQL | SELECT TIMESTAMP WITH TIME ZONE 'epoch' + Unix timestamp) * INTERVAL '1 second'; |
| Python | 先 import time 然后 time.gmtime(Unix timestamp) |
| Ruby | Time.at(Unix timestamp) |
| SQL Server | DATEADD(s, Unix timestamp, '1970-01-01 00:00:00') |
| VBScript / ASP | DateAdd("s", Unix timestamp, "01/01/1970 00:00:00") |
| 其他操作系统<br/>(如果Perl被安装在系统中) | 命令行状态：perl -e "print scalar(localtime(Unix timestamp))" |
| Objective-C | [NSDate dateWithTimeIntervalSince1970:1363948516]; |
| Swift | NSDate(timeIntervalSince1970: 1363948516) |

##### 如何在不同编程语言中实现普通时间 → Unix时间戳(Unix timestamp)？

| 语言  | 代码  |
|:-------------: |:---------------|
| Java | long epoch = new java.text.SimpleDateFormat("dd/MM/yyyy HH:mm:ss").parse("01/01/1970 01:00:00"); |
| JavaScript | var commonTime = new Date(Date.UTC(year, month - 1, day, hour, minute, second))
| MySQL | SELECT unix_timestamp(time)<br/>时间格式: YYYY-MM-DD HH:MM:SS 或 YYMMDD 或 YYYYMMDD |
| Perl | 先 use Time::Local 然后	my $time = timelocal($sec, $min, $hour, $day, $month, $year); |
| PHP | mktime(hour, minute, second, day, month, year) |
| PostgreSQL | SELECT extract(epoch FROM date('YYYY-MM-DD HH:MM:SS')); |
| Python | 先 import time 然后 int(time.mktime(time.strptime('YYYY-MM-DD HH:MM:SS', '%Y-%m-%d %H:%M:%S'))) |
| Ruby | Time.local(year, month, day, hour, minute, second) |
| SQL Server | SELECT DATEDIFF(s, '1970-01-01 00:00:00', time) |
| Unix / Linux | date +%s -d"Jan 1, 1970 00:00:01" |
| VBScript / ASP | DateDiff("s", "01/01/1970 00:00:00", time) |
| Objective-C | [date timeIntervalSince1970]; |
| Swift | NSDate().timeIntervalSince1970 |

### 效果图
（无）

### 备注
（无）
