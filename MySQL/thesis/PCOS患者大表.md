1. 门诊表先按照身份证号分组，把同一患者的所有就诊ID合并成一个列表。
2. 住院表也先按照身份证号分组，把同一患者的所有住院号合并成一个列表。
3. 将这两张已经按患者归并的表进行全外连接。
4. 对身份证号缺失的记录，分别从两张原始表中提取，单独添加到结果中。

```sql
-- 1.表 PCOS门诊信息 按照身份证号归并
SELECT
  `身份证号`,
  GROUP_CONCAT(
    DISTINCT `就诊ID号`
    ORDER BY `就诊ID号`
    SEPARATOR ','
    ) AS visit_id
FROM `门诊信息-pcos筛选（未排除？）`
WHERE `身份证号` IS NOT NULL
GROUP BY `身份证号`;

-- GROUP_CONCAT()语句
-- GROUP：按身份证号分区，CONCAT：连接函数，意即将一个身份证号下的多个就诊ID合并
-- DISTINCT：去除重复就诊ID号
-- ORDER BY：按照ID排序
-- SEPARTOR：使用','作为分隔符

-- 2.表 PCOS住院信息 按照身份证号归并
SELECT
  `身份证号`,
  GROUP_CONCAT(
    DISTINCT `住院号`
    ORDER BY `住院号`
    SEPARATOR ','
    ) AS admission_id
FROM `住院信息-pcos筛选`
WHERE `身份证号` IS NOT NULL
GROUP BY `身份证号`;
```

```sql
-- 3.全外连接
WITH
-- WITH A AS (), B AS ()：临时定义表A、B，()中是临时表的来源；写在SELECT之前

outpatient AS (
  SELECT
    `身份证号`,
    GROUP_CONCAT(
      DISTINCT `就诊ID号`
      ORDER BY `就诊ID号`
      SEPARATOR ','
      ) AS visit_id
  FROM `门诊信息-pcos筛选（未排除？）`
  WHERE `身份证号` IS NOT NULL
  GROUP BY `身份证号`
  ),

inpatient AS (
  SELECT
    `身份证号`,
    GROUP_CONCAT(
      DISTINCT `住院号`
      ORDER BY `住院号`
      SEPARATOR ','
      ) AS admission_id
  FROM `住院信息-pcos筛选`
  WHERE `身份证号` IS NOT NULL
  GROUP BY `身份证号`
  )
  
SELECT
  o.`身份证号`,
  o.visit_id,
  i.admission_id
FROM outpatient o
LEFT JOIN inpatient i
  ON o.`身份证号` = i.`身份证号`
  
UNION ALL
-- UNION：上下合并两张表，自动去除重复行
-- UNION ALL：上下合并两张表，不去除重复行

-- 上表遗漏了只有住院号的患者，下面补上
SELECT
  i.`身份证号`,
  NULL AS visit_id,
  i.admission_id
FROM outpatient o
RIGHT JOIN inpatient i
  ON o.`身份证号` = i.`身份证号`
WHERE o.`身份证号` IS NULL
```

```sql
-- 4.补充身份证缺失记录
WITH
outpatient AS (
  SELECT
    `身份证号`,
    GROUP_CONCAT(
      DISTINCT `就诊ID号`
      ORDER BY `就诊ID号`
      SEPARATOR ','
      ) AS visit_id
  FROM `门诊信息-pcos筛选（未排除？）`
  WHERE `身份证号` IS NOT NULL
  GROUP BY `身份证号`
  ),

inpatient AS (
  SELECT
    `身份证号`,
    GROUP_CONCAT(
      DISTINCT `住院号`
      ORDER BY `住院号`
      SEPARATOR ','
      ) AS admission_id
  FROM `住院信息-pcos筛选`
  WHERE `身份证号` IS NOT NULL
  GROUP BY `身份证号`
  )
  
SELECT
  o.`身份证号`,
  o.visit_id,
  i.admission_id
FROM outpatient o
LEFT JOIN inpatient i
  ON o.`身份证号` = i.`身份证号`
  
UNION ALL

SELECT
  i.`身份证号`,
  NULL AS visit_id,
  i.admission_id
FROM outpatient o
RIGHT JOIN inpatient i
  ON o.`身份证号` = i.`身份证号`
WHERE o.`身份证号` IS NULL

UNION ALL

SELECT
  NULL AS `身份证号`,
  `就诊ID号` AS visit_id,
  NULL AS admission_id
FROM `门诊信息-pcos筛选（未排除？）`
WHERE `身份证号` IS NULL

UNION ALL

SELECT
  NULL AS `身份证号`,
  NULL AS visit_id,
  `住院号` AS admission_id
FROM `住院信息-pcos筛选`
WHERE `身份证号` IS NULL
```