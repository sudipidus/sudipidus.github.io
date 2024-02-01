---
layout: post
title: Currying in functional programming
tags: [functional-programming,programming,currying,golang]
date:  "2024-02-01"
---

### Currying
Currying is an idea of decomposing a function and making the expression more clearer. (Note: it's just expression decomposition and not execution)

##### Example In Go:

```go
package main

import (
	"fmt"
	"strings"
)

func main() {

	fmt.Println(someComplexOperation("hello", "there", "programmer"))

	fmt.Println(someComplexOperation("hello", "there", "manager"))

	fmt.Println(someComplexOperation("hello", "there", "CEO"))

	// Currying this function
	// By looking at the fuction we see that it can be decomposed, first step of computing from first 2 arguments
	// can be done and reused..
	intermediateFunction := someComplexOperationCurried("hello", "there")
	fmt.Println(intermediateFunction("programmer"))
	fmt.Println(intermediateFunction("manager"))
	fmt.Println(intermediateFunction("CEO"))

}

func someComplexOperation(a, b, c string) string {
	{
		a = strings.ToUpper(a)
		b = strings.ToLower(b)
	}
	return fmt.Sprintf("%s  %s  fellow %s", a, b, c)
}

func someComplexOperationCurried(a, b string) func(string) string {
	return func(c string) string {
		{
			a = strings.ToUpper(a)
			b = strings.ToLower(b)
		}
		return fmt.Sprintf("%s  %s  fellow %s", a, b, c)
	}
}
```

Output:
```bash
HELLO  there  fellow programmer
HELLO  there  fellow manager
HELLO  there  fellow CEO
HELLO  there  fellow programmer
HELLO  there  fellow manager
HELLO  there  fellow CEO
```



In the above example `someComplexOperation` is a function which operates on 3 arguments. From the usage pattern we identified that some operation is common and thus for clearer expresion we created a curried version of that function which returns another function (accepting a string and returning a string)


One thing to note is this doesn't speed up or simplify the actual execution at all. Eg if we had some time consuming step involving first 2 args:

```go
    {
		a = strings.ToUpper(a)
		b = strings.ToLower(b)
        time.Sleep(3*time.Second)
	}
```

Both the curried and non curried expression would take the same time. Because actual invocation of all functions happens in the final step.

Here is one more realistic example:
```go
func GenerateConfig(env string) func(component string) string {
	return func(component string) string {
		// Generate configuration based on environment and component
		return fmt.Sprintf("%s environment - %s configuration", env, component)
	}
}

func main() {
	// Create specialized functions for Production and Development environments
	prodConfig := GenerateConfig("Production")
	devConfig := GenerateConfig("Development")

	// Use the specialized functions to generate configuration strings
	fmt.Println(prodConfig("Database")) // Production environment - Database configuration
	fmt.Println(devConfig("Logging"))    // Development environment - Logging configuration
}
```


Currying in functional programming is idea-wise very similar to object construction decomposition (i.e. builder pattern in OOP) where we create the object one step at a time and each builder step returns an intermediate step upon which we can further build before finally invoking build.

