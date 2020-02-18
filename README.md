[![GoDoc](https://godoc.org/github.com/jinzhu/gorm?status.svg)](https://godoc.org/github.com/jinzhu/gorm)

# gorm
GORM Guides [https://gorm.io](https://gorm.io)

## Install
```bash
$ go get -u github.com/jinzhu/gorm
```

## Declaring Models
```go
type User struct {
  gorm.Model
  Name         string
  Age          sql.NullInt64
  Birthday     *time.Time
  Email        string  `gorm:"type:varchar(100);unique_index"`
  Role         string  `gorm:"size:255"` // set field size to 255
  MemberNumber *string `gorm:"unique;not null"` // set member number to unique and not null
  Num          int     `gorm:"AUTO_INCREMENT"` // set num to auto incrementable
  Address      string  `gorm:"index:addr"` // create index with name `addr` for address
  IgnoreMe     int     `gorm:"-"` // ignore this field
}
```

See more documentation model in here https://gorm.io/docs/models.html

## Conventions
```go
// gorm.Model definition
type Model struct {
  ID        uint `gorm:"primary_key"`
  CreatedAt time.Time
  UpdatedAt time.Time
  DeletedAt *time.Time
}

// Inject fields `ID`, `CreatedAt`, `UpdatedAt`, `DeletedAt` into model `User`
type User struct {
  gorm.Model
  Name string
}

// Declaring model w/o gorm.Model
type User struct {
  ID   int
  Name string
}
```

See more documentation conventions in here https://gorm.io/docs/conventions.html

## Connecting to database
### MySQL
```go
import (
  "github.com/jinzhu/gorm"
  _ "github.com/jinzhu/gorm/dialects/mysql"
)

func main() {
  db, err := gorm.Open("mysql", "user:password@/dbname?charset=utf8&parseTime=True&loc=Local")
  defer db.Close()
}
```

See more database connection documentation https://gorm.io/docs/connecting_to_the_database.html

## CRUD Interface
### Create
```go
user := User{Name: "Jinzhu", Age: 18, Birthday: time.Now()}

db.NewRecord(user) // => returns `true` as primary key is blank

db.Create(&user)

db.NewRecord(user) // => return `false` after `user` created

```

See more query in here https://gorm.io/docs/create.html

### Query
```go
// Get first record, order by primary key
db.First(&user)
//// SELECT * FROM users ORDER BY id LIMIT 1;

// Get one record, no specified order
db.Take(&user)
//// SELECT * FROM users LIMIT 1;

// Get last record, order by primary key
db.Last(&user)
//// SELECT * FROM users ORDER BY id DESC LIMIT 1;

// Get all records
db.Find(&users)
//// SELECT * FROM users;

// Get record with primary key (only works for integer primary key)
db.First(&user, 10)
//// SELECT * FROM users WHERE id = 10;
```

See more query in here https://gorm.io/docs/query.html

### Update
```go
db.First(&user)

user.Name = "jinzhu 2"
user.Age = 100
db.Save(&user)

//// UPDATE users SET name='jinzhu 2', age=100, birthday='2016-01-01', updated_at = '2013-11-17 21:34:10' WHERE id=111;
```

See more update docs in here https://gorm.io/docs/update.html

### Delete
```go
// Delete an existing record
db.Delete(&email)
//// DELETE from emails where id=10;

// Add extra SQL option for deleting SQL
db.Set("gorm:delete_option", "OPTION (OPTIMIZE FOR UNKNOWN)").Delete(&email)
//// DELETE from emails where id=10 OPTION (OPTIMIZE FOR UNKNOWN);
```

See more delete docs in here https://gorm.io/docs/delete.html