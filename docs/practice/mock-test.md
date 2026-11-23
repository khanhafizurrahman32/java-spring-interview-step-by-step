# Mixed mock test (40 MCQs)

**Rules:** 60 minutes, no IDE, no searching. Write your answers down, then check the key at the bottom. Questions are new (not repeated from the topic pages).

**Scoring:** 34+ ready for senior screenings · 28–33 review weak pages · under 28 redo the 2-week plan.

---

**1.** What prints?
```java
String a = "java";
String b = "ja" + "va";
String c = "ja";
String d = c + "va";
System.out.println((a == b) + " " + (a == d));
```
A) `true true` B) `true false` C) `false false` D) `false true`

**2.** A `HashMap` key object is mutated after insertion so its `hashCode` changes. `map.get(sameKeyObject)` most likely returns:
A) The value B) `null` C) Throws `ConcurrentModificationException` D) Throws `IllegalStateException`

**3.** Which allows a `null` key?
A) `TreeMap` (natural ordering) B) `ConcurrentHashMap` C) `HashMap` D) `Hashtable`

**4.** An `ArrayList` created with `new ArrayList<>()` receives its 11th element. Its new capacity is:
A) 11 B) 15 C) 20 D) 16

**5.** What prints?
```java
List<Integer> xs = new ArrayList<>(List.of(1, 2, 3));
xs.remove(1);
System.out.println(xs);
```
A) `[2, 3]` B) `[1, 3]` C) `[1, 2, 3]` D) Compile error

**6.** Which collection's iterator never throws `ConcurrentModificationException` and never reflects writes made after the iterator was created?
A) `ArrayList` B) `ConcurrentHashMap.keySet()` C) `CopyOnWriteArrayList` D) `Vector`

**7.** A class has `synchronized void a()` and `static synchronized void b()`. Can two threads run `a()` on one instance and `b()` at the same time?
A) No, same lock B) Yes, different locks (instance vs `Class` object) C) Only on Java 21 D) Deadlock

**8.** Submitting a task to a saturated `ThreadPoolExecutor` with the default rejection policy:
A) Blocks the caller B) Runs it on the caller thread C) Throws `RejectedExecutionException` D) Silently drops it

**9.** `f` returns `CompletableFuture<User>`. To chain `g(User)` which also returns a `CompletableFuture`, use:
A) `thenApply` B) `thenCompose` C) `thenAccept` D) `thenRun`

**10.** Which is true about `CountDownLatch`?
A) It can be reset and reused B) Threads calling `await()` block until the count reaches zero C) It limits concurrent access to N permits D) It requires exactly N threads to call `await()`

**11.** What does this return?
```java
IntStream.iterate(1, i -> i * 2).limit(5).sum()
```
A) 15 B) 31 C) 32 D) Infinite loop

**12.** `Optional.ofNullable(null).map(String::valueOf).orElse("x")` returns:
A) `"null"` B) `null` C) `"x"` D) Throws NPE

**13.** The map returned by `Collectors.groupingBy(f)` by default:
A) Is a `TreeMap` B) Is a `HashMap` with no guaranteed order C) Is unmodifiable D) Preserves encounter order

**14.** In Java 8+, the string pool lives in:
A) PermGen B) Metaspace C) The heap D) The thread stack

**15.** `-Xss` configures:
A) Max heap B) Thread stack size C) Metaspace size D) Survivor ratio

**16.** `OutOfMemoryError: unable to create native thread` most likely means:
A) The heap is full B) Too many threads for the OS/container limits C) Metaspace is full D) A deadlock

**17.** Default GC on a multi-core server with Java 21:
A) Parallel B) ZGC C) G1 D) Serial

**18.** A latency-critical service with a 100 GB heap needs pauses around 1 ms. Best collector to try:
A) Serial B) Parallel C) ZGC D) CMS

**19.** Entries in a `WeakHashMap` are removed when:
A) The map exceeds its capacity B) Their key is no longer strongly reachable and a GC has run C) After 60 seconds D) The value becomes null

**20.** A record's compact constructor is mainly used to:
A) Add extra instance fields B) Validate or normalise component values C) Make the record mutable D) Extend another class

**21.** A singleton `@Service` keeps a `private int requestCount` field and increments it in each request. The problem:
A) None, singletons are thread-safe B) Race condition: the bean is shared across request threads C) A new bean is created per request D) Spring resets the field

**22.** A bean's `@PostConstruct` method calls its own `@Transactional` method. The call runs:
A) In a transaction B) Without a transaction C) Throws at startup D) In a read-only transaction

