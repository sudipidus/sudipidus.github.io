---
date: "2020-01-19"
layout: post
title: On Code Readability 
tags: [code, coding-practice]
---

Almost everyone starts off coding in a very imperative fashion. You figure out the rules, syntax of the language and you figure out the logical solution to the problem and start off coding. As you start doing so
you discover some idiosyncracies of the language and you start focusing on making the code more and more 'concise'. If unchecked this can lead to obfuscation where you just increase the cognitive load of the program. You write a very smart-ass and clever piece of code which can take hours to decipher. The code is concise and does what you want but think in terms of the maintainer or even you yourself, fiew years/months down the line. May be you switched to another programming language and are probably thinking 'who the fuck wrote that?' until you 'git blame' it and find that it's you after all.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">“Programs must be written for people to read, and only incidentally for machines to execute.”<br><br>― Harold Abelson, Structure and Interpretation of Computer Programs <a href="https://twitter.com/hashtag/code?src=hash&amp;ref_src=twsrc%5Etfw">#code</a> <a href="https://twitter.com/hashtag/readability?src=hash&amp;ref_src=twsrc%5Etfw">#readability</a> <a href="https://twitter.com/hashtag/programming?src=hash&amp;ref_src=twsrc%5Etfw">#programming</a> <a href="https://twitter.com/hashtag/goodcodingpractice?src=hash&amp;ref_src=twsrc%5Etfw">#goodcodingpractice</a><a href="https://twitter.com/hashtag/codingisfun?src=hash&amp;ref_src=twsrc%5Etfw">#codingisfun</a></p>&mdash; sudip bhandari (@sudip_post) <a href="https://twitter.com/sudip_post/status/1218538610149842946?ref_src=twsrc%5Etfw">January 18, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script> 

After a series of discussions that ensued with this tweet I had the following insights:

- Every programmer aims to writes concise and unambiguous code. (fair enough)

- To do so you have to be familiar with the nitty gritty details of the language you are writing in. (You can't expect anyone to write code that is 'readable' to a code-illiterate person) (fair enough)

- You start over-emphasizing on 2 and start becoming more esoteric, using the hacks of the language to do some operation. Following is a lame js example:

```javascript
Array [ "ram", "sita" ]
b[Number(4>5)]
"ram"
b[Number(4<5)]
"sita"
//This works but why would anyone do it?
```

This is more common in dynamic languages like python, ruby, javascript. The lingo, way of doing things around this language is so strong and can easily be turned wild with obfuscation. You can write a clever ruby line that does some awesome trick but imagine yourself visiting that code a month later or someone else maintaining it. It's a nightmare



- Readable code is not short code. Readable code is not extra verbose code either. Being programmers we should be fairly literate in this regard and we shouldn't load our cognition with unnecessary details. Likewise we shouldn't load it with esoteric expressions either







