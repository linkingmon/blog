---
title: "Building RAG + LLM System"
date: 2026-05-08
tags: ["rag", "llm", "ai", "open-webui"]
---

*[Dictated orally and compiled by ChatGPT]*

Recently, I have been experimenting with OpenWebUI and trying to understand how it builds a ChatGPT-like interface on top of different language models, tools, RAG pipelines, and memory systems.

At first glance, OpenWebUI looks like a frontend chat application. But after digging into it more deeply, I found that the interesting part is not only the UI itself. The real value is how it connects many different components together: OpenAI-compatible models, Anthropic-style models, local LLMs, MCP tools, Python interpreters, image analysis tools, web search, RAG, and its own built-in memory system.

## The System Prompt Matters More Than I Expected
One thing that surprised me a lot is how important the system prompt is.

When I first built my own RAG system, I asked Codex to generate the prompt for me. The prompt was something like: "Please answer the question based on the following RAG chunks. If you do not know the answer, say you do not know. ". This looks reasonable at first. However, in practice, the generated answers were often too conservative. The model frequently replied that it did not know the answer, even when the retrieved chunks actually contained enough information.

This made me realize that RAG is not only about retrieval quality. Of course, chunking, embedding, and relevance search are important. But after the chunks are retrieved, the system prompt still decides how the model should interpret and use them.

So I started tuning the prompt manually. I had many test cases, so I could compare different behaviors repeatedly. At the same time, I compared my own implementation with OpenWebUI’s implementation. After spending quite a long time analyzing both sides, I found that the prompt design was one of the key differences.

The prompt needs to guide the model to use the retrieved context properly, but it should not make the model become too afraid of answering. This balance is actually quite subtle.

## Memory as a Tool

Another interesting part is OpenWebUI’s memory system.

Originally, I thought memory was just some hidden context automatically injected into the conversation. But after reading more of the implementation, I found that it can also be understood as a tool.

The memory tool has a specific schema. This schema is inserted into the system prompt, so the language model knows what kind of structured output it should generate when it wants to use memory.

This is a very interesting design because it turns memory into something the model can interact with through a formal interface, instead of just treating memory as plain text.

For example, when the model wants to save or retrieve something, it does not simply write a normal sentence. Instead, it may output a structure that matches the expected schema. Then the backend can detect this structure and decide what to do next.

## Native and Non-Native Tool Support

This also led me to understand the difference between native and non-native tool support.

For native tool support, the model can directly call the tool through the model provider’s tool-calling mechanism. If the tool schema matches, the system can execute the tool and return the result back to the model.

For non-native tool support, the idea is a little different. The model may still output a tool-like schema in its response, but the backend has to parse the model output manually. After detecting the schema, the system can extract it, execute the corresponding tool, and then continue the processing flow.

## What I Learned

The biggest lesson for me is that building an LLM application is not only about connecting an API.

A real LLM system has many layers:

* how the frontend sends the conversation
* how the backend manages model providers
* how tools are described to the model
* how tool calls are detected and executed
* how RAG chunks are retrieved and inserted
* how the system prompt controls the model behavior
* how memory is stored, searched, and reused

Among these parts, I used to think retrieval and model quality were the most important. But after this experiment, I feel that system prompt design and tool orchestration are equally important.

Especially for RAG, the model does not automatically know how to use retrieved chunks correctly. We need to design the instruction carefully, test many cases, compare different behaviors, and gradually tune the system.

For me, this project is not only about making a local LLM chat system. It is also a way to understand how RAG, memory, system prompts, and tool calling can work together as one complete application.
