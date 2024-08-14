---
layout: post
title: Thinking about Gall's law - Designing Systems
tags: [system-design, gall's law, desing-principles]
date:  "2024-08-14"
---
 
I encountered this quote while reading Martin Klepmann's Desining Data Intensive Application which got me thinking:


> A complex system that works is invariably found to have evolved from a simple system that works. The inverse proposition also appears to be true: A complex system designed from scratch > never works and cannot be made to work.

> - John Gall, Systemantics (1975)


Apparently this is a famous known "laws" in software engineering. (although the original context in which the author said this is in regards to general systems (mechanical systems like Titanic ship or human systems like an organization; come to think of it, even software systems are just an extension of such systems).

Statements as generic as this are of course prone to a lot of exceptions but I will take this as a contextual heuristic and think about some of the examples:

- WWW: World wide web was designed without any grandiose plan of it becoming as big as it is today or as complex as it is today. It was designed with fundamental simplicity and the complexity go evolved along with the usage. Had it been initially designed with complexity (foreseeing what could happen in the future, who knows how it would have turned out)

- KISS: Keep it simple stupid. In software engineering it's kind of assumed as prudent to think and accomodate for future use cases. Softwares written that way are difficult to maintain and can cause additional issue because of those loaded assumption. What should happen instead is the design which inherrently gives flexibility to accomodate changes in the future not the loaded assumptions themselves. How to make those possibilities open is a mix of design choice as well as the features provided by the language.

- Interfaces (Abstraction) should be discovered not invented:  This is attributed to one of the Go designers Rob Pike and to me this is such a profound insight. Although said in terms of interfaces (one of Go's features) this is really about abstraction and design. Inventing or starting off with interfaces (abstraction) is akin to starting off with loaded assumption. Doing this in a language like Java where interfaces are explicitly implemented becomes even a design and maintainence nightmare. Instead you start off with what's required and if it evolves to an extent where it better be abtracted then go for it. Simplicity is the ultimate sophistication indeed.