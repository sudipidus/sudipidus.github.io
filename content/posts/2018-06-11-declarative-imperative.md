---
date: "2018-06-11"
title: Declarative vs Imperative programming
layout: post
tags: [programming, declarative, imperative, programming-style]
---

Imperative programming focuses on 'how' and Declarative programming focuses on 'what'.

**Examples**

*C#*

```csharp
//Collection of integer objects
List<int> collection = new List<int> { 1, 2, 3, 4, 5 };

//imperatively checking and creating a list of odd numbers
List<int> results = new List<int>();
foreach(var num in collection)
{
    if (num % 2 != 0)
          results.Add(num);
}

//doing the same 'declaratively' by using LINQ
var results = collection.Where( num => num % 2 != 0);
```

*Java*

```java
List<Integer> numbers = new ArrayList<Integer>(Arrays.asList(1,2,3,5,8,13,21));

//declarative approach
List<Integer> oddList = new ArrayList<>();
for (int each: numbers){
    if (numbers%2!=0) oddList.add(each);
}

//imperative approach using java-8 stream
oddList = numbers.stream().filter(each -> each % 2 != 0).collect(Collectors.toList());
```


*SQL*

```sql
//finding top 5 scorers from a table
//sql is declarative language
select * from students order by marks desc limit 5;
//how does it happen internally? Consult your sql vendor (mysql, postgresql)
```


- Imperative programming style demands us to write step by step process detailing what to do and how to do.
- Declarative programming abstracts away as much detail as possible within underlying non-mutable functions and programmer just has to follow the internal API and state what they want
- It's all about abstraction. LINQ or java stream or SQL engine internally do everything in an optimized way so the programmer can rest assured and go about doing the important stuff.
