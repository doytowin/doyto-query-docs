# Sorting

```yaml
doyto.query.config:
  sort-fields-map:
    win.doyto.query.test.TestQuery:
      - id
      - username
      - userLevel
```

| Query String                            | ORDER BY clause                        | Memo                                             |
|-----------------------------------------|----------------------------------------|--------------------------------------------------|
| ?sort=id,desc                           | ORDER BY id DESC                       |                                                  |
| ?sort.id=asc\&sort=id,desc              | ORDER BY id ASC                        | `sort` will be ignored since `sort.id` is passed |
| ?sort.username=asc\&sort.userLevel=desc | ORDER BY username ASC, user_level DESC |                                                  |
