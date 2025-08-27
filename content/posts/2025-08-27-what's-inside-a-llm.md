---
date: 2025-08-27
layout: post
title: "How Ollama Stores and Runs Models Locally"
tags: [ollama, local-ai, llama, qwen, deepseek]
---

If you use [Ollama](https://ollama.ai) to run AI models locally, you might wonder what’s actually happening under the hood. Where are the files stored? What do they contain? And what happens when you *actually* run a model?

This post breaks it down.

## Where Ollama Stores Its Files

On macOS/Linux, Ollama stores its data under `~/.ollama/`. Here’s a peek inside:

```sh
tree ~/.ollama/
~/.ollama/
├── history
├── id_ed25519
├── id_ed25519.pub
├── logs/
│   ├── app-*.log
│   └── server-*.log
└── models/
    ├── blobs/
    │   ├── sha256-...
    │   └── sha256-...
    └── manifests/
        └── registry.ollama.ai/library/
            ├── llama3/
            │   └── latest
            ├── qwen3/
            │   └── 8b
            └── deepseek-r1/
                └── 8b
```

* **`manifests/`** → tiny JSON files (indexes) describing each model: metadata + which blobs to use.
* **`blobs/`** → large binary chunks (actual model weights), content-addressed by SHA-256 hash.

Together, these two pieces *are* the model.

---

## Inspecting a Model

You can get model details with:

```sh
ollama show llama3
```

Which outputs something like:

```
Model
  architecture        llama
  parameters          8.0B
  context length      8192
  embedding length    4096
  quantization        Q4_0

Capabilities
  completion

License
  META LLAMA 3 COMMUNITY LICENSE AGREEMENT
  Release Date: April 18, 2024
```

---

## A Sample Manifest

Here’s what a manifest JSON looks like:

```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
  "config": {
    "mediaType": "application/vnd.docker.container.image.v1+json",
    "digest": "sha256:3f8eb4da...",
    "size": 485
  },
  "layers": [
    {
      "mediaType": "application/vnd.ollama.image.model",
      "digest": "sha256:6a0746a1...",
      "size": 4661211424
    },
    {
      "mediaType": "application/vnd.ollama.image.license",
      "digest": "sha256:4fa551d4...",
      "size": 12403
    }
  ]
}
```

Think of the manifest as the *bill of materials* and the blobs as the *actual parts*.

---

## What Happens When You Run a Model

Running a model is a multi-step process:

### 1. Resolving the Model

When you call:

```sh
ollama run llama3:8b
```

Ollama resolves the tag (`llama3:8b`) to a manifest. If the manifest or blobs are missing locally, Ollama fetches them from the registry.

### 2. Mapping Blobs

The manifest lists which blobs (weights) are required. Missing blobs are downloaded and stored under `models/blobs/`. Deduplication is automatic since everything is hash-addressed.

### 3. Loading into Memory

Ollama’s backend (based on `llama.cpp`) loads the blobs into memory:

* **GPU VRAM** if you have a supported GPU and enough capacity.
* **System RAM** otherwise, with slower performance.

By default, blobs are **memory-mapped** (`mmap`), so only the needed parts get paged into RAM/VRAM. You can disable mmap to force a full load.

### 4. Runtime Caches

As the model generates text, it builds a **KV cache** (key/value tensors of past tokens). This cache grows with context length and is usually the biggest extra memory consumer. Ollama supports quantizing the KV cache to save VRAM.

### 5. The Inference Loop

For each token:

1. Prompt → tokens → embeddings
2. Transformer layers → attention + MLP
3. Next-token probabilities → sample
4. KV cache updated → repeat

Through this loop, the weights stay loaded while the KV cache grows. Performance depends heavily on VRAM, quantization, and context size.

---

## TL;DR

* **Manifests** = JSON indexes (model metadata + blob list).
* **Blobs** = actual weights (huge binary files).
* Ollama memory-maps blobs → loads into VRAM/RAM.
* KV cache grows during inference, making VRAM the key bottleneck.

So, when you run `ollama run llama3`, you’re essentially:

1. Resolving a manifest
2. Ensuring blobs are present
3. Mapping weights into memory
4. Running the inference loop with a growing KV cache

That’s Ollama’s model storage & execution in a nutshell.

