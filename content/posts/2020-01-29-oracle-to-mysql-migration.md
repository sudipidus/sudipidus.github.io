---
layout: post
title: Oracle to Mysql Migration 
tags: [oracle, mysql, rdbms, dba, database]
---

**ifnull <=> nvl**

Oracle: ```select nvl(null,1) from dual;```

Mysql: ```select ifnull(null,1) from dual;```

**dual is optional in mysql. Oracle needs a 'from expression' for all the queries. Dual table is just a dummy to serve that syntax**

```
desc dual;
Name                           Null     Type                                                                                                                                                                                          
------------------------------ -------- --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 
DUMMY                                   VARCHAR2(1)
```

**fetching rownum in oracle and mysql**

Oracle: ```select rownum, $column from  $table``` (rownum is a pseudocolumn in oracle)

Mysql: ```select row_number() over() as row_num, $column from $table
          SET @rownum:=0; SELECT @rownum:=@rownum+1 AS rownum, $column FROM $table``` (for older mysql versions)


[sqlines](http://www.sqlines.com/oracle-to-mysql) is a good resource on this topic.