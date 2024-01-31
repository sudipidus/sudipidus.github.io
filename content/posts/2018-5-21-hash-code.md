---
layout: post
title: Equals and HashCode in Java
tags: [java, hashing]
date:  "2018-05-21"
---

The parent of all 'Object' classes in Java has, among many, two notable methods: equals and hashCode. For the most of the part, we can go about without tinkering/overriding these methods, but they play a significant role when we are dealing with any kind of data structure whose implementation depends on hashing. Maps, Sets, and their various derivatives and implementations of the Java Collection framework depend on the hashing of objects. Let's consider a situation where we have to create a set of some objects for our consideration.

```java
class Room{
    private String name;
    private Integer capaciy;
    public Room(String name, Integer capacity){
        this.name=name;
        this.capacity=capacity;
    }
    //getters and setters

    //original inherited equals and hashCode
}
```




Let's try creating a unique collection of room objects using the Set interface's HashSet implementation in the Java collection framework.

```java
Room room1=new Room("Deluxe",20);
Room room2=new Room("Deluxe",20);

Set<Room> rooms = new HashSet<>();
rooms.add(room1);
rooms.add(room2);

//room1.hashCode() != room2.hashCode()

//rooms contains both rooms since they are basically two different objects in the
//Java heap and 'aren't equal'. They consequently have different hashCodes. 
```




When we instantiate objects of Room and start creating a set of unique room objects, we run into a problem. Since all the objects are 'new instances' and have different representations in memory, they aren't equal and have different hashCodes.

Since Set is basically a hashMap mapping an object's presence as a key to a true/false value, as the containment of the object in each instance gets a new hashCode, and even though name and capacity might be the same in our given example, each instance gets a new hashCode and is added to the set. Thus comes the need to override equals and hashCode so that the compiler knows what sort of equality we want to infer in an object and how would we be interested in calculating the hash.

```java
/*
For our use case two room instances would be considered same if their name and capacity were the same
*/
@Override
public boolean equals(Object o) {
    if (this == o)
        return true;
    if (o == null || getClass() != o.getClass())
        return false;
    Room room = (Room) o;
    return Objects.equals(name, room.name) &&
        Objects.equals(capacity, room.capacity);
}

/*
While generating hashCode, we should include the parameters that play a role
in determining the uniquenes of an instance
*/
@Override
public int hashCode() {
    return Objects.hash(capacity, name);
}
```




Now that we have set up the identity for our objects, and we overrode equals and hashCode, we can go about working with Set and other data structures that depend internally on hashing.