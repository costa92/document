| 字符     | 备注                                         |
| ------ | ------------------------------------------ |
| %%     | 一个文字的 %                                    |
| %a     | 当前locale 的星期名缩写(例如： 日，代表星期日)               |
| %A     | 当前locale 的星期名全称 (如：星期日)                    |
| %b     | 当前locale 的月名缩写 (如：一，代表一月)                  |
| %B     | 当前locale 的月名全称 (如：一月)                      |
| %c     | 当前locale 的日期和时间 (如：2005年3月3日 星期四 23:05:25) |
| %C     | 世纪；比如 %Y，通常为省略当前年份的后两位数字(例如：20)            |
| %Y     | 年                                          |
| %y     | 年份后两位 (00..99)                             |
| %m     | 月                                          |
| %d     | 日                                          |
| %D     |  按月计的日期；等于%m/%d/%y                         |
| %e     |  按月计的日期，添加空格，等于%_d                         |
| %F     | 完整日期格式，等价于 %Y-%m-%d                        |
| %H     | 小时 24 小时制 hour (00..23)                    |
| %I     | 小时 12 小时制 hour (01..12)                    |
| %M     | 分钟                                         |
| %S     | 秒钟                                         |
| %s     | 当前时间秒数                                     |
| %T     | 时钟 等于 %H:%M:%S                             |
| %c     | 本地时间和日期                                    |
| %j     | 一年中的第几天                                    |
| %W     | 一年中的第几周 星期一为一周的第一天                         |
| %w     | 星期几 week(0..6) ; 0 是星期天                    |
| %u     | 星期几 week(1..7) ; 1 是星期一                    |
| %U     | 一年中的第几周 星期日为一周的第一天                         |
| %V     | ISO 周数 星期一为一周的第一天, ISO 周编号                 |
| %x     | 日期 (e.g., 12/31/99)                        |
| %X     | 时间 (e.g., 23:13:48)                        |
| %z     | 时区 数字格式 (e.g., +0800)                      |
| %:z    | 时区 +08:00                                  |
| %::z   | 时区 +08:00:00                               |
| %:::z  | 时区 +08                                     |
| %Z     | 时区缩写 CST                                   |
| %n     | 换行                                         |
| %N     | 纳秒(000000000-999999999)                    |

1. 时间戳转换成时间格式

```Bash
date -d @1718866413  "+%Y-%m-%d %H:%M:%S"
=> 2024-06-20 14:53:33
```

2. 获取当前时间戳

```Bash
 date +%s
 => 1718869999
```

3. 获取当天的时钟

```Bash
date +%T  
=> 15:54:22
```

4. 获取某个时间节点对应的时间戳

```Bash
date -d "2021-12-20"  +%s
=> 1639929600

date -d "2021-12-20 20:20:10"  +%s
=> 1640002810
```

5. 获取当前时间或指定时间是全年的第几天

```Bash
# 当前时间
date  +%j
=> 172 
# 指定时间
date -d "2021-12-20 20:20:10"  +%j
=> 354
```

6. 当前时间是第几周

```Bash
date +%W
=> 25
```

7. 查看当前时间日前与时间

```Bash
date  "+%x %X"
=> 06/20/2024 04:09:04 PM
```

8. 查看当前时区

```Bash
date +%z
=> +0800
```

9. 设置时区获取时间

```Bash
# 当前时间戳转换
TZ='America/Los_Angeles' date "+%Y-%m-%d %H:%M:%S"
=> 2024-06-20 01:22:26

TZ="Asia/Shanghai" date "+%Y-%m-%d %H:%M:%S"
=> 2024-06-20 16:26:38

# 指定时间戳转换
TZ='America/Los_Angeles'  date -d @1718866413 "+%Y-%m-%d %H:%M:%S"
=> 2024-06-19 23:53:33

TZ="Asia/Shanghai" date -d @1718866413 "+%Y-%m-%d %H:%M:%S"
=> 2024-06-20 14:53:33
```

参考时区表 [https://en.wikipedia.org/wiki/List_of_tz_database_time_zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)

通过 `timedatectl list-timezones` 列出可用的时区

```Bash
timedatectl list-timezones

=>Africa/Abidjan
Africa/Accra
Africa/Addis_Ababa
Africa/Algiers
Africa/Asmara
Africa/Asmera
Africa/Bamako
Africa/Bangui
Africa/Banjul
Africa/Bissau
Africa/Blantyre
...
```

## 设置系统日期和时间

```Bash
 date –set="20140125 09:17:00"
```

通过 `man date` 查看 date 更多参数