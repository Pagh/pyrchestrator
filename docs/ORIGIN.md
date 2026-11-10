# Why pyrchestrator exists

**English** · [日本語](ORIGIN.ja.md)

*Written November 2026, before the first line of code.*

## In one paragraph

I worked as a junior RPA developer with UiPath for about two and a half years. When I stepped back to ask whether RPA was still worth investing in, I found that the part of these platforms that companies really pay for is not the drag-and-drop designer. It is the orchestrator: the layer that decides which robot does what, retries what failed, keeps logs and credentials, and keeps working when something breaks at 3 a.m. AI has made writing automation code cheap. It has not made running it reliably cheap. pyrchestrator is my attempt to understand that layer by building a small version of it myself.

## Where it started

In September 2022 I started working as an RPA developer at a large consulting firm. Before that I had never heard of RPA, and neither had any of my friends. That is normal: RPA lives in the back office of banks, insurers and large companies, and people outside that world never see it.

I learned UiPath on the job: Studio, the REFramework, selectors, queues, Orchestrator. For a long time I thought the value of these tools was the visual workflow: pre-built blocks you connect together, readable even by people who don't code.

## The question: is RPA still worth it?

In late 2026 I tried to answer one question honestly: **is RPA a growing field, a declining one, or something else?** What I found:

- **The market is mature, not dead.** UiPath grew revenue 13% and ARR 11% in fiscal 2026 and reached its first full year of GAAP profitability. Its customer count stayed flat (about 10,750), so growth comes from existing customers spending more, not from new ones.
- **The pure "RPA developer" role is shrinking into something broader.** Job titles are moving toward "automation engineer" and "agentic automation". Postings mix UiPath with Python, LLMs, RAG and document AI.
- **Certifications show the shift.** UiPath retired its classic RPA certifications and launched agentic ones. Microsoft retired the Power Automate RPA Developer exam (PL-500) in June 2026 with no direct replacement.

## What I learned from looking at Japan

I spent extra time on the Japanese market because I am studying Japanese, and because it is one of the largest RPA markets in the world. What I found there made the problem very concrete:

- **IT people work outside the companies that use the systems.** About 74% of Japanese IT professionals work for IT vendors and system integrators. In the US about 65% work inside user companies (IPA, DX White Paper 2023). Companies buy IT instead of building it, so few people inside them understand their own systems.
- **The missing role is a bridge, not a coder.** 85% of Japanese companies report a shortage of digital-transformation talent (US: 24%, Germany: 45%). The most-missed profile is the "business architect", someone who understands a business process, redesigns it and gets it built (IPA, DX Trends 2025).
- **Automation of parts, not processes.** Japanese companies tend to optimize individual tasks rather than whole processes. RPA, which automates a process exactly as it is, fits that pattern almost perfectly.
- **Abandoned robots (野良ロボット).** Many robots were built bottom-up by office staff during the 2018–2021 boom. When their creators changed roles or left, nobody could maintain them. They break when an Excel column moves, or keep running and produce wrong results. In many write-ups the root cause is not the tool. It is that nobody owned maintenance.

## The realization

Comparing UiPath, Power Automate, Japanese tools like WinActor and BizRobo!, and plain code, I reached a conclusion that changed how I see my own job:

> **Writing the code for a robot was always the cheap part. Running it in production for years is the expensive part.**

What companies pay UiPath for is mostly Orchestrator and everything around it:

- work queues with priorities and transactions, so that no two robots process the same item
- automatic retries, and the difference between a business exception (don't retry) and a system exception (retry)
- detecting when a robot has died and putting its work back in the queue
- logs, screenshots, credentials stored safely, audit trails, permissions

AI tools can now write a Python robot in minutes. None of the list above comes with it. A script that nobody inside the company can read or run safely is just an abandoned robot in a new form.

## Why build an orchestrator

I could have built a simpler project. I chose this one because:

1. **I want to understand what I used to take for granted.** In UiPath, "Add Queue Item" and "Get Transaction Item" just worked. I want to know what has to be true underneath: atomic claiming, leases, heartbeats, idempotency, retries.
2. **The market is moving toward code plus a platform.** Even UiPath now lets coding agents write automations that are then deployed and governed on its platform. Understanding the platform layer seems more durable than knowing any single designer.
3. **It is honest about scope.** pyrchestrator does not try to replace UiPath. It is a small, readable implementation of the same ideas, with each concept mapped back to its UiPath equivalent.

## Goals and non-goals

**Goals**
- A working orchestrator for Python robots: queues, retries, logs, scheduling, 3–4 parallel workers.
- Each version runs on its own, even if the project stops there.
- Clear documentation of every design decision.

**Non-goals**
- Production use or competing with UiPath, Power Automate or Temporal.
- Using Celery, Redis or Temporal. Building these mechanisms by hand is the point.
- AI-generated code. I write the code myself. AI reviews each phase and helps me find what I got wrong.

## Sources

- [UiPath fiscal 2026 results](https://ir.uipath.com/news/detail/431/mailto:PR@uipath.com)
- [IPA, DX Trends 2025 (Japanese)](https://www.ipa.go.jp/digital/chousa/dx-trend/dx-trend-2025.html)
- [IPA DX White Paper 2023 data, via Impress (Japanese)](https://it.impress.co.jp/articles/-/17986)
- [Microsoft retired certification exams (PL-500)](https://learn.microsoft.com/certifications/retired-certification-exams)
- [UiPath certifications](https://www.uipath.com/rpa/academy/certifications)
- [Abandoned RPA robots, PR Times (Japanese)](https://prtimes.jp/main/html/rd/p/000000072.000005204.html)
- [UiPath for Coding Agents](https://www.uipath.com/developers/coding-agents)
