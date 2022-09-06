# 1. 数据去重-取id最大值

```sql
SELECT
	* 
FROM
	`p_edifice_house_owner` 
WHERE
	`p_edifice_house_owner`.`deleted_at` IS NULL 
	AND (
	id IN ( SELECT MAX( id ) FROM `p_edifice_house_owner` WHERE `p_edifice_house_owner`.`deleted_at` IS NULL AND (( project_id = '9c4daa01-0e57-4e9e-aaf2-90ccaf504e16' )) GROUP BY phone, username )) 
ORDER BY
	id DESC 
	LIMIT 10 OFFSET 0
```