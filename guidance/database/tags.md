# Database tags guidance

This guidance shows how to tag span, when you trace a Database access.

Here are some demonstrations.

### Trace a MySQL query statement
Background story:

Use Spring JdbcTemplate (Java ORM framework), query a user from `users`.

- Database env
  - vendor: Oracle MySQL
  - ip: 10.5.34.18
  - port: 3306
  - db_instance: consumers
  - username: readonly_user
- SQL: select * from users where user_id = 1

- Tags

|TAG_KEY|TAG_VALUE|
|-------------|-------------|
|db.type | sql |
|db.instance| consumers|
|db.user| readonly_user|
|db.statement| select * from users where user_id = 1|
|peer.ipv4| 10.5.34.18|
|peer.port| 3306|
|peer.service| mysql/jdbi/query|
|span.kind| client|
|component| Spring JdbcTemplate |

### Trace a Redis set
Background story:

Use [redis-py (Redis Python Client)](https://github.com/andymccurdy/redis-py), set a key:value pair into Redis.

- Redis env
  - vender: redis-py
  - ip: 19.84.11.8
  - port: 6379
  - db: 15
- Use redis-py:
```python
>>> import redis
>>> r = redis.StrictRedis(host='localhost', port=6379, db=0)
>>> r.set("mykey", "WuValue")
```

- Tags

|TAG_KEY|TAG_VALUE|
|-------------|-------------|
|db.type | redis |
|db.instance| 15|
|db.statement| SET mykey "WuValue"|
|peer.ipv4| 19.84.11.8|
|peer.port| 6379|
|peer.service| redis/redis-py/set|
|span.kind| client|
|component| redis-py |