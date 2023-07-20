# 1. 多表查询-JOIN

摘自信洁项目

```sql
SELECT
	per.*,
	ppu.real_name AS audit_user_name 
FROM
	p_enter_request AS per
	JOIN p_property_user AS ppu ON ppu.record_id = per.audit_user_id 
WHERE
	per.record_id = 'bfecffe0-cfd5-4294-bfc3-e310eea5f931' 
	AND per.deleted_at IS NULL 
ORDER BY
	per.id DESC 
	LIMIT 1
```

```sql
SELECT
	p.project_name,
	e.building_name,
	CONCAT_WS( '-', eh.house_unit, eh.floor_num, eh.house_name ) AS house_name 
FROM
	p_enter_request_house AS erh
	JOIN p_edifice_house AS eh ON erh.house_id = eh.record_id
	JOIN p_edifice AS e ON erh.edifice_id = e.record_id
	JOIN p_projects AS p ON erh.project_id = p.record_id 
WHERE
	enter_request_id = 'bfecffe0-cfd5-4294-bfc3-e310eea5f931' 
	AND erh.deleted_at IS NULL 
ORDER BY
	erh.id DESC
```

```sql
SELECT
	eho.record_id AS eho_id,
	eho.owner_type,
	p.project_name,
	CONCAT_WS(
		'-',
		e.building_name,
		eh.floor_num,
		eh.house_unit,
		house_name 
	) AS eh_name 
FROM
	p_edifice_house_owner AS eho
	JOIN p_edifice_house AS eh ON eho.house_id = eh.record_id
	JOIN p_edifice AS e ON eh.building_id = e.record_id
	JOIN p_projects AS p ON p.record_id = e.project_id 
WHERE
	username = '123' 
	AND phone = '15163855652' 
	AND eho.deleted_at IS NULL;
```

```sql
SELECT
	p.project_name,
	CONCAT_WS( '-', e.building_name, eh.house_unit, eh.house_name ) AS eh_name,
	eho.owner_type,
	poa.archive_id AS eho_id 
FROM
	p_house_owner_auth AS poa
	JOIN p_edifice_house_owner AS eho ON eho.record_id = poa.archive_id
	JOIN p_edifice_house AS eh ON eho.house_id = eh.record_id
	JOIN p_edifice AS e ON eho.edifice_id = e.record_id
	JOIN p_projects AS p ON eho.project_id = p.record_id 
WHERE
	user_id = '5c258d441d41c84e051152b4' 
	AND poa.deleted_at IS NULL 
	AND eho.deleted_at IS NULL 
	AND eh.deleted_at IS NULL 
	AND e.deleted_at IS NULL 
	AND p.deleted_at IS NULL
```