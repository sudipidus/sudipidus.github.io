---
layout: post
title: Working with Spring Data and Json
tags: [spring, java, json, spring-data]
---

**1. Overview**

Json is, in many places, the preferred way of storing data due to it's brevity (mostly compared to xml) and human readability. While working in any web framework which involve operation with data models and templates, usage of json data format is almost inevitable. Languages like Python, Ruby even have this format conceptually built into the core language library itself. Hash in ruby and dictionaries in python which allow json-(dictionary/hash) conversion with an ease. Although java doesn't have similar counterpart built into the core language library there is an abundance of external libraries that help us achieve this goal. Two libraries in particular are jackson and google's gson.

**2. Working with Json in Spring**

At controller level, spring framework has a provision where it internally handles the representation of domain object into corresponding json using Jackson Library. This default choice of library can be overridden. SpringBoot by design follows the principle of convention over configuration so that developer doesn't have to worry much about making choices at each step. 


```java
@PutMapping(produces = {"application/json"})
@ResponseBody
public UpdateResponse someMethod(){ 
//do something
return UpdateResponseInstance;
}
```



In the above example whenever the controller gets called someMethod returns an instance of UpdateResponse which is internally converted to it's corresponding json representation. Spring internally uses Jackson API in order to accomplish this task.

However, if we want to get a json representation of our model at places other than controller level we can just go about doing this using Jackson ObjectMapper.

```java
import com.fasterxml.jackson.databind.ObjectMapper;
ObjectMapper mapper = new ObjectMapper();
SomeModelClass someModelObject = someModelRepository.findById(idValue).get();
String modelJson = mapper.writeValueAsString(someModelObject);
```

Likewise, we can convert incoming json string into corresponding domain object using Jackson. Since we need to know what object to map to we need two parameters: json string, domain class.

```java
import com.fasterxml.jackson.databind.ObjectMapper;
DomainObject domainObject = new ObjectMapper().readValue(json, DomainObject.class); 
```

**3. When model is not there**

Sometimes, however, we have to deal with json which don't yet have a corresponding class representation. In such cases I personally find Google's gson to be easier to use. For example:

```java
import com.google.gson.JsonObject;
JsonObject inputJson = new JsonParser().parse(requestString).getAsJsonObject();
Set<String> keys = inputJson.keySet(); // this is more useful when class is not modeled yet
```
This approach of doing things is particularly popular among programmers who use interpreted languages like python, ruby. While I feel it makes the whole process easier: no need to create a separate class just so you can parse it, I also feel it's error prone. What if the keys are missing? It has been my experience that working with huge structured json or any other data format (xml for example) becomes whole lot easier when we start templating them which helps us catch errors and we can rest assured and go about working with the data.

**4. Conclusion**

Data templating helps programmers work efficiently when working with huge structured data formats like json. Java has a rich set of libraries which help us just achieve it. At MVC level, spring-boot makes it really easier and inutuitive to work with such data template (pojos) to format conversions.
