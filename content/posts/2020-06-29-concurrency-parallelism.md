---
date: "2020-06-29"
layout: post
title: Parallelism, Concurrency And Golang
tags: [parallelism, concurrency, threads, golang, programming]
---


### Concurrecy Vs Parallelism (Go-lang Example)

This is the general understanding of concurrency vs parallelism.

### Parallelism:

   Having multiple threads do similar task which are independent of each other in terms of data and resource that they require to do so. Eg: Google crawler can spawn thousands of threads and each thread can do it's task independently.


### Concurrency:

   Concurrency comes into picture when you have shared data, shared resource among the threads. In a transactional system this means you have to synchronize the critical section of the code using some techniques like Locks, semaphores, etc.

[my answer on stack-overflow](https://stackoverflow.com/a/42340792/4589003)


### New perspective

   Concurrency is about composition of tasks. Instead of thinking in sequential imperative way you think of your program as a bunch of tasks that should be accomplished and in doing so those tasks

   - communicate to each other or the main thread if required

   - may run in separate kernel thread (this is not a hard requirement)

   [Rob Pike: Concurrency is not Parallelism](https://www.youtube.com/watch?v=cN_DpYBzKso)

   So concurrency is more about an increased layer of abstraction of composing tasks. In traditional languages like java with imperative thread management, you attain concurrency by creating threads and introducing syncing and messaging mechanism amongst them. This is obviously non trivial and introduces a lot of bugs. Ask a java developer how often he has to painfully go through threaddump to assess the application error. (BLOCKED threads)


   In modern languages (designed with concurrency in mind) like go-lang, concurrency is obtained at a higher abstraction level. 

### concurrency in golang 

   - we need a unit that's lighter than thread. (We all read how process is heavy and thread is light. It turns out threads are not that light. You have constraints on number of threads (unless you are okay with heavy context switching)) and their management. So go has go-routines which are lighter units. These are multiplexed to os threads. A go program can have thousands of go routines. Handling of those routines, assigning os thread to them, moving them around to different threads if required is handled by program runtime. Your code just has to focus on task composition.

   - go routines 'Don't communicate by sharing memory; they share memory by communicating' unlike the traditional way where you have a shared variable which acts as message system, go routines use channels to communicate which work like unix pipes (one of the most powerful ideas in computer science)


Using these simple ideas we achieve a higher level of abstraction to handle concurrency in go-lang. 
It's very common for a typical go program to have millions of go routines. (Of course they won't always run parallel but that's not really the point, your eventual throughput will improve and so will the correctness of the program)


### go program with concurrency and timer

Finally I present a simple go program that kicks off a timer and runs a timed quiz (reading off a csv file) using go routines and messaging. [Jon Calhoun Course](https://gophercises.com/)

```golang
package main

import (
	"encoding/csv"
	"flag"
	"fmt"
	"log"
	"os"
	"strings"
	"time"
)

func main(){
	csvFile:=flag.String("csv","problems.csv","a csv file in format (question, answer)")
	timeLimit:=flag.Int("limit",2,"time limit for the quiz")
	flag.Parse()
	file, err := os.Open(*csvFile)
	if (err!=nil){
		log.Fatalf("file %v couldn't be opened",*csvFile)
		os.Exit(1)
	}
	correct:=0
	timer:= time.NewTimer(time.Duration(*timeLimit) *time.Second )
	reader := csv.NewReader(file)
	lines, err := reader.ReadAll()
	if (err!=nil){
		fmt.Print("couldn't read the line")
	}else{
		problems := parseProblem(lines)
		for index, p := range problems {
			fmt.Printf("Problem: # %d  %v\n",index+1,p.question)

			//anonymous function with channel closure
			answerChannel:=make(chan string)
			go func(){
				var answer string
				fmt.Scanf("%s",&answer)
				//usage of closure to pass the data (channel is part of closure)
				answerChannel<-answer
			}()
			select {
			case <-timer.C:
				fmt.Printf("You scored %d out of %d\n",correct,len(lines) )
				return
			case answer:=<-answerChannel:
				//optionally you can use strings.TrimSpace
				if answer==strings.Trim(p.answer," "){
					correct++
				}

			}
		}
		fmt.Printf("You scored %d out of %d\n",correct,len(lines) )
	}


}

func parseProblem(lines [][]string) []problem{

	//YOu can use append (but better to use make if the size is known before-hand.. append would have
	//to figure out the size and allocate dynamically which can hinder performance
	problems := make([]problem, len(lines))
	for i, line := range lines {
		problems[i]=problem{
			question: line[0],
			answer: line[1],
		}
	}
	return problems
}

type problem struct {
	question string
	answer string
}
```

### Sample output

```golang
 cli-quiz go build main.go && ./main --limit 5
Problem: # 1  what is your name?
sudip
Problem: # 2  what is your age?
27
You scored 2 out of 2
➜  cli-quiz go build main.go && ./main --limit 2
Problem: # 1  what is your name?
You scored 0 out of 2
```

