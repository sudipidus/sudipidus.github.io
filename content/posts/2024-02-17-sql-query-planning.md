---
layout: post
title: Quirks of Query Planner
tags: [sql,postgres,query-optimization,query-planning,engineering]
date:  "2024-02-17"
---

## Query Planning

Every SQL database (postgres,mysql) has a query planner built into it whose job is to find
the best strategy to come up with the results given the query, its predicates and other expressions.
Following examples are from postgres where I explore around how query planner makes its decision based on the 
data size in the table and the state of the database even in the presence of index.

### Creating a test table with an index

```sql
CREATE TABLE students (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255),
    age INT
);

CREATE INDEX idx_students_name ON students (name);
```

### Creating a few entries to the table and analyzing a select query plan
(recall that we have an index on students name)

```sql
INSERT INTO students (name, age) SELECT md5(random()::text), (random() * 50 + 18)::int FROM generate_series(1, 100);
```

Now we query using students name

```sql
explain select * from students where name='Michael';
                       QUERY PLAN
---------------------------------------------------------
 Seq Scan on students  (cost=0.00..2.48 rows=1 width=41)
   Filter: ((name)::text = 'Michael'::text)
(2 rows)
```

Notice that although we have an index defined the query planner went ahead with the sequential scan.
(This is primarily because we have less data and sequential scan can benefit from space locality - when disk pages are
read into memory those are read in pages, nearby data is more likely to be present - same reason why arrays have
a better space locality than linked list)


I can still force it to use index by hinting my query planner

```sql
SET enable_seqscan = off;
SET
sql_lessons=# explain select * from students  where name='Michael';
                                    QUERY PLAN

----------------------------------------------------------------------------------
-
 Index Scan using idx_students_name on students  (cost=0.14..8.16 rows=1 width=41)
   Index Cond: ((name)::text = 'Michael'::text)
(2 rows)

sql_lessons=# RESET enable_seqscan;
```

Is it advisable to use this?
Largely, No. But if you have solid evidence (which you can obtain using explain analyse - which plans and executes
and compares the forecasts then definitely go for it.)


### Loading our table further so that query planner thinks cache-locality benefits aren't worth

```sql

INSERT INTO students (name, age) SELECT md5(random()::text), (random() * 50 + 18)::int FROM generate_series(1, 10000);

explain select * from students  where name='Michael';
                                    QUERY PLAN

----------------------------------------------------------------------------------
-
 Index Scan using idx_students_name on students  (cost=0.29..8.30 rows=1 width=41)
   Index Cond: ((name)::text = 'Michael'::text)
(2 rows)
```

Let's further explore by adding entries to a fresh table and see where it starts to do index scan 
and then drop those additional records and see what happens.

```sql
 INSERT INTO students (name, age) SELECT md5(random()::text), (random() * 50 + 18)::int FROM generate_series(1, 100);
INSERT 0 100
sql_lessons=# explain select * from students  where name='Michaell';
                       QUERY PLAN
---------------------------------------------------------
 Seq Scan on students  (cost=0.00..4.88 rows=1 width=41)
   Filter: ((name)::text = 'Michaell'::text)
(2 rows)

sql_lessons=# INSERT INTO students (name, age) SELECT md5(random()::text), (random() * 50 + 18)::int FROM generate_series(1, 1000);
INSERT 0 1000
sql_lessons=# explain select * from students  where name='Michaell';
                                    QUERY PLAN

----------------------------------------------------------------------------------
-
 Index Scan using idx_students_name on students  (cost=0.28..8.29 rows=1 width=41)
   Index Cond: ((name)::text = 'Michaell'::text)
(2 rows)

sql_lessons=# delete from students where id in (select id from students order by random() limit 1000);
DELETE 1000
sql_lessons=# explain select * from students  where name='Michaell';
                                    QUERY PLAN

----------------------------------------------------------------------------------
-
 Index Scan using idx_students_name on students  (cost=0.27..8.29 rows=1 width=41)
   Index Cond: ((name)::text = 'Michaell'::text)
(2 rows)
```

We see that initially with few records it did sequential and switched to index scanning when we loaded 
more data. We also dropped those exact additional rows but query planner still chose index scan. strange!!


Now we 'full vacuum' our table and see what happens

```sql
vacuum full students;
VACUUM
sql_lessons=# explain select * from students  where name='Michaell';
                       QUERY PLAN
---------------------------------------------------------
 Seq Scan on students  (cost=0.00..6.12 rows=1 width=41)
   Filter: ((name)::text = 'Michaell'::text)
(2 rows)
```

And we are back to doing sequential scan.

Indeed a lot goes behind the execution of our queries.

