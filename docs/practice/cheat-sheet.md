# One-page cheat sheet

Read this the night before. Every line is a fact that MCQ distractors are built around, followed by the one-line *reason* it is true, so you can rebuild the answer instead of memorising it. Each section links to the page with the full explanation.

## Java core

Full details: [Core language traps](../java/01-core-language.md).

- **Java is pass-by-value; for objects, the reference is what gets copied.** A method can mutate the object you passed, but reassigning its parameter never changes your variable.
- **`equals` true ⇒ `hashCode` equal. Not the reverse.** Hash collections pick the bucket by `hashCode` and only then compare with `equals`, so equal objects must land in the same bucket. Unequal objects *may* share a hash code (a collision) because an `int` has only ~4.3 billion values but there are infinitely many possible objects (`"Aa"` and `"BB"` both hash to 2112), and `equals` sorts them out inside the bucket.
- **`Integer` cache is -128..127**, so `==` on boxed values in that range compares the same cached object (`true`), and outside it two different objects (`false`). Always use `equals`.
- **Money: `new BigDecimal("0.1")`, compare with `compareTo`.** The `double` constructor keeps the binary rounding error, and `equals` also compares scale (`2.0` ≠ `2.00`).
- **Compile-time constant concatenation is pooled; runtime concatenation is not.** `"a" + "b"` (or with a `final` constant) is joined by the compiler into the pooled literal `"ab"`; with a normal variable it's joined at runtime into a new object, so `==` is `false`.
- **`return` in `finally` overrides the try's return value and swallows any exception. `finally` is skipped only on `System.exit`/a crash**, because then the JVM itself stops and nothing runs on the way out.
- **try-with-resources closes in reverse order** (later resources depend on earlier ones), **and exceptions from `close()` become suppressed** on the body's exception, so the real failure isn't hidden.
- **Overloading is chosen at compile time** (from declared types). **Overriding is chosen at runtime** (from the actual object). **Static methods are hidden, not overridden**: there's no object to dispatch on.
- **Generics are erased** (no `new T()`, no overloads differing only by type argument), **invariant** (`List<String>` is not a `List<Object>`), **PECS** (read from `? extends`, write to `? super`). **Arrays are covariant**, so bad stores are only caught at runtime (`ArrayStoreException`).
- **`list.remove(1)` on a `List<Integer>` removes index 1**, not the value 1: `remove(int)` matches the `int` argument exactly, without boxing, so the compiler prefers it over `remove(Object)`. Use `list.remove(Integer.valueOf(1))` for the value.

## Collections

Full details: [Collections internals](../java/02-collections-internals.md).

- **HashMap: capacity 16, load factor 0.75** (resize after 12 entries), **doubles** on resize, a bucket **treeifies past 8** nodes only if capacity **≥ 64** (otherwise it resizes instead), **untreeifies at 6** (the gap avoids flip-flopping), and allows **one `null` key**.
- **ConcurrentHashMap: CAS on empty buckets + lock on the bucket's first node**, **no nulls** (so `get` returning `null` always means "absent"), **weakly consistent** iterators (never throw CME), and use **`computeIfAbsent`/`merge`** because check-then-act across two calls is a race.
- **ArrayList starts at 10 and grows 1.5×.** Prefer **`ArrayDeque`** over `Stack` (synchronized legacy) and `LinkedList` (one node object per element, poor cache locality).
- **TreeMap is a red-black tree, O(log n), and decides duplicates by the comparator**, never `equals`, so a comparator returning 0 makes two items "the same".
- **PriorityQueue is a min-heap; its iterator is not sorted.** A heap only orders parents before children; only `poll()` returns elements in order.
- **LRU cache: `new LinkedHashMap<>(16, 0.75f, true)` + override `removeEldestEntry`.** `true` = access order, so the eldest entry is the least recently used.
- **`Arrays.asList` is fixed-size** (backed by the array: `set` works, `add` throws). **`List.of` is immutable and rejects nulls.** **`unmodifiableList` is a read-only view**: the source can still change.

## Concurrency

Full details: [Concurrency](../java/03-concurrency.md).

- **`volatile` = visibility + ordering, not atomicity.** `count++` is read-add-write, and another thread can run between the steps.
- **Happens-before edges: unlock → later lock of the same monitor, volatile write → later read, `start()` → thread body, thread body → `join()` returning, and transitivity.** Without one of these, another thread may see stale values forever.
- **`wait()` releases the monitor; `sleep()` keeps every lock.** Call `wait()` while holding the monitor (or get `IllegalMonitorStateException`) and in a `while` loop, because of spurious wakeups and conditions changing before you reacquire the lock.
- **ThreadPoolExecutor order: core threads → queue → extra threads up to max → reject** (default **`AbortPolicy`** throws). The queue fills *before* extra threads are created.
- **`newFixedThreadPool` has an unbounded queue** (tasks pile up → OOM). **`newCachedThreadPool` has unbounded threads** (thread explosion). Build `ThreadPoolExecutor` with a bounded queue instead.
- **`thenApply` = map, `thenCompose` = flatMap** (for functions that return another future). **`*Async` without an executor runs on `ForkJoinPool.commonPool()`**, shared with parallel streams, so don't block there.
- **`CountDownLatch` is one-shot; `CyclicBarrier` resets and is reusable; `Semaphore` hands out N permits** to limit concurrency.
- **Virtual threads (21) are for blocking I/O; don't pool them**, they're cheap. **On 21–23, blocking inside `synchronized` pins the carrier thread**; use `ReentrantLock` (fixed in 24).
- **Deadlock fix: acquire locks in one global order, or `tryLock` with a timeout.** Each breaks one of the four necessary conditions (circular wait, hold-and-wait).