**23.** Which modifies **bean definitions** before any bean instance is created?
A) `BeanPostProcessor` B) `BeanFactoryPostProcessor` C) `@PostConstruct` D) `ApplicationRunner`

**24.** `application.yml` sets `app.mode=basic`; `application-prod.yml` sets `app.mode=full`; profile `prod` is active. The value is:
A) `basic` B) `full` C) Startup fails D) Both, as a list

**25.** `@ConditionalOnMissingBean` on an auto-configured bean means:
A) Create it only if no bean of that type is already defined B) Always override user beans C) Create it lazily D) Create it only in tests

**26.** A `private` method annotated `@Transactional`, called from a public method of the same class:
A) Gets its own transaction B) Is not transactional by itself C) Causes a startup error D) Joins with REQUIRES_NEW

**27.** `REQUIRES_NEW` called inside an existing transaction:
A) Joins the existing one B) Suspends it and starts an independent transaction C) Creates a savepoint D) Throws

**28.** With Hibernate, `@Transactional(readOnly = true)` mainly:
A) Blocks all writes at the database B) Sets manual flush mode, skipping dirty checking C) Uses SERIALIZABLE isolation D) Disables the first-level cache

**29.** A read-only screen lists 500 orders with customer names and suffers N+1 queries. The most efficient fix:
A) Make `customer` EAGER B) A DTO projection query joining customer C) Turn on open-in-view D) Increase the connection pool

**30.** Spring Boot's default for `spring.jpa.open-in-view`:
A) `false` B) `true`, with a warning logged C) Not set D) Depends on the database

**31.** `orphanRemoval = true` on `@OneToMany` means:
A) Children are deleted when removed from the parent's collection B) The parent is deleted with the last child C) Children are never deleted D) Same as LAZY

**32.** Which annotation enables optimistic locking on an entity?
A) `@Lock` B) `@Version` C) `@Transactional(isolation = SERIALIZABLE)` D) `@OptimisticLock`

**33.** It is reasonable to disable CSRF protection when:
A) The app uses cookie sessions B) The API is stateless and uses `Authorization: Bearer` tokens C) The app uses form login D) Never

**34.** A call with an expired JWT should get:
A) 401 B) 403 C) 400 D) 500

**35.** Best way to store user passwords:
A) AES-encrypted B) SHA-256 hashed C) BCrypt (or Argon2) via a `PasswordEncoder` D) Base64

**36.** `@WebMvcTest` loads:
A) The full application context B) Only MVC components such as controllers, advice and filters C) Only repositories D) Nothing

**37.** The reliable way to micro-benchmark a Java method:
A) `System.currentTimeMillis()` around a loop B) JMH C) A debugger D) `jstat`

**38.** A thread dump shows 150 threads BLOCKED waiting to lock the same object. This indicates:
A) GC pressure B) Lock contention on a shared monitor C) A memory leak D) JIT deoptimisation

**39.** `Executors.newFixedThreadPool(10)` under a traffic burst far above capacity risks:
A) Thread explosion B) Unbounded queue growth leading to OOM C) Deadlock D) Tasks rejected immediately

**40.** On Java 21, the main benefit of virtual threads is:
A) Faster CPU-bound computation B) Cheap blocking for many concurrent I/O-bound tasks C) Automatic deadlock prevention D) No need for thread safety

---

## Answer key

Each explanation says why the correct answer is right **and** why the tempting wrong answers are wrong, then links to the page that covers the topic.

<details markdown="1"><summary>Show answers and explanations</summary>

**1. B.** `"ja" + "va"` consists only of literals, so the compiler joins it into the literal `"java"`, which is the same pooled object as `a`: `a == b` is `true`. `c` is a normal (non-`final`) variable, so `c + "va"` is computed at runtime and creates a new, unpooled `String`: `a == d` is `false`, even though `a.equals(d)` is `true`. Had `c` been declared `final`, `d` would also be a compile-time constant and the answer would be A. ([Read more](../java/01-core-language.md))

**2. B.** `HashMap` stored the entry in the bucket chosen by the key's hash *at insertion time* and never moves it. After the mutation, `get` computes the new hash, looks in a different bucket and finds nothing, so it returns `null`. The entry is still in the map (iteration shows it), but it is stranded. No exception is thrown (C and D): CME is only about iterating during structural changes. This is why map keys should be immutable. ([Read more](../java/01-core-language.md))

**3. C.** `HashMap` special-cases `null` (hash 0, bucket 0) and allows exactly one `null` key. `ConcurrentHashMap` (B) and `Hashtable` (D) reject `null` keys and values with `NullPointerException`, so that `get` returning `null` unambiguously means "absent". `TreeMap` with natural ordering (A) must call `compareTo` on the key, which throws NPE for `null`. ([Read more](../java/02-collections-internals.md))

