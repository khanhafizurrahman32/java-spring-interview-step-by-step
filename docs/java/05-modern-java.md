# Modern Java (8 to 21+)

> **TL;DR** Know which feature arrived in which **LTS** release (8, 11, 17, 21, 25), because "which version introduced X?" is a common MCQ. **LTS (long-term support)** releases get years of security fixes, so companies upgrade from one LTS to the next and skip the six-monthly releases in between. The modern data-modelling toolkit is **records + sealed types + pattern-matching switch**: records describe each case as an immutable value, `sealed` closes the list of cases, and `switch` patterns let the compiler check that you handled every one.

## What arrived when

Since Java 9, a new version ships every six months (March and September), and every two years one of them is LTS. A feature usually appears as a **preview** (usable with `--enable-preview`, may still change) in one or more versions before becoming **final** (standard). The table lists the version where each feature became final.

| Version | Headline features |
|---|---|
| **8 (LTS, 2014)** | **Lambdas** and method references; the **Streams** API; **`Optional`**; **default and static methods in interfaces** (so interfaces like `Collection` could gain `stream()` without breaking every existing implementation); the **`java.time`** date/time API, which replaces the mutable, error-prone `Date`/`Calendar`; **`CompletableFuture`**; **Metaspace** replaces PermGen for class metadata (see [Memory](../jvm/01-memory.md)). |
| 9 | **Modules (JPMS)**: `module-info.java` declares what a JAR exports and requires; **`List.of`/`Set.of`/`Map.of`** immutable factories; **JShell**, an interactive REPL; **G1 becomes the default garbage collector**; **private interface methods**, to share code between default methods. |
| 10 | **`var`** for local variables: the compiler infers the type from the initializer. |
| **11 (LTS, 2018)** | Standard **`HttpClient`** (HTTP/2, async) in `java.net.http`; new `String` methods **`isBlank`**, **`strip`** (Unicode-aware `trim`), **`lines`**, **`repeat`**; run a single-file program directly with **`java File.java`**, with no separate `javac` step. |
| 14 | **Switch expressions** become standard: `switch` can return a value and uses `case X ->` with no fall-through. **Helpful NullPointerException messages** (on by default from 15). |
| 15 | **Text blocks** (`"""` multi-line strings); **ZGC** becomes production-ready. |
| 16 | **Records**; **pattern matching for `instanceof`**; **`Stream.toList()`**. |
| **17 (LTS, 2021)** | **Sealed classes**; **strong encapsulation of JDK internals**: code can no longer reflectively access internal APIs like `sun.misc` classes without explicit `--add-opens`, which broke some old libraries and is a common upgrade pain point. |
| **21 (LTS, 2023)** | **Virtual threads**; **pattern matching for `switch`**; **record patterns** (deconstruct a record in a pattern); **sequenced collections**; **generational ZGC** (opt-in with `-XX:+ZGenerational`). |
| 22 | **Unnamed variables `_`** for values you must declare but don't use (`catch (Exception _)`); the **Foreign Function & Memory API**, a safer replacement for JNI to call native code and manage off-heap memory. |
| 24 | Virtual threads no longer **pin** their carrier thread when blocking inside `synchronized` (JEP 491; see [Concurrency](03-concurrency.md)). |
| **25 (LTS, 2025)** | **Scoped values** (an immutable, bounded alternative to `ThreadLocal` that suits virtual threads); **compact source files and instance `main` methods** (`void main() { ... }` with no class declaration, for scripts and teaching); **flexible constructor bodies** (statements such as argument validation are allowed before `super(...)`); **module import declarations** (`import module java.base;` imports every package the module exports). |

**Spring Boot 3.x (and 4.x) requires Java 17+**, because Spring Framework 6 moved its baseline to Java 17 and to the Jakarta EE 9+ `jakarta.*` package names. That is why many teams' "upgrade Spring Boot" project is also an "upgrade to Java 17" project.

## Records

A **record** is a class whose whole purpose is to carry a fixed set of values. You list the values (the *components*) in the header, and the compiler generates the constructor, fields, accessors, `equals`, `hashCode` and `toString` for you.

