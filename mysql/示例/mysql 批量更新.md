# 批量更新

更新批量数据一条执行的

```mysql
UPDATE HT_LIVE_COURSE_CONDITION SET 
updated_at = 1646899807, 
updated_by = 0,  
sort = case live_id 
	when 2733 Then 0 
	when 2792 Then 1
	END
WHERE live_id IN (2733,2792)
```

多条执行执行

```mysql
UPDATE HT_LIVE_COURSE_CONDITION SET 
updated_at = 1646899807, 
updated_by = 0,  
sort= 0
where live_id 2733

UPDATE HT_LIVE_COURSE_CONDITION SET 
updated_at = 1646899807, 
updated_by = 0,  
sort= 1
where live_id 2792
```

