---
layout: post
title: About Fast Compilation of Go
tags: [go, compilation, programming]
date:  "2024-09-01"
---
 
### Is Go Fast?

Go is faster than interpreted languages like python in terms of sheer execution since it's compiled to native executable format.

Go is slower than other compiled languages like C,Rust in terms of execution. This is primarily because although compiled go runs with a 'runtime' which does some housekeeping (scheduling and multiplexing goroutines, managing/freeing memory (GC) etc). This overhead is much smaller than a virtual machine required for languages like java but is an overhead nonetheless.

But where Go triumps is in terms of compilation speed. Popular joke being go was conceptualized when google engineers were waiting for their C++ code to compile. Faster compilation time plays a vital role in terms of developer experience. While it's common for java projects (gradle, maven etc) to take tens of minutes for build to happen for a reasonably sized project, it's mostly a matter of few seconds in go.


#### What makes it compile so fast?

1. Cyclic import is an error in go. Dependencies in a package form a directed acyclic graph. Compilation happens in reverse hierarchy. For a depency like A->B->C (C is compiled first and binary is used in B and then in A subsequently). Due to dependencies being acyclic they can be compiled in parallel as well.  This forces a certain restriction and discipline while organizing the code but it is worth the sacrifice.

2. Unused imports are an error. Unused variables are an error. You can't 'by mistake' leave a huge chunk of libraries imported into your codebase that's actually not used. (Such a disciplinary approach)

3. The language itself is very small with few keywords and the compiler has been designed in such a way that there is no need for symbol table.

4. The object file for a compiled Go package records export information not just for the package itself, but for its dependencies too. When compiling a package, the compiler must read one object file for each import but need not look beyond these files. Also the dependencies are stored in the beginning of these object files so the compiler can stop reading the file as soon as it gets the information. [Go : The Programming Language](https://www.amazon.com/dp/0134190440)


### Futher reading

- Most languages and frameworks have libraries and tools for 'live-reloading'. These are essentially file watchers who on detection of file changes (for certain kinds of files in certain frequency which are most of the times configurable) initiate the build, compilation process. This is certainly a pragmatic way to have a workflow with faster feedback. 

Eg: Nodemon for js, SpringBoot Developer tools for java, livereload for python, air for go

- While working on dockerized setups (like docker compose), it often takes more ten 10 seconds to bring the apps up/down etc but that's primarily because of the way container orchestration works. (SIGTERM being sent and a default wait of 10 seconds before SIGKILL - giving time or application to do some cleanup activity)