```java
public record Money(String currency, BigDecimal amount) {
    public Money {                                  // compact constructor: no parameter list, runs before fields are assigned
        Objects.requireNonNull(currency);           // validate: reject invalid state at construction time
        if (amount.signum() < 0) throw new IllegalArgumentException("negative");
        amount = amount.setScale(2, RoundingMode.HALF_EVEN); // normalise: reassigns the PARAMETER, which is then stored in the field
    }
    public Money plus(Money other) { return new Money(currency, amount.add(other.amount)); } // "modify" by returning a new record
    public static Money zero(String c) { return new Money(c, BigDecimal.ZERO); }             // static factories are allowed
}
```

The **compact constructor** is the place for validation and normalisation. You write no parameter list and no field assignments; the compiler adds `this.currency = currency; this.amount = amount;` at the end automatically, so reassigning a parameter (as with `amount` above) changes what gets stored. Normalising the scale here also means two `Money` values for the same amount are always `equals` (avoiding the `BigDecimal` scale trap from [Core language traps](01-core-language.md)).

- **Implicitly `final`**: no class can extend a record. Each component becomes a **`private final` field** with a public accessor method named after the component, **`currency()`**, not `getCurrency()`. Frameworks like Jackson and Spring understand this naming; some older libraries that expect JavaBean getters do not.
- **`equals`, `hashCode` and `toString` are generated from all components**: two records are equal exactly when all their components are equal, and `toString` prints `Money[currency=EUR, amount=10.00]`. You may override them, but you rarely should.
- **What records can and cannot do.** They *can* implement interfaces, add instance and static methods, declare static fields, have extra constructors (which must delegate to the canonical one) and be generic. They *cannot* extend another class, because every record already implicitly extends `java.lang.Record`, and Java has single inheritance. They *cannot* declare extra instance fields, because the state of a record is by definition exactly its components; extra hidden state would break the generated `equals`/`hashCode`.
- **Only shallowly immutable.** The fields are `final`, so a field can't be pointed at a different object, but if a component is a mutable object like an `ArrayList`, its *contents* can still change, through the caller's reference or through the accessor. Make a defensive copy in the compact constructor: `items = List.copyOf(items);`. Now the record holds its own unmodifiable list.
- **Good for**: DTOs (request/response bodies), value objects (`Money`, `Range`), map keys (correct `equals`/`hashCode` for free), and intermediate results in streams (`record WordCount(String word, long count)`). **Not for JPA entities**: Hibernate needs a no-argument constructor to instantiate entities, mutable fields to set loaded values and track changes, and non-final classes to create lazy-loading proxies. Records provide none of these. They are fine for JPA *projections* (read-only query results).

## Sealed types

A `sealed` class or interface states exactly which classes may extend or implement it. Normally anyone can implement a public interface; sealing **closes the hierarchy**, so the compiler knows the complete list of subtypes. That is what later lets `switch` check that you covered every case.

```java
public sealed interface PaymentResult permits Approved, Declined, Pending {} // only these three may implement it
public record Approved(String txId) implements PaymentResult {}
public record Declined(String reason) implements PaymentResult {}
public record Pending(Instant retryAt) implements PaymentResult {}
```

Each permitted subclass must say how it continues the hierarchy by being one of:

