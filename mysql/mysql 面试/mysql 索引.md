# mysql 索引

1. ab 组合索引，下面的sql 是否用到ab 索引是否生效。
    ```sql
    select a,b from table where a order by b desc
    ```
    答案： ab 组合索引生效
    
2. 