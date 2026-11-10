# pyrchestrator

**English** · [日本語](README.ja.md)

A lightweight orchestrator for RPA robots written in Python: work queues with priorities, retries, logs, scheduling, and 3–4 robots running in parallel.

It is built from scratch to learn how RPA orchestration actually works, the part of platforms like UiPath Orchestrator that keeps robots running in production. The point is to understand that part, not to compete with those platforms.

> **Status:** design phase, before v0.1. No runnable code yet.
> Why this project exists: [docs/ORIGIN.md](docs/ORIGIN.md)

---

## Why

Writing the code for a robot is the cheap part, and with AI it is cheaper still. The expensive part is running that robot for years:

- which robot picks up which work item, and making sure no two robots pick up the same one
- what happens when a robot crashes halfway through a transaction
- which errors are worth retrying and which are not
- where the logs, screenshots and credentials live

This project rebuilds that layer piece by piece to understand it. The full story is in [docs/ORIGIN.md](docs/ORIGIN.md).

## Planned architecture

```
 CLI / REST API ──► Database (SQLite → PostgreSQL) ◄── Scheduler
                         ▲
          atomic claim   │
          ┌──────────────┼──────────────┐
       Worker 1       Worker 2       Worker 3/4
          │
    robot.py runs in its own process (timeout, can be stopped)
          │
    SDK: get_transaction() · set_status() · log() · get_asset()
```

## Roadmap

Each version must run on its own and gets a git tag. If development stops at any version, what exists still works.

| Version | Goal |
|---|---|
| **v0.1** | Queue on SQLite, CLI to add items, one worker that claims, processes and marks items |
| **v0.2** | 3–4 parallel workers with atomic claiming. Retries. Business vs system exceptions. Lease and heartbeat, so items from a dead worker go back to the queue |
| **v0.3** | Real robots as subprocesses, a small SDK, logs in the DB, screenshot on error. Demo: [RPA Challenge](https://rpachallenge.com) with Playwright |
| v0.4 | REST API (FastAPI) and a simple web dashboard |
| v0.5 | Scheduler (cron), queue triggers, priorities between processes |
| v0.6 | Encrypted assets, API-key auth, audit log |
| v0.7 | PostgreSQL (`FOR UPDATE SKIP LOCKED`), Docker Compose, CI |

## UiPath Orchestrator concepts → pyrchestrator

| UiPath Orchestrator | pyrchestrator (planned) | Version |
|---|---|---|
| Queue / Queue Item | `queue` / `queue_item` tables | v0.1 |
| Unique Reference | unique `reference` per queue (idempotency) | v0.1 |
| Business Rule Exception | `BusinessError`: not retried | v0.2 |
| System / Application Exception | `SystemError`: retried up to `max_retries` | v0.2 |
| Robot / Machine | worker process with heartbeat and capacity | v0.2 |
| Process / Job | registered robot / single execution | v0.3 |
| Logs | `log_entry` table + screenshots | v0.3 |
| Triggers (time, queue) | scheduler | v0.5 |
| Assets / Credentials | encrypted `asset` table | v0.6 |

## Principles

- **Hand-written code.** I write the code myself. AI is used as a reviewer at the end of each phase, not as the author.
- **Concepts before frameworks.** No Celery, Redis or Temporal. Building claiming, leases and retries by hand is the point of the project.
- **Honest limits.** Each release lists what it does not do yet.

## Known limitations

Everything: the project has not started yet. See the roadmap.

## License

[MIT](LICENSE)
