# One-page cheat sheet

Read this the night before. Every line is a fact that MCQ distractors are built around.

## Java core

- Java is **pass-by-value**; references are copied.
- `equals` true ⇒ `hashCode` equal. Not the reverse.
- `Integer` cache: **-128..127**. `new BigDecimal("0.1")`, compare with `compareTo`.
- Compile-time constant concatenation is pooled; runtime concatenation is not.
- `return` in `finally` overrides and swallows. `finally` skips only on `System.exit`/crash.
- try-with-resources: closes in **reverse** order; close exceptions become **suppressed**.
- Overloading: compile time. Overriding: runtime. Static methods: **hidden**.
- Generics: **erased**, invariant, PECS. Arrays are covariant (`ArrayStoreException`).
- `list.remove(1)` removes **index 1**.

## Collections

- HashMap: **16**, **0.75**, doubles, treeify at **8** if capacity ≥ **64**, untreeify at **6**, one null key.
- ConcurrentHashMap: CAS + per-bucket lock, **no nulls**, weakly consistent iterators, use `computeIfAbsent`/`merge`.
- ArrayList: 10, grows **1.5x**. Prefer `ArrayDeque` over `Stack`/`LinkedList`.
- TreeMap: red-black, O(log n), duplicates by **comparator**.
- PriorityQueue: min-heap; **iterator not sorted**.
- LRU: `LinkedHashMap(16, 0.75f, true)` + `removeEldestEntry`.
- `Arrays.asList`: fixed-size. `List.of`: immutable, no nulls. `unmodifiableList`: a view.

## Concurrency

- volatile = visibility + ordering, **not** atomicity.
- Happens-before: unlock→lock, volatile write→read, `start()`, `join()`, transitivity.
- `wait()` releases the lock (call in a while loop, holding the monitor); `sleep()` doesn't.
- ThreadPoolExecutor: core → **queue** → max → reject (default **AbortPolicy**).
- `newFixedThreadPool`: unbounded queue. `newCachedThreadPool`: unbounded threads.
- `thenApply` = map, `thenCompose` = flatMap. Async default = **commonPool**.
- Latch: one-shot. Barrier: reusable. Semaphore: N permits.
- Virtual threads (21): for blocking I/O; don't pool; pinning in `synchronized` on 21–23.
- Deadlock fix: global lock ordering, `tryLock` with timeout.

## Streams

- Lazy until terminal; elements flow one at a time; single use.
- `toMap` duplicate key → **IllegalStateException**; null value → NPE.
- `partitioningBy` always has true and false keys.
- `orElse` eager, `orElseGet` lazy.
- `Stream.toList()` unmodifiable, allows nulls.
- Parallel streams share the common ForkJoinPool; avoid blocking inside.

## Modern Java

- 8 lambdas/streams · 9 modules, `List.of`, G1 default · 10 `var` · 11 HttpClient · 16 records · 17 sealed · **21 virtual threads, pattern switch** · 25 LTS.
- Records: final, shallowly immutable, accessors `x()`, can implement interfaces.

## JVM

- Heap (objects), stack (frames, locals, references), **Metaspace** (class metadata, native, Java 8+).
- Class loading: load → verify → prepare → resolve → initialise. Parent delegation: bootstrap → platform → app.
- Constants (`static final` literals) don't trigger class init.
- OOMs: heap space, GC overhead, Metaspace, native thread, direct buffer.
- GC = reachability from roots (cycles collected). Default **G1** (9+). **ZGC** for low latency. **CMS removed in 14**.
- Containers: default heap **25%** of memory → set `MaxRAMPercentage`.
- Soft: cleared under memory pressure. Weak: next GC. Phantom: cleanup.
- Tools: `jcmd`, `jstack` (nid = hex TID), heap dump + MAT, **JFR**, NMT, JMH.

## Spring core & Boot

- Lifecycle: instantiate → inject → Aware → BPP before → `@PostConstruct` → BPP after (**proxies**) → ready → `@PreDestroy`.
- Default scope **singleton** (not thread-safe by magic). Prototype in singleton: injected once.
- `@Qualifier` beats `@Primary`. Constructor injection preferred.
- Circular references prohibited by default since **Boot 2.6**.
- `@Configuration` (full mode) proxies `@Bean` methods → same singleton.
- `@SpringBootApplication` = config + auto-config + component scan (main package and below).
- Auto-config backs off via `@ConditionalOnMissingBean`. Debug: `--debug`.
- Precedence: CLI args > env vars > profile files > `application.yml`.
- Actuator exposes only `health` over HTTP by default.
- Boot 3: Java 17+, `jakarta.*`, ProblemDetail, Micrometer Observation.

## Proxies & transactions

- Boot uses **CGLIB**. No advice on self-calls, `private`, `final`, or `new`-created objects.
- Rollback by default on **RuntimeException/Error only**; checked → commit.
- Default propagation **REQUIRED**. REQUIRES_NEW suspends (needs a 2nd connection). NESTED = savepoint. MANDATORY throws without tx; NEVER throws with tx.
- Inner REQUIRED failure caught by outer → **UnexpectedRollbackException**.
- Isolation DEFAULT = DB default (Postgres **READ COMMITTED**).
- Transactions are **thread-bound**: `@Async` doesn't share them.
- After-commit work: `@TransactionalEventListener` (default AFTER_COMMIT) or outbox.

## JPA

- `@ManyToOne`/`@OneToOne` **EAGER** by default → make them LAZY.
- N+1 fixes: JOIN FETCH, `@EntityGraph`, `default_batch_fetch_size`, DTO projections.
- Collection fetch join + paging → in-memory pagination. Two `List` fetches → `MultipleBagFetchException`.
- Managed entities are dirty-checked; no `save()` needed. `merge` returns a new instance.
- IDENTITY ids disable insert batching; use SEQUENCE.
- Bulk JPQL bypasses the persistence context. `deleteAll()` deletes one by one.
- Owning side (FK side) decides what's persisted. No `cascade` on `@ManyToOne`.
- `@Version` = optimistic; `PESSIMISTIC_WRITE` = `SELECT ... FOR UPDATE`.
- `open-in-view` defaults to **true**; most teams turn it off.

## Security

- Filter chain → AuthenticationManager → Provider → SecurityContext (**ThreadLocal**).
- `SecurityFilterChain` bean; `WebSecurityConfigurerAdapter` is gone; `requestMatchers`, first match wins.
- **401** = not authenticated; **403** = not allowed (or missing CSRF token).
- `hasRole("ADMIN")` ⇒ `ROLE_ADMIN`.
- CSRF on for cookie sessions; can disable for stateless bearer-token APIs.
- BCrypt via `DelegatingPasswordEncoder`. Short-lived JWTs + refresh tokens.

## Web & testing

- Filters → DispatcherServlet → HandlerMapping → Interceptor → Controller → converters.
- `@Valid` failure → `MethodArgumentNotValidException` → 400. Use `ProblemDetail` in `@RestControllerAdvice`.
- `RestClient` for new synchronous code; always set timeouts.
- `@WebMvcTest` (no services), `@DataJpaTest` (embedded DB, rollback), `@SpringBootTest` (everything), Testcontainers + `@ServiceConnection`.
- `@MockitoBean` replaces `@MockBean` (Boot 3.4+). Different mocks = new context = slower suite.
