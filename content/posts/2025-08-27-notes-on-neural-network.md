---
date: 2025-08-27
layout: post
title: "Notes On Neural Network"
tags: [neural-network,ai]
---


PS: These are notes from my study/review mostly from AI learning mode.


**Core Concepts**
- **Neuron = weights + bias + activation function.**
    - **Weights:** Measure how much each input matters.
    - **Bias:** Shifts the threshold a neuron needs to “fire.”
    - **Activation function:** Provides non-linearity, letting networks learn curves, not just straight lines.

**Single Neuron Example**
- Compute the raw sum: $$ z = w_1x_1 + w_2x_2 + ... + b $$.
- Apply activation (e.g., **sigmoid** squashes to $$ (0,1) $$; **ReLU** outputs max($$ 0, z $$)).

**Why Activation & Bias Matter**
- Without activation: Network can only model straight lines.
- Without bias: Outputs are stuck—can’t shift the function.

**Multi-layer Networks**
- Connecting neurons in layers (input, hidden, output) builds a **neural network**.
- **Feedforward structure:** Data flows forward through each layer.

**Training Neural Networks**
- **Forward pass:** Compute outputs; compare against target; measure error (loss).
- **Backpropagation:** Use derivatives (chain rule) to assign “blame” for error to each neuron’s weights/biases.
- **Gradient descent:** Update weights/biases by a small step to reduce the error (learning rate controls step size).

**Learning Rate**
- Too big: Network overshoots and fails to learn.
- Too small: Network learns extremely slowly.

**Problems in Deep Networks**
- **Vanishing gradient:** Gradients in early layers become tiny, stopping learning. (Activation like ReLU helps fix this.)
- Each neuron/bias is independent—enables diverse, nuanced learning.

**RNNs & Transformers**
- **RNNs:** Process sequences step by step, use hidden state (memory). Struggle with long-distance context and speed.
- **Transformers:** Use attention—each word sees every other word in parallel. Fast, handles long dependencies, forms basis of modern NLP models (like GPT, BERT).

**Attention & Softmax**
- Each word has Query, Key, Value vectors.
- Attention scores are calculated with dot products (e.g., “it” attends to “cat”).
- **Softmax** converts scores to probabilities (positive, sum to 1); sharpens big differences.
- **Why exponential?** Clean math, always positive, elegant derivative ($$ e^x $$'s derivative is itself), natural scaling.
- **Boltzmann distribution:** From physics, inspired softmax; models probabilities over energy states.

**Optimizers**
- Beyond basic gradient descent: methods like **momentum** (carries velocity for faster convergence) and others (not detailed here yet).

**In summary:**  
You now understand—from scratch—how neural nets are structured, how they use weights, bias, and activation, how training works (via backpropagation and gradient descent), why design choices like ReLU and softmax matter, the shift from RNNs to Transformers, and some historical intuition behind the exponential function’s role in machine learning.[1]

[1](https://chatgpt.com/c/68ab68dd-0c9c-8322-a903-c97e7a85ecc8)

