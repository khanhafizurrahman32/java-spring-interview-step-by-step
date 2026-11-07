# Java & Spring Boot, Step by Step

A free interview handbook for **Java, the JVM and Spring Boot**: mental models first, then the exact traps that MCQ tests and interviewers target, then practice questions with explanations.

Built for senior backend engineers preparing for vetted-network screenings (Toptal, Turing, Arc and similar), tech-stack MCQ tests, and senior/staff backend interviews.

**Read it online:** https://khanhafizurrahman32.github.io/java-spring-interview-step-by-step/
**Companion handbook:** [System Design, Step by Step](https://khanhafizurrahman32.github.io/System-design-step-by-step/)

## Contents

| Area | Pages |
|---|---|
| Start | [Home](docs/index.md) · [Start here: 2-week and 4-week plans](docs/start-here.md) |
| Java | [Core language traps](docs/java/01-core-language.md) · [Collections internals](docs/java/02-collections-internals.md) · [Concurrency](docs/java/03-concurrency.md) · [Streams](docs/java/04-streams.md) · [Modern Java](docs/java/05-modern-java.md) |
| JVM | [Memory & class loading](docs/jvm/01-memory.md) · [Garbage collection](docs/jvm/02-garbage-collection.md) · [JIT & troubleshooting](docs/jvm/03-performance-troubleshooting.md) |
| Spring | [IoC & DI](docs/spring/01-ioc-di.md) · [Boot internals](docs/spring/02-boot-internals.md) · [Proxies & AOP](docs/spring/03-proxies-aop.md) · [Transactions](docs/spring/04-transactions.md) · [JPA pitfalls](docs/spring/05-jpa-pitfalls.md) · [Security](docs/spring/06-security.md) · [Web & testing](docs/spring/07-web-testing.md) |
| Practice | [40-question mock test](docs/practice/mock-test.md) · [One-page cheat sheet](docs/practice/cheat-sheet.md) |

Every topic page follows the same shape: **TL;DR → mental model diagram → deep dive → code you should be able to write → traps → say it out loud → practice MCQs**.

Examples target **Java 21** and **Spring Boot 3.x**.

## Run locally

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
mkdocs serve        # http://127.0.0.1:8000
```

## Publish to GitHub Pages

1. Create the repository `java-spring-interview-step-by-step` and push this folder to `main`.
2. The included workflow (`.github/workflows/deploy.yml`) builds the site and pushes it to the `gh-pages` branch.
3. In **Settings → Pages**, set the source to the `gh-pages` branch, root folder.

## Contributing

Spotted a mistake or something unclear? Open an issue or a pull request. Each page is plain Markdown and renders directly on GitHub (including the Mermaid diagrams).

## License

Content: CC BY 4.0. Code samples: MIT.
