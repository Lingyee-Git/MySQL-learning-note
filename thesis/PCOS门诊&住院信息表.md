
```sql
SELECT 就诊ID号, 身份证号, 诊断, 就诊时间, 家庭住址
FROM 门诊信息
WHERE 诊断 LIKE '%多囊%'
OR 诊断 LIKE '%PCOS%'
```

```sql
SELECT 住院号, 身份证号, 入院时间, 出院时间, 家庭住址
FROM 住院信息
WHERE 
       主诊断编码 LIKE 'E28.2%'
    OR 主诊断名称 LIKE '%多囊卵巢%'
    OR 其他诊断1 LIKE 'E28.2%'
    OR 其他诊断名称1 LIKE '%多囊卵巢%'
    OR 其他诊断2 LIKE 'E28.2%'
    OR 其他诊断名称2 LIKE '%多囊卵巢%'
    OR 其他诊断3 LIKE 'E28.2%'
    OR 其他诊断名称3 LIKE '%多囊卵巢%'
    OR 其他诊断4 LIKE 'E28.2%'
    OR 其他诊断名称4 LIKE '%多囊卵巢%'
    OR 其他诊断5 LIKE 'E28.2%'
    OR 其他诊断名称5 LIKE '%多囊卵巢%'
    OR 其他诊断6 LIKE 'E28.2%'
    OR 其他诊断名称6 LIKE '%多囊卵巢%'
    OR 其他诊断7 LIKE 'E28.2%'
    OR 其他诊断名称7 LIKE '%多囊卵巢%'
    OR 其他诊断8 LIKE 'E28.2%'
    OR 其他诊断名称8 LIKE '%多囊卵巢%'
    OR 其他诊断9 LIKE 'E28.2%'
    OR 其他诊断名称9 LIKE '%多囊卵巢%'
    OR 其他诊断10 LIKE 'E28.2%'
    OR 其他诊断名称10 LIKE '%多囊卵巢%'
```