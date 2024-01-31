---
date: "2019-05-02"
layout: post
title: Good Object Modelling 
tags: [object,oop,object-modelling,java]
---

## OOP is all about Encapsulation

Yes, that's right. Object Oriented Programming aims to encapsulate properties and methods into a consolidated object so that's operations can be carried out on the object. The whole aim was to move from procedural functions which were not easy to reason about and prove correctness. But this principle often gets violated and people write procedural code using objects. Classic Example is:

```java
class Rectangle {
    private Long length;
    private Long breadth;

    //gettters
    //setters
    //constructors
}
```

And caller calculates area by:
```java
Rectangle r = new Rectangle (3,4);
System.out.print("Area is: "+(r.getLength()*r.getBreadth());
```

This is a very bad example of misusing objects. This is infact a procedural, non encapsulated code which just happens to use object. This example could be worse if we had empty constructor for Rectangle.
```java
Rectangle r = new Rectangle();
r.setLength();
r.setBreadth();
```

Again a terrible misuse of objects. We have created an object which is impossible to reason about regarding it's correctness/completeness. I think such behavior is encouraged because we start off it writing an empty constructor, public getters/setters as soon as we declare a class. I have realized that this is indeed a very bad practice. More so, this is something that encourages the programmer to violate the basic foundations upon which OOP was built.

### 1. Don't write empty constructors:

 Don't let your objects be created `statelesss`. Having empty objects makes the code very hard to reason about. You never can rely what the state of object is. Imagine sending your friend to do something for you (make coffee lets say) and your friend goes. But wait, he can't walk because he doesn't have legs, or may be can't prepare because he doesn't have hands. See, you can't reason about the state of your object (friend). Unless there is a valid reasonable scenario (jackson object mapper comes to mind) avoid the use of empty constructors.

 *By avoiding empty constructors we avoid the scenario where object can be in inconsistent state. This also avoids the cases for programmers to have public setters.*
 
### 2. Don't start off with exposing all the getters

 It's very convinient and tempting to start off by exposing all the public getters. This is a serious violation of encapsulation principle. We might genuinely need some public getters (toString comes to mind) but add those only when required strictly.

 - Before you `get` from an oject, think why. What are you going to do with that value. Are you going to calculate something? Then is it not better to have it modelled as a method within the object. This would largely improve the consistency wherever the object is used within the code.

In our above example: we realized we needed to calculate area of Rectangle much longer after the object was created (a valid scenario, we can't always have a clear insight). Instead of getting length and breadth and multiplying them we should think whether we can obtain by still maintaining the encapsulation. 

#### Good Encapsulation

```java
class Rectangle{
    private Long length;
    private Long breadth;

    public Rectangle(Long length, Long breadth){
        this.length=length;
        this.breadth=breadth;
    }

    public Long getArea(){
        return this.length*this.breadth;
    }
}
```
Now this is fairly better encapsulation. We don't have empty constructor and setters either. We can fairly reason that if there is a rectangle object it's a consistent and valid one. If someone wants to compute area of that object there is a consistent method which serves the purpose eradicating the need of public getters.
