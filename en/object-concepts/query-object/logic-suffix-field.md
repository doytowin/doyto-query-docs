# Logic-Suffix Field

By default, the query conditions corresponding to the fields of the query object are connected by AND in GoooQo.

If we want to use the logical operator OR to connect the query conditions, we need to define a struct or array with the suffix Or in the query object.

GoooQo supports the following three definitions:

```go
type UserQuery struct {
	PageQuerygo
	//...
	NameStartOr *[]string
	UserOr      *UserQuery
	UsersOr     *[]UserQuery
}
```

#### NameStartOr \*\[]string

```go
userQuery := UserQuery{NameStartOr: &[]string{"Bob", "John", "Tim"}}
users, err := userDataAccess.Query(ctx, userQuery)
// SQL="SELECT id, name, score, memo, deleted FROM t_user 
// WHERE (name LIKE ? OR name LIKE ? OR name LIKE ?)" args="[Bob% John% Tim%]"
```

#### UserOr \*UserQuery

```go
userQuery := UserQuery{UserOr: &UserQuery{IdIn: &[]int64{1, 4, 12}, Deleted: P(true)}}
users, err := userDataAccess.Query(ctx, userQuery)
// SQL="SELECT id, name, score, memo, deleted FROM t_user 
// WHERE (id IN (?, ?, ?) OR deleted = ?)" args="[1 4 12 true]"
```

#### UsersOr \*\[]UserQuery

```go
userQuery := UserQuery{UsersOr: &[]UserQuery{
	{IdIn: &[]int64{1, 4, 12}, Deleted: P(true)},
	{IdGt: P(int64(10)), Deleted: P(false)},
}}
users, err := userDataAccess.Query(ctx, userQuery)
// SQL="SELECT id, name, score, memo, deleted FROM t_user
// WHERE (id IN (?, ?, ?) AND deleted = ? OR id > ? AND deleted = ?)"
// args="[1 4 12 true 10 false]"
```

Check:

[https://blog.doyto.win/post/goooqo-or-clause-en/](https://blog.doyto.win/post/goooqo-or-clause-en/)
