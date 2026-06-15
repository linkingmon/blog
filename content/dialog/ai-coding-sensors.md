
---
title: "Sensors for AI Coding Agents: My Reading Notes"
date: 2026-05-25
tags: ["ai"]
---

Recently, I read a blog post about using “sensors” to help AI coding agents write better code.

## Basic Sensors
At first, when I saw the word “sensor,” I felt it sounded a little abstract. But after reading the article, my understanding is that these sensors are basically some lightweight feedback mechanisms around the codebase. They can be simple tests, static analysis tools, lint rules, or architecture checks. The main idea is not to let AI freely modify the code without any boundary, but to give it some signals so it can understand whether its changes are reasonable or risky.

The first part of the article talks about simple static code analysis. For example, the author checks things like file length and function length. These are not complicated rules. A file should not become too long. A function should not become too large. But what I found interesting is that the author does not treat the threshold as a hard and stupid rule.

For example, maybe the rule says a function should be under 50 lines. But the AI is allowed to decide that 60 or 70 lines may still be acceptable if there is a good reason. This sounds small, but I think it is actually quite important. Because in real software development, rules are rarely absolute. A rule is usually a guideline, and sometimes breaking the rule is acceptable if the trade-off is clear.

**The useful part is that when the AI breaks the threshold, it has to explain why. This makes code review easier.** Instead of just seeing that the AI created a long function, we can understand whether it had a reasonable design reason or whether it was just generating messy code. In this sense, the sensor is not only preventing bad code. It is also forcing the AI to make its reasoning more visible.

## More Complex Sensors
The article then discusses more complex sensors, such as dependency rules. For example, some files should only import or include files from certain layers. A lower-level module should not depend on a higher-level module. A domain layer should not directly depend on infrastructure details. These rules are more architectural than simple lint rules.

The final experiment in the article is about coupling analysis. The author studied how different files depend on each other and tried to use this information to help the AI understand the codebase. But this result was not as useful as expected.

I think this also makes sense. Coupling data is tricky. If one file is connected to many other files, it does not always mean the file is badly designed. Sometimes it is a problematic God object. But sometimes it is just an important core abstraction that many modules naturally need to use. If the AI only sees the graph data, it may make the wrong conclusion. It may think, “This file has many connections, so I should refactor it,” even though the file may actually be a stable and important part of the architecture.

Later, **the author used another skill module based on modularity analysis** to help the AI understand the codebase design more deeply. After that, the coupling report became more useful. This also feels reasonable to me. Raw data alone is not enough. The AI needs context. A report can show signals, but it does not automatically explain meaning.

## My Feedbacks
This is also related to something I have been thinking about recently. I want to build a system or platform where AI agents can manage code changes, run checks, request modifications, and move through a controlled workflow. In that kind of system, sensors are probably necessary.

My current thinking is that AI should be used for imagination, exploration, and flexible decision-making. But for things like tests, lint, static analysis, dependency rules, and syntax checks, we should not rely only on the AI’s own judgment. These checks should be executed regularly, either at fixed points in the workflow or when the AI decides it needs feedback.

**A good AI coding workflow probably needs both parts. The AI needs freedom to propose changes, but the environment needs sensors to provide feedback.** The AI can explore, but the system should tell it when it is stepping outside the safe boundary. This is a bit like driving a car. The driver can choose the route, but the dashboard still needs to show speed, fuel, engine warnings, and maybe occasionally scream when something is very wrong.

After reading this article, my takeaway is that sensors are not just small tools around the AI. They may become one of the core parts of AI-assisted software development. The more powerful the agent becomes, the more important these feedback mechanisms become. 