**4. B.** `new ArrayList<>()` starts with an empty shared array and allocates capacity 10 on the first `add`. When the 11th element doesn't fit, it grows to `old + old/2` = 10 + 5 = **15** and copies the elements over. Growing by just 1 (A) would make every add O(n); doubling (C) is what `HashMap` does, not `ArrayList`. ([Read more](../java/02-collections-internals.md))

**5. B.** `List<Integer>` has two `remove` overloads: `remove(int index)` and `remove(Object o)`. The argument `1` is an `int`, which matches `remove(int)` exactly without boxing, so the compiler picks it and the element at **index 1** (the value `2`) is removed, leaving `[1, 3]`. To remove the value 1, call `xs.remove(Integer.valueOf(1))`. ([Read more](../java/01-core-language.md))

**6. C.** A `CopyOnWriteArrayList` iterator works on the array snapshot that existed when the iterator was created; writes replace the array with a new copy, so the iterator never sees them and never throws. `ConcurrentHashMap` views (B) also never throw, but they are *weakly consistent* and *may* reflect later writes, so they fail the second condition. `ArrayList` (A) and `Vector` (D) iterators are fail-fast and throw CME. ([Read more](../java/02-collections-internals.md))

**7. B.** A `synchronized` instance method locks the instance (`this`), while a `static synchronized` method locks the `Class` object. They are two different monitors, so the two methods don't exclude each other and can run concurrently. That also means they don't protect shared static state from each other. ([Read more](../java/03-concurrency.md))

**8. C.** The default `RejectedExecutionHandler` is `AbortPolicy`, which throws `RejectedExecutionException` when both the queue and the maximum thread count are full. B describes `CallerRunsPolicy` and D `DiscardPolicy`, both of which must be chosen explicitly. No built-in policy blocks the caller (A). ([Read more](../java/03-concurrency.md))

**9. B.** `g` returns a `CompletableFuture<…>`, so `thenApply(g)` (A) would produce a nested `CompletableFuture<CompletableFuture<…>>`. `thenCompose` is the flatMap of futures: it waits for the inner future and gives you a flat `CompletableFuture<…>`. `thenAccept` (C) and `thenRun` (D) produce no result value. ([Read more](../java/03-concurrency.md))

**10. B.** Threads calling `await()` block until other threads have called `countDown()` enough times to bring the count to zero; then all waiters are released. The latch is one-shot and cannot be reset (A describes `CyclicBarrier`). Permits (C) are `Semaphore`. Any number of threads may call `await()` (D); the count is decremented by `countDown()`, not by `await()`. ([Read more](../java/03-concurrency.md))

**11. B.** `IntStream.iterate(1, i -> i * 2)` is infinite (1, 2, 4, 8, 16, …), but streams are lazy and `limit(5)` is short-circuiting, so only five elements are ever generated: 1 + 2 + 4 + 8 + 16 = **31**. Without `limit`, `sum()` would loop forever (D). ([Read more](../java/04-streams.md))

**12. C.** `Optional.ofNullable(null)` is an empty Optional. `map` on an empty Optional does not call the function at all and stays empty, so `orElse("x")` returns `"x"`. `String.valueOf(null)` would return `"null"` (A), but it is never called. No NPE (D), because `ofNullable` (unlike `of`) accepts `null`. ([Read more](../java/04-streams.md))

**13. B.** `groupingBy(f)` collects into a `HashMap` by default, so key order is unspecified and the map is mutable. For sorted keys pass a map factory, `groupingBy(f, TreeMap::new, toList())`; for encounter order use `LinkedHashMap::new`. ([Read more](../java/04-streams.md))

**14. C.** The string pool was moved from PermGen to the regular heap in Java 7, so in Java 8+ it is on the heap, sized by `-Xmx` and garbage-collected. PermGen (A) no longer exists in Java 8. Metaspace (B) holds class metadata, not string objects. ([Read more](../jvm/01-memory.md))

**15. B.** `-Xss` sets the stack size of each thread (about 1 MB by default on 64-bit Linux). A larger value allows deeper recursion before `StackOverflowError`, but costs more memory per thread. Max heap is `-Xmx` (A), and Metaspace is `-XX:MaxMetaspaceSize` (C). ([Read more](../jvm/01-memory.md))

**16. B.** This error comes from the operating system refusing to create another thread, not from the heap. Typical causes: a thread leak (executors never shut down, unbounded pools), the per-user process limit (`ulimit -u`), the container's PID limit, or too little native memory for another thread stack. Take a thread dump and count the threads. ([Read more](../jvm/01-memory.md))

