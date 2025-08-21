---
date: 2024-12-31
layout: post
title: Ollama
tags: [LLM, terminal, productivity, AI]
---


## What is Ollama?

Ollama is a platform that makes it easy to run large language models (LLMs) like Llama, Gemma, Mistral, and many others on your local machine. It provides a simple interface for downloading, running, and interacting with these models directly from the terminal or via API, without relying on cloud services. This is especially useful for developers who want privacy, speed, and full control over their AI workflows.

## Supported Models

Ollama supports a wide variety of open-source models, including:

- **Llama (Meta):** Llama 2, Llama 3, Llama 3.2, Llama 4, and more, in sizes from 1B to 400B parameters.
- **Gemma (Google):** Gemma 2, Gemma 3, Gemma 3n, with lightweight options for laptops and edge devices.
- **Mistral:** 7B, 12B, 22B, and 24B models, including Mistral Small and Mistral Large.
- **Phi (Microsoft):** Phi-3, Phi-4, and Phi-4 Mini, with models as small as 3.8B parameters.
- **Qwen (Alibaba):** Qwen2.5, Qwen3, Qwen3-coder, and more, with sizes from 0.5B to 235B.
- **Code Llama, StarCoder, DeepSeek, Granite, Moondream, and many others.**

You can browse the full model library at [ollama.com/library](https://ollama.com/library).

### Lightweight Models

If you have limited hardware, Ollama supports several lightweight models that can run on most modern laptops:

- **Gemma 3 (1B, 4B)**
- **Llama 3.2 (1B, 3B)**
- **Phi-4 Mini (3.8B)**
- **TinyLlama (1.1B)**
- **SmolLM2 (135M, 360M, 1.7B)**
- **Moondream 2 (1.4B, vision model)**

These models are suitable for quick tasks, code completion, and chat, even on machines with 8GB RAM.

## Hardware Requirements

The hardware needed depends on the size of the model:

- **1B–4B models:** Run comfortably on most modern laptops with 8GB RAM.
- **7B models:** At least 8GB RAM recommended.
- **13B models:** 16GB RAM recommended.
- **33B models:** 32GB RAM or more recommended.
- **70B+ models:** High-end workstation or server with 64GB+ RAM and a powerful GPU.

Ollama works on macOS, Windows, Linux, and even via Docker. For best performance, a recent CPU and (optionally) a dedicated GPU are helpful, but not required for smaller models.


### Example: Querying Llama from the Terminal

You can send a prompt to your local Llama instance using `curl`:

```
curl -X POST http://localhost:11434/api/chat \
-H "Content-Type: application/json" \
-d '{
      "model": "llama3",
      "messages": [{"role": "user", "content": "What is the capital of Germany?"}], "stream":false
    }'
```

This will return a response from the Llama model running on your machine.


## Use Cases and Integrations

Ollama can be integrated into a wide range of local workflows:

- **Terminal autocompletion:** Use plugins like `ollama.nvim` for Neovim or Emacs clients to get code and command suggestions as you type.
- **Bash command help:** Query for explanations or usage examples for shell commands directly from your terminal.
- **On-the-fly troubleshooting:** Ask the LLM for help when you encounter errors or need quick solutions.
- **Local chatbots and assistants:** Integrate with desktop apps like Open WebUI, Chatbot UI, or LibreChat for a local ChatGPT-like experience.
- **IDE integration:** Use extensions for VS Code, JetBrains, or other editors to get code completion, refactoring, and documentation help.
- **RAG (Retrieval-Augmented Generation):** Connect Ollama to your local files or knowledge base for context-aware answers.
- **APIs and scripting:** Use the REST API or libraries (Python, JS, Go, etc.) to build custom tools, automations, or agents.

### Community Integrations

There are many open-source projects that integrate Ollama with different platforms, including:
- **Open WebUI, Lobe Chat, Chatbox, big-AGI, LibreChat** (web/desktop UIs)
- **Neovim, Emacs, VS Code, JetBrains** (editor/IDE plugins)
- **LangChain, LlamaIndex, Spring AI, Haystack** (frameworks for building LLM-powered apps)
- **Discord, Telegram, Obsidian, Logseq** (chatbots, note-taking, and more)

You can find more integrations in the [Ollama Community Integrations](https://github.com/ollama/ollama#community-integrations) list.


## Why Use Ollama?

Running LLMs locally gives you privacy, speed, and flexibility. You can integrate AI into your workflow without sending data to external servers, and you can customize the experience to fit your needs. With support for a wide range of models and easy integration into your favorite tools, Ollama is a great choice for anyone looking to bring AI capabilities to their local environment.
