---
layout: post
title: Let's Talk Tooling
tags:
  - tooling
  - programming
  - engineering
date: 2024-12-31
---
### Tooling Matters
I have been working as a software engineer for about 8 years as of this writing. Having worked in various types of organizations during this period (3 engineers starting up to hypergrowth phase companies to fairly established ones) I have had exposure various kinds of codebases, practices, tooling, conventions etc and I have come to realize how important tooling can be. Let's talk examples:

*How easy & quick is it for someone to get started with your project?*
- Say a new person joins the org, how fast can that person be upto speed with a working setup
- What is the degree of automation? startup scripts, database migrations, test scripts etc
- What is the ease of development environment setup? (language, framework, libraries)


Some features that have impressed me:
- Containerization (docker or similar technology to encapsulate the dependencies and the steps)
- Orchestration even for the local development setup ([[docker-compose for local dev]])
- It's not a bad idea to commit IDE run configurations ([[Check IDE config to git]])
- database migration scripts (with linter)
- Documented API (Eg: oas documentation - yamls or annotations) -> publish them to your documentation portal (using openapi) and also to postman collection (or any other similar tool) (It's a breeze for annotation based (eg: Springboot and swagger) but with careful review yaml based documentations can also be kept in sync with the changes)