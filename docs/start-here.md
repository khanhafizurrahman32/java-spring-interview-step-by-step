# Start here

## How MCQ tech-stack tests are built

Platform tests (Turing-style tech-stack tests, HackerRank/Codility Java tests, vendor screenings) mostly use four question types:

| Type | Example | How to beat it |
|---|---|---|
| **Output prediction** | "What does this snippet print?" | Trace slowly. Look for integer caching, string identity, overflow, short-circuiting, lazy streams. |
| **Internals** | "When does a HashMap bucket become a tree?" | Know the numbers: 16, 0.75, 8, 64, 6. |
| **Behaviour under failure** | "A checked exception is thrown in a `@Transactional` method. What happens?" | Know the defaults, then the override. |
| **Best choice** | "Which collection for a thread-safe, read-mostly listener list?" | Eliminate answers that are correct but wrong for the constraints. |

Two rules that win marks:

1. **Read the question twice.** Words like *always*, *never*, *guaranteed*, *by default* decide the answer.
2. **Eliminate first.** Two options are usually obviously wrong. Choose between the remaining two by asking "what are the defaults?"

## 2-week plan (interview is close)

| Day | Read | Practise |
|---|---|---|
| 1 | Core language traps | Page MCQs |
| 2 | Collections internals | Page MCQs + write an LRU cache |
| 3–4 | Concurrency | Page MCQs + write a producer/consumer |
| 5 | Streams + Modern Java | Page MCQs |
| 6 | JVM memory + GC | Page MCQs |
| 7 | JIT & troubleshooting | Explain a high-CPU runbook out loud |
| 8 | IoC & DI + Boot internals | Page MCQs |
| 9 | Proxies & AOP + Transactions | Page MCQs |
| 10 | JPA pitfalls | Page MCQs + fix an N+1 |
| 11 | Security | Page MCQs |
| 12 | Web & testing | Page MCQs |
| 13 | Mock test (timed, 60 min) | Review every wrong answer |
| 14 | Cheat sheet + retake the mock test | Rest |

## 4-week plan (steady pace, 1 hour a day)

- **Week 1:** Java core, collections, streams, modern Java.
- **Week 2:** Concurrency (spend real time here) and the JVM pages.
- **Week 3:** All Spring pages.
- **Week 4:** Mock test twice, rewrite your weakest page in your own words, and practise the "Say it out loud" sections with a timer.

## How to use the MCQs

- Answer **before** opening the answer block. Write your choice down.
- For each wrong answer, write one sentence: *"I picked X because I believed Y. Actually Z."* That sentence is what sticks.
- Retake a page's MCQs three days later. Spaced repetition beats rereading.

## Senior-level signal

At senior level, interviewers care less about *what* and more about *when and why*:

- Not "ConcurrentHashMap is thread-safe", but "I'd use it for a high-read shared cache; for read-mostly listener lists `CopyOnWriteArrayList` is better because iteration never locks."
- Not "use `@Transactional`", but "the proxy only intercepts external calls, so I keep transactional boundaries on the service's public entry points."

Every page ends with a **Say it out loud** section written in that style.
