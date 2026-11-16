	本宝典所有操作SQL zoo上完成，数据来自SQL zoo

```sql
-- SQL查询语句语法结构和运行顺序
-- 语法结构
SELECT - FROM - WHERE - GROUP BY - HAVING - ORDER BY - LIMIT
-- 运行顺序
FROM - WHERE - GROUP BY - HAVING - SELECT - ORDER BY - LIMIT
```
![[Pasted image 20261116180003.png]]

## 基础语法和运行原理
### 1. SELECT & FROM

```sql
-- 语法
SELECT 字段名 FROM 表名

-- 1.从数据库中选择几列
SELECT name, continent FROM world;
SELECT name, continent, population FROM world;
-- 也可以修改导出列顺序
SELECT name, population, continent FROM world;

-- 2.一次性导出所有列
SELECT * FROM world;

-- 3. 指定导出列别名
SELECT name as 国家名称, continent as 大洲, population as 人口 FROM world;
-- 可以省略as(推荐)
SELECT name 国家名称, continent 大洲, population 人口 FROM world

-- 4.去重
-- 在SELECT之后、列名之前加distict，结果会去除后加的所有列内容都重复的结果
SELECT distinct name, continent FROM world
-- 如只输入continent则只返回八大州

-- 5.计算字段的运用
-- 如增加一列原数据库没有的人均GDP=gdp/population
SELECT name, continent, gdp/population 人均GDP FROM world
```

### 2. WHERE
```sql
-- 语法
SELECT 字段名 FROM 表名 [WHERE 表达式]
-- 作用
-- 表达式限定查询行必须满足的条件，where是为了通过表达式筛选出符合查询条件的行数据

-- 1.运算符
-- 常见运算符
-- = 等于；> 大于；< 小于；>= 大于等于；<= 小于等于；<>或!= 不等于
-- between a and b [a,b]；in 条件范围筛选；not in 不在该条件范围；is null 为空值；is not null 不为空值
-- and 与；or 或；not 非

-- 例1：查询人口数至少为2亿的国家
SELECT name, population
FROM world
WHERE population >= 200000000

-- 例2：查询德国（Germany）的人口
SELECT name, population
FROM world
WHERE name = 'Germany'

-- 例3：查询瑞典（Sweden）、挪威（Norway）、丹麦（Denmark）的国家和人口
SELECT name, population
FROM world
WHERE name in ('Sweden', 'Norway', 'Denmark')

-- 例4：查询面积（area）在250000和300000之间的国家和面积
SELECT name, area
FROM world
WHERE area between 250000 and 300000

-- 2.模糊查询like
-- 语法
SELECT 字段名 FROM 表名 WHERE 表达式 LIKE '通配符+字符'
--通配符
-- %通配符：用来匹配多个字符，不指定长度
-- _占位符:用来匹配单个字符

-- 例1：查询国家名中C开头ia结尾的国家
SELECT name
FROM world
WHERE name LIKE 'C%ia'

-- 例2：查询国家名中第二个字符为t的国家
SELECT name
WHERE name LIKE '_t%'

-- 例3：查询国家名中含有两个o且这两个o被两个字符隔开的国家
SELECT name
FROM world
WHERE name LIKE '%o__o%'

-- 3.多条件查询
-- 例：查询国家名中含有三个a且面积大于600000的国家及其面积，或者人口大于13亿且面积大于500万的国家及其面积
SELECT name, area
FROM world
WHERE name LIKE '%a%a%a%' AND area >= 600000
OR population > 1300000000 AND area >= 5000000
-- 注意：MySQL中and的执行优先级高于or，或者也可以加括号
```

