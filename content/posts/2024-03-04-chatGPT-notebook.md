---
layout: post
title: ChatGPT Notebook - A Chrome Extension
tags: [programming,chatGPT,LLM,chrome-extension]
date:  "2024-03-04"
---

### Trend of LLM

Large Language Models continue to find new use cases. Though they started off as smart chatbots developers are using the open source models (thanks to corporates like Meta who open source models and no thanks to OpenAI) and fine tuning them to be used for specific domains. It's so obvious that in the future these LLM are going to be our personal copilot, our wingman each tailored to each individual.

There is another interesting area of research in the field of LLM where there is a race to increase the context length. Turns out the process of handling more tokens in a context is very much compute and memory extensive. As of Feb, 2024: Google's Gemini model blew everyone out of the water with their capability to support 1 Million token in production. Larger the number of tokens, better the answers from LLM. The common consumer LLM like chatGPT and Gemini which work over web interface maintain a session of the chat. To achieve this chat capability these services internally append all question answers so far in the prompt behind the scene.

```bash
Q1:
A1:

Q2:
A2:
```

now when the user types question 3 (Q3), it's presented to the model with prepended Q1+A1+Q2+A2. This collective length is the token length. If text becomes longer than token length, LLM will no longer have access to earlier information. (all of these are in context)


So with these 2 things:
- instruction fine tuned LLM
- increasing trend of context length
we will be able to treat these chat sessions like a notebook. A Notebook because with the inreased context LLM will be able to provide us much better answer. If you are researching about topic A and a few days later you want to research something more under topic A, it makes sense to continue the chat from where you left off because it has all the context. Furthermore, we can name and save this chats and share over the internet as well.


Okay, all of this hyping up because I have built a chrome extension (chatGPT notebook - plan to include gemini too) which keeps a searchable (by name) interface to your chat history.

[Github Repo](https://github.com/sudipidus/chatgpt-notebook)

![chatGPT-notebook](https://raw.githubusercontent.com/sudipidus/sudipidus.github.io/main/resources/screencast_chatgpt_notebook.gif)