## Streams

Full details: [Streams](../java/04-streams.md).

- **Lazy until the terminal operation; elements flow through all stages one at a time; a stream can be used once** (then `IllegalStateException`).
- **`toMap` with a duplicate key → `IllegalStateException`** (pass a merge function); **a `null` value → NPE**, because it uses `HashMap.merge`.
- **`partitioningBy` always has both `true` and `false` keys**; `groupingBy` only has keys that occur.
- **`orElse(x)` is eager** (Java evaluates `x` before the call, even when a value is present); **`orElseGet(supplier)` is lazy**.
- **`Stream.toList()` is unmodifiable and allows nulls**; `Collectors.toUnmodifiableList()` rejects nulls; `Collectors.toList()` guarantees nothing.
- **Parallel streams share the common ForkJoinPool** (~cores − 1 threads); blocking inside them starves `CompletableFuture`s across the app.

## Modern Java

Full details: [Modern Java](../java/05-modern-java.md).

- **8**: lambdas, streams, `Optional`, `java.time` · **9**: modules, `List.of`, G1 default · **10**: `var` · **11**: `HttpClient`, `String.strip/isBlank` · **16**: records, `instanceof` patterns · **17**: sealed classes · **21**: **virtual threads, pattern `switch`, record patterns** · **25**: next LTS (scoped values, instance `main`). LTS releases are 8, 11, 17, 21, 25.
- **Records are final, shallowly immutable** (copy mutable components with `List.copyOf`), **have accessors named `x()`**, and **can implement interfaces but can't extend a class** (they already extend `Record`).

## JVM

Full details: [Memory](../jvm/01-memory.md), [GC](../jvm/02-garbage-collection.md), [Troubleshooting](../jvm/03-performance-troubleshooting.md).

- **Heap = objects; stack = frames with locals and references; Metaspace = class metadata, in native memory since Java 8** (replacing the fixed-size PermGen).
- **Class loading: load → verify → prepare (defaults) → resolve → initialise (static initializers run).** **Parent delegation: bootstrap → platform → application**, so user code can't replace `java.lang.String`.
- **Reading a compile-time constant (`static final int X = 10`) doesn't trigger class initialization**: the value is copied into the caller at compile time.
- **OOM messages name the area that ran out: `Java heap space`, `GC overhead limit exceeded`, `Metaspace`, `unable to create native thread`, `Direct buffer memory`.** Each points to a different cause.
- **GC = reachability from roots, so cycles are collected. Default is G1 (since 9); ZGC for sub-millisecond pauses; CMS was removed in 14.**
- **In containers, the default max heap is only 25 % of the memory limit** → set `-XX:MaxRAMPercentage` (often 70–75).
- **Soft references are cleared only under memory pressure; weak ones at the next GC; phantom ones are for post-mortem cleanup (`Cleaner`).**
- **Tools: `jcmd` (everything), `jstack` (match `nid` = the hex OS thread ID from `top -H`), heap dump + Eclipse MAT, JFR (low-overhead production profiling), NMT (native memory), JMH (honest benchmarks).**

## Spring core & Boot

Full details: [IoC & DI](../spring/01-ioc-di.md), [Boot internals](../spring/02-boot-internals.md).

- **Lifecycle: instantiate → inject → Aware callbacks → BPP before-init → `@PostConstruct` → BPP after-init (proxies created here) → ready → `@PreDestroy`.** So `@PostConstruct` runs on the raw object, with no `@Transactional`.
- **Default scope is singleton: one shared instance, not thread-safe by magic.** A prototype injected into a singleton is created only once; use `ObjectProvider`.
- **`@Qualifier` beats `@Primary`** (a local, explicit request beats the global default). **Constructor injection is preferred** (final fields, testable with `new`, fails fast).
- **Circular references are prohibited by default since Boot 2.6**; constructor cycles never worked at all.
- **`@Configuration` (full mode) is CGLIB-subclassed, so calling one `@Bean` method from another returns the same singleton**; `proxyBeanMethods = false` creates a new object per call.
- **`@SpringBootApplication` = `@SpringBootConfiguration` + `@EnableAutoConfiguration` + `@ComponentScan` of the main class's package and below** (sibling packages are not scanned).
- **Auto-configuration backs off via `@ConditionalOnMissingBean`** when you define your own bean. **`--debug` prints the conditions report.**
- **Property precedence (higher wins): command-line args > Java system properties > OS env vars > config files outside the JAR > config files inside the JAR.** Profile-specific files beat the base file at the same location.
- **Actuator exposes only `health` over HTTP by default**; never expose `env` or `heapdump` publicly.
- **Boot 3 = Java 17+, `jakarta.*` instead of `javax.*`, `ProblemDetail`, Micrometer Observation** (replacing Sleuth).