**17. C.** G1 has been the default on server-class machines (≥ 2 CPUs and ≥ ~1792 MB of memory) since Java 9, including Java 21. ZGC (B) must be enabled with `-XX:+UseZGC`. Parallel (A) was the Java 8 default. Serial (D) is only chosen automatically on small machines and containers. ([Read more](../jvm/02-garbage-collection.md))

**18. C.** ZGC does marking and compaction concurrently with the application, so its pauses are typically under a millisecond and don't grow with heap size, even at 100 GB+. Serial (A) and Parallel (B) are fully stop-the-world, and a full collection of a 100 GB heap would take many seconds. CMS (D) was removed in Java 14. ([Read more](../jvm/02-garbage-collection.md))

**19. B.** `WeakHashMap` holds its *keys* through weak references. Once nothing else references a key strongly, the next GC clears the weak reference, and the map drops the entry the next time you use it. There is no size limit or timer (A, C), and the value (D) has nothing to do with it. Note: a value that references its own key keeps the key strongly reachable, so the entry is never removed. ([Read more](../jvm/02-garbage-collection.md))

**20. B.** A compact constructor has no parameter list, runs before the fields are assigned, and is the place to validate (`requireNonNull`, range checks) or normalise (reassign a parameter, e.g. trim a string or set a `BigDecimal` scale). Records can't declare extra instance fields (A), are shallowly immutable (C), and can't extend a class (D), because they already extend `java.lang.Record`. ([Read more](../java/05-modern-java.md))

**21. B.** There is one instance of a singleton bean, shared by every request thread. `requestCount++` is a non-atomic read-add-write, so concurrent requests lose increments, a race condition. Singletons are not thread-safe automatically (A), and Spring neither creates a bean per request (C) nor resets fields (D). Use an `AtomicLong`/`LongAdder` or, better, a Micrometer counter. ([Read more](../spring/01-ioc-di.md))

**22. B.** Two independent reasons. `@PostConstruct` runs on the raw target object *before* the post-processor wraps it in a transactional proxy, and calling one of your own methods is self-invocation (`this.method()`), which never goes through a proxy anyway. So the method runs without a transaction and no error is raised (C). Use an `ApplicationReadyEvent` listener or call the method through another bean. ([Read more](../spring/01-ioc-di.md))

**23. B.** A `BeanFactoryPostProcessor` runs after all bean *definitions* are loaded but before any bean is created, and can change those definitions (resolving `${…}` placeholders is the classic example). A `BeanPostProcessor` (A) works on bean *instances* after they are created. `@PostConstruct` (C) and `ApplicationRunner` (D) run much later. ([Read more](../spring/01-ioc-di.md))

**24. B.** When a profile is active, `application-{profile}.yml` is loaded on top of `application.yml`, and its values override the base file's for the same keys. So `app.mode` is `full`. Keys only in the base file remain available. There is no conflict error (C); a property has a single value (D). ([Read more](../spring/02-boot-internals.md))

**25. A.** `@ConditionalOnMissingBean` makes the auto-configured bean conditional on there being *no* bean of that type yet. User configuration is processed before auto-configuration, so if you defined your own bean, Boot's version backs off. It never overrides your bean (B), and it has nothing to do with laziness (C) or tests (D). ([Read more](../spring/02-boot-internals.md))

**26. B.** A `private` method can't be overridden by the CGLIB subclass or declared on an interface, so no proxy can intercept it, and its `@Transactional` is ignored. It is also called through `this`, which bypasses the proxy regardless. It simply runs inside whatever transaction the calling public method has (or none). Spring doesn't fail at startup (C). ([Read more](../spring/03-proxies-aop.md))

**27. B.** `REQUIRES_NEW` parks (suspends) the current transaction, which keeps its connection, opens a completely independent transaction on a second connection, commits or rolls back that one when the method ends, and then resumes the outer one. A describes REQUIRED, C describes NESTED, and D describes NEVER. ([Read more](../spring/04-transactions.md))

**28. B.** Spring puts the Hibernate session into manual flush mode and read-only mode, so Hibernate skips dirty-checking snapshots and doesn't flush at commit, which saves memory and CPU on reads. It is an optimisation hint: it doesn't reliably block writes at the database (A), doesn't change isolation (C), and the first-level cache still works (D). ([Read more](../spring/04-transactions.md))

