---
layout: post
title: SQL Injection - 101
tags: [programming,sql,sql-injection,cybersecurity]
date:  "2024-02-04"
---

### SQL Injection

Starting off with one of my favorites [https://xkcd.com/327/](xkcd comic)

![exploits-of-a-mom-xkcd](https://github.com/sudipidus/sudipidus.github.io/blob/main/resource/exploits-of-a-mom-xkcd.png)


SQL Injection is a way where a user is able to send some 'code' as part of the input data to the server all the way back to the database. (UI layer doesn't sanitize/validate, API layer let's the malicious 'code' reach the database because it's not mindful in how to separate 'query' and 'data').

Taking the example from xkcd:

```sql
-- Student's name (in the joke is):  " Robert'); drop table students;--"
```

Funny name :D

Now if API layer is doing simple string concatenation of this with the query and handing the output to the database driver, the driver is mostly going to assume to whole thing as one query. If it was a query for fetching record from students table, the malicious query would look something like this:

```sql
 select * from students where name='Rober'); drop table students;--)
 --- this is a comment in sql
 --- ; this terminates a sql statement
```

Here the malicious code we want to execute is 'drop table'.

- by putting ';' in front (we are getting rid of first part of the query. ;-> terminates)
- by putting '--' we are terminating the part after drop command


Even if UI has some data validation this could be easily bypassed by directly invoking the API.

If endpoint is something like:
```bash
http://insecure-api.com/users/$name

# we can send data as
http://insecure-api.com/users/Rober%27%29%3B%20drop%20table%20students%3B--

# Rober%27%29%3B%20drop%20table%20students%3B-- (is url encoded value of our malicious input)

```


### At API Layer (Backend)

- injection way: You form the query in application logic by doing strong concat/interpolation
- Safe way: you invoke the db driver with query and data (separately), or using prepared statements

Here is a short example in golang.

```go
import (
"database/sql"

_ "github.com/lib/pq"
)

func main() {
    connStr := "user=postgres dbname=school "
    db, err := sql.Open("postgres", connStr)
    if err != nil {
        log.Fatal(err)
    }

    name:=`Rober'); drop table students;--`
    query:=fmt.Sprintf("select * from students where name=%s",name)
    //This is bad.. this will create query verbatim and hand it off to underlying database driver

   
    // This hands off the query to database driver with a parameter placeholder for the data
    // Underlying driver takes care of execution and ensuring it's presented correctly to the database.
    rows, err := db.Query("SELECT * FROM students WHERE name = $1", name)
}
```

Question to reader:

- Can we make the underlying database driver more intelligent (not to slack off at application layer but to make the system more fool-proof)?

Turns out some database driver do it some extent. I found this snipped on popular postgres driver for go:

https://github.com/lib/pq/blob/3d613208bca2e74f2a20e04126ed30bcb5c4cc27/encode_test.go#L512
(guard against null byte sequence)


Study further:
- [sql injection cheat sheet](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html)