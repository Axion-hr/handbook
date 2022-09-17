# Time

## Postgres&#x20;

timestamp vs timestamptz (?)

&#x20;[https://medium.com/building-the-system/how-to-store-dates-and-times-in-postgresql-269bda8d6403](https://medium.com/building-the-system/how-to-store-dates-and-times-in-postgresql-269bda8d6403)

```
SELECT '2000-01-01 00:00:00 +00:00'::timestamptz at time zone 'Europe/Rome' as "Rome's timestamp";
+----------------------+
|  Rome's timestamp    |
+----------------------+
| 2000-01-01 01:00:00  |
+----------------------+
```

**timestamptz** doesn't store original timezone information! It is saved in UTC, but it allows calculation of different timezone at database level.