**29. B.** A DTO projection (`select new OrderView(o.id, c.name) from Order o join o.customer c`) fetches exactly the needed columns in a single query, creates no managed entities, and does no dirty checking: ideal for a read-only list. EAGER (A) affects every query that loads orders and still causes N+1 queries for queries that don't join. Open-in-view (C) only lets lazy loading happen later, and a bigger pool (D) just runs the 500 extra queries faster. ([Read more](../spring/05-jpa-pitfalls.md))

**30. B.** Spring Boot sets `spring.jpa.open-in-view=true` by default and logs a warning at startup saying so, because it keeps a persistence context, and a database connection, open for the whole web request. Many teams set it to `false` explicitly and fetch what they need in the service layer. ([Read more](../spring/05-jpa-pitfalls.md))

**31. A.** With `orphanRemoval = true`, removing a child from the parent's collection (`order.getItems().remove(item)`) makes Hibernate issue a `DELETE` for that child at flush, because a child without its parent shouldn't exist. Deleting the parent also deletes its children. It never deletes the parent (B), and it has nothing to do with fetch type (D). ([Read more](../spring/05-jpa-pitfalls.md))

**32. B.** Adding a `@Version` field (numeric or timestamp) makes Hibernate include `AND version = ?` in every update and increment the version. If another transaction changed the row first, the update matches 0 rows and an optimistic-locking exception is thrown. `@Lock` (A) is for requesting explicit (usually pessimistic) locks on queries. SERIALIZABLE (C) is an isolation level, not entity versioning. `@OptimisticLock` (D) is a Hibernate annotation for *excluding* a field from version increments. ([Read more](../spring/05-jpa-pitfalls.md))

**33. B.** CSRF works because browsers automatically attach cookies to cross-site requests. A stateless API that authenticates only with an `Authorization: Bearer` header is not exposed, because the browser never adds that header by itself, and a malicious page can't read the token. Cookie sessions (A) and form login (C) rely on cookies, so they need CSRF protection. ([Read more](../spring/06-security.md))

**34. A.** An expired token means the request is not (validly) authenticated, so it gets 401 Unauthorized, with a `WWW-Authenticate: Bearer error="invalid_token"` header telling the client to obtain a new token. 403 (B) is for an authenticated user lacking permission. 400 (C) is for malformed requests. ([Read more](../spring/06-security.md))

**35. C.** BCrypt (or Argon2/SCrypt/PBKDF2) is deliberately slow, uses a random salt per password and has a tunable cost, so stolen hashes are expensive to crack. AES (A) is reversible: one leaked key exposes every password. SHA-256 (B) is designed to be fast, so GPUs can try billions of guesses per second. Base64 (D) is just an encoding, not protection at all. ([Read more](../spring/06-security.md))

**36. B.** `@WebMvcTest` is a test slice that loads only web-layer components: controllers, `@ControllerAdvice`, filters, converters, Jackson and security configuration. Services and repositories are not created, so you provide them with `@MockitoBean`. The full context (A) is `@SpringBootTest`; repositories only (C) is `@DataJpaTest`. ([Read more](../spring/07-web-testing.md))

**37. B.** JMH (Java Microbenchmark Harness) runs warm-up iterations so the JIT has compiled the code, prevents dead-code elimination (via `Blackhole`), forks fresh JVMs and reports statistics. A timing loop (A) mostly measures the interpreter or code the JIT optimised away. A debugger (C) distorts timing completely. `jstat` (D) shows GC statistics. ([Read more](../jvm/03-performance-troubleshooting.md))

**38. B.** `BLOCKED` means waiting to enter a `synchronized` block. 150 threads waiting on the same lock address means one thread holds the monitor and everyone else is queueing: lock contention. Find the thread with `- locked <same address>` to see what it is doing, then shrink the critical section, use a concurrent data structure, or use finer-grained locks. GC pressure (A) would show busy GC threads. ([Read more](../jvm/03-performance-troubleshooting.md))

**39. B.** `newFixedThreadPool` caps the threads at 10 but uses an unbounded `LinkedBlockingQueue`. In a burst, the excess tasks pile up in the queue without limit, using more and more heap (and latency grows with the queue) until `OutOfMemoryError`. Tasks are never rejected (D), and thread explosion (A) is the `newCachedThreadPool` risk. ([Read more](../java/03-concurrency.md))

**40. B.** A virtual thread that blocks on I/O is unmounted from its carrier platform thread, which is then free to run other virtual threads, so you can have huge numbers of cheap, simple blocking threads. They don't add CPU capacity (A), don't prevent deadlocks (C), and shared mutable state still needs synchronization (D). ([Read more](../java/03-concurrency.md))

</details>

For every wrong answer, reread the related page and write one sentence: *"I picked X because I believed Y. Actually Z."* Writing down the false belief is what stops you from falling for the same distractor twice.