### 3. ORDER BY
```sql
-- 语法：
SELECT 字段名
FROM 表名
[WHERE 表达式]
[ORDER BY 字段名 asc|desc]
-- 作用：规定查询出的结果集显示的顺序；字段顺序规定排序顺序
-- asc 升序（默认）；desc 降序

-- 例1：查询姓名以"Sir"开头的获奖者，获奖年份和科目；查询结果按年份降序排序，再按科目升序排序
SELECT winner, yr, subject
FROM nobel
WHERE winner like 'Sir%'
ORDER BY yr desc, subject asc

-- 例2：查询1984年所有获奖者的姓名和奖项科目，结果将化学奖和物理学奖排在最后，然后按照科目排序，再按照获奖者姓名排序
SELECT winner, subject
FROM nobel
WHERE yr = 1984
ORDER BY subject in ('chemistry', 'physics'), subject, winner
-- subject in ('chemistry', 'physics')相当于创造一个新的逻辑变量，在括号中相当于1，不在相当于0
```

### 4. LIMIT
```sql
-- 语法
SELECT 字段名
FROM 表名
[WHERE 表达式]
[ORDER BY 字段名 asc|desc]
[LIMIT [位置偏移量,]行数]
-- 限制查询结果集显示的行数

-- 例1：查询面积排名前三的面积数值是多少
SELECT name, area
FROM world
ORDER BY area desc
LIMIT 3

-- 例2：查询人口数第4到第7的国家和人口
SELECT name, area
FROM world
ORDER BY area desc
LIMIT 3, 4
```

### 5. 聚合函数 & GROUP BY
```sql
-- 聚合函数：适用于需要获取数据的汇总信息，例如sum和，avg平均，max最大，min最小
-- GROUP BY：规定依据哪个字段分组聚合，常和聚合函数联用
-- 语法
SELECT 字段名
FROM 表名
[WHERE 表达式]
[GROUP BY 字段名1]
[ORDER BY 字段名 asc|desc]
[LIMIT [位置偏移量,]行数]

-- 单独使用聚合函数
-- 例1：查询非洲总人口
SELECT sum(population)
FROM world
WHERE continent = 'Africa'

-- 例2：计算表格行数
SELECT count(*)
FROM world
-- 如改为：
SELECT count(*), count(gdp)
FROM world
-- 会发现二者并不一致，因为字段可能有空值；聚合函数忽略空值

-- 单独使用GROUP BY
-- 例3：去重
SELECT continent
FROM world
GROUP BY continent
-- 结果与distinct相同而逻辑不同，GROUP BY去重的逻辑是先分区再分组

-- 聚合函数和GROUP BY联用
-- 例4：查询每个大洲和大洲内的国家数量
SELECT continent, count(name)
FROM world
GROUP BY continent

-- 例5：查询2013至2015年每年每个科目的获奖人数，结果按年份从大到小、人数从大到小排序
SELECT yr, subject, count(winner)
FROM nobel
WHERE yr between 2013 and 2015
GROUP BY yr, subject
ORDER BY yr desc, count(winner) desc

-- GROUP BY之后，只能添加聚合字段，如不能ORDER BY winner，也不能SELECT winner；GROUP BY需要与SELECT相呼应，比如如果SELECT里需要展示非聚合字段winner,那么就把winner添加到GROUP BY中
```

### 6. HAVING & 简单运行原理

```sql
-- 语法
...
[HAVING 表达式]
...
-- 限定分组聚合后的查询行必须满足的条件；WHERE是聚合前筛选，选行；HAVING是聚合后筛选，选组

-- 例1：查询总人口数量至少为1亿的大洲
SELECT continent, sum(population)
FROM world
GROUP BY continent
HAVING sum(population) > 100000000

-- 运行原理
-- 例2：查询总人口数至少为3亿的大洲及其平均GDP，其中只有GDP高于200亿且人口数大于6000万或者GDP低于80亿且首都中含有三个a的国家计入计算，最后按国家数从大到小排序，只显示第一行
SELECT continent, avg(gdp) 平均GDP
FROM world
WHERE gdp > 20000000000 AND population > 60000000
OR gdp < 800000 AND capital LIKE '%a%a%a%'
GROUP BY continent
HAVING sum(population) >= 300000000
ORDER BY count(name) desc
LIMIT 1
```