## Proxies & transactions

Full details: [Proxies & AOP](../spring/03-proxies-aop.md), [Transactions](../spring/04-transactions.md).

- **Boot uses CGLIB subclass proxies. No advice on self-calls (`this.x()` skips the proxy), `private` or `final` methods (can't be overridden), or objects created with `new`** (never seen by the container).
- **Rollback by default only on `RuntimeException`/`Error`; a checked exception commits.** Use `rollbackFor = Exception.class`.
- **Default propagation is REQUIRED** (join or start). **REQUIRES_NEW** suspends the outer transaction (needs a second connection; can starve the pool). **NESTED** = savepoint. **MANDATORY** throws without a transaction; **NEVER** throws with one.
- **An inner REQUIRED method's runtime exception, caught by the outer method → `UnexpectedRollbackException` at commit**, because the shared transaction was already marked rollback-only.
- **`Isolation.DEFAULT` = the database's default** (PostgreSQL, Oracle, SQL Server: READ COMMITTED; MySQL InnoDB: REPEATABLE READ).
- **Transactions are bound to the thread**, so `@Async` and other threads don't share them.
- **Work that must happen only after commit: `@TransactionalEventListener` (default `AFTER_COMMIT`), or the transactional outbox** for messages that must never be lost.

## JPA

Full details: [JPA pitfalls](../spring/05-jpa-pitfalls.md).

- **`@ManyToOne`/`@OneToOne` are EAGER by default** (the `*ToMany` ones are LAZY) → make them LAZY, because EAGER can't be turned off per query.
- **N+1 fixes: JOIN FETCH, `@EntityGraph`, `default_batch_fetch_size` (safety net), DTO projections** (best for read-only views).
- **Collection fetch join + paging → Hibernate paginates in memory** (one SQL row per child). **Fetch-joining two `List`s → `MultipleBagFetchException`** (a cartesian product).
- **Managed entities are dirty-checked, so no `save()` is needed** for updates. **`merge` returns a *different*, managed instance**: use the return value of `save()`.
- **IDENTITY ids disable insert batching** (Hibernate must insert immediately to learn the id); use a pooled SEQUENCE.
- **Bulk JPQL updates bypass the persistence context** (loaded entities go stale; use `clearAutomatically`). **`deleteAll()` loads and deletes one by one**; `deleteAllInBatch()` is one statement.
- **Only the owning side (the one with the foreign key, without `mappedBy`) is persisted.** **Never cascade on `@ManyToOne`** (deleting a child would delete the parent).
- **`@Version` = optimistic locking** (conflict → exception, retry); **`PESSIMISTIC_WRITE` = `SELECT ... FOR UPDATE`** (others wait).
- **`spring.jpa.open-in-view` defaults to true**, which hides lazy-loading problems and holds a connection per request; most teams turn it off.

## Security

Full details: [Spring Security](../spring/06-security.md).

- **Filter chain → `AuthenticationManager` → `AuthenticationProvider` → `SecurityContext` (a `ThreadLocal`)**, so other threads don't see the user unless it's propagated.
- **Configure with a `SecurityFilterChain` bean** (`WebSecurityConfigurerAdapter` was removed in 6); **use `requestMatchers`, and the first matching rule wins**, so specific rules go first.
- **401 = not authenticated** (who are you?); **403 = authenticated but not allowed** (or a missing CSRF token, the usual reason for "every POST is 403").
- **`hasRole("ADMIN")` checks the authority `ROLE_ADMIN`**; `hasAuthority("ADMIN")` checks exactly `ADMIN`.
- **Keep CSRF on for cookie/session apps; it's safe to disable for stateless bearer-token APIs**, because browsers never attach the `Authorization` header automatically.
- **Hash passwords with BCrypt via `DelegatingPasswordEncoder`** (slow, salted, upgradable). **Use short-lived JWTs + refresh tokens**, because JWTs can't easily be revoked.

## Web & testing

Full details: [Web & testing](../spring/07-web-testing.md).

- **Request flow: filters → `DispatcherServlet` → `HandlerMapping` → interceptor `preHandle` → controller → message converters.** Security filters run before any interceptor.
- **A failed `@Valid` body → `MethodArgumentNotValidException` → 400.** Map errors centrally to **`ProblemDetail`** (RFC 9457) in a `@RestControllerAdvice`.
- **Use `RestClient` for new synchronous code; always set connect and read timeouts** so a slow downstream can't hold your threads forever.
- **`@WebMvcTest` = web layer only (mock the services); `@DataJpaTest` = JPA only, embedded DB, rollback per test; `@SpringBootTest` = everything; Testcontainers + `@ServiceConnection` for a real database.**
- **`@MockitoBean` replaces `@MockBean` (Boot 3.4+). A different set of mocks = a new application context = a slower suite**, because the context cache can't reuse it.
