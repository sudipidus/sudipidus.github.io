---
layout: post
title: Debugging driven development (is bad)
tags: [engineering,programming,coding,debugging]
---


We have pretty powerful modern IDE and tooling around debugging (including structured logging and so on). It's so easy to yield to this paradigm of programming. Now I think it's quite a bad thing. Here are my reasons:

(disclaimer - my reasons against debugging only when considered as a primary approach to coming up with solution)

- too much reliance on debugging (if something goes wrong step through) takes away a key thing (thinking from first principles). Instead of strong assertion about what should be, we debug through and come up with different cases. Solution built for those ad hoc cases (because we didn't think through properly) won't generalize and we can damn sure expect one more scenario that would need stepping through!!

- if you are debugging often it implies that either the initial assertion of what should be is incomplete or it's not adequately covered with tests. Spending an extra with inclusive assertion and tests is much more productive than multiple hourly (even daily) debugging sessions in the future.

- excessive debugging also means that the programmer is thinking less (of course assumming something and not validating is even worse) but have tests for the assumptions.


