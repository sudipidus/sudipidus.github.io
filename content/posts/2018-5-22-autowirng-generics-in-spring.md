---
title: Autowiring Generics In Spring
layout: post
tags: [spring, spring-boot, autowiring, Dependency-Injection, IOC]
date: "2018-05-22"
---
`@Autowired` is heavily used in Spring for injecting dependencies. By entrusting the framework itself to inject dependencies (eg: repositories interface) programmer can rest assurred and focus on core business logic of the application.

We can also use `@Autowired` in order to initialize java collections which use Generics. Let's say we have an interface which could be implemented by a given number of classes. We can Register each of those classes in Spring Context with `@Context` annotation. Optionally we can also name the component using `@Component($name)`. This becomes particularly useful if we want to assosciate each context with a key-word. Using component name also enables to qualify required bean whenever spring tries to autowire using `ualifier($beanName)`. One use case could be that the key-word is json field name and the corresponding class is the utility class to operate on the model assosciated with that json.

Sample Interface:
```java
interface GenericUpdater{
    public boolean act();
}
```

There are 'n' number of implementations.
```java
@Component("inventory")
public InventoryUpdater implements GenericUpdater{
    @Overrides
    public boolean act(){
        //do some stuff
        return true;
    }
}
```

Now we can Autowire a collection. Let's say, a map which stores each of those 'implementors' with context name.
```java

```java
@Autowired
//Some Generic Collection
Map<String,GenericUpdater> updaterMap;
```

Spring now scans for all the implementations of `GenericUpdater` and injects them into this map keyed with the name given in `@Component($name)`.


Stack Overflow answer demo:


[see this](https://stackoverflow.com/a/50481837/4589003)