### 7. 部分常见函数
```sql
-- 数学函数
round(x,y) -- 四舍五入函数
-- 对x值进行四舍五入，精确到小数点后y位
-- y为负值时，保留小数点左边相应的位数为0，不进行四舍五入

-- 字符串函数
concat(s1,s2,...) -- 连接字符串函数
-- 连接参数s1、s2等产生的字符串
-- 任意参数为null时，则返回null
-- 例题：
SELECT confirmed, deaths, recovered, recovered/confirmed, concat(round((recovered/confirmed)*100,2),'%') 治愈率
FROM covid
WHERE recovered/confirmed > 0.3
-- 例题：查询首都和名称，其中首都需是国家名称的扩展，但不能与国家名相同（如Mexico City与Mexico）
SELECT capital, name
FROM world
WHERE capital like concat('%',name,'%')
AND capital != name

replace(s,s1,s2) -- 替换函数
-- 使用s2替换s中的所有s1
-- 例题：
SELECT distinct name, replace(name,'a','替换') 替换
FROM covid

left(s,n) right(s,n) substring(s,n,len)
-- left函数返回字符串s最左边n个字符
-- right函数返回字符串s最右边n个字符
-- substring函数返回字符串s从第n个字符起取长度为len的子字符串，没有len值则从第n个字符起到最后一位
-- 例题：
SELECT distinct name, substring(name,2,3),substring(-2,3)
FROM covid

-- 数据类型转换函数
cast(x as type) -- 转换数据类型的函数
-- type参数可以填写char(n)、date、time、datatime、decimal等

-- 日期时间函数
year(date) month(date) day(date) -- 获取年月日的函数
-- 例题
SELECT whn 更新时间, year(whn) 年, month(whn) 月, day(whn) 日
FROM covid
WHERE recovered > 0

date_add(date,interval expr type) & date_sub(date,interval expr type) -- 对指定起始时间进行加减操作
-- date指定起始时间
-- expr指定起始时间添加或减去的时间间隔
-- type指示expr被解释的方式，如second、minute、hour、day、week、month、quarter、year等
-- 例如：date_add('2021-08-03 23:59:59',interval 1 second)返回2021-08-04 24:00:00；date_add('2021-08-03 23:59:59',interval 1 second)返回2021-08-04 00:00:00
-- 例题
SELECT whn 更新时间, date_add(whn,interval 2 day) 加2天
FROM covid
WHERE recovered > 0

datediff(date1,date2) -- 计算两个日期之间间隔的天数

date_format(date,format) -- 将日期和时间格式化，format随用随查

-- 条件判断函数
if(expr,v1,v2)

case when
case expr when v1 then r1 [when v2 then r2] ... [else rn] end
-- 例如：case 2 when 1 then 'one' when 2 then 'two' else more end;返回two
case when v1 then r1 [when v2 then r2] ... [else rn] end
-- 例如：case when 1<0 then 'T' else 'F' end;返回F
--例题
SELECT recovered 累计治愈人数, case when recovered = 1 then 'one' when recovered > 1 then 'more' else '0' end
FROM covid
WHERE recovered > 0
```

### 8.窗口函数

