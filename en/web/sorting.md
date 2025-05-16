# Sorting



```yaml
doyto.query.config:
  sort-fields-map:
    win.doyto.query.test.TestQuery:
      - id
      - username
      - userLevel
```

| Query String               | ORDER BY clause | Memo                                            |
| -------------------------- | --------------- | ----------------------------------------------- |
| ?sort.id=asc\&sort=id,desc | id ASC          | `sort` will be ignored if `sort.xxx`  is passed |
|                            |                 |                                                 |
|                            |                 |                                                 |