- **`final`**: nothing may extend it further.
- **`sealed`**: it may be extended only by its own `permits` list.
- **`non-sealed`**: it re-opens the hierarchy, and anyone may extend it. (This is a deliberate escape hatch; the compiler then can't be exhaustive below that point.)

Records are implicitly `final`, which is why the three records above need no modifier. Permitted subclasses must live in the same module (or, if you don't use modules, the same package) as the sealed type. If they are all in the same source file, the `permits` clause can be omitted and the compiler infers it.

## Pattern matching

```java
// instanceof pattern (16): test the type AND bind a typed variable in one step
if (obj instanceof String s && !s.isBlank()) { use(s); }   // s is in scope only where the test is known to be true

// switch pattern + record deconstruction (21)
String describe(PaymentResult r) {
    return switch (r) {
        case Approved(String txId)               -> "ok " + txId;          // record pattern: pulls out the component directly
        case Declined(String reason) when reason.contains("fraud") -> "blocked"; // guard: extra condition for this case
        case Declined d                           -> "declined: " + d.reason(); // every other Declined
        case Pending p                            -> "retry at " + p.retryAt();
    };   // no default needed: the sealed hierarchy is exhaustive
}
```

Before Java 16 you wrote `if (obj instanceof String) { String s = (String) obj; ... }`: a type test, then a cast that repeats the type. A pattern does both at once and cannot get the cast wrong. A **record pattern** like `Approved(String txId)` goes one step further and unpacks the record's components into variables. A **guard** (`when ...`) adds a condition, so one type can be split into several cases.

- **Exhaustiveness is the point.** Because `PaymentResult` is sealed, the compiler knows these three types are the only possibilities and accepts the switch without a `default`. If you later add `record Refunded(...)` to the `permits` list, every exhaustive switch over `PaymentResult` **fails to compile** until it handles `Refunded`, so the compiler gives you a to-do list instead of a production bug. A `default` branch would silently swallow the new case, which is why you should avoid `default` on sealed hierarchies.
- **More specific cases must come before general ones.** Cases are tried top to bottom. If `case Declined d` came before `case Declined(...) when ...`, the guarded case could never match. The compiler reports such a **dominated** case as an error instead of letting dead code through.
- **`null` handling.** Traditionally a `switch` on a `null` value throws `NullPointerException`. You can now write **`case null ->`** (or `case null, default ->`) to handle it explicitly. Without that case, a `null` selector still throws NPE, so existing code doesn't change behaviour.
- **Arrow form does not fall through.** With `case X ->`, exactly one branch runs, with no `break` needed. The old colon form (`case X:`) still *falls through* into the next case unless you write `break`, which is a classic source of bugs. Prefer arrows.

## Other useful bits

- **`var`** lets the compiler infer the type of a **local variable** from its initializer: `var users = new ArrayList<User>();` is exactly `ArrayList<User> users = ...`. The variable is still **statically typed**: its type is fixed at compile time, and you can't later assign a `String` to it. It is allowed only for locals (including `for` loops and lambda parameters), not for fields, method parameters or return types, because those are part of a class's API and should be written out explicitly. `var x = null;` does not compile: `null` has no type to infer. `var` without an initializer doesn't either.
- **Text blocks** (`"""`): multi-line string literals. The compiler removes the **incidental indentation** (the common leading whitespace that exists only because the code is indented) and normalises line endings, so embedded SQL, JSON or HTML in tests stays readable without `\n` and `+` everywhere.

    ```java
    String json = """
        {"name": "Ana", "age": 30}
        """;   // no escaping of the quotes needed; the closing """ position controls indentation
    ```

- **Sequenced collections (21)**: a new `SequencedCollection` interface gives every collection with a defined order the same methods: `getFirst()`, `getLast()`, `addFirst()`, `addLast()`, `removeFirst()`, `removeLast()` and `reversed()` (a reversed *view*, not a copy). It applies to `List`, `Deque`, `LinkedHashSet` and sorted sets; `SequencedMap` gives `LinkedHashMap` and `TreeMap` `firstEntry()`, `lastEntry()` and `pollLastEntry()`. Before Java 21, getting the last element of a `LinkedHashSet` required iterating the whole set.
- **Helpful NPEs**: instead of a bare `NullPointerException`, the message names exactly what was `null`, e.g. `Cannot invoke "String.length()" because "user.name" is null`. That matters for chained calls like `a.b().c().d()`, where the old message didn't say which link was null.
- **Default method conflicts**: a default method is an interface method with a body. If a class implements two interfaces that both provide a default method with the same signature, the compiler cannot guess which one you want, so the class **must override** it. Inside the override it can delegate to a specific one with `A.super.method()`. (If a superclass provides the method, the class's version wins automatically: "classes win over interfaces".)

## Traps

- "Records are deeply immutable." **Only shallowly**: the fields can't be reassigned, but a mutable component such as a `List` can still be modified. Copy it with `List.copyOf` in the compact constructor.
- "Record accessors are `getX()`." They are `x()`: the accessor has the same name as the component.
- "`var` makes Java dynamically typed." **No.** The type is inferred once at compile time and fixed; `var` only saves you from typing it.
- "Sealed classes require `final` subclasses." Each permitted subclass may be `final`, `sealed` (with its own `permits` list) or `non-sealed` (re-opened to everyone).
- "Virtual threads arrived in 17." **21** (previewed in 19 and 20).

## Say it out loud

**"Why combine sealed types with records and switch patterns?"**
Together they give Java algebraic data types: a closed set of cases (sealed), where each case is an immutable value carrying exactly its own data (records), and a switch that can unpack those values and must handle every case (patterns plus exhaustiveness checking). The practical payoff is safety during change: when I add a new payment outcome, every switch that needs to handle it becomes a compile error pointing at the exact line, instead of falling into a default branch and becoming a runtime bug.

## Practice MCQs

**Q1.** Which is true about records?

- A) They can extend another class
- B) They can declare additional instance fields
- C) They can implement interfaces
- D) Accessors are named `getX()`