```sql
-- 语法
窗口函数over([partition by 字段名][order by 字段名 asc|desc])
-- partition by指定分区依据，order by指定排序依据
-- 窗口函数 = 在不合并原始数据行的情况下，对一组相关数据进行计算。相当于不影响原表格的情况下，新开了一个窗口执行操作，完成后再将结果匹配回原表格

-- 8.1 排序窗口函数
rank()over() -- 并列排名，后续跳号
dense_rank()over() -- 并列排名，后续不跳号
row_number()over() -- 不允许并列，逐行编号

-- 例题：查询每一年S14000021选区中所有候选人所在的团体和得票数，并对每一年中的所有候选人根据选票的高低赋予名次，最后根据团体和年份排序
SELECT yr, firstName, lastName, party, votes, 
       RANK()OVER(PARTITION BY yr ORDER BY votes desc) AS posn 
FROM ge
WHERE constituency = 'S14000021'
ORDER BY party, yr
-- 逻辑：读取ge - 筛选出S14000021选区 - 执行窗口函数，RANK()表明要排序，OVER()指定规则，PARTITION BY yr指定在每一年内排序，ORDER BY votes desc指定按得票数降序排序，别名为posn - 按照party再yr排序 - SELECT语句选出对应变量
-- 由此可见，这个语句可以加上排名，而不会改变原本的表显示内容和顺序

-- 8.2 偏移分析函数
lag(字段名,偏移量[,默认值])over() -- 读取之前行
lead(字段名,偏移量[,默认值])over() -- 读取之后行

-- 例题：查询法国和德国1月每天新增确诊人数，最后显示国家名、标准日期、当天截至时间累计确诊人数、每天新增确诊人数，按照截至时间排序
SELECT name, date_format(whn,'%Y-%m-%d') date, confirmed, (confirmed - lag(confirmed,1)over(PARTITION BY name ORDER by whn)) AS 新增
FROM covid
WHERE name IN ('France','Germany') AND month(whn)=1
ORDER BY whn
-- lag(confirmed,1)：表明confirmed字段，向上偏移一行，也就是读取上一行的数据
-- over(PARTITION BY name ORDER by whn)：按照国家划分窗口，按照时间排序，因为要使得上一行是下一行的同一国家上一行的数据
-- 此外，此代码有一个bug，会导致2021年1月1日的新增是拿2020年1月31日的数据计算的；由此可见偏移分析函数读取的是上一行而不是按照某数据顺序读取，故一定要在OVER()中排好序
-- 例题：查询意大利每周新增确诊数（每周一确诊相减），最后显示国家名、标准日期、每周新增人数
SELECT name, date(whn), confirmed - LAG(confirmed,1)OVER(ORDER BY whn) AS 每周新增
FROM covid
WHERE name = 'Italy' AND weekday(whn) = 0
ORDER BY whn
```

### 9.表连接
```sql
-- 表连接逻辑：遍历所有，向下增添
-- 先遍历两表中所有能匹配的记录，比如左表id为1的记录有1个，右表有3个，那么会生成三行记录，11、12、13，即全部组合一遍
-- FULL OUTER JOIN：保留全部记录，匹配完成后查找两表中尚未被匹配的记录补NULL（MySQL不支持）
-- INNER JOIN：只保留匹配成功的记录，即去掉存在尚未被匹配的记录的行
-- LEFT JOIN：保留左表全部记录，即右表不能匹配时保留右表的NULL
-- RIGHT JOIN：保留右表全部记录，即左表不能匹配时保留左表的NULL

-- 9.1 内连接（默认）
SELECT 字段名
FROM 表名1 [INNER] JOIN 表名2 ON 表名1.字段名 = 表名2.字段名

-- 9.2 左连接
SELECT 字段名
FROM 表名1 LEFT JOIN 表名2 ON 表名1.字段名 = 表名2.字段名

-- 9.3 右连接
SELECT 字段名
FROM 表名1 RIGHT JOIN 表名2 ON 表名1.字段名 = 表名2.字段名

-- 例题1：查询名字中有Mario的球员进球时双方队伍的名称及球员姓名
	-- 球员-赛事匹配来自表goal
	-- 赛事-队伍匹配来自表game
SELECT team1, team2, player
FROM game JOIN goal ON game.id = goal.matchid
WHERE player like '%Mario%'

-- 例题2：查询队伍1的教练是"Fernando Santos"的球队名称、比赛日期和赛事编号
	-- 教练-球队来自eteam
	-- 球队-赛事来自game
SELECT eteam.id, mdate, game.id
FROM game JOIN eteam ON game.team1 = eteam.id
WHERE coach = 'Fernando Santos'

-- 例题3：使用合适的连接显示所有教师及其所教授的科目名
	-- 教师-学系来自表teacher
	-- 学系-科目来自表dept
	-- 所有教师表示表teacher要全部保留
SELECT t.name, d.name
FROM teacher t LEFT JOIN dept d ON t.dept = d.id

-- 练习
-- 1.查询至少出演过第1主角30次的演员
SELECT name, count(actorid)
FROM casting c JOIN actor a ON c.actorid = a.id
WHERE ord = 1
GROUP BY name
HAVING count(movieid) >= 30

-- 2.查询在比赛前十分钟有进球记录的球员，他的队伍编号、教练、进球时间
SELECT player, teamid, coach, gtime
FROM goal g JOIN eteam e ON g.teamid = e.id
WHERE gtime <= 10

-- 3.查询每场比赛每个球队的得分情况，最后按照举办时间、赛事编号、队伍1和队伍2排序
SELECT mdate,
team1, SUM(CASE WHEN team1 = teamid THEN 1 ELSE 0 END) score1,
team2, SUM(CASE WHEN team2 = teamid THEN 1 ELSE 0 END) score2
FROM goal RIGHT JOIN game ON goal.matchid = game.id
GROUP BY mdate, team1, team2
ORDER BY mdate, matchid, team1, team2
```

