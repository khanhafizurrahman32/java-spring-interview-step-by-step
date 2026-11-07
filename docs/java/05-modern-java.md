# Modern Java (8 to 21+)

> **TL;DR** Know which feature arrived in which **LTS** (8, 11, 17, 21, 25), because "which version introduced X?" is a common MCQ. The modern data-modelling toolkit is **records + sealed types + pattern-matching switch**: together they let the compiler check that you handled every case.

## What arrived when

| Version | Headline features |
|---|---|
| **8 (LTS)** | Lambdas, streams, `Optional`, default/static interface methods, `java.time`, `CompletableFuture`, Metaspace replaces PermGen |
| 9 | Modules (JPMS), `List.of`/`Map.of`, JShell, G1 becomes default GC, private interface methods |
| 10 | `var` for local variables |
| **11 (LTS)** | Standard `HttpClient`, `String.isBlank/strip/lines/repeat`, run single-file programs with `java File.java` |
| 14 | Switch expressions (standard), helpful NullPointerException messages |
| 15 | Text blocks, ZGC production-ready |
| 16 | Records, pattern matching for `instanceof`, `Stream.toList()` |
| **17 (LTS)** | Sealed classes, strong encapsulation of JDK internals |
| **21 (LTS)** | Virtual threads, pattern matching for `switch`, record patterns, sequenced collections, generational ZGC (opt-in) |
| 22 | Unnamed variables `_`, Foreign Function & Memory API |
| 24 | Virtual threads no longer pin on `synchronized` (most cases) |
| **25 (LTS)** | Scoped values, compact source files and instance `main` methods, flexible constructor bodies, module import declarations |

Spring Boot 3.x requires **Java 17+**.

## Records

```java
public record Money(String currency, BigDecimal amount) {
    public Money {                                  // compact constructor: validate/normalise
        Objects.requireNonNull(currency);
        if (amount.signum() < 0) throw new IllegalArgumentException("negative");
        amount = amount.setScale(2, RoundingMode.HALF_EVEN);
    }
    public Money plus(Money other) { return new Money(currency, amount.add(other.amount)); }
    public static Money zero(String c) { return new Money(c, BigDecimal.ZERO); }
}
```

- Implicitly `final`; components become `private final` fields with accessor methods named `currency()`, not `getCurrency()`.
- `equals`, `hashCode`, `toString` generated from all components.
- Can implement interfaces, add methods and static fields. **Cannot** extend a class or declare extra instance fields.
- **Shallowly** immutable: a `List` component can still be mutated. Copy it: `items = List.copyOf(items);`.
- Good for DTOs, value objects, map keys, stream intermediate results. **Not** for JPA entities (entities need a no-arg constructor and mutable state).

## Sealed types

```java
public sealed interface PaymentResult permits Approved, Declined, Pending {}
public record Approved(String txId) implements PaymentResult {}
public record Declined(String reason) implements PaymentResult {}
public record Pending(Instant retryAt) implements PaymentResult {}
```

Each permitted subclass must be `final`, `sealed`, or `non-sealed`. Records are implicitly final.

## Pattern matching

```java
// instanceof pattern (16)
if (obj instanceof String s && !s.isBlank()) { use(s); }

// switch pattern + record deconstruction (21)
String describe(PaymentResult r) {
    return switch (r) {
        case Approved(String txId)               -> "ok " + txId;
        case Declined(String reason) when reason.contains("fraud") -> "blocked";
        case Declined d                           -> "declined: " + d.reason();
        case Pending p                            -> "retry at " + p.retryAt();
    };   // no default needed: the sealed hierarchy is exhaustive
}
```

- Add a new permitted type and every exhaustive switch **fails to compile** until handled. That's the point.
- More specific cases must come **before** general ones (a dominated case is a compile error).
- Switch can now handle `null` explicitly with `case null`. Without it, a `null` selector throws NPE.
- Arrow-form `case X ->` does **not** fall through. Old colon-form does.

## Other useful bits

- **`var`**: local variables only (not fields, parameters, or return types); the type is still static. `var x = null;` does not compile.
- **Text blocks**: `"""` multi-line strings with incidental indentation stripped. Great for SQL and JSON in tests.
- **Sequenced collections (21)**: `getFirst()`, `getLast()`, `reversed()`, `addFirst()` on `List`, `Deque`, `LinkedHashSet`, `LinkedHashMap` (`firstEntry()`, `pollLastEntry()`).
- **Helpful NPEs**: messages like `Cannot invoke "String.length()" because "user.name" is null`.
- **Default methods**: if a class inherits the same default method from two interfaces, it **must override** it (call one with `A.super.method()`).

## Traps

- "Records are deeply immutable." Only shallowly.
- "Record accessors are `getX()`." They are `x()`.
- "`var` makes Java dynamically typed." The type is inferred at compile time and fixed.
- "Sealed classes require `final` subclasses." `sealed` or `non-sealed` are also allowed.
- "Virtual threads arrived in 17." **21**.

## Say it out loud

**"Why combine sealed types with records and switch patterns?"**
It gives you algebraic data types: a closed set of cases, each an immutable value, and the compiler forces every switch to handle all of them. When I add a new payment outcome, every place that needs to handle it becomes a compile error instead of a runtime bug.

## Practice MCQs

**Q1.** Which is true about records?
- A) They can extend another class
- B) They can declare additional instance fields
- C) They can implement interfaces
- D) Accessors are named `getX()`

<details markdown="1"><summary>Answer</summary>

**C.** Records implicitly extend `java.lang.Record`, allow only static extra fields, and accessors use the component name.
</details>

**Q2.** A subclass of a sealed class must be declared:
- A) Only `final`
- B) `final`, `sealed`, or `non-sealed`
- C) `abstract`
- D) In the same file

<details markdown="1"><summary>Answer</summary>

**B.** (They must be in the same module, or the same package for the unnamed module, not necessarily the same file.)
</details>

**Q3.** Which LTS introduced virtual threads as a final feature?
- A) 11
- B) 17
- C) 19
- D) 21

<details markdown="1"><summary>Answer</summary>

**D.** Previewed in 19 and 20, final in 21.
</details>

**Q4.** Which compiles?
- A) `var x = null;`
- B) `var list = new ArrayList<String>();`
- C) `private var name = "a";` (as a field)
- D) `public var get() { ... }`

<details markdown="1"><summary>Answer</summary>

**B.** `var` is for local variables with an initializer whose type can be inferred.
</details>

**Q5.** A switch over a sealed interface with all permitted subtypes covered and no `default`:
- A) Fails to compile
- B) Compiles; adding a new subtype later breaks compilation of the switch
- C) Throws at runtime if a new subtype appears
- D) Only works with enums

<details markdown="1"><summary>Answer</summary>

**B.** Exhaustiveness is checked by the compiler.
</details>

## Related

- [Core language traps](01-core-language.md)
- [Concurrency](03-concurrency.md): virtual threads in depth.