<details markdown="1"><summary>Answer</summary>

**C.** Records can implement any interfaces. A is wrong because every record implicitly extends `java.lang.Record`, and Java allows only one superclass. B is wrong because a record's state is exactly its components; only `static` fields may be added. D is wrong because accessors use the component name, `x()`.
</details>

**Q2.** A subclass of a sealed class must be declared:

- A) Only `final`
- B) `final`, `sealed`, or `non-sealed`
- C) `abstract`
- D) In the same file

<details markdown="1"><summary>Answer</summary>

**B.** Every permitted subclass must state explicitly how the hierarchy continues: closed (`final`), restricted again (`sealed`), or re-opened (`non-sealed`). Records and enums are implicitly final. D is too strict: subclasses must be in the same module, or in the same package when there is no named module, but not necessarily the same file. (Putting them in the same file only lets you omit the `permits` clause.)
</details>

**Q3.** Which LTS introduced virtual threads as a final feature?

- A) 11
- B) 17
- C) 19
- D) 21

<details markdown="1"><summary>Answer</summary>

**D.** Virtual threads (Project Loom, JEP 444) were previewed in Java 19 and 20 and became final in 21. C is wrong twice over: 19 had them only as a preview, and 19 is not an LTS release.
</details>

**Q4.** Which compiles?

- A) `var x = null;`
- B) `var list = new ArrayList<String>();`
- C) `private var name = "a";` (as a field)
- D) `public var get() { ... }`

<details markdown="1"><summary>Answer</summary>

**B.** The initializer's type, `ArrayList<String>`, is fully known, so `var` can infer it. A fails because `null` has no type to infer from. C and D fail because `var` is only for local variables; fields and return types are part of a class's API and must be declared explicitly.
</details>

**Q5.** A switch over a sealed interface with all permitted subtypes covered and no `default`:

- A) Fails to compile
- B) Compiles; adding a new subtype later breaks compilation of the switch
- C) Throws at runtime if a new subtype appears
- D) Only works with enums

<details markdown="1"><summary>Answer</summary>

**B.** The compiler knows the complete list of permitted subtypes, so it accepts the switch as exhaustive without a `default`, and when a new permitted subtype is added, it flags every switch that doesn't handle it. (C can only happen if the sealed interface is recompiled with a new subtype but the switch's class is *not* recompiled, in which case the compiler-inserted fallback throws `MatchException`; in a normal build you get the compile error first.) D is wrong: exhaustive switches work with sealed types as well as enums.
</details>

## Related

- [Core language traps](01-core-language.md): the `equals`/`hashCode` contract that records implement for you.
- [Concurrency](03-concurrency.md): virtual threads in depth, including pinning.
