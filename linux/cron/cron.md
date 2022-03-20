# cron 

### 详解
```sh
 *      *      *     *     *      *   * 
{秒数} {分钟} {小时} {日期} {月份} {星期} {年份} 
```

**示例**

下面是一些cron任务示例。
```sh
* * * * * /home/dan/bin/script.sh: 每分钟运行。
0 * * * * /home/dan/bin/script.sh: 每小时运行。
0 0 * * * /home/dan/bin/script.sh: 每天零点运行。
0 9,18 * * * /home/dan/bin/script.sh: 在每天的9AM和6PM运行。
0 9-18 * * * /home/dan/bin/script.sh: 在9AM到6PM的每个小时运行。
0 9-18 * * 1-5 /home/dan/bin/script.sh: 周一到周五的9AM到6PM每小时运行。
*/10 * * * * /home/dan/bin/script.sh: 每10分钟运行。
```

每一个域可出现的字符如下：

* Seconds(秒): 可出现", - * /"四个字符，有效范围为0-59的整数
* Minutes(分): 可出现", - * /"四个字符，有效范围为0-59的整数
* Hours(小时): 可出现", - * /"四个字符，有效范围为0-23的整数
* DayofMonth(日期): 可出现", - * / ? L W C"八个字符，有效范围为0-31的整数
* Month(月份):  可出现", - * /"四个字符，有效范围为1-12的整数或JAN-DEc
* DayofWeek(星期):  可出现", - * / ? L C #"四个字符，有效范围为1-7的整数或SUN-SAT两个范围。1表示星期天，2表示星期一， 依次类推
* Year(年):  可出现", - * /"四个字符，有效范围为1970-2099年

每一个域都使用数字，但还可以出现如下特殊字符，它们的含义是：

(1)*：表示匹配该域的任意值，假如在Minutes域使用*, 即表示每分钟都会触发事件。

(2)?:只能用在DayofMonth和DayofWeek两个域。它也匹配域的任意值，但实际不会。因为DayofMonth和DayofWeek会相互影响。例如想在每月的20日触发调度，不管20日到底是星期几，则只能使用如下写法： 13 13 15 20 * ?, 其中最后一位只能用？，而不能使用*，如果使用*表示不管星期几都会触发，实际上并不是这样。

(3)-:表示范围，例如在Minutes域使用5-20，表示从5分到20分钟每分钟触发一次

(4)/：表示起始时间开始触发，然后每隔固定时间触发一次，例如在Minutes域使用5/20,则意味着5分钟触发一次，而25，45等分别触发一次.

(5),:表示列出枚举值值。例如：在Minutes域使用5,20，则意味着在5和20分每分钟触发一次。

(6)L:表示最后，只能出现在DayofWeek和DayofMonth域，如果在DayofWeek域使用5L,意味着在最后的一个星期四触发。

(7)W:表示有效工作日(周一到周五),只能出现在DayofMonth域，系统将在离指定日期的最近的有效工作日触发事件。例如：在 DayofMonth使用5W，如果5日是星期六，则将在最近的工作日：星期五，即4日触发。如果5日是星期天，则在6日(周一)触发；如果5日在星期一到星期五中的一天，则就在5日触发。另外一点，W的最近寻找不会跨过月份

(8)LW:这两个字符可以连用，表示在某个月最后一个工作日，即最后一个星期五。

(9)#:用于确定每个月第几个星期几，只能出现在DayofMonth域。例如在4#2，表示某月的第二个星期三。


字段 允许值 允许的特殊字符

秒 0-59 , - * /
分 0-59 , - * /
小时 0-23 , - * /
日期 1-31 , - * ? / L W C
月份 1-12 或者 JAN-DEC , - * /
星期 1-7 或者 SUN-SAT , - * ? / L C #
年（可选） 留空, 1970-2099 , - * /


### cron 表达式0 0/10 * * * 与 0 */10 * * *的区别

先说下各个字符代表的含义。0代表从0分开始，*代表任意字符，／代表递增。


* 0 0/10 * * *代表从0分钟开始，每10分钟执行任务一次。

* 0 */10 * * *代表从任务启动开始每10分钟执行任务一次。

**例如**：从5:07执行，该任务第一种写法会在5:10的时候进行执行，写法二会在5:17进行执行。