### 10. 子查询

```sql
-- 子查询本身就是一段完整的查询语句，然后用括号()包裹嵌套在主查询语句中，可以多层嵌套，运行顺序先于主查询

-- 10.1 where基于子查询条件筛选
-- 例1：查询出GDP高于欧洲所有国家的所有国家
SELECT name
FROM world
WHERE gdp is not null
AND gdp > (
           SELECT max(gdp)
           FROM world
           WHERE continent = 'Europe'
           )

-- 例2：查询跟Argentina和Australia在同一大洲的所有国家及其所属大洲，并按照国家名进行排序
SELECT name, continent
FROM world
WHERE continent in (
                    SELECT continent
                    FROM world
                    WHERE name in ('Argentina','Australia')
                    )
ORDER BY name

-- 10.2 from基于子查询作为数据表
-- 例3：查询2017年所有在爱丁堡选区(S14000021-S14000026)当选议员所在constituency和party
-- 先筛选出2017年爱丁堡选区的所有议员，以及他们各自的排名，再选出得票最高者
SELECT constituency, party
FROM (
      SELECT constituency, party, votes,
      RANK()OVER(PARTITION BY constituency ORDER BY votes desc) AS posn
      FROM ge
      WHERE yr = 2017
      AND constituency between 'S14000021' and 'S14000026'
      ) AS rk
WHERE rk.posn = 1
-- 对窗口函数进行筛选必须使用子查询，因为筛选的时候窗口函数尚未执行
-- FROM子查询必须给别名

--练习题
-- 查询Europe人均GDP大于United Kingdom的国家
SELECT name
FROM world
WHERE gdp / population > (
                          SELECT gdp / population
                          FROM world
                          WHERE name = 'United Kingdom'
                          )
AND continent = 'Europe'

-- 查询population超过Canada但是少于Poland的国家，结果显示name和population
SELECT name, population
FROM world
WHERE population > (
                    SELECT population
                    FROM world
                    WHERE name = 'Canada'
                    )
AND population < (
                  SELECT population
                  FROM world
                  WHERE name = 'Poland'
                 )

-- 查询所有国家人口均≤25000000的大洲，及其国家名和人口
SELECT continent, name, population
FROM world
WHERE continent NOT IN (
                        SELECT continent
                        FROM world
                        WHERE population > 25000000)

-- 查询每个大洲面积最大的国家，显示该大洲、国家名和面积
SELECT continent, name, area
FROM (
      SELECT continent, name, area,
      RANK()OVER(PARTITION BY continent ORDER BY area desc) AS posn
      FROM world) AS rk
WHERE rk.posn = 1

-- 解法2：
SELECT continent, name, area
FROM world
WHERE (continent, area) IN (
                            SELECT continent, max(area)
                            FROM world
                            GROUP BY continent)

-- 查询德国和意大利每天新增治愈人数并从高到低排序，查询结果国家名，截至日期（输出格式为****年**月**日），按新增治愈人数排序
SELECT name, 日期, 新增治愈人数,
RANK()OVER(PARTITION BY name ORDER BY 新增治愈人数 desc) AS 排名
FROM (
SELECT name, date_format(whn,'%Y年%m月%d日') 日期,
recovered - LAG(recovered,1)OVER(PARTITION BY name ORDER BY whn) AS 新增治愈人数
FROM covid
WHERE name in ('Italy', 'Germany')
) AS win
ORDER BY 排名
-- WHERE、HAVING不能使用窗口函数，但是ORDER BY可以使用
```
