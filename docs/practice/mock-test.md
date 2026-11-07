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

<details markdown="1"><summary>Show answers and explanations</summary>

| # | Ans | Why |
|---|---|---|
| 1 | B | `"ja" + "va"` is folded at compile time; `c + "va"` builds a new string at runtime. |
| 2 | B | The lookup searches the bucket for the new hash; the entry sits in the old bucket. |
| 3 | C | HashMap allows one null key; CHM and Hashtable reject nulls; TreeMap natural ordering throws NPE. |
| 4 | B | Capacity 10 grows by 1.5x to 15. |
| 5 | B | `remove(int)` removes by **index**; use `remove(Integer.valueOf(1))` to remove by value. |
| 6 | C | Copy-on-write iterators work on a snapshot. CHM views are weakly consistent (may see updates). |
| 7 | B | Instance methods lock `this`; static methods lock the `Class` object. |
| 8 | C | `AbortPolicy` is the default. |
| 9 | B | `thenCompose` flattens a nested future (like `flatMap`). |
| 10 | B | Latches are one-shot; permits are `Semaphore`. |
| 11 | B | 1 + 2 + 4 + 8 + 16 = 31. `limit` short-circuits the infinite stream. |
| 12 | C | `map` on an empty Optional stays empty. |
| 13 | B | Pass a map factory (e.g., `TreeMap::new`) for ordering. |
| 14 | C | Moved to the heap in Java 7. |
| 15 | B | |
| 16 | B | Check thread leaks, `ulimit -u`, container PID limits. |
| 17 | C | G1 has been the default since Java 9. |
| 18 | C | ZGC targets sub-millisecond pauses on very large heaps. CMS was removed. |
| 19 | B | Weak keys are cleared once only weakly reachable. |
| 20 | B | Records can't add instance fields or extend classes. |
| 21 | B | Singleton beans are shared; use `AtomicInteger`/`LongAdder` or a metrics library. |
| 22 | B | `@PostConstruct` runs on the target before the proxy exists, and it's self-invocation anyway. |
| 23 | B | `BeanPostProcessor` works on instances. |
| 24 | B | Profile-specific files override the default file. |
| 25 | A | Auto-configuration backs off when you define your own bean. |
| 26 | B | Private methods aren't proxied; the call runs in whatever transaction the caller has. |
| 27 | B | NESTED is the savepoint one. |
| 28 | B | It's an optimisation hint. |
| 29 | B | Fetch exactly the columns needed in one query. |
| 30 | B | Many teams set it to `false`. |
| 31 | A | |
| 32 | B | `@Lock` is for pessimistic (or explicit) locking on queries. |
| 33 | B | Browsers don't attach bearer headers automatically, so CSRF doesn't apply. |
| 34 | A | Authentication failed; 403 is for authenticated-but-not-allowed. |
| 35 | C | Slow, salted, adaptive hashing. |
| 36 | B | Services must be mocked. |
| 37 | B | Handles warm-up, dead-code elimination and forking. |
| 38 | B | Reduce critical sections, use concurrent structures or finer-grained locks. |
| 39 | B | Its `LinkedBlockingQueue` is unbounded. |
| 40 | B | Blocking I/O unmounts the virtual thread from its carrier. |

</details>

For every wrong answer, reread the related page and write one sentence: *"I picked X because I believed Y. Actually Z."*
