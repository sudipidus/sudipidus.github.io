---
layout: post
title: Kind of method overriding in Go
tags: [go,golang,oop,object,class,method-overriding]
date:  "2024-08-16"
---

### Go's approach to Object 
Go is a fairly simplistic language and doesn't come with inbuilt classes and other features that most object oriented programming languages have. It gives you building blocks in the form of structs and it's upto programmer to design the abstraction around it. There is no inheritence - Go takes a very strong stance on composition over inheritence camp. (even other languages have practitioners who preach composition over inheritence but it's not strictly enforced). Go doesn't have explicit interface implementation either - which makes it possible to "discover abstraction instead of inventing them."


However, using struct embedding we can do a sort of "method override" in golang.

Following example demonstrates that fact:

```golang
package main

import "fmt"

type Animal struct {
	name string
}

func (A Animal) Speak() {
	fmt.Printf("%s speaks\n", A.name)
}

type Dog struct {
	*Animal
	breed string
}

func (D Dog) Speak() {
	fmt.Printf("%s speaks\n", D.breed)
}

func main() {
	A := Animal{"generic animal"}
	A.Speak()

	D := Dog{Animal: &A, breed: "German Shepherd"}
	D.Speak()
	D.Animal.Speak()
}
```

#### Output:

```
generic animal speaks
German Shepherd speaks
generic animal speaks
```

#### How about overloading?
Nope.
Go doesn't provide any way for programmers to overload methods. Although the language itself has a method `make` which is overloaded.

Eg:

```golang
m:=make(map[int]int) //making map
s:=make([]int,0,10) //making a slice
```

