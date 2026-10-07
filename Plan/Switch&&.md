---
title: 150-Day Elite SDE-1 Curriculum
aliases: [SDE1-150, Elite SDE-1 Plan]
tags: [sde1, dsa, java, spring-boot, backend, lld, hld, sql, ml, ai, llm, interview-prep]
days: 150
max-hours-per-day: 5
primary-language: Java 21
ml-language: Python 3.12
flagship-project: Triage
created: 2026-10-07
---

# 150-Day Elite SDE-1 Curriculum

> [!abstract] What this document is
> An exact, day-by-day training program. Every day lists what to learn, what to implement, which exact problems to solve, which exact design to produce, which exact project feature to ship, and what to revise. No day exceeds 5 hours.

## Navigation

- [[#Phase 1 — Java Foundation + DSA Core Patterns (Days 1–20)]]
- [[#Phase 2 — DSA Acceleration + Java Proficiency + First Spring Boot (Days 21–45)]]
- [[#Phase 3 — Production Backend + SQL + LLD Foundations (Days 46–70)]]
- [[#Phase 4 — LLD Systems + HLD Foundations + Concurrency + CS Fundamentals (Days 71–90)]]
- [[#Phase 5 — Python, ML, Deep Learning, LLM Engineering (Days 91–117)]]
- [[#Phase 6 — ML System Design + Cloud + Reliability + Security (Days 118–130)]]
- [[#Phase 7 — Interview War Mode (Days 131–150)]]
- [[#Appendix A — Flagship Project Defense Question Bank]]
- [[#Appendix B — Behavioral Story Bank and Amazon Leadership Principles Map]]
- [[#Appendix C — Mock Interview Protocols]]
- [[#Appendix D — Skill Tier Classification]]

Topic hubs (create these as notes if you want backlinks): [[DSA]] · [[Java]] · [[Backend]] · [[Spring Boot]] · [[SQL]] · [[LLD]] · [[HLD]] · [[ML]] · [[AI Engineering]] · [[CS Fundamentals]] · [[Triage Project]] · [[Behavioral]]

## Conventions used in every day

- **Solve** = write the brute force first, then the optimal solution in Java, state time/space complexity, list 3 edge cases, and write 2 custom test inputs. Target 25 minutes per medium, 15 per easy, 40 per hard.
- **Re-solve** = closed-book, in Java, timed. If it takes more than 1.5× target, it goes back into the queue 7 days later.
- **Timed set** = problems solved back-to-back with a running clock, no editor autocomplete, thinking out loud as if an interviewer were present.
- **Design** = written document with: assumptions, functional and non-functional requirements, estimation, API, data model, architecture diagram (Mermaid in Obsidian), bottlenecks, failure modes, tradeoffs, scaling plan.
- **Implement (LLD)** = compilable Java with unit tests, in the `lld/` module of your practice repo.
- Problem names are LeetCode names unless prefixed with `CF` (Codeforces), `AC` (AtCoder), `HR` (HackerRank), `PGX` (pgexercises.com), `GfG` (GeeksforGeeks). Problems marked `(NeetCode)` are LeetCode-premium problems with a free mirror on neetcode.io.
- Math is inline, e.g. $O(n \log n)$.

## The flagship project: Triage

> [!info] Triage — AI-assisted support ticket intelligence platform
> A production-grade backend that ingests customer support tickets from multiple channels, classifies and prioritizes them with a trained ML model, routes them to agents, enforces SLAs with background jobs, offers semantic search over tickets and a knowledge base, and generates retrieval-grounded reply suggestions with an LLM. Everything is measured.

**Why this project.** It is a real product category (Zendesk, Intercom, Freshdesk all ship this). It forces event-driven ingestion, idempotency, caching, SLA timers, authz, search, an ML classifier with a real metric, embeddings, RAG, LLM evaluation, prompt-injection defense, and observability. It is bounded enough to finish in 150 days at 1 hour per day average.

**Architecture (target state, Day 130).**

```mermaid
flowchart LR
  Client[Web/API clients] --> GW[Spring Boot API: triage-api]
  GW --> PG[(PostgreSQL + pgvector)]
  GW --> RD[(Redis: cache, rate limits, locks)]
  GW --> K[(Kafka)]
  K --> WK[triage-worker: consumers, SLA jobs, outbox relay]
  WK --> PG
  WK --> MLS[Python ML service: FastAPI]
  MLS --> PG
  MLS --> LLM[LLM API or local Ollama]
  GW --> OTEL[OpenTelemetry Collector]
  WK --> OTEL
  MLS --> OTEL
  OTEL --> PROM[Prometheus + Grafana + Tempo]
```

**Repositories.** One GitHub monorepo `triage/` with `triage-api/` (Spring Boot), `triage-worker/` (Spring Boot), `triage-ml/` (Python, FastAPI), `infra/` (Docker Compose, Terraform), `docs/` (ADRs, runbooks), `load/` (k6 scripts). Plus a separate `sde-prep/` repo for DSA (`dsa/`), LLD (`lld/`), and notes.

**Stack.** Java 21, Spring Boot 3.3, Spring Data JPA, Flyway, PostgreSQL 16 with pgvector, Redis 7, Apache Kafka 3.7 (KRaft), Testcontainers, JUnit 5, Mockito, Micrometer + OpenTelemetry, Prometheus, Grafana, Tempo, Docker, GitHub Actions, k6, Python 3.12, FastAPI, scikit-learn, sentence-transformers, PyTorch, an OpenAI-compatible LLM endpoint (or Ollama `llama3.1:8b` locally), Terraform, AWS (one EC2 host running Compose, ECR, S3, RDS optional).

**Metrics you will measure (never fabricated).** p50/p95/p99 latency per endpoint, requests per second at p95 < 200 ms, Kafka consumer lag, ingestion throughput (tickets/second), cache hit rate, DB query p95 and index usage, test coverage (JaCoCo line and branch), classifier macro-F1 and per-class F1, retrieval recall@5 and MRR, LLM answer faithfulness and groundedness scores from your eval harness, inference p95 latency, error budget burn, monthly cloud cost.

**Datasets.** Hugging Face `bitext/Bitext-customer-support-llm-chatbot-training-dataset` (27 intents, 11 categories; used for classification and as a seed knowledge base), plus a self-authored knowledge base of 40 Markdown articles you write on Days 104–106 so that RAG has a grounded corpus.

## Phase map

| Phase | Days | Primary load | Secondary load | Project state |
|---|---|---|---|---|
| 1 | 1–20 | Java 0→OOP/collections, DSA core patterns | Git, Linux, HTTP | Requirements, architecture v0, domain model in plain Java |
| 2 | 21–45 | DSA (lists, trees, graphs, DP), Java concurrency, Spring Boot basics | SQL start, OOP/UML | Spring Boot CRUD, Flyway, validation, Testcontainers |
| 3 | 46–70 | Backend (JPA, security, Redis, Kafka, observability), SQL, LLD patterns | DSA maintenance, OS/Networking | Auth, cache, Kafka ingestion, SLA jobs, outbox, Docker, CI |
| 4 | 71–90 | LLD systems, HLD fundamentals + first systems, DSA hard | DBMS internals, networking | Rate limiting, search, structured logging, load-test baseline |
| 5 | 91–117 | Python, ML, DL, LLM/RAG engineering | HLD systems, DSA maintenance | ML service, classifier, embeddings, semantic search, RAG, eval harness |
| 6 | 118–130 | ML system design, cloud deploy, Terraform, security, reliability | DSA hard, HLD | Deployed, monitored, load-tested, chaos-tested, documented |
| 7 | 131–150 | Mocks: coding, LLD, HLD, CS, ML, behavioral; project defense | Spaced revision | Final architecture review, README, demo, defense deck |

# Phase 1 — Java Foundation + DSA Core Patterns (Days 1–20)

> [!note] Phase goal
> Java from zero to confident OOP + collections. DSA core patterns (arrays, hashing, two pointers, sliding window, prefix sums, binary search, sorting, linked lists, stacks, heaps) learned, reinforced, and re-solved. Triage requirements and domain model exist in plain Java.


## Day 2 — Strings, StringBuilder, fast I/O; prefix sums

**Total: 5 hours**

### Java / Backend — 2 hours

- Learn: `String` immutability, `charAt`, `substring`, `split`, `equals` vs `==`, `String.format`, `StringBuilder` (`append`, `insert`, `reverse`, `setCharAt`), `char` arithmetic (`c - 'a'`), `Character.isDigit/isLetter`, `Integer.parseInt`, `String.valueOf`, `BufferedReader` + `StringTokenizer` for competitive input, `PrintWriter` for output.
- Implement: `FastIO.java` template (read ints, longs, lines; flush output). `StringKit.java` with `isPalindrome`, `countVowels`, `toTitleCase`, `compressRunLength("aaabb") -> "a3b2"`.
- Exact exercise: `CF 71A Way Too Long Words`, `CF 4A Watermelon`, `CF 231A Team` using your `FastIO` template on Codeforces (submit and get Accepted).

### DSA — 2.5 hours

- Learn: prefix sum array $P[i] = \sum_{j<i} a[j]$, range sum in $O(1)$, prefix sum with hash map for "subarray sum equals k", 2D prefix sums.
- Implement: `PrefixSum` class with `build(int[])`, `rangeSum(l, r)`; `PrefixSum2D` with `rectSum(r1,c1,r2,c2)`.
- Solve:
  1. Running Sum of 1d Array
  2. Find Pivot Index
  3. Range Sum Query - Immutable
  4. Subarray Sum Equals K
  5. Range Sum Query 2D - Immutable
- Timed task: `CF 466C Number of Ways` (prefix sums, 30 minutes).
- Revision: re-explain Maximum Subarray from Day 1 in one paragraph.

### Capstone Project — 0.5 hours

- Build: `docs/01-requirements.md`.
- Deliverable: functional requirements as numbered user stories (ticket create via API/email webhook, classification, assignment, SLA breach alerts, search, suggested replies, agent and lead roles), non-functional targets (p95 read < 100 ms, ingest 200 tickets/s burst, 99.9% availability, RPO 15 min).

### End-of-Day Check

- [ ] I can derive the prefix-sum + hash-map trick for Subarray Sum Equals K including the `map.put(0, 1)` seed.
- [ ] I can explain why `StringBuilder` beats `+=` in a loop ($O(n)$ vs $O(n^2)$).

## Day 3 — Classes and objects; hashing patterns

**Total: 5 hours**

### Java / Backend — 2 hours

- Learn: classes, fields, constructors, constructor chaining with `this(...)`, instance vs static members, access modifiers (`private`, package-private, `protected`, `public`), `this`, `toString`, getters/setters and when not to write them, `static` factory methods, packages and imports.
- Implement: `BankAccount` with `deposit`, `withdraw` (throw `IllegalArgumentException` on negative or insufficient), `balance`, `transactions` list, `static int openAccounts`. `Money` as a plain class holding `long cents` and a `Currency` string.
- Exact exercise: 6 JUnit tests for `BankAccount` including overdraft rejection and that `openAccounts` increments.

### DSA — 2.5 hours

- Learn: `HashMap`/`HashSet` internals at a conceptual level (buckets, load factor 0.75, resize, $O(1)$ average), `getOrDefault`, `merge`, `computeIfAbsent`, hashing of strings vs sorted keys vs count arrays, frequency counting.
- Implement: `charFrequency(String)` returning `int[26]`.
- Solve:
  1. Valid Anagram
  2. Group Anagrams
  3. Top K Frequent Elements (bucket sort version)
  4. Longest Consecutive Sequence
  5. First Unique Character in a String
- Timed task: Isomorphic Strings (15 minutes).
- Revision: re-solve Two Sum closed-book (10 minutes).

### Capstone Project — 0.5 hours

- Build: `docs/02-domain-glossary.md`.
- Deliverable: glossary and lifecycle for `Ticket` (NEW → TRIAGED → ASSIGNED → IN_PROGRESS → WAITING_ON_CUSTOMER → RESOLVED → CLOSED), `Agent`, `Team`, `Queue`, `SLAPolicy`, `KnowledgeArticle`, `Suggestion`, `Classification`; a state diagram in Mermaid.

### End-of-Day Check

- [ ] I can explain the $O(n)$ argument for Longest Consecutive Sequence (only start-of-sequence numbers trigger a walk).
- [ ] I can explain what a constructor is for and why validation belongs inside it.

## Day 4 — Encapsulation, records, enums, equals/hashCode; two pointers

**Total: 5 hours**

### Java / Backend — 2 hours

- Learn: encapsulation and invariants, `equals`/`hashCode` contract (symmetric, consistent, equal objects have equal hashes), `Objects.equals`, `Objects.hash`, `record` (compact constructors, auto equals/hashCode), `enum` with fields and methods, `EnumMap`, `switch` on enums, `Comparable`.
- Implement: `record Point(int x, int y)`; `enum TicketStatus` with `canTransitionTo(TicketStatus)`; a class `Version` implementing `Comparable<Version>` with correct `equals`/`hashCode`.
- Exact exercise: test that `new HashSet<>(List.of(new Version(1,2,3), new Version(1,2,3))).size() == 1`.

### DSA — 2.5 hours

- Learn: two pointers (opposite ends, same direction, fast/slow), when sorting enables two pointers, de-duplication in 3Sum.
- Solve:
  1. Valid Palindrome
  2. Two Sum II - Input Array Is Sorted
  3. 3Sum
  4. Container With Most Water
  5. Move Zeroes
- Timed task: Remove Duplicates from Sorted Array (10 minutes), Squares of a Sorted Array (12 minutes).
- Revision: re-solve Group Anagrams closed-book.

### Capstone Project — 0.5 hours

- Build: `docs/03-api-sketch.md`.
- Deliverable: first REST surface: `POST /tickets`, `GET /tickets/{id}`, `GET /tickets?status=&assignee=&page=`, `PATCH /tickets/{id}/status`, `POST /tickets/{id}/assign`, `GET /search?q=`, `POST /tickets/{id}/suggestions`, with request/response JSON examples and error envelope `{code, message, details, traceId}`.

### End-of-Day Check

- [ ] I can state the equals/hashCode contract and what breaks in a `HashMap` if it is violated.
- [ ] I can explain the greedy move in Container With Most Water.

## Day 5 — Inheritance, polymorphism, interfaces; fixed-size sliding window

**Total: 5 hours**

### Java / Backend — 2 hours

- Learn: inheritance, `super`, method overriding, `@Override`, dynamic dispatch, abstract classes, interfaces, default and static interface methods, "program to an interface", composition over inheritance, `instanceof` pattern matching, sealed interfaces (Java 17+).
- Implement: `sealed interface Shape permits Circle, Rectangle, Triangle` with `area()`; `abstract class Notification` with `Email`, `Sms`, `Push` subclasses and a `NotificationSender` interface; use a `List<Shape>` and a `switch` pattern over the sealed type.
- Exact exercise: refactor `BankAccount` into `interface Account` + `SavingsAccount` (interest) + `CheckingAccount` (overdraft limit); tests for polymorphic behavior.

### DSA — 2.5 hours

- Learn: fixed-size sliding window, maintaining a window aggregate in $O(1)$ per step, window with a frequency array.
- Solve:
  1. Maximum Average Subarray I
  2. Find All Anagrams in a String
  3. Permutation in String
  4. Maximum Number of Vowels in a Substring of Given Length
  5. Sliding Subarray Beauty
- Timed task: Contains Duplicate II (12 minutes).
- Revision: re-solve 3Sum closed-book (25 minutes).

### Capstone Project — 0.5 hours

- Build: `docs/04-architecture-v0.md`.
- Deliverable: Mermaid diagram of v0 (API + Postgres only), a list of what v1 adds (Redis, Kafka, worker, ML service), and ADR-001 "Use PostgreSQL as the system of record" with alternatives considered (MongoDB, MySQL) and reasons.

### End-of-Day Check

- [ ] I can explain dynamic dispatch and why `List<Shape>` can hold three concrete types.
- [ ] I can explain why the sliding window is $O(n)$ even though it looks like nested loops.

## Day 6 — Exceptions, Git workflow; variable-size sliding window

**Total: 5 hours**

### Java / Backend — 2 hours

- Learn: checked vs unchecked exceptions, `try/catch/finally`, try-with-resources and `AutoCloseable`, custom exception hierarchies, exception chaining (`cause`), when to throw vs return `Optional`, never swallowing exceptions, stack traces. Git: `init`, `add`, `commit`, branches, `merge`, `rebase`, `log --oneline --graph`, `.gitignore`, conventional commit messages, pull requests.
- Implement: `TicketNotFoundException extends RuntimeException`, `InvalidTransitionException`, a `Result<T>` sealed type (`Ok`/`Err`); a `ResourceGuard implements AutoCloseable` demo.
- Exact exercise: move `sde-prep` and `triage` onto feature-branch workflow; open and merge your first PR to `main` with a squash merge.

### DSA — 2.5 hours

- Learn: variable-size window template (expand right, shrink left while invalid), "at most k distinct", "longest valid", "shortest valid" variants.
- Solve:
  1. Longest Substring Without Repeating Characters
  2. Minimum Size Subarray Sum
  3. Longest Repeating Character Replacement
  4. Fruit Into Baskets
  5. Max Consecutive Ones III
- Timed task: Subarray Product Less Than K (20 minutes).
- Revision: re-solve Find All Anagrams in a String closed-book.

### Capstone Project — 0.5 hours

- Build: `docs/adr/ADR-002-monorepo.md` and PR template.
- Deliverable: ADR explaining monorepo vs polyrepo for Triage; `.github/PULL_REQUEST_TEMPLATE.md` with checklist (tests, migration, docs, metrics).

### End-of-Day Check

- [ ] I can write the variable-window template from memory and explain the invariant.
- [ ] I can explain when to use a checked exception and why `RuntimeException` is preferred for programming errors.

## Day 7 — Collections framework; string manipulation problems

**Total: 5 hours**

### Java / Backend — 2 hours

- Learn: `List` (`ArrayList` vs `LinkedList`), `ArrayDeque` as stack and queue (never `Stack`), `HashMap`, `LinkedHashMap` (insertion order, access order for LRU), `TreeMap` (`floorKey`, `ceilingKey`, `headMap`), `HashSet`, `TreeSet`, `PriorityQueue` with `Comparator`, `Collections.sort`, `List.of` immutability, `Iterator` and `ConcurrentModificationException`, `Comparator.comparing().thenComparing().reversed()`.
- Implement: `CollectionsDrill.java`: group words by length into `TreeMap<Integer, List<String>>`; LRU of size 3 using `LinkedHashMap` with `removeEldestEntry`; top-3 by frequency with a `PriorityQueue`.
- Exact exercise: `Comparator` chains sorting `Employee(name, dept, salary)` by dept asc, salary desc, name asc; tests.

### DSA — 2.5 hours

- Learn: common string techniques: reverse in place, two-pointer compare, building with `StringBuilder`, manual parsing with state, `char[]` conversion.
- Solve:
  1. Reverse Words in a String
  2. Longest Common Prefix
  3. String Compression
  4. Valid Palindrome II
  5. Encode and Decode Strings (NeetCode)
  6. String to Integer (atoi)
- Timed task: Longest Palindromic Substring (expand around center, 25 minutes).
- Revision: re-solve Longest Substring Without Repeating Characters closed-book.

### Capstone Project — 0.5 hours

- Build: plain-Java domain in `triage-api/src/main/java/.../domain/` (no Spring yet).
- Deliverable: `Ticket` class with id, subject, body, channel enum, status enum with validated transitions, priority enum, `createdAt`, `updatedAt`; `InvalidTransitionException`; 8 unit tests covering the state machine.

### End-of-Day Check

- [ ] I can pick the right collection for 6 scenarios (LRU, ordered keys, dedupe, FIFO, top-k, stack) and justify in one sentence each.
- [ ] I can explain the $O(n^2)$ expand-around-center and why it beats brute force $O(n^3)$.

## Day 8 — Generics and custom data structures; mixed timed set 1

**Total: 5 hours**

### Java / Backend — 2 hours

- Learn: generic classes and methods, bounded types `<T extends Comparable<T>>`, wildcards `? extends` / `? super` (PECS), type erasure and why `new T[]` fails, generic interfaces.
- Implement: `MyArrayList<T>` (grow by doubling), `MyStack<T>`, `MyQueue<T>` (circular array), `MySinglyLinkedList<T>` with `addFirst`, `addLast`, `removeFirst`, `reverse`, iterator.
- Exact exercise: tests proving amortized doubling: insert $10^6$ elements and assert capacity grew fewer than 25 times.

### DSA — 2.5 hours

- Learn: how to attack an unseen problem: restate, examples, brute force, identify bottleneck, pick pattern, verify on edge cases.
- Timed set (90 minutes, closed-book, talk out loud):
  1. Product of Array Except Self
  2. Merge Sorted Array
  3. Longest Substring with At Most Two Distinct Characters (NeetCode) — or free alternative: Fruit Into Baskets re-derived without notes
  4. Trapping Rain Water (hard: two-pointer; accept partial credit)
- Revision (60 minutes): re-solve Longest Consecutive Sequence, Container With Most Water, Subarray Sum Equals K; write a one-line "trigger" for each pattern learned so far in `dsa/notes/patterns.md`.

### Capstone Project — 0.5 hours

- Build: `SLAPolicy` domain class: `firstResponseMinutes`, `resolutionMinutes` per priority; `SlaCalculator.deadline(ticket, policy)` using `java.time` with a business-hours flag.
- Deliverable: 5 tests including a ticket created at 17:59 on Friday with business hours on.

### End-of-Day Check

- [ ] I can explain PECS with `Collections.copy(List<? super T>, List<? extends T>)`.
- [ ] I can explain the two-pointer Trapping Rain Water proof (water at `i` bounded by smaller of maxima).

## Day 9 — Lambdas, streams, Optional; sorting algorithms

**Total: 5 hours**

### Java / Backend — 2 hours

- Learn: functional interfaces (`Function`, `BiFunction`, `Predicate`, `Supplier`, `Consumer`, `UnaryOperator`), lambdas, method references, `Stream` pipeline (`map`, `filter`, `sorted`, `distinct`, `limit`, `flatMap`, `collect`, `groupingBy`, `counting`, `partitioningBy`, `joining`, `toMap` with merge), `IntStream.range`, laziness, when streams hurt readability, `Optional` (`map`, `orElseThrow`, never `.get()`), `Optional` is not for fields.
- Implement: `StreamsDrill.java`: from a `List<Ticket>` compute count by status, top 5 oldest open tickets, average age per priority, `Map<Agent, List<Ticket>>`, and a `String` CSV line via `joining`.
- Exact exercise: rewrite three loops from Day 7 as streams and one stream back into a loop; decide which reads better and write why in a comment.

### DSA — 2.5 hours

- Learn: stability, comparison sorts lower bound $\Omega(n \log n)$, merge sort, quicksort with random pivot and three-way partition, counting sort, bucket sort, Java's `Arrays.sort` (dual-pivot quicksort for primitives, TimSort for objects), sorting with custom comparators, sorting as a preprocessing step.
- Implement: `mergeSort(int[])`, `quickSort(int[])` (random pivot), `countingSort(int[] in 0..k)`, and a test comparing all three against `Arrays.sort` on 1000 random arrays.
- Solve:
  1. Sort Colors
  2. Largest Number
  3. Sort an Array (implement, do not call library)
  4. Kth Largest Element in an Array (quickselect)
  5. Relative Sort Array
- Timed task: Merge Intervals (20 minutes; intervals formally on Day 22).
- Revision: re-solve Top K Frequent Elements closed-book.

### Capstone Project — 0.5 hours

- Build: `TicketRouter` in plain Java: a `RoutingRule` functional interface and three rules (by category, by language, by VIP flag) composed with streams.
- Deliverable: tests showing rule precedence and a fallback queue.

### End-of-Day Check

- [ ] I can explain why quicksort is $O(n^2)$ worst case and how a random pivot makes that improbable.
- [ ] I can write a `groupingBy` with a downstream `counting()` from memory.

## Day 10 — Immutability and memory model; binary search fundamentals

**Total: 5 hours**

### Java / Backend — 2 hours

- Learn: immutability (final fields, no setters, defensive copies, `List.copyOf`), value objects, `String` pool and `intern`, `Integer` cache from -128 to 127 and why `==` on boxed types is a bug, autoboxing cost in hot loops, stack vs heap, references vs values, pass-by-value of references, escape of `this` in constructors, `hashCode` caching.
- Implement: immutable `Money` with `plus`, `minus`, `times`, currency check; immutable `TicketSnapshot` record with defensive copy of a `List<String> tags`.
- Exact exercise: write a test demonstrating `Integer a=127,b=127; a==b` true and `128` false; write the explanation in a comment.

### DSA — 2.5 hours

- Learn: binary search invariants (`lo`, `hi` closed vs half-open), `mid = lo + (hi - lo) / 2`, lower bound and upper bound, termination proof, `Arrays.binarySearch` and `Collections.binarySearch` return conventions.
- Implement: `lowerBound(int[] a, int target)`, `upperBound`, and generic `firstTrue(int lo, int hi, IntPredicate p)`.
- Solve:
  1. Binary Search
  2. Search Insert Position
  3. First Bad Version
  4. Find First and Last Position of Element in Sorted Array
  5. Sqrt(x)
  6. Guess Number Higher or Lower
- Timed task: Valid Perfect Square (10 minutes), Search a 2D Matrix (15 minutes).
- Revision: re-solve Sort Colors closed-book.

### Capstone Project — 0.5 hours

- Build: `docs/05-data-model-v0.md`.
- Deliverable: ERD (Mermaid `erDiagram`) for `tickets`, `ticket_events`, `agents`, `teams`, `queues`, `sla_policies`, `knowledge_articles`; primary keys as UUID v7 (ADR-003 explaining why not auto-increment), `created_at` timestamps with time zone.

### End-of-Day Check

- [ ] I can write the `firstTrue` binary search template and prove it terminates.
- [ ] I can explain why an immutable class is automatically thread-safe.
## Day 11 — Linux and Bash; binary search on the answer

**Total: 5 hours**

### Java / Backend — 1.5 hours

- Learn: Linux file system layout, `ls -la`, permissions and `chmod`, `ps`, `top`, `kill`, signals, `grep -rn`, `find`, `awk '{print $2}'`, `sed -i`, pipes, redirection, `xargs`, `curl -v`, `ssh`, `scp`, `tail -f`, `journalctl`, environment variables, `PATH`, shell scripts with `set -euo pipefail`, `cron`.
- Implement: `scripts/log-stats.sh` that takes an access log and prints top 10 paths by count and p95 latency using `sort`, `uniq -c`, `awk`.
- Exact exercise: generate a 100k-line fake log with a Java program, run the script, and time it.

### DSA — 3 hours

- Learn: binary search on the answer (monotonic predicate), rotated arrays, peak finding, searching on a function of the index.
- Solve:
  1. Search in Rotated Sorted Array
  2. Find Minimum in Rotated Sorted Array
  3. Koko Eating Bananas
  4. Capacity To Ship Packages Within D Days
  5. Find Peak Element
  6. Minimum Number of Days to Make m Bouquets
- Timed task: `CF 474B Worms` (binary search or prefix, 20 minutes), `AC ABC 146 C - Buy an Integer` (25 minutes).
- Revision: re-solve Find First and Last Position of Element in Sorted Array closed-book.

### Capstone Project — 0.5 hours

- Build: `Makefile` or `scripts/dev.sh` with targets `build`, `test`, `run`, `lint`.
- Deliverable: `README.md` quickstart section that works from a fresh clone.

### End-of-Day Check

- [ ] I can explain why Koko's predicate is monotonic and how that justifies binary search.
- [ ] I can explain how the rotated-array search decides which half is sorted.

## Day 12 — JVM internals; advanced binary search

**Total: 5 hours**

### Java / Backend — 1.5 hours

- Learn: compile to bytecode, class loading (bootstrap, platform, application), JIT (C1/C2, tiered), interpreter vs compiled, heap generations (young: eden + survivors, old), minor vs major GC, G1 defaults, ZGC in one paragraph, `-Xms`, `-Xmx`, `-XX:+UseG1GC`, stack size, `OutOfMemoryError` vs `StackOverflowError`, `jps`, `jstat -gc`, `jcmd GC.heap_info`, `jmap -histo`, reading a heap dump in VisualVM.
- Implement: `MemoryLeak.java` that grows a static `List<byte[]>`; run with `-Xmx64m`, watch `jstat -gc` until OOM; fix it.
- Exact exercise: write a 10-line note on what happens from `java Main` to the first line executing.

### DSA — 3 hours

- Learn: binary search across two arrays, minimizing the maximum, searching on real numbers with fixed iterations.
- Solve:
  1. Median of Two Sorted Arrays
  2. Split Array Largest Sum
  3. Time Based Key-Value Store
  4. Kth Smallest Element in a Sorted Matrix
  5. Minimize Max Distance to Gas Station (NeetCode) — or free alternative: Magnetic Force Between Two Balls
- Timed task: `AC ABC 174 E - Logs` (25 minutes).
- Revision: re-solve Koko Eating Bananas and Search in Rotated Sorted Array closed-book.

### Capstone Project — 0.5 hours

- Build: `docs/06-capacity-v0.md`.
- Deliverable: back-of-envelope: 50k tickets/day baseline, 200/s burst, average ticket 2 KB, 3 years retention ⇒ storage in GB, read:write ratio assumption 20:1, QPS at peak, memory needed for hot cache of last 24 hours.

### End-of-Day Check

- [ ] I can explain the partition invariant in Median of Two Sorted Arrays.
- [ ] I can explain young vs old generation and what a stop-the-world pause is.

## Day 13 — Build tools and testing discipline; hashing + prefix combinations

**Total: 5 hours**

### Java / Backend — 1.5 hours

- Learn: Maven lifecycle (`validate`, `compile`, `test`, `package`, `verify`), `pom.xml` dependencies and scopes, `maven-surefire`, JaCoCo plugin, JUnit 5 (`@ParameterizedTest`, `@CsvSource`, `@Nested`, `@BeforeEach`), AssertJ fluent assertions, Mockito (`mock`, `when`, `verify`, `ArgumentCaptor`), the test pyramid, arrange-act-assert, test naming `method_condition_expectedResult`.
- Implement: add JaCoCo to `sde-prep` and `triage-api`; parameterized tests for `Warmup.isPrime`.
- Exact exercise: mock a `Clock` in `SlaCalculator` tests so that deadlines are deterministic.

### DSA — 3 hours

- Learn: hash map + prefix state patterns (remainders, parity, balance), hashing of pairs, counting pairs with a running map.
- Solve:
  1. Contiguous Array
  2. Continuous Subarray Sum
  3. Subarray Sums Divisible by K
  4. Number of Good Pairs
  5. Count Number of Nice Subarrays
  6. Minimum Operations to Reduce X to Zero
- Timed task: `CF 1398C Good Subarrays` (25 minutes), `CF 1296C Yet Another Walking Robot` (25 minutes).
- Revision: re-solve Subarray Sum Equals K closed-book, explain the remainder trick in Continuous Subarray Sum aloud.

### Capstone Project — 0.5 hours

- Build: JaCoCo threshold in `triage-api` pom (fail below 70% line coverage for `domain` package).
- Deliverable: green `mvn verify` run with the report in `target/site/jacoco`.

### End-of-Day Check

- [ ] I can explain why "same remainder seen before" means a subarray divisible by k exists.
- [ ] I can write a parameterized JUnit test and a Mockito `verify` from memory.

## Day 14 — I/O, JSON, java.time; mixed timed set 2

**Total: 5 hours**

### Java / Backend — 1.5 hours

- Learn: `java.nio.file.Files` (`readAllLines`, `lines`, `write`), `Path`, `BufferedReader`, character encodings (UTF-8 always), Jackson `ObjectMapper` (`readValue`, `writeValueAsString`, `@JsonProperty`, `@JsonIgnore`, records with Jackson, `JavaTimeModule`), `java.time` (`Instant`, `LocalDateTime`, `ZonedDateTime`, `Duration`, `DateTimeFormatter`, never `java.util.Date`), ISO-8601.
- Implement: `TicketJsonCodec` that serializes `Ticket` to JSON and back, round-trip test; `ImportTickets` reading a CSV of 1000 fake tickets into domain objects.
- Exact exercise: parse `2026-10-07T10:15:30+05:30` and print it in UTC and in `America/New_York`.

### DSA — 3 hours

- Timed set (100 minutes, closed-book):
  1. Longest Palindromic Substring
  2. Minimum Window Substring (hard)
  3. 4Sum
  4. Search a 2D Matrix II
- Revision (80 minutes): review every wrong or slow problem from Days 1–13; re-solve the 4 weakest; update `dsa/notes/patterns.md` with a "trigger ⇒ technique" table for: sorted input, "subarray sum", "longest/shortest window", "kth", "monotonic predicate", "duplicates".

### Capstone Project — 0.5 hours

- Build: `TicketFixtures` test utility generating realistic tickets (Faker-like, hand-written).
- Deliverable: 20 sample tickets in `docs/samples/tickets.json`.

### End-of-Day Check

- [ ] I can explain the two-counter approach in Minimum Window Substring (`have` vs `need`).
- [ ] I can explain the difference between `Instant` and `LocalDateTime` and which to store in a database.

## Day 15 — HTTP from first principles; linked lists I

**Total: 5 hours**

### Java / Backend — 2 hours

- Learn: TCP connection, HTTP/1.1 request line, headers, body, status code classes, idempotent vs safe methods, `Content-Type`, `Accept`, `ETag`, `Cache-Control`, cookies, keep-alive, chunked encoding, HTTP/2 multiplexing in one paragraph, `curl -v` reading, REST resource naming, URL design, JSON conventions, error envelopes, 400 vs 404 vs 409 vs 422.
- Implement: a raw HTTP server with `java.net.ServerSocket` that parses the request line and headers and returns JSON for `GET /health` and `POST /echo`; test it with `curl`.
- Exact exercise: use `curl -v` on `https://httpbin.org/get` and annotate every header in a note.

### DSA — 2.5 hours

- Learn: singly linked list node, dummy head trick, fast/slow pointers, in-place reversal, splicing.
- Implement: `ListNode` and helpers `fromArray`, `toArray`.
- Solve:
  1. Reverse Linked List
  2. Merge Two Sorted Lists
  3. Linked List Cycle
  4. Middle of the Linked List
  5. Remove Nth Node From End of List
  6. Palindrome Linked List
- Timed task: Delete the Middle Node of a Linked List (12 minutes).
- Revision: re-solve Minimum Window Substring (yesterday's hard) with notes allowed, then closed-book tomorrow.

### Capstone Project — 0.5 hours

- Build: `docs/07-api-conventions.md`.
- Deliverable: Triage API conventions: versioning via `/api/v1`, pagination via cursor (`?cursor=&limit=`), filtering, sorting, error envelope, `Idempotency-Key` header on `POST /tickets`, `ETag` on `GET /tickets/{id}`.

### End-of-Day Check

- [ ] I can explain idempotency and why `PUT` and `DELETE` are idempotent while `POST` is not.
- [ ] I can reverse a linked list iteratively and recursively on a whiteboard.

## Day 16 — Threads and synchronization; linked lists II and LRU

**Total: 5 hours**

### Java / Backend — 2 hours

- Learn: `Thread`, `Runnable`, `start` vs `run`, `join`, race conditions, `synchronized` (method and block, intrinsic lock), `volatile` and visibility, happens-before, the Java Memory Model in three rules, `wait`/`notify` basics, thread states, daemon threads, why `++` is not atomic.
- Implement: `UnsafeCounter` vs `SynchronizedCounter` vs `AtomicCounter`, run 8 threads × 100k increments, print results; a `volatile boolean running` stop flag.
- Exact exercise: produce a deadlock with two locks acquired in opposite order, take a thread dump with `jstack`, read it, fix with lock ordering.

### DSA — 2.5 hours

- Learn: doubly linked list, hash map + DLL for $O(1)$ LRU, list arithmetic with carries, interleaving.
- Solve:
  1. Reorder List
  2. Add Two Numbers
  3. Copy List with Random Pointer
  4. Linked List Cycle II
  5. LRU Cache
  6. Swap Nodes in Pairs
- Timed task: Reverse Nodes in k-Group (hard, 35 minutes).
- Revision: re-solve Minimum Window Substring closed-book.

### Capstone Project — 0.5 hours

- Build: `InMemoryTicketRepository` (plain Java `ConcurrentHashMap`) implementing a `TicketRepository` interface with `save`, `findById`, `findByStatus`.
- Deliverable: tests, including a concurrent-save test with 10 threads.

### End-of-Day Check

- [ ] I can explain why `volatile` does not make `count++` safe.
- [ ] I can implement LRU Cache in under 15 minutes.

## Day 17 — Executors and concurrent collections; stacks

**Total: 5 hours**

### Java / Backend — 2 hours

- Learn: `ExecutorService`, `Executors.newFixedThreadPool`, `Callable`, `Future`, `invokeAll`, shutdown semantics, `ThreadPoolExecutor` parameters (core, max, queue, rejection policy), `CountDownLatch`, `CyclicBarrier`, `Semaphore`, `ConcurrentHashMap` (`compute`, `merge`), `CopyOnWriteArrayList`, `AtomicInteger`, `AtomicLong`, `LongAdder`.
- Implement: `ParallelWordCount` over 20 files with a fixed pool and `ConcurrentHashMap<String, LongAdder>`; a `RateLimitedExecutor` using `Semaphore(5)`.
- Exact exercise: measure runtime of word count with 1, 2, 4, 8 threads; note where speedup flattens and why.

### DSA — 2.5 hours

- Learn: stack use cases (matching, evaluation, undo, nearest-greater), `ArrayDeque` as stack.
- Solve:
  1. Valid Parentheses
  2. Min Stack
  3. Evaluate Reverse Polish Notation
  4. Implement Queue using Stacks
  5. Daily Temperatures
  6. Simplify Path
- Timed task: Basic Calculator II (25 minutes).
- Revision: re-solve LRU Cache closed-book.

### Capstone Project — 0.5 hours

- Build: `docs/adr/ADR-004-async-boundaries.md`.
- Deliverable: list of operations that must be synchronous (create ticket, read ticket) vs asynchronous (classification, embeddings, notifications, SLA checks) and the reasoning (latency budget vs failure isolation).

### End-of-Day Check

- [ ] I can describe the 7 constructor parameters of `ThreadPoolExecutor` and what happens when the queue is full.
- [ ] I can explain the monotonic-stack idea behind Daily Temperatures.

## Day 18 — Locks, CompletableFuture, virtual threads; monotonic stack and deque

**Total: 5 hours**

### Java / Backend — 2 hours

- Learn: `ReentrantLock` (`tryLock` with timeout, fairness), `ReadWriteLock`, `Condition`, `BlockingQueue` producer-consumer, `CompletableFuture` (`supplyAsync`, `thenApply`, `thenCompose`, `thenCombine`, `exceptionally`, `allOf`, timeouts via `orTimeout`), virtual threads in Java 21 (`Executors.newVirtualThreadPerTaskExecutor`), when virtual threads help (blocking I/O) and when they do not (CPU-bound, `synchronized` pinning).
- Implement: bounded producer-consumer with `ArrayBlockingQueue`; `CompletableFuture` fan-out that calls three fake services in parallel and combines; the same with 10,000 virtual threads doing `Thread.sleep(100)`.
- Exact exercise: compare wall time of 10,000 sleeps on a 16-thread platform pool vs virtual threads.

### DSA — 2.5 hours

- Learn: monotonic stack (increasing/decreasing), nearest smaller/greater to left/right, monotonic deque for sliding-window maximum.
- Solve:
  1. Next Greater Element I
  2. Next Greater Element II
  3. Largest Rectangle in Histogram
  4. Car Fleet
  5. Remove K Digits
  6. Sliding Window Maximum
- Timed task: Sum of Subarray Minimums (30 minutes).
- Revision: re-solve Daily Temperatures closed-book.

### Capstone Project — 0.5 hours

- Build: `AsyncClassifierStub` using `CompletableFuture` to simulate a 300 ms classification with timeout and fallback to `UNCLASSIFIED`.
- Deliverable: test proving a 1 s timeout yields the fallback.

### End-of-Day Check

- [ ] I can prove Largest Rectangle in Histogram is $O(n)$ with the stack argument (each index pushed and popped once).
- [ ] I can explain virtual thread pinning and the `synchronized` caveat.

## Day 19 — Mini DI container → Spring Boot; heaps I

**Total: 5 hours**

### Java / Backend — 2 hours

- Learn: Inversion of Control, Dependency Injection (constructor injection only), why DI enables testing, bean lifecycle, singleton vs prototype scope, `@Component`, `@Service`, `@Repository`, `@Configuration`, `@Bean`, component scanning, `@Value`, `application.yml`, profiles, Spring Boot starters, auto-configuration, the Actuator `/actuator/health`.
- Implement: `MiniContainer` (60 lines) that instantiates classes via reflection, resolving constructor parameters recursively, and caches singletons. Then generate a Spring Boot 3.3 project `triage-api` via start.spring.io with Web, Validation, Actuator, and run it.
- Exact exercise: move `TicketRepository` + `InMemoryTicketRepository` into Spring as beans; inject into a `TicketService`; `@SpringBootTest` that asserts the context loads.

### DSA — 2.5 hours

- Learn: binary heap array layout, sift-up/sift-down, `PriorityQueue` (min-heap default), max-heap with `Collections.reverseOrder()`, top-k with size-k heap, heapify in $O(n)$.
- Implement: `MinHeap` class with `push`, `pop`, `peek`, `heapify(int[])`.
- Solve:
  1. Kth Largest Element in a Stream
  2. Last Stone Weight
  3. K Closest Points to Origin
  4. Kth Largest Element in an Array (heap version; compare with quickselect)
  5. Sort Characters By Frequency
- Timed task: Top K Frequent Words (20 minutes).
- Revision: re-solve Sliding Window Maximum closed-book.

### Capstone Project — 0.5 hours

- Build: `GET /actuator/health` plus `GET /api/v1/ping` controller returning `{status, time, version}`.
- Deliverable: `curl` output pasted in `docs/08-first-run.md`.

### End-of-Day Check

- [ ] I can explain what Spring does that my `MiniContainer` does not (lifecycle, proxies, scopes, AOP).
- [ ] I can explain why heapify is $O(n)$ and not $O(n \log n)$.

## Day 20 — Spring Boot REST basics; Phase 1 assessment

**Total: 5 hours**

### Java / Backend — 2 hours

- Learn: `@RestController`, `@RequestMapping`, `@GetMapping`, `@PostMapping`, `@PathVariable`, `@RequestParam`, `@RequestBody`, DTOs as records, Bean Validation (`@NotBlank`, `@Size`, `@Valid`), `ResponseEntity`, `@ControllerAdvice` + `@ExceptionHandler`, `ProblemDetail` (RFC 7807), `@WebMvcTest` with `MockMvc`.
- Implement: `TicketController` with `POST /api/v1/tickets` (validated), `GET /api/v1/tickets/{id}` (404 via `TicketNotFoundException` → `ProblemDetail`), backed by the in-memory repository.
- Exact exercise: `MockMvc` tests: 201 on valid create, 400 with field errors on blank subject, 404 on missing id.

### DSA — 2.5 hours

- Phase 1 assessment (timed, 120 minutes, closed-book, speak aloud):
  1. Longest Repeating Character Replacement
  2. Kth Largest Element in an Array
  3. Reorder List
  4. Largest Rectangle in Histogram
  5. Search in Rotated Sorted Array II (unseen)
- Revision (30 minutes): score yourself: each solved within target and correct = 2, correct but slow = 1, else 0. Below 7/10 ⇒ on Day 21 replace the Timed task with re-solving the failures.

### Capstone Project — 0.5 hours

- Build: `docs/09-phase1-review.md`.
- Deliverable: what exists, what is stubbed, the list of Day 21–45 milestones (copied from this plan) and the open risks.

### End-of-Day Check

- [ ] I can explain the request lifecycle from `DispatcherServlet` to controller to `ProblemDetail`.
- [ ] I scored at least 7/10 on the assessment, or I have scheduled the re-solves.
# Phase 2 — DSA Acceleration + Java Proficiency + First Spring Boot (Days 21–45)

> [!note] Phase goal
> Heaps, intervals, greedy, recursion, backtracking, trees, BST, trie, graphs (BFS, DFS, topological sort, DSU, shortest paths, MST), and all core DP families. Spring Data JPA, Flyway, Testcontainers, PostgreSQL and SQL fundamentals. OOP/UML/SOLID as the LLD on-ramp. Triage becomes a real persisted CRUD service with integration tests.

## Day 21 — PostgreSQL and SQL basics; heaps II

**Total: 5 hours**

### Java / Backend — 1.5 hours

- Learn: run PostgreSQL 16 in Docker (`docker run -e POSTGRES_PASSWORD=... -p 5432:5432 postgres:16`), `psql`, `CREATE TABLE`, types (`uuid`, `text`, `timestamptz`, `jsonb`, `numeric`), `PRIMARY KEY`, `FOREIGN KEY`, `NOT NULL`, `CHECK`, `SELECT`, `WHERE`, `ORDER BY`, `LIMIT`, `INSERT`, `UPDATE`, `DELETE`, `RETURNING`.
- Implement: `infra/docker-compose.yml` with `postgres` service and a named volume; `infra/sql/schema-v0.sql` creating `tickets`, `agents`, `teams`.
- Exact SQL problems: `PGX Basic` set — "Retrieve everything from a table", "Retrieve specific columns", "Control which rows are retrieved", "Control which rows are retrieved - part 2", "Basic string searches", "Matching against multiple possible values", "Classify results into buckets", "Working with dates", "Removing duplicates, and ordering results", "Combining results from multiple queries".

### DSA — 3 hours

- Learn: two heaps for running median, k-way merge, scheduling with cooldown, greedy with a heap.
- Solve:
  1. Top K Frequent Elements (heap version)
  2. Task Scheduler
  3. Find Median from Data Stream
  4. Merge k Sorted Lists
  5. Reorganize String
  6. Single-Threaded CPU
- Timed task: Minimum Cost to Hire K Workers (hard, 35 minutes).
- Revision: re-solve K Closest Points to Origin closed-book; re-solve any Day 20 failures.

### Capstone Project — 0.5 hours

- Build: `TicketStatus` and `Priority` as Postgres enums vs text with `CHECK` constraint (choose text + CHECK, record ADR-005 on why).
- Deliverable: schema loads cleanly via `psql -f`.

### End-of-Day Check

- [ ] I can explain the two-heap invariant for the running median.
- [ ] I can write a table with constraints and query it with a `CHECK` violation demonstrated.

## Day 22 — Spring Data JPA and Flyway; intervals

**Total: 5 hours**

### Java / Backend — 2 hours

- Learn: JPA entities, `@Entity`, `@Id`, `@Table`, `@Column`, `@Enumerated(EnumType.STRING)`, `@Version` for optimistic locking, `@CreationTimestamp`, `JpaRepository`, derived queries (`findByStatus`), `@Query` with JPQL, `Pageable`, Hibernate `ddl-auto` set to `validate` only, Flyway migrations (`V1__init.sql`), `spring.datasource.*`, HikariCP defaults (pool size 10), the open-session-in-view trap (disable it).
- Implement: `TicketEntity`, `TicketJpaRepository`, `V1__init.sql`, `V2__agents_teams.sql`; wire `TicketService` to JPA; `spring.jpa.open-in-view=false`.
- Exact exercise: `@DataJpaTest` against Postgres via Testcontainers (`@ServiceConnection`), test `findByStatus` and optimistic lock failure on concurrent update.

### DSA — 2.5 hours

- Learn: sort by start, merge, sweep line, "minimum rooms" with heap or counting events, interval intersections.
- Solve:
  1. Merge Intervals
  2. Insert Interval
  3. Non-overlapping Intervals
  4. Meeting Rooms II (NeetCode)
  5. Interval List Intersections
  6. Minimum Number of Arrows to Burst Balloons
- Timed task: My Calendar I (20 minutes), Employee Free Time (NeetCode, 25 minutes).
- Revision: re-solve Task Scheduler closed-book.

### Capstone Project — 0.5 hours

- Build: replace the in-memory repository with JPA behind the same interface.
- Deliverable: `POST` and `GET` tickets persist across restarts; migration history visible in `flyway_schema_history`.

### End-of-Day Check

- [ ] I can explain why `ddl-auto=update` is forbidden in production and what Flyway guarantees.
- [ ] I can explain the sweep-line proof for Meeting Rooms II.

## Day 23 — Transactions and JPA pitfalls; greedy

**Total: 5 hours**

### Java / Backend — 2 hours

- Learn: `@Transactional` semantics (proxy-based, self-invocation does not work, rollback on unchecked only by default, `readOnly`), propagation (`REQUIRED`, `REQUIRES_NEW`), isolation levels (READ COMMITTED default in Postgres, REPEATABLE READ, SERIALIZABLE), lazy vs eager fetching, `LazyInitializationException`, N+1 problem and `JOIN FETCH` / `@EntityGraph`, DTO projections, `saveAndFlush`, when to use plain JDBC (`JdbcTemplate`/`JdbcClient`).
- Implement: `TicketEvent` entity (`@ManyToOne` lazy to ticket); service method `changeStatus` that appends an event in the same transaction; a deliberate N+1 in a list endpoint, observed via `spring.jpa.show-sql`, then fixed with `@EntityGraph`.
- Exact exercise: test that an exception thrown after the event insert rolls back both ticket and event.

### DSA — 2.5 hours

- Learn: greedy choice property, exchange argument, when greedy fails (coin change with arbitrary coins), proving with a counterexample search.
- Solve:
  1. Jump Game
  2. Jump Game II
  3. Gas Station
  4. Hand of Straights
  5. Partition Labels
  6. Valid Parenthesis String
- Timed task: `CF 1343B Balanced Array` (12 minutes), `CF 1360B Honest Coach` (12 minutes), Boats to Save People (15 minutes).
- Revision: re-solve Merge Intervals and Non-overlapping Intervals closed-book.

### Capstone Project — 0.5 hours

- Build: `PATCH /api/v1/tickets/{id}/status` with transition validation and event append.
- Deliverable: integration test for an invalid transition returning 409 with `ProblemDetail`.

### End-of-Day Check

- [ ] I can explain why a `@Transactional` method called from the same class is not transactional.
- [ ] I can state the exchange argument for Non-overlapping Intervals (keep the earliest end).

## Day 24 — Configuration, profiles, logging; recursion fundamentals

**Total: 5 hours**

### Java / Backend — 1.5 hours

- Learn: `application.yml` per profile (`local`, `test`, `prod`), environment variable overrides, `@ConfigurationProperties` records, secrets never in Git, SLF4J + Logback, log levels, parameterized logging, MDC, JSON logs via `logstash-logback-encoder`, what to log and what never to log (PII, tokens).
- Implement: `TriageProperties` record (`sla`, `routing`, `classifier.timeoutMs`); JSON logging in `prod` profile; a request logging filter that logs method, path, status, duration.
- Exact exercise: run with `SPRING_PROFILES_ACTIVE=prod` and confirm JSON logs; run locally and confirm readable logs.

### DSA — 3 hours

- Learn: recursion tree, base cases, call stack depth, memoization as a first optimization, tail recursion, converting recursion to iteration with an explicit stack.
- Implement: `power(x, n)` in $O(\log n)$, `fibonacci` naive then memo, `towerOfHanoi(n)` printing moves, `printSubsets("abc")`, `permutations("abc")`, `flattenNested(List<Object>)`.
- Solve:
  1. Pow(x, n)
  2. Climbing Stairs (recursion → memo)
  3. Reverse Linked List (recursive)
  4. Merge Two Sorted Lists (recursive)
  5. Fibonacci Number (memo)
  6. Letter Case Permutation
- Timed task: K-th Symbol in Grammar (20 minutes).
- Revision: re-solve Jump Game II closed-book.

### Capstone Project — 0.5 hours

- Build: `docs/10-config-and-secrets.md`.
- Deliverable: table of every config key, default, and source (env var name), plus `.env.example`.

### End-of-Day Check

- [ ] I can draw the recursion tree for naive Fibonacci and explain why memoization makes it $O(n)$.
- [ ] I can explain MDC and why a correlation id matters across logs.

## Day 25 — Backtracking I; cursor pagination

**Total: 5 hours**

### DSA — 3 hours

- Learn: backtracking template (choose, explore, un-choose), pruning, handling duplicates by sorting and skipping, complexity $O(2^n)$ or $O(n!)$ with output size.
- Solve:
  1. Subsets
  2. Subsets II
  3. Permutations
  4. Combinations
  5. Combination Sum
  6. Combination Sum II
- Timed task: Permutations II (20 minutes).
- Revision: re-solve Pow(x, n) and Letter Case Permutation closed-book.

### Java / Backend — 1.5 hours

- Learn: offset pagination cost ($O(\text{offset})$ scan), keyset/cursor pagination on `(created_at, id)`, encoding cursors as base64, stable sorting, filtering by status/assignee, `Specification` or hand-written JPQL/`JdbcClient` for dynamic filters.
- Implement: `GET /api/v1/tickets?status=&assignee=&cursor=&limit=` with keyset pagination and a composite index `(created_at DESC, id DESC)` in `V3__ticket_indexes.sql`.
- Exact exercise: insert 100k tickets with a SQL `generate_series` script; `EXPLAIN ANALYZE` the offset-based query at offset 90000 vs keyset; record both plans in `docs/11-pagination-benchmark.md`.

### Capstone Project — 0.5 hours

- Build: `TicketListResponse` DTO with `items`, `nextCursor`.
- Deliverable: integration test paging through 25 tickets with `limit=10` yielding 10, 10, 5, then `nextCursor=null`.

### End-of-Day Check

- [ ] I can explain the duplicate-skipping condition in Subsets II (`i > start && nums[i] == nums[i-1]`).
- [ ] I can explain why keyset pagination stays $O(\log n + \text{limit})$ regardless of page depth.

## Day 26 — Backtracking II; OOP modeling and UML (LLD start)

**Total: 5 hours**

### DSA — 2.5 hours

- Learn: grid backtracking with visited marking, string partitioning, constraint satisfaction.
- Solve:
  1. Word Search
  2. Palindrome Partitioning
  3. Letter Combinations of a Phone Number
  4. N-Queens
  5. Generate Parentheses
- Timed task: Restore IP Addresses (25 minutes).
- Revision: re-solve Combination Sum II closed-book.

### LLD / HLD — 2 hours

- Learn: UML class diagrams (association, aggregation, composition, dependency, inheritance, multiplicity), sequence diagrams, Mermaid `classDiagram` and `sequenceDiagram`, how to turn requirements into nouns (classes) and verbs (methods), identifying entities vs value objects vs services, interfaces as contracts, coupling and cohesion.
- Design: model a **Library Management System** on paper: `Library`, `Book`, `BookCopy`, `Member`, `Loan`, `Catalog`, `Fine`; write the Mermaid class diagram and a sequence diagram for "member borrows a copy".
- Implement: the classes in `lld/library` in Java with `Catalog.search(title)`, `Library.checkout(member, copy)` enforcing max 5 loans and no checkout of a reserved copy; 6 unit tests.
- Exact interview questions: "Why is `Loan` its own class instead of fields on `BookCopy`?", "Where does the fine calculation live and why?", "What changes if we add e-books?"

### Capstone Project — 0.5 hours

- Build: Mermaid class diagram of Triage's domain as implemented so far in `docs/12-class-diagram.md`.
- Deliverable: diagram plus three identified smells (e.g., `TicketService` doing routing and SLA) to fix on Day 29.

### End-of-Day Check

- [ ] I can draw composition vs aggregation and give a Triage example of each.
- [ ] I can explain the N-Queens diagonal check with `row - col` and `row + col` sets.

## Day 27 — Mixed timed set 3; SQL joins and aggregation

**Total: 5 hours**

### DSA — 3 hours

- Timed set (100 minutes, closed-book):
  1. Subarray Sum Equals K
  2. Insert Interval
  3. Word Search
  4. Find Median from Data Stream
  5. Minimum Remove to Make Valid Parentheses (unseen)
- Revision (80 minutes): review failures; re-solve Permutations, Largest Rectangle in Histogram, Merge k Sorted Lists; extend `patterns.md` with greedy, backtracking, and heap triggers.

### Java / Backend — 1.5 hours

- Learn: `INNER`, `LEFT`, `RIGHT`, `FULL` joins, self joins, `GROUP BY`, `HAVING`, `COUNT(DISTINCT)`, `COALESCE`, `CASE`, subqueries in `WHERE`/`FROM`/`SELECT`, `EXISTS` vs `IN`, `UNION` vs `UNION ALL`.
- Exact SQL problems: Combine Two Tables, Employees Earning More Than Their Managers, Duplicate Emails, Customers Who Never Order, Department Highest Salary, Rising Temperature, `PGX Joins and Subqueries` — "Retrieve the start times of members' bookings", "Produce a list of all members who have recommended another member", "Produce a list of all members, along with their recommender", "Produce a list of all members, along with their recommender, using no joins", "Produce a list of costly bookings".

### Capstone Project — 0.5 hours

- Build: `GET /api/v1/teams/{id}/stats` returning open tickets per status and per priority via a single `GROUP BY` query with `JdbcClient`.
- Deliverable: test on seeded data.

### End-of-Day Check

- [ ] I can explain the difference between `WHERE` and `HAVING` and when `EXISTS` beats `IN`.
- [ ] I scored at least 7/10 on the timed set or have scheduled re-solves for Day 29.

## Day 28 — Trees I; SOLID principles

**Total: 5 hours**

### DSA — 2.5 hours

- Learn: `TreeNode`, recursive structure, preorder/inorder/postorder recursive and iterative, height vs depth, subtree problems return values up.
- Implement: all three traversals iteratively; level order with a queue.
- Solve:
  1. Maximum Depth of Binary Tree
  2. Invert Binary Tree
  3. Same Tree
  4. Subtree of Another Tree
  5. Diameter of Binary Tree
  6. Balanced Binary Tree
- Timed task: Symmetric Tree (12 minutes), Merge Two Binary Trees (10 minutes).
- Revision: re-solve N-Queens closed-book.

### LLD / HLD — 2 hours

- Learn: SOLID with code: Single Responsibility (reasons to change), Open/Closed (extend via new classes), Liskov (contracts, `Square extends Rectangle` violation), Interface Segregation (fat interfaces), Dependency Inversion (depend on abstractions, injection), plus DRY, KISS, YAGNI, Law of Demeter, Tell-Don't-Ask.
- Design: refactor a deliberately bad `OrderProcessor` (given: one class that validates, calculates tax by `if country ==`, saves to DB, sends email) into SRP-compliant classes with a `TaxStrategy` interface and injected `OrderRepository`, `Notifier`.
- Implement: before and after in `lld/solid`; tests for the after version with fakes.
- Exact interview questions: "Give an LSP violation you have seen", "How does DIP differ from DI?", "When is OCP over-engineering?"

### Capstone Project — 0.5 hours

- Build: `V4__sla_policies.sql` and `SlaPolicyEntity`; seed default policies per priority.
- Deliverable: `GET /api/v1/sla-policies`.

### End-of-Day Check

- [ ] I can explain each SOLID principle with a one-line Triage example.
- [ ] I can write iterative inorder traversal from memory.

## Day 29 — Trees II; Triage service layer refactor

**Total: 5 hours**

### DSA — 2.5 hours

- Learn: BFS by levels, right view, path sums with backtracking, LCA, global-max pattern (return partial up, update global).
- Solve:
  1. Binary Tree Level Order Traversal
  2. Binary Tree Right Side View
  3. Count Good Nodes in Binary Tree
  4. Lowest Common Ancestor of a Binary Tree
  5. Path Sum II
  6. Binary Tree Maximum Path Sum
- Timed task: Binary Tree Zigzag Level Order Traversal (18 minutes).
- Revision: re-solve Diameter of Binary Tree closed-book; re-solve Day 27 failures.

### Java / Backend — 2 hours

- Learn: hexagonal/ports-and-adapters layering in Spring (controller → application service → domain → repository port → JPA adapter), mapping entities to domain objects (MapStruct or hand-written mappers), package-by-feature, `ArchUnit` to enforce that `domain` does not import Spring.
- Implement: split `TicketService` into `CreateTicketUseCase`, `ChangeTicketStatusUseCase`, `AssignTicketUseCase`, `TicketQueryService`; `TicketRepository` port in `domain`, `JpaTicketRepository` adapter; an `ArchUnit` test.
- Exact exercise: all previous tests still pass after the refactor; `ArchUnit` fails if you add a Spring import to `domain` (prove it, then revert).

### Capstone Project — 0.5 hours

- Build: `docs/adr/ADR-006-hexagonal-layering.md`.
- Deliverable: package diagram and the rule set enforced by ArchUnit.

### End-of-Day Check

- [ ] I can explain why Binary Tree Maximum Path Sum returns one branch upward but records two downward.
- [ ] I can explain what a port and an adapter are, with Triage examples.

## Day 30 — BST and tree construction; Day-30 milestone review

**Total: 5 hours**

### DSA — 3 hours

- Learn: BST invariant with bounds, inorder yields sorted order, insert/delete with successor, serialization with null markers, reconstruction from preorder + inorder.
- Solve:
  1. Validate Binary Search Tree
  2. Kth Smallest Element in a BST
  3. Lowest Common Ancestor of a Binary Search Tree
  4. Insert into a Binary Search Tree
  5. Delete Node in a BST
  6. Construct Binary Tree from Preorder and Inorder Traversal
  7. Serialize and Deserialize Binary Tree
- Timed task: Convert Sorted Array to Binary Search Tree (12 minutes), Binary Search Tree Iterator (20 minutes).
- Revision: re-solve Lowest Common Ancestor of a Binary Tree closed-book.

### Java / Backend — 1 hour

- Learn: `TreeMap`/`TreeSet` as a red-black tree in the JDK, $O(\log n)$ operations, `floorEntry`, `ceilingEntry`, `subMap`; when to prefer over a hash map.
- Implement: `SlaDeadlineIndex` using `TreeMap<Instant, Set<TicketId>>` with `dueBefore(Instant)` in $O(\log n + k)$.
- Exact exercise: tests for insertion, removal on status change, and range query.

### Capstone Project — 1 hour

- Build: Day-30 milestone: `docs/13-milestone-30.md`.
- Deliverable: current endpoints table, test count, coverage %, migration list, open issues; tag `v0.1.0` in Git.

### End-of-Day Check

- [ ] I can explain why validating a BST by checking only parent/child is wrong and bounds are needed.
- [ ] Triage has persisted tickets, status transitions, keyset pagination, SLA policies, and Testcontainers tests.
## Day 31 — Trie; Spring Security foundations

**Total: 5 hours**

### DSA — 2.5 hours

- Learn: trie node with `children[26]` or `HashMap`, insert/search/startsWith, wildcard search with DFS, trie + backtracking on a grid, memory tradeoffs.
- Implement: `Trie` with `insert`, `search`, `startsWith`, `delete`.
- Solve:
  1. Implement Trie (Prefix Tree)
  2. Design Add and Search Words Data Structure
  3. Word Search II
  4. Replace Words
  5. Longest Word in Dictionary
- Timed task: Maximum XOR of Two Numbers in an Array (binary trie, 30 minutes).
- Revision: re-solve Serialize and Deserialize Binary Tree closed-book.

### Java / Backend — 2 hours

- Learn: authentication vs authorization, password hashing (BCrypt, cost factor), Spring Security 6 filter chain, `SecurityFilterChain` bean, stateless sessions, `UserDetailsService`, `PasswordEncoder`, CSRF (disable for stateless APIs and why), CORS configuration, method security `@PreAuthorize`.
- Implement: `users` table (`V5__users.sql`) with `email`, `password_hash`, `role` (AGENT, LEAD, ADMIN); `POST /api/v1/auth/register`; BCrypt encoding; security config permitting `/auth/**` and `/actuator/health`, requiring auth elsewhere.
- Exact exercise: test that `GET /api/v1/tickets` returns 401 without credentials.

### Capstone Project — 0.5 hours

- Build: `User` domain and `UserRepository` port.
- Deliverable: registration integration test with duplicate-email conflict (409).

### End-of-Day Check

- [ ] I can explain Word Search II's pruning (remove words from the trie after found).
- [ ] I can describe the order of Spring Security filters relevant to a stateless JWT API.

## Day 32 — Graphs I: representations, BFS, DFS; JWT

**Total: 5 hours**

### DSA — 2.5 hours

- Learn: adjacency list vs matrix, directed vs undirected, weighted, grid as implicit graph, BFS (queue, visited on enqueue), DFS (recursive and iterative), connected components, complexity $O(V + E)$.
- Implement: `Graph` class with `addEdge`, `bfs(start)`, `dfs(start)`, `components()`.
- Solve:
  1. Number of Islands
  2. Clone Graph
  3. Max Area of Island
  4. Flood Fill
  5. Number of Provinces
  6. Find if Path Exists in Graph
- Timed task: Island Perimeter (12 minutes), `CF 1365D Solve The Maze` (BFS, 30 minutes).
- Revision: re-solve Implement Trie closed-book.

### Java / Backend — 2 hours

- Learn: JWT structure (header, payload, signature), HS256 vs RS256, claims (`sub`, `exp`, `iat`, `roles`), access vs refresh tokens, token expiry and rotation, where to store tokens on clients, why JWTs cannot be revoked without a denylist, Spring's `OncePerRequestFilter` for bearer tokens, `jjwt` or Nimbus library.
- Implement: `POST /api/v1/auth/login` issuing a 15-minute access token and a 7-day refresh token (stored hashed in `refresh_tokens` table); `JwtAuthenticationFilter`; `POST /auth/refresh`; `POST /auth/logout` revoking refresh token.
- Exact exercise: tests: login → access token works; expired token → 401; refresh rotates and old refresh token fails.

### Capstone Project — 0.5 hours

- Build: `docs/adr/ADR-007-auth-strategy.md`.
- Deliverable: why JWT access + rotating refresh vs server sessions; threat list (token theft, replay, XSS).

### End-of-Day Check

- [ ] I can explain why BFS marks visited on enqueue rather than dequeue.
- [ ] I can explain what a JWT signature proves and what it does not (confidentiality).

## Day 33 — Graphs II: multi-source BFS, grid problems; authorization

**Total: 5 hours**

### DSA — 2.5 hours

- Learn: multi-source BFS, BFS on state space, reverse-thinking from boundaries, word transformation graphs.
- Solve:
  1. Rotting Oranges
  2. 01 Matrix
  3. Pacific Atlantic Water Flow
  4. Surrounded Regions
  5. Shortest Path in Binary Matrix
  6. Word Ladder
- Timed task: Open the Lock (25 minutes).
- Revision: re-solve Number of Islands closed-book; re-solve Clone Graph.

### Java / Backend — 2 hours

- Learn: RBAC vs ABAC, roles vs permissions, `@PreAuthorize("hasRole('LEAD')")`, ownership checks in the service layer (an agent can only modify assigned tickets), `AuthenticationPrincipal`, security context propagation to async threads, audit trail of who changed what.
- Implement: roles enforced: AGENT reads/updates own tickets, LEAD assigns and reads all, ADMIN manages users and SLA policies; `actor_id` on every `ticket_events` row.
- Exact exercise: `@WithMockUser(roles = "AGENT")` tests proving a 403 on assignment and a 200 for LEAD.

### Capstone Project — 0.5 hours

- Build: `POST /api/v1/tickets/{id}/assign` with LEAD-only guard and event append.
- Deliverable: integration tests for 403 and success paths.

### End-of-Day Check

- [ ] I can explain why Pacific Atlantic is solved by DFS from the oceans inward.
- [ ] I can explain where authorization checks belong (service layer) and why not only at the controller.

## Day 34 — Topological sort; Testcontainers integration test suite

**Total: 5 hours**

### DSA — 2.5 hours

- Learn: DAGs, Kahn's algorithm (indegree queue), DFS post-order topological sort, cycle detection in directed graphs (colors), ordering constraints.
- Implement: `topoSortKahn(int n, int[][] edges)` and DFS variant; cycle detection.
- Solve:
  1. Course Schedule
  2. Course Schedule II
  3. Minimum Height Trees
  4. Alien Dictionary (NeetCode)
  5. Find All Possible Recipes from Given Supplies
  6. Longest Increasing Path in a Matrix
- Timed task: Parallel Courses (NeetCode) or free alternative Parallel Courses III (25 minutes).
- Revision: re-solve Rotting Oranges and Word Ladder closed-book.

### Java / Backend — 2 hours

- Learn: Testcontainers lifecycle, singleton container pattern for speed, `@ServiceConnection`, test data builders, `@Sql` scripts, `@Transactional` rollback in tests and its pitfalls, `TestRestTemplate` vs `WebTestClient`, contract of each test layer (unit, slice, integration, end-to-end).
- Implement: `AbstractIntegrationTest` with a shared Postgres container; convert all controller tests to real-HTTP tests through `WebTestClient`; a `TicketTestBuilder`.
- Exact exercise: whole suite under 60 seconds locally; record the time in `docs/14-test-strategy.md` with the layer table.

### Capstone Project — 0.5 hours

- Build: `docs/14-test-strategy.md`.
- Deliverable: which behaviors are tested at which layer, with counts.

### End-of-Day Check

- [ ] I can implement Kahn's algorithm in 5 minutes and explain how it detects cycles.
- [ ] I can explain why `@Transactional` on integration tests can hide bugs (lazy loading, commit-time constraints).

## Day 35 — Union-Find; Redis fundamentals

**Total: 5 hours**

### DSA — 2.5 hours

- Learn: DSU with path compression and union by rank/size, near-constant amortized $O(\alpha(n))$, applications: cycle detection in undirected graphs, components, Kruskal, equivalence classes.
- Implement: `DSU` class with `find`, `union`, `componentCount`, `size(x)`.
- Solve:
  1. Redundant Connection
  2. Number of Provinces (DSU version)
  3. Graph Valid Tree (NeetCode)
  4. Accounts Merge
  5. Satisfiability of Equality Equations
  6. Number of Operations to Make Network Connected
- Timed task: `AC ABC 177 D - Friends` (20 minutes), Most Stones Removed with Same Row or Column (25 minutes).
- Revision: re-solve Course Schedule II closed-book.

### Java / Backend — 2 hours

- Learn: Redis data types (string, hash, list, set, sorted set), TTL, `SETNX` and atomicity, Lua scripts, persistence (RDB vs AOF), eviction policies (`allkeys-lru`), single-threaded command execution, pipelining, Redis as cache vs as a data store, Spring `StringRedisTemplate`, Lettuce client, `redis-cli`.
- Implement: Redis in `docker-compose.yml`; `RedisHealthIndicator`; a small `RedisDrill` using sorted sets to rank agents by resolved tickets and a hash for a ticket summary with TTL.
- Exact exercise: use `redis-cli MONITOR` while running your drill; note each command.

### Capstone Project — 0.5 hours

- Build: `docs/adr/ADR-008-redis-usage.md`.
- Deliverable: list of Redis uses in Triage (read cache, rate limiting, distributed lock for SLA scanner, idempotency keys) and what happens if Redis is down for each (degrade, not fail).

### End-of-Day Check

- [ ] I can explain union by rank and path compression and why both are needed for $O(\alpha(n))$.
- [ ] I can explain why `SET key value NX PX 30000` is the basis for a lock.

## Day 36 — Shortest paths; cache-aside in Triage

**Total: 5 hours**

### DSA — 2.5 hours

- Learn: Dijkstra with a priority queue (lazy deletion), why it fails with negative edges, Bellman-Ford and its $k$-edge variant, 0-1 BFS, shortest path in DAG via topological order, Floyd-Warshall in one paragraph.
- Implement: `dijkstra(List<List<int[]>> adj, int src)` returning distances; `bellmanFord`.
- Solve:
  1. Network Delay Time
  2. Cheapest Flights Within K Stops
  3. Path with Minimum Effort
  4. Swim in Rising Water
  5. Path with Maximum Probability
- Timed task: Minimum Cost to Make at Least One Valid Path in a Grid (0-1 BFS, 30 minutes).
- Revision: re-solve Accounts Merge closed-book.

### Java / Backend — 2 hours

- Learn: cache-aside, read-through, write-through, write-behind; TTL selection; cache stampede and single-flight; invalidation on write; cache keys and versioning; Spring Cache abstraction (`@Cacheable`, `@CacheEvict`) vs explicit `RedisTemplate`; serialization with Jackson; measuring hit rate with Micrometer.
- Implement: cache-aside for `GET /tickets/{id}` with a 5-minute TTL, eviction on status change/assignment, a `cache.hit`/`cache.miss` counter; `@Cacheable` for `GET /sla-policies`.
- Exact exercise: integration test asserting the second read does not hit Postgres (count queries via a Hibernate statistics or a `DataSource` proxy).

### Capstone Project — 0.5 hours

- Build: hit-rate gauge exposed at `/actuator/metrics/cache.gets`.
- Deliverable: screenshot or output pasted in `docs/15-cache.md` with a 100-read loop showing ≥ 95% hits.

### End-of-Day Check

- [ ] I can explain why Dijkstra's greedy choice is correct only with non-negative weights.
- [ ] I can explain cache stampede and two mitigations.

## Day 37 — MST and Eulerian path; Kafka fundamentals

**Total: 5 hours**

### DSA — 2.5 hours

- Learn: Kruskal (sort edges + DSU), Prim (heap), when MST appears in interviews, Hierholzer's algorithm for Eulerian path, bus-route BFS over routes.
- Implement: `kruskal(int n, int[][] edges)`, `prim`.
- Solve:
  1. Min Cost to Connect All Points
  2. Connecting Cities With Minimum Cost (NeetCode) — or free alternative Optimize Water Distribution in a Village (NeetCode)
  3. Reconstruct Itinerary
  4. Bus Routes
  5. Find Critical and Pseudo-Critical Edges in Minimum Spanning Tree
- Timed task: `CF 1363C Game On Leaves` (20 minutes).
- Revision: re-solve Network Delay Time closed-book.

### Java / Backend — 2 hours

- Learn: Kafka topics, partitions, offsets, producers (acks, idempotent producer, keys determine partition), consumer groups, rebalancing, at-most-once vs at-least-once vs effectively-once, retention, compaction, dead-letter topics, why ordering is per-partition, KRaft mode, `kafka-console-consumer`.
- Implement: Kafka (single-broker KRaft) in `docker-compose.yml`; Spring `KafkaTemplate` producer publishing `ticket.created` events keyed by `ticketId` with a versioned JSON schema; a console consumer verifying.
- Exact exercise: produce 10,000 events in a loop; observe partition distribution with `kafka-get-offsets`.

### Capstone Project — 0.5 hours

- Build: `TicketCreatedEvent` record with `eventId`, `occurredAt`, `schemaVersion`.
- Deliverable: `docs/16-events.md` listing every event, key, and partition count (6).

### End-of-Day Check

- [ ] I can explain why Kafka guarantees ordering only within a partition and how keys exploit that.
- [ ] I can explain Kruskal's correctness via the cut property.

## Day 38 — Mixed timed set 4 (graphs); transactional outbox

**Total: 5 hours**

### DSA — 3 hours

- Timed set (100 minutes, closed-book):
  1. Course Schedule
  2. Pacific Atlantic Water Flow
  3. Cheapest Flights Within K Stops
  4. Redundant Connection
  5. Evaluate Division (unseen)
- Revision (80 minutes): re-solve failures; re-solve Word Ladder, Swim in Rising Water, Min Cost to Connect All Points; add graph triggers to `patterns.md` (grid ⇒ BFS/DFS, prerequisites ⇒ topo, "connected"/"merge" ⇒ DSU, weighted min ⇒ Dijkstra, k stops ⇒ Bellman-Ford).

### Java / Backend — 1.5 hours

- Learn: dual-write problem, transactional outbox pattern, outbox relay (poller) vs CDC (Debezium) in one paragraph, idempotent consumers with a processed-events table, exactly-once as effectively-once.
- Implement: `outbox` table (`V6__outbox.sql`); `CreateTicketUseCase` writes ticket + outbox row in one transaction; `OutboxRelay` scheduled every 500 ms publishing unsent rows to Kafka and marking them sent; `processed_events` table for consumers.
- Exact exercise: kill Kafka, create tickets, restart Kafka, verify all events eventually arrive with no duplicates in the consumer.

### Capstone Project — 0.5 hours

- Build: `docs/adr/ADR-009-outbox.md`.
- Deliverable: failure matrix: DB commit fails, Kafka down, relay crashes mid-batch, consumer crashes after side effect.

### End-of-Day Check

- [ ] I can explain the dual-write problem and how the outbox solves it.
- [ ] I scored at least 7/10 on the graph timed set.

## Day 39 — DP I: 1D; Kafka consumer in triage-worker

**Total: 5 hours**

### DSA — 2.5 hours

- Learn: DP definition (overlapping subproblems + optimal substructure), state, transition, base case, order, top-down vs bottom-up, space optimization, counting vs optimization DP.
- Solve:
  1. Climbing Stairs
  2. Min Cost Climbing Stairs
  3. House Robber
  4. House Robber II
  5. Decode Ways
  6. Coin Change
- Timed task: `AC Educational DP Contest A - Frog 1` and `B - Frog 2` (25 minutes total).
- Revision: re-solve Evaluate Division (yesterday's unseen) closed-book.

### Java / Backend — 2 hours

- Learn: Spring Kafka `@KafkaListener`, consumer group config, manual ack mode, error handling (`DefaultErrorHandler` with backoff), dead-letter topic via `DeadLetterPublishingRecoverer`, concurrency per partition, poison-pill handling, consumer lag.
- Implement: new module `triage-worker` consuming `ticket.created`, inserting into `processed_events` idempotently, and calling a stub classifier that sets `category = PENDING`; DLT after 3 retries.
- Exact exercise: publish a malformed event; verify it lands in `ticket.created.DLT` and the consumer keeps processing.

### Capstone Project — 0.5 hours

- Build: `docker-compose.yml` now runs `postgres`, `redis`, `kafka`, `triage-api`, `triage-worker`.
- Deliverable: `docker compose up` ends with both services healthy.

### End-of-Day Check

- [ ] I can define a DP state in one sentence for each of today's problems.
- [ ] I can explain at-least-once delivery plus idempotent consumer = effectively-once.

## Day 40 — DP II: 1D with subsequences; background jobs and SLA scanner

**Total: 5 hours**

### DSA — 2.5 hours

- Learn: DP over prefixes with inner loop over previous indices $O(n^2)$, LIS with patience sorting $O(n \log n)$, subset-sum as boolean DP, word segmentation.
- Solve:
  1. Maximum Product Subarray
  2. Word Break
  3. Longest Increasing Subsequence
  4. Partition Equal Subset Sum
  5. Perfect Squares
  6. Number of Longest Increasing Subsequence
- Timed task: `AC Educational DP Contest C - Vacation` (15 minutes), Russian Doll Envelopes (hard, 30 minutes).
- Revision: re-solve Coin Change and House Robber II closed-book.

### Java / Backend — 2 hours

- Learn: `@Scheduled` with `fixedDelay`, scheduling in multi-instance deployments (needs a distributed lock), ShedLock or a Redis `SET NX` lock, batch processing with pagination, `SKIP LOCKED` for job queues in Postgres, job idempotency, backoff.
- Implement: `SlaScanner` in `triage-worker` running every 60 s under a Redis lock, selecting tickets whose `first_response_due_at < now()` and `status in (NEW, TRIAGED)`, emitting `sla.breached` to Kafka and setting `sla_breached = true`.
- Exact exercise: run two worker instances; prove only one scans per tick (log the lock holder).

### Capstone Project — 0.5 hours

- Build: `V7__sla_columns.sql` (`first_response_due_at`, `resolution_due_at`, `sla_breached`), computed at creation from the policy.
- Deliverable: integration test: create P1 ticket with a 1-minute policy, advance the clock via injected `Clock`, run the scanner, assert breach.

### End-of-Day Check

- [ ] I can explain LIS in $O(n \log n)$ and why the `tails` array is not the actual subsequence.
- [ ] I can explain why `@Scheduled` without a lock is a bug in a 2-replica deployment.
## Day 41 — DP III: knapsack family; notifications and the outbox relay

**Total: 5 hours**

### DSA — 2.5 hours

- Learn: 0/1 knapsack (iterate capacity downward), unbounded knapsack (upward), counting ways vs min/max, 2-dimensional capacity.
- Solve:
  1. 0 - 1 Knapsack Problem (GfG)
  2. Target Sum
  3. Coin Change II
  4. Last Stone Weight II
  5. Ones and Zeroes
  6. Combination Sum IV (order matters: permutations vs combinations)
- Timed task: `AC Educational DP Contest D - Knapsack 1` and `E - Knapsack 2` (35 minutes).
- Revision: re-solve Partition Equal Subset Sum closed-book.

### Java / Backend — 2 hours

- Learn: notification channels, templating, retry with exponential backoff and jitter, dedupe keys, user preferences, fan-out from events, why notifications are consumers not producers of truth.
- Implement: `NotificationConsumer` on `ticket.assigned` and `sla.breached` that writes `notifications` rows and "sends" via a `NotificationSender` interface with `LogSender` (console) and `WebhookSender` (HTTP POST to a configurable URL with 3 retries).
- Exact exercise: run a local `httpbin` container as the webhook target; verify delivery and a retry on a 500.

### Capstone Project — 0.5 hours

- Build: `V8__notifications.sql`, `GET /api/v1/notifications?unread=true`.
- Deliverable: assignment produces a notification row within 2 s in an integration test polling with Awaitility.

### End-of-Day Check

- [ ] I can explain why 0/1 knapsack iterates capacity downward and unbounded upward.
- [ ] I can explain jitter and why synchronized retries cause thundering herds.

## Day 42 — DP IV: 2D grid and strings; SQL window functions

**Total: 5 hours**

### DSA — 2.5 hours

- Learn: grid DP with obstacles, two-string DP tables (LCS, edit distance), reading the table to reconstruct the answer, space optimization to two rows.
- Solve:
  1. Unique Paths
  2. Unique Paths II
  3. Minimum Path Sum
  4. Longest Common Subsequence
  5. Edit Distance
  6. Maximal Square
- Timed task: `AC Educational DP Contest F - LCS` (reconstruct, 20 minutes), `H - Grid 1` (10 minutes).
- Revision: re-solve Coin Change II closed-book.

### Java / Backend — 2 hours

- Learn: window functions `ROW_NUMBER`, `RANK`, `DENSE_RANK`, `LAG`, `LEAD`, `SUM() OVER (PARTITION BY ... ORDER BY ...)`, running totals, moving averages with frames, top-N per group, `FILTER` clause, `DISTINCT ON`.
- Exact SQL problems: Rank Scores, Department Top Three Salaries, Consecutive Numbers, Nth Highest Salary, Exchange Seats, Restaurant Growth, Product Sales Analysis III, `PGX Aggregates` — "Produce a list of the total number of slots booked per facility per month, part 2", "Output the facility id that has the highest number of slots booked", "Produce a list of member names, with each row containing the total member count", "Output the position of each member in the list of members ordered by join date", "Output the facility id that has the highest number of slots booked, again".
- Implement: Triage report query: per team per day, tickets created, resolved, and a 7-day moving average of resolution time, exposed as `GET /api/v1/reports/resolution-trend`.

### Capstone Project — 0.5 hours

- Build: `V9__ticket_resolved_at.sql` and the report endpoint.
- Deliverable: test on 30 days of seeded data.

### End-of-Day Check

- [ ] I can explain the difference between `RANK` and `DENSE_RANK` and write a top-3-per-group query.
- [ ] I can reconstruct the LCS from the DP table.

## Day 43 — DP V: subsequences and palindromes; indexes and EXPLAIN

**Total: 5 hours**

### DSA — 2.5 hours

- Learn: palindromic DP on intervals of a string, counting distinct subsequences, interleaving as 2D reachability, regex DP with star transitions.
- Solve:
  1. Longest Palindromic Subsequence
  2. Palindromic Substrings
  3. Distinct Subsequences
  4. Interleaving String
  5. Regular Expression Matching
- Timed task: Wildcard Matching (hard, 30 minutes).
- Revision: re-solve Edit Distance closed-book.

### Java / Backend — 2 hours

- Learn: B-tree indexes, composite index column order, covering indexes with `INCLUDE`, partial indexes, `GIN` for `jsonb` and full-text, `EXPLAIN (ANALYZE, BUFFERS)`, seq scan vs index scan vs bitmap scan, selectivity, `pg_stat_user_indexes`, when indexes hurt writes, `VACUUM` and bloat in one paragraph.
- Implement: add `(status, priority, created_at)` index and a partial index `WHERE status <> 'CLOSED'`; `pg_trgm` index on `subject` for `ILIKE` search; measure before/after with `EXPLAIN ANALYZE` on 100k rows.
- Exact exercise: `docs/17-index-benchmark.md` with plans and timings for 4 queries.

### Capstone Project — 0.5 hours

- Build: `GET /api/v1/tickets?q=` using `ILIKE` + trigram index as the first search (replaced by semantic search on Day 107).
- Deliverable: p95 under 20 ms on 100k rows measured with a 200-request loop.

### End-of-Day Check

- [ ] I can read an `EXPLAIN ANALYZE` plan and spot the slow node.
- [ ] I can explain the `*` transition in Regular Expression Matching (`dp[i][j-2]` or match-and-stay).

## Day 44 — DP VI: interval and stock DP; Docker

**Total: 5 hours**

### DSA — 2.5 hours

- Learn: interval DP (choose the last operation), state machines for stock problems (holding/not holding/cooldown), $k$-transaction DP.
- Solve:
  1. Burst Balloons
  2. Minimum Cost to Cut a Stick
  3. Best Time to Buy and Sell Stock with Cooldown
  4. Best Time to Buy and Sell Stock IV
  5. Stone Game
- Timed task: `AC Educational DP Contest N - Slimes` (30 minutes).
- Revision: re-solve Longest Palindromic Subsequence closed-book.

### Java / Backend — 2 hours

- Learn: Docker images vs containers, layers and cache, multi-stage builds, distroless/Temurin JRE base, `.dockerignore`, `EXPOSE`, health checks, env vars, volumes, networks, Compose `depends_on` with `condition: service_healthy`, image size, JVM container awareness (`-XX:MaxRAMPercentage`), non-root user.
- Implement: multi-stage `Dockerfile` for `triage-api` and `triage-worker` (Maven build stage → JRE 21 runtime, non-root, health check on `/actuator/health`); Compose builds both.
- Exact exercise: get the API image under 250 MB; document the layer sizes with `docker history`.

### Capstone Project — 0.5 hours

- Build: `infra/docker-compose.yml` final local topology with health-gated startup order.
- Deliverable: `docker compose up --build` from clean clone works in under 3 minutes.

### End-of-Day Check

- [ ] I can explain why interval DP chooses the "last" balloon rather than the first.
- [ ] I can explain Docker layer caching and why dependencies are copied before source.

## Day 45 — DP VII: bitmask DP; Phase 2 assessment

**Total: 5 hours**

### DSA — 3.5 hours

- Learn: bitmask representation of subsets, iterating submasks, DP over subsets $O(2^n \cdot n)$, Hamiltonian-style problems.
- Solve:
  1. Partition to K Equal Sum Subsets
  2. Shortest Path Visiting All Nodes
  3. Minimum Number of Work Sessions to Finish the Tasks
  4. `AC Educational DP Contest O - Matching`
- Phase 2 assessment (timed, 90 minutes, closed-book):
  1. Validate Binary Search Tree
  2. Word Break
  3. Rotting Oranges
  4. Longest Increasing Subsequence
  5. Maximum Profit in Job Scheduling (unseen, hard)
- Revision: score as on Day 20; failures go to Day 47's revision slot.

### Java / Backend — 1 hour

- Learn: GitHub Actions basics: workflow triggers, jobs, steps, caching Maven, service containers, secrets, status checks required on `main`.
- Implement: `.github/workflows/ci.yml` running `mvn -B verify` for both modules with Testcontainers (Docker available on `ubuntu-latest`), uploading the JaCoCo report as an artifact.
- Exact exercise: open a PR that breaks a test, watch CI fail, fix, merge.

### Capstone Project — 0.5 hours

- Build: branch protection on `main` requiring CI.
- Deliverable: `docs/18-milestone-45.md` and tag `v0.2.0`.

### End-of-Day Check

- [ ] I can explain the submask enumeration loop `for (s = m; s > 0; s = (s - 1) & m)`.
- [ ] CI is green on `main` and blocks merges on failure.

# Phase 3 — Production Backend + SQL + LLD Foundations (Days 46–70)

> [!note] Phase goal
> Observability, resilience, API hardening, advanced SQL and DB internals, OS and networking fundamentals, and all core design patterns implemented in Java. DSA moves to 1.5–2 hours per day of mixed and harder problems with scheduled re-solves. Triage reaches a deployable, observable, load-tested v0.5.

## Day 46 — Bit manipulation; observability I (metrics)

**Total: 5 hours**

### DSA — 2 hours

- Learn: AND/OR/XOR/NOT, shifts, `n & (n-1)` clears lowest set bit, `n & -n` isolates it, XOR cancellation, bit counting, two's complement, `Integer.bitCount`, `Long.numberOfTrailingZeros`.
- Solve:
  1. Single Number
  2. Number of 1 Bits
  3. Counting Bits
  4. Reverse Bits
  5. Missing Number
  6. Sum of Two Integers
  7. Single Number III
- Timed task: `CF 282A Bit++` (5 minutes), Bitwise AND of Numbers Range (20 minutes).
- Revision: re-solve Burst Balloons closed-book.

### Java / Backend — 2.5 hours

- Learn: the three pillars (metrics, logs, traces), Micrometer meters (counter, gauge, timer, distribution summary), Prometheus exposition, histograms vs summaries, percentiles, RED (rate, errors, duration) and USE methods, `@Timed`, JVM and HikariCP metrics out of the box, Grafana dashboards, alert rules.
- Implement: Prometheus + Grafana in Compose scraping both services; custom timers `ticket.create.duration`, counters `ticket.created.total` tagged by channel, gauge `kafka.consumer.lag`; a Grafana dashboard JSON committed to `infra/grafana/triage.json` with p95 latency, RPS, error rate, consumer lag, cache hit rate.
- Exact exercise: generate load with a 1,000-request script; screenshot the dashboard into `docs/19-observability.md`.

### Capstone Project — 0.5 hours

- Build: alert rule: p95 of `POST /tickets` > 300 ms for 5 minutes.
- Deliverable: rule file loaded by Prometheus and visible in its UI.

### End-of-Day Check

- [ ] I can explain why `a ^ b ^ b == a` solves Single Number and how Single Number III splits by a differing bit.
- [ ] I can explain the RED method and why histograms are needed for p95 across instances.

## Day 47 — Math for interviews; observability II (tracing and logs)

**Total: 5 hours**

### DSA — 2 hours

- Learn: modular arithmetic, fast exponentiation, primes with a sieve, gcd/lcm, combinatorics basics ($nCr$ with Pascal or modular inverse), overflow checks, matrix simulation.
- Solve:
  1. Count Primes
  2. Happy Number
  3. Plus One
  4. Multiply Strings
  5. Rotate Image
  6. Spiral Matrix
  7. Set Matrix Zeroes
- Timed task: `CF 1352C K-th Not Divisible by n` (15 minutes), `CF 1511B GCD Length` (20 minutes).
- Revision: re-solve Day 45 assessment failures.

### Java / Backend — 2.5 hours

- Learn: distributed tracing concepts (trace, span, context propagation, W3C `traceparent`), OpenTelemetry SDK and Java agent, Micrometer Tracing bridge, exporting to Tempo, correlating logs with `traceId` in MDC, Loki for logs in one paragraph, sampling.
- Implement: OpenTelemetry Collector + Tempo in Compose; auto-instrumentation via the OTel Java agent for both services; Kafka header propagation so a trace spans API → outbox → worker; `traceId` in every JSON log line.
- Exact exercise: create a ticket, open the trace in Grafana, see spans for HTTP, JDBC, Kafka produce, Kafka consume, and the classifier stub.

### Capstone Project — 0.5 hours

- Build: `docs/19-observability.md` section "How to debug a slow request".
- Deliverable: a written walk-through using one real trace id.

### End-of-Day Check

- [ ] I can explain the sieve's $O(n \log \log n)$ and modular exponentiation.
- [ ] I can explain how trace context crosses a Kafka boundary.

## Day 48 — Advanced data structures I (Fenwick, segment tree); OS I (processes, threads, scheduling)

**Total: 5 hours**

### DSA — 2.5 hours

- Learn: Fenwick tree (BIT) for prefix sums with point updates in $O(\log n)$, segment tree with point update and range query, lazy propagation in one paragraph, coordinate compression.
- Implement: `Fenwick` (`add`, `prefixSum`, `rangeSum`), `SegmentTree` (range sum and range min).
- Solve:
  1. Range Sum Query - Mutable
  2. Count of Smaller Numbers After Self
  3. Reverse Pairs
  4. `AC Educational DP Contest Q - Flowers`
- Timed task: Number of Longest Increasing Subsequence revisited with a Fenwick approach (optional), Create Sorted Array through Instructions (30 minutes).
- Revision: re-solve Count Primes and Spiral Matrix closed-book.

### CS Fundamentals — 2 hours

- Learn: process vs thread, PCB, context switch cost, user vs kernel mode, system calls, scheduling (FCFS, SJF, round-robin, MLFQ, CFS in Linux), preemption, priority inversion, `fork`/`exec`, zombie and orphan processes, signals, Linux `/proc`, `nice`.
- Implement: Java program that spawns a child process with `ProcessBuilder`, pipes output, and times a context-switch-heavy workload (1000 threads ping-ponging via `SynchronousQueue`).
- Exact interview questions: "What exactly happens on a context switch?", "Why are threads cheaper than processes?", "How does the Linux scheduler pick the next task?", "What is a zombie process?"

### Capstone Project — 0.5 hours

- Build: `docs/20-runtime-profile.md`.
- Deliverable: thread count, heap usage, and GC pause stats of `triage-api` under the Day 46 load, captured via Actuator metrics.

### End-of-Day Check

- [ ] I can explain why Count of Smaller Numbers After Self needs a BIT over compressed values processed right to left.
- [ ] I can explain the difference between a context switch and a mode switch.

## Day 49 — String algorithms (KMP, Rabin-Karp, Z); resilience patterns

**Total: 5 hours**

### DSA — 2.5 hours

- Learn: KMP failure function, Rabin-Karp rolling hash with double hashing, Z-algorithm, when to use each, Manacher's in one paragraph.
- Implement: `kmpSearch(text, pattern)`, `zArray(s)`, `rollingHash`.
- Solve:
  1. Find the Index of the First Occurrence in a String
  2. Repeated DNA Sequences
  3. Shortest Palindrome
  4. Longest Happy Prefix
  5. Longest Duplicate Substring
- Timed task: Repeated Substring Pattern (15 minutes), `CF 126B Password` (Z-function, 30 minutes).
- Revision: re-solve Range Sum Query - Mutable closed-book.

### Java / Backend — 2 hours

- Learn: timeouts everywhere (connect, read, pool), retries with backoff and budgets, circuit breaker states (closed, open, half-open), bulkheads, fallbacks, Resilience4j annotations, idempotency as the prerequisite for safe retries, load shedding, graceful degradation.
- Implement: Resilience4j `@CircuitBreaker` + `@Retry` + `@TimeLimiter` on the classifier client call in `triage-worker`; fallback sets `category=UNCLASSIFIED` and emits a metric; `WebhookSender` gets a bulkhead of 4.
- Exact exercise: run the stub classifier with a 50% failure rate; observe the breaker open in metrics, then half-open and recover.

### Capstone Project — 0.5 hours

- Build: Resilience4j metrics on the Grafana dashboard.
- Deliverable: breaker state panel.

### End-of-Day Check

- [ ] I can derive the KMP failure function by hand for `aabaaab`.
- [ ] I can explain the three circuit-breaker states and the half-open probe.

## Day 50 — Design problems (data structure design); networking I (TCP, UDP, DNS)

**Total: 5 hours**

### DSA — 2.5 hours

- Learn: composing hash maps, arrays, heaps, and linked lists for $O(1)$ APIs, lazy deletion, timestamps with binary search.
- Solve:
  1. Insert Delete GetRandom O(1)
  2. LFU Cache
  3. Design Twitter
  4. Design Hit Counter (NeetCode)
  5. Snapshot Array
  6. Design Underground System
- Timed task: Min Stack re-solve (8 minutes), Time Based Key-Value Store re-solve (15 minutes).
- Revision: re-solve LRU Cache closed-book (target 12 minutes).

### CS Fundamentals — 2 hours

- Learn: OSI vs TCP/IP layers, IP and ports, TCP three-way handshake, sequence numbers, flow control, congestion control (slow start, AIMD), `TIME_WAIT`, Nagle, head-of-line blocking, UDP use cases, DNS resolution path (recursive, root, TLD, authoritative, TTL), `dig`, `nslookup`, `tcpdump` basics, `netstat`/`ss`.
- Implement: `tcpdump -i lo port 8080` while calling your API; annotate the handshake and FIN; a Java UDP echo server/client.
- Exact interview questions: "What happens when you type a URL and press Enter?" (give a 3-minute answer covering DNS, TCP, TLS, HTTP, rendering), "Why is UDP used for DNS and video?", "What is `TIME_WAIT` for?"

### Capstone Project — 0.5 hours

- Build: HikariCP and HTTP client timeouts explicitly configured (connect 2 s, read 5 s, pool 30 s).
- Deliverable: `docs/21-timeouts.md` table of every timeout in the system.

### End-of-Day Check

- [ ] I can explain LFU with frequency buckets and why it is $O(1)$.
- [ ] I can give the full "URL to page" answer in 3 minutes without notes.
## Day 51 — Sliding window and hashing (hard); Strategy, Factory, Abstract Factory

**Total: 5 hours**

### DSA — 2 hours

- Solve:
  1. Minimum Window Substring (re-derive, then compare with your Day 16 solution)
  2. Substring with Concatenation of All Words
  3. Longest Substring with At Most K Distinct Characters (NeetCode)
  4. Count Vowel Substrings of a String
  5. Subarrays with K Different Integers (exactly k = atMost(k) − atMost(k−1))
- Timed task: Minimum Swaps to Group All 1's Together II (20 minutes).
- Revision: re-solve Insert Delete GetRandom O(1) and Design Twitter closed-book.

### LLD / HLD — 2.5 hours

- Learn: Strategy (interchangeable algorithms behind an interface), Factory Method, Simple Factory, Abstract Factory (families of related objects), when a `Map<Enum, Strategy>` beats a factory switch, pattern smells (pattern for its own sake).
- Design: a **Payment Processing** module: `PaymentStrategy` (`CardPayment`, `UpiPayment`, `WalletPayment`), `PaymentStrategyFactory`, and an Abstract Factory producing `Validator` + `Receipt` families per region (`IndiaFactory`, `EuFactory`).
- Implement: `lld/patterns/strategy-factory` with tests; then refactor Triage's `TicketRouter` rules into a `RoutingStrategy` resolved from a `Map<Channel, RoutingStrategy>` registered as Spring beans.
- Exact interview questions: "Strategy vs State: what is the difference?", "How do you add a new payment method without touching existing code?", "When is Abstract Factory overkill?"

### Capstone Project — 0.5 hours

- Build: routing strategies as Spring beans; `RoutingStrategyRegistry`.
- Deliverable: existing router tests pass; a new `ChatChannelStrategy` added in one new file only.

### End-of-Day Check

- [ ] I can explain exactly-k via at-most-k and why it is $O(n)$.
- [ ] I can implement Strategy + Factory in Java in 10 minutes and name a Triage use of each.

## Day 52 — Greedy (hard); Builder, Singleton, Prototype; OpenAPI

**Total: 5 hours**

### DSA — 2 hours

- Solve:
  1. Candy
  2. Minimum Number of Refueling Stops
  3. IPO
  4. Maximum Number of Events That Can Be Attended
  5. Two City Scheduling
- Timed task: `CF 1374C Move Brackets` (10 minutes), `CF 1399C Boats Competition` (25 minutes).
- Revision: re-solve Substring with Concatenation of All Words closed-book.

### LLD / HLD — 1.5 hours

- Learn: Builder (telescoping constructors, immutability, validation at `build()`), Singleton (lazy, eager, double-checked locking with `volatile`, holder idiom, enum singleton, why it hurts testing), Prototype (copy constructors, deep vs shallow copy, `clone()` pitfalls).
- Design: `HttpRequest` builder with required and optional fields; `MetricsRegistry` singleton via holder idiom; `TicketTemplate` prototype with deep copy of tags.
- Implement: `lld/patterns/creational` with tests including a concurrency test for the singleton (1000 threads, one instance).
- Exact interview questions: "Why is double-checked locking broken without `volatile`?", "Builder vs static factory vs record?", "How do you test code that uses a singleton?"

### Java / Backend — 1 hour

- Learn: OpenAPI 3 via `springdoc-openapi`, Swagger UI, documenting DTOs, error responses, security schemes, generating a client, API-first vs code-first.
- Implement: `springdoc` on `triage-api`; annotate the ticket endpoints; commit the generated `openapi.json` to `docs/api/`.
- Exact exercise: diff the generated spec in CI (fail if uncommitted drift).

### Capstone Project — 0.5 hours

- Build: `TicketResponse` and `CreateTicketRequest` fully documented with examples.
- Deliverable: Swagger UI at `/swagger-ui.html` protected behind auth in `prod`.

### End-of-Day Check

- [ ] I can explain why Candy needs two passes and why a single pass fails.
- [ ] I can write the holder-idiom singleton from memory and explain JVM class-init guarantees.

## Day 53 — Backtracking (hard); OS II (memory, virtual memory); idempotency keys

**Total: 5 hours**

### DSA — 2 hours

- Solve:
  1. Sudoku Solver
  2. Expression Add Operators
  3. Word Break II
  4. Beautiful Arrangement
- Timed task: Matchsticks to Square (25 minutes).
- Revision: re-solve Candy and IPO closed-book.

### CS Fundamentals — 2 hours

- Learn: address spaces, virtual memory, paging, page tables, TLB, page faults, demand paging, swapping, thrashing, segmentation, memory-mapped files, copy-on-write on `fork`, stack vs heap at the OS level, `malloc` arenas in one paragraph, how the JVM heap sits in virtual memory, RSS vs virtual size, OOM killer.
- Implement: Java `MappedByteBuffer` reading a 1 GB file; compare with `FileInputStream` throughput; observe RSS with `ps -o rss`.
- Exact interview questions: "What is a page fault and what happens on one?", "What is the TLB and why does locality matter?", "Why does a 32-bit process max out at 4 GB?", "What does the OOM killer do and how does it choose?"

### Java / Backend — 0.5 hours

- Learn: idempotency keys for `POST`, storing key → response hash with TTL, conflict on same key with different payload (422), scope per user.
- Implement: `Idempotency-Key` support on `POST /api/v1/tickets` backed by Redis (`SET NX` + stored response, 24 h TTL).

### Capstone Project — 0.5 hours

- Build: idempotency filter + tests (same key twice ⇒ same 201 body, one row in DB; same key different body ⇒ 422).
- Deliverable: `docs/22-idempotency.md`.

### End-of-Day Check

- [ ] I can explain the TLB miss path and what demand paging buys.
- [ ] I can explain how my idempotency implementation behaves if Redis loses the key.

## Day 54 — Two heaps and scheduling (hard); Observer, Command, State

**Total: 5 hours**

### DSA — 2 hours

- Solve:
  1. Sliding Window Median
  2. Meeting Rooms III
  3. Process Tasks Using Servers
  4. Maximum Performance of a Team
- Timed task: Kth Smallest Element in a Sorted Matrix re-solve (15 minutes), Furthest Building You Can Reach (20 minutes).
- Revision: re-solve Sudoku Solver closed-book.

### LLD / HLD — 2.5 hours

- Learn: Observer (subject/listeners, push vs pull, event bus, memory leaks from un-unsubscribed listeners), Command (encapsulate requests, undo/redo, queues), State (state objects owning transitions, replacing status `switch` ladders).
- Design: **Text Editor** with `Command` undo/redo stack; **Ticket State Machine** with `TicketState` interface (`New`, `Triaged`, `Assigned`, `InProgress`, `WaitingOnCustomer`, `Resolved`, `Closed`) and an `Observer` that emits domain events on transitions.
- Implement: `lld/patterns/behavioral-1` with tests; refactor Triage's `Ticket` transitions into State objects behind the same public API (all existing tests must pass).
- Exact interview questions: "How does Observer differ from pub/sub?", "Where would Command appear in a backend system?", "Does State violate OCP when a new status is added?"

### Capstone Project — 0.5 hours

- Build: `DomainEventPublisher` port; Spring `ApplicationEventPublisher` adapter; `TicketAssigned`, `TicketResolved` events feeding the outbox.
- Deliverable: outbox rows for all three events in an integration test.

### End-of-Day Check

- [ ] I can explain lazy deletion in Sliding Window Median.
- [ ] I can explain the State pattern and show the Triage before/after.

## Day 55 — Graph (hard); DBMS internals I (storage, B+trees, WAL, MVCC)

**Total: 5 hours**

### DSA — 2 hours

- Solve:
  1. Word Ladder II
  2. Critical Connections in a Network (Tarjan bridges)
  3. Number of Ways to Arrive at Destination
  4. Minimum Cost to Make at Least One Valid Path in a Grid (re-derive)
- Timed task: Is Graph Bipartite? (15 minutes), Possible Bipartition (15 minutes).
- Revision: re-solve Reconstruct Itinerary closed-book.

### CS Fundamentals — 2.5 hours

- Learn: heap files and pages (8 KB in Postgres), tuples and TOAST, B+tree structure and fanout, why B+trees over binary trees on disk, clustered vs non-clustered indexes (InnoDB vs Postgres heap), write-ahead log, checkpoints, crash recovery, MVCC in Postgres (xmin/xmax, snapshots, dead tuples, `VACUUM`, `autovacuum`), hot updates, `fillfactor`, LSM trees vs B-trees (write-optimized stores like RocksDB/Cassandra) in a paragraph.
- Implement: `BPlusTree` in Java (order 4, insert + range scan, no deletes) with tests; run `SELECT xmin, xmax, * FROM tickets` before and after an update, observe MVCC versions, run `VACUUM VERBOSE`.
- Exact SQL problems: Trips and Users, Game Play Analysis IV, Human Traffic of Stadium, Market Analysis I, Monthly Transactions I.

### Capstone Project — 0.5 hours

- Build: `docs/23-db-internals-notes.md`.
- Deliverable: how `tickets` is stored, which indexes exist, estimated bloat after the Day 25 benchmark, and the autovacuum settings you chose.

### End-of-Day Check

- [ ] I can explain Tarjan's low-link condition for a bridge.
- [ ] I can explain MVCC and why long transactions cause bloat.

## Day 56 — Mixed timed set 5; networking II (TLS, HTTPS, HTTP/2, HTTP/3, WebSockets, gRPC)

**Total: 5 hours**

### DSA — 2.5 hours

- Timed set (100 minutes, closed-book):
  1. Reorganize String
  2. Longest Increasing Path in a Matrix
  3. Edit Distance
  4. Design Hit Counter (NeetCode)
  5. Minimum Interval to Include Each Query (unseen, hard)
- Revision (50 minutes): re-solve failures; re-solve Critical Connections in a Network.

### CS Fundamentals — 2 hours

- Learn: TLS 1.3 handshake, certificates and chains, SNI, ALPN, HTTPS cost and session resumption, HTTP/2 (multiplexing, header compression, server push deprecated), HTTP/3 and QUIC (UDP, no head-of-line blocking), WebSockets handshake and framing, SSE, gRPC (protobuf, HTTP/2, streaming) vs REST, long polling.
- Implement: Caddy (or nginx) in Compose terminating TLS with a self-signed cert in front of `triage-api`; `openssl s_client -connect` and read the handshake; a Spring WebSocket endpoint `/ws/notifications` pushing new notifications to the logged-in agent.
- Exact interview questions: "Walk through a TLS 1.3 handshake", "Why does HTTP/3 use UDP?", "WebSocket vs SSE vs long polling: when?", "When would you choose gRPC inside Triage?"

### Capstone Project — 0.5 hours

- Build: WebSocket notification push wired to the notification consumer.
- Deliverable: manual test with `websocat` and a note in `docs/24-realtime.md`.

### End-of-Day Check

- [ ] I can explain Minimum Interval to Include Each Query via sorting + heap.
- [ ] I can explain what a certificate chain proves and what SNI is for.

## Day 57 — DP (hard); Decorator, Adapter, Facade, Proxy; Redis rate limiter

**Total: 5 hours**

### DSA — 2 hours

- Solve:
  1. Maximal Rectangle
  2. Longest Valid Parentheses
  3. Frog Jump
  4. Cherry Pickup
- Timed task: Best Time to Buy and Sell Stock III (20 minutes).
- Revision: re-solve Minimum Interval to Include Each Query closed-book.

### LLD / HLD — 2 hours

- Learn: Decorator (wrapping behavior, Java I/O streams, Spring's `BufferedReader`), Adapter (bridging interfaces, anti-corruption layers), Facade (simplifying subsystems), Proxy (lazy, protection, caching, remote; how Spring AOP proxies `@Transactional` and `@Cacheable`).
- Design: `NotificationSender` decorated with `RetryingSender`, `MeteredSender`, `LoggingSender`; an `Adapter` from a third-party `LegacyEmailClient` to `NotificationSender`; a `TriageFacade` for "create and auto-assign"; a `CachingTicketRepositoryProxy`.
- Implement: `lld/patterns/structural` with tests; apply `RetryingSender` and `MeteredSender` decorators in Triage instead of the inline retry from Day 41.
- Exact interview questions: "Decorator vs Proxy vs Adapter: identical structure, different intent; explain", "How does Spring create a proxy for a class without an interface?", "Why does calling a `@Transactional` method via `this` skip the proxy?"

### Java / Backend — 0.5 hours

- Learn: token bucket vs sliding window log vs sliding window counter vs fixed window, per-user vs per-IP keys, 429 with `Retry-After`, rate limiting at gateway vs service.
- Implement: Redis token bucket as a Lua script (atomic), `RateLimitFilter` at 100 requests/minute per user.

### Capstone Project — 0.5 hours

- Build: rate limiter filter + metrics (`ratelimit.rejected.total`).
- Deliverable: test hitting 101 requests ⇒ last one 429 with `Retry-After`.

### End-of-Day Check

- [ ] I can explain the stack-based $O(n)$ Longest Valid Parentheses.
- [ ] I can explain why the token bucket must be atomic and how Lua gives that in Redis.

## Day 58 — Binary search (hard); DBMS internals II (transactions, locks, deadlocks)

**Total: 5 hours**

### DSA — 2 hours

- Solve:
  1. Find K-th Smallest Pair Distance
  2. Nth Magical Number
  3. Minimize Maximum of Array
  4. Maximum Running Time of N Computers
- Timed task: `CF 1324C Frog Jumps` (15 minutes), Split Array Largest Sum re-solve (15 minutes).
- Revision: re-solve Maximal Rectangle closed-book.

### CS Fundamentals — 2.5 hours

- Learn: ACID precisely, anomalies (dirty read, non-repeatable read, phantom, write skew, lost update), isolation levels in Postgres (READ COMMITTED, REPEATABLE READ, SERIALIZABLE with SSI), row locks (`FOR UPDATE`, `FOR SHARE`, `SKIP LOCKED`, `NOWAIT`), advisory locks, lock queues, deadlock detection, optimistic vs pessimistic locking, two-phase locking, 2PC and sagas in one paragraph.
- Implement: two `psql` sessions reproducing a lost update under READ COMMITTED, then fixing it with `SELECT ... FOR UPDATE` and separately with `@Version`; reproduce a deadlock and read the Postgres error; a Java test using two threads and `SERIALIZABLE` that gets a serialization failure and retries.
- Exact SQL problems: Immediate Food Delivery II, Capital Gain/Loss, Movie Rating, Count Salary Categories, Confirmation Rate.

### Capstone Project — 0.5 hours

- Build: `AssignTicketUseCase` uses `SELECT ... FOR UPDATE` on the ticket and `@Version`; retry on `OptimisticLockException` once.
- Deliverable: concurrent-assign test: 10 threads, one wins, 9 get 409.

### End-of-Day Check

- [ ] I can explain write skew and why only SERIALIZABLE prevents it.
- [ ] I can explain the binary search on answer in Find K-th Smallest Pair Distance with a two-pointer count.

## Day 59 — Monotonic deque (hard); Template Method, Chain of Responsibility, Composite, Iterator

**Total: 5 hours**

### DSA — 2 hours

- Solve:
  1. Shortest Subarray with Sum at Least K
  2. Constrained Subsequence Sum
  3. Jump Game VI
  4. Max Value of Equation
- Timed task: Sliding Window Maximum re-solve (12 minutes).
- Revision: re-solve Find K-th Smallest Pair Distance closed-book.

### LLD / HLD — 2.5 hours

- Learn: Template Method (skeleton with hooks; prefer composition when possible), Chain of Responsibility (pipelines, middleware, Spring filters), Composite (tree structures, uniform treatment), Iterator (custom `Iterable`), Null Object, Specification pattern for query rules.
- Design: a **Ticket Ingestion Pipeline** as a Chain of Responsibility: `SpamFilterHandler`, `PiiRedactionHandler`, `DedupeHandler`, `LanguageDetectHandler`, `EnrichHandler`; a **File System** Composite (`Directory`/`File` with `size()`); a `ReportGenerator` Template Method with `CsvReport`/`JsonReport`.
- Implement: `lld/patterns/behavioral-2` with tests; wire the ingestion pipeline into `CreateTicketUseCase` (handlers as ordered Spring beans with `@Order`).
- Exact interview questions: "How do you stop the chain and report why?", "Template Method vs Strategy?", "How does Composite handle cycles?"

### Capstone Project — 0.5 hours

- Build: pipeline with `SpamFilterHandler` (keyword list) and `PiiRedactionHandler` (regex for emails/phones) in Triage.
- Deliverable: tests showing redaction in stored body and rejection (422) of spam.

### End-of-Day Check

- [ ] I can explain why Shortest Subarray with Sum at Least K needs a deque over prefix sums and why the window trick fails with negatives.
- [ ] I can name the pattern behind Spring's filter chain and `JdbcTemplate` callbacks.

## Day 60 — Day-60 milestone: timed set 6, k6 load-test baseline, API hardening

**Total: 5 hours**

### DSA — 2 hours

- Timed set (80 minutes, closed-book):
  1. Kth Largest Element in an Array
  2. Course Schedule II
  3. Longest Common Subsequence
  4. LFU Cache
- Revision (40 minutes): re-solve Jump Game VI and Constrained Subsequence Sum; update `patterns.md` with "max/min over a window of previous dp values ⇒ monotonic deque".

### Java / Backend — 2 hours

- Learn: k6 scripts (VUs, stages, thresholds, checks), open vs closed load models, p95 thresholds as CI gates, warming up the JVM, reading HikariCP saturation, identifying the first bottleneck.
- Implement: `load/create-and-read.js` (70% reads, 30% creates, 50 VUs, 3 minutes) with thresholds `http_req_duration p(95) < 200`; run against Compose; capture Grafana panels.
- Exact exercise: `docs/25-load-baseline.md` with RPS, p50/p95/p99, error rate, CPU, DB connections in use, and the single biggest bottleneck you observed.

### Capstone Project — 1 hour

- Build: security headers (HSTS, `X-Content-Type-Options`, `Content-Security-Policy` for Swagger only), `ETag` on `GET /tickets/{id}` with `If-None-Match` ⇒ 304, request size limits, Trivy image scan + OWASP dependency-check in CI.
- Deliverable: `docs/26-milestone-60.md`; tag `v0.3.0`.

### End-of-Day Check

- [ ] I can state my system's measured p95 and the bottleneck without looking.
- [ ] I scored at least 6/8 on the timed set.
## Day 61 — Virtual contest; LLD system: Parking Lot

**Total: 5 hours**

### DSA — 2 hours

- Timed task: virtual contest `Codeforces Round 1352 (Div. 4)` — problems A through E in 100 minutes (A Sum of Round Numbers, B Same Parity Summands, C K-th Not Divisible by n, D Alice, Bob and Candies, E Special Elements). Upsolve F after time.
- Revision: write the one-line idea for each solved problem in `dsa/notes/contests.md`.

### LLD / HLD — 2.5 hours

- Design: **Parking Lot** — requirements (multiple floors, spot sizes, vehicle types, entry/exit gates, ticketing, hourly pricing, payment), class diagram (`ParkingLot`, `Floor`, `Spot`, `Vehicle` hierarchy, `Ticket`, `PricingStrategy`, `SpotAllocationStrategy`, `Gate`, `PaymentProcessor`), sequence for entry and exit, concurrency on spot allocation (lock per floor or `synchronized` on spot lists).
- Implement: `lld/systems/parkinglot` with `park(vehicle)` returning a ticket, `unpark(ticket)` computing fees, nearest-spot strategy; 10 tests including "lot full" and concurrent parking of 50 vehicles into 10 spots (exactly 10 succeed).
- Exact interview questions: "How do you support EV charging spots?", "How do you make allocation thread-safe without a global lock?", "Where does pricing live and how do you add weekend pricing?"

### Capstone Project — 0.5 hours

- Build: `docs/27-lld-of-triage.md` first draft: class diagram of the ingestion pipeline, state machine, routing, and notifications as they exist now.
- Deliverable: diagram committed.

### End-of-Day Check

- [ ] I solved at least A–D within time.
- [ ] I can present the Parking Lot design in 20 minutes from requirements to code.

## Day 62 — Trie + DP; OS III (synchronization and deadlock); Kafka consumer tuning

**Total: 5 hours**

### DSA — 1.5 hours

- Solve:
  1. Concatenated Words
  2. Palindrome Pairs
  3. Word Break II re-solve (closed-book)
- Timed task: Extra Characters in a String (20 minutes).

### CS Fundamentals — 2 hours

- Learn: critical sections, mutex vs semaphore vs monitor, condition variables and spurious wakeups, the four deadlock conditions, prevention vs avoidance (Banker's) vs detection, livelock, starvation, readers-writers, dining philosophers, lock-free basics with CAS and the ABA problem.
- Implement: in Java, a `BoundedBuffer` with `ReentrantLock` + two `Condition`s; dining philosophers with ordered lock acquisition; a readers-writers with `ReadWriteLock` and a starvation demo with fairness toggled.
- Exact interview questions: "Mutex vs binary semaphore?", "Explain the four Coffman conditions and which one Java's lock ordering breaks", "What is a spurious wakeup and why `while` not `if`?", "What is CAS and the ABA problem?"

### Java / Backend — 1 hour

- Learn: Kafka consumer `max.poll.records`, `max.poll.interval.ms`, concurrency = partitions, batch listeners, commit strategies, rebalance listeners, consumer lag alerting, backpressure by pausing partitions.
- Implement: `concurrency=3` for the classification consumer, batch size 50, lag alert at 1,000; a `pause()` when the ML service breaker is open.

### Capstone Project — 0.5 hours

- Build: lag panel + alert; pause/resume on breaker state.
- Deliverable: test or manual run showing lag drains after breaker closes, noted in `docs/28-consumer-tuning.md`.

### End-of-Day Check

- [ ] I can implement a bounded buffer with conditions from memory.
- [ ] I can explain what happens when `max.poll.interval.ms` is exceeded.

## Day 63 — Sweep line (hard); LLD system: Vending Machine (State)

**Total: 5 hours**

### DSA — 2 hours

- Solve:
  1. The Skyline Problem
  2. Car Pooling
  3. My Calendar III
  4. Number of Flowers in Full Bloom
- Timed task: Meeting Rooms II re-solve (12 minutes).
- Revision: re-solve Concatenated Words closed-book.

### LLD / HLD — 2.5 hours

- Design: **Vending Machine** — states (`Idle`, `HasMoney`, `Dispensing`, `OutOfStock`), inventory, coins/notes, change-making (greedy with denomination check), refund, admin restock; class diagram and state diagram.
- Implement: `lld/systems/vendingmachine` with the State pattern; 10 tests including insufficient funds, exact change unavailable, and restock while idle.
- Exact interview questions: "Why State instead of an enum and switch?", "How do you make change when greedy fails?" (DP from Day 39), "How would you persist machine state across restarts?"

### Capstone Project — 0.5 hours

- Build: `GET /api/v1/tickets/{id}/timeline` returning events ordered by time with actor names (one join query).
- Deliverable: integration test on a ticket with 5 events.

### End-of-Day Check

- [ ] I can explain The Skyline Problem using a max-heap with lazy deletion or a `TreeMap` of counts.
- [ ] I can present the Vending Machine design in 15 minutes.

## Day 64 — HLD I: building blocks (DNS, load balancing, stateless services, CDN); two-heaps revision

**Total: 5 hours**

### LLD / HLD — 2.5 hours

- Learn: client-server over DNS, L4 vs L7 load balancers, algorithms (round robin, least connections, consistent hashing, weighted), health checks, sticky sessions and why to avoid them, stateless services with externalized session/cache, horizontal vs vertical scaling, reverse proxies and API gateways (auth, rate limiting, routing), CDN (edge caching, cache keys, TTL, invalidation, signed URLs), anycast in a sentence, latency numbers every programmer should know (memorize: L1 1 ns, RAM 100 ns, SSD read 100 µs, same-DC round trip 500 µs, HDD seek 10 ms, cross-continent 150 ms).
- Design: **Scale a single Spring Boot server to 3 replicas**: diagram with nginx, health checks, config for zero-downtime deploys (rolling), and a list of every piece of state you had to move out of the process.
- Exact interview questions: "Why can't I use sticky sessions for Triage?", "L4 or L7 for WebSockets?", "How does a CDN decide what to cache?"

### DSA — 1.5 hours

- Solve:
  1. Find Median from Data Stream re-solve
  2. Kth Largest Element in a Stream re-solve
  3. Smallest Range Covering Elements from K Lists
- Timed task: Ugly Number II (15 minutes).

### Capstone Project — 1 hour

- Build: nginx (or Caddy) load balancing two `triage-api` replicas in Compose, round robin with health checks; prove statelessness by killing one replica mid k6 run.
- Deliverable: `docs/29-horizontal-scaling.md` with the error-rate graph during the kill.

### End-of-Day Check

- [ ] I can recite the latency numbers and use them in an estimate.
- [ ] Triage runs with 2 API replicas and no shared in-process state.

## Day 65 — HLD II: caching layers, replication, sharding, consistent hashing

**Total: 5 hours**

### LLD / HLD — 2.5 hours

- Learn: cache tiers (client, CDN, gateway, application, DB), eviction (LRU, LFU, TTL), hot keys, cache consistency; database replication (leader-follower, sync vs async, replication lag, read-your-writes, failover, split brain), multi-leader and leaderless in a paragraph; partitioning (range, hash, directory), rebalancing, hot spots, consistent hashing with virtual nodes, secondary indexes under sharding (local vs global), cross-shard queries and joins, resharding.
- Design: **Shard Triage's `tickets` table at 1 billion rows**: shard key choice (`team_id` vs `ticket_id` vs `created_at`), how search and reports work across shards, how to move a hot team.
- Implement: `ConsistentHashRing` in Java with virtual nodes; test that adding a node remaps about $1/n$ of keys.
- Exact interview questions: "Why consistent hashing with virtual nodes?", "How do you serve read-your-writes with async replicas?", "What breaks when you shard by `team_id`?"

### DSA — 1.5 hours

- Solve:
  1. Design Hit Counter re-solve
  2. Random Pick with Weight
  3. Random Pick Index (reservoir sampling)
  4. Shuffle an Array (Fisher-Yates)
- Timed task: Encode and Decode TinyURL (10 minutes).

### Capstone Project — 1 hour

- Build: Postgres streaming replica in Compose (primary + one hot standby); route `GET /tickets?` list queries to the replica via a routing `DataSource` (`AbstractRoutingDataSource`) with a `@ReadOnlyReplica` annotation.
- Deliverable: `docs/adr/ADR-010-read-replicas.md` including the read-your-writes rule (reads within 2 s of a write go to primary).

### End-of-Day Check

- [ ] I can explain reservoir sampling and prove uniformity for $k=1$.
- [ ] I can draw a consistent-hash ring and explain virtual nodes.

## Day 66 — HLD III: queues, consistency models, CAP/PACELC; LLD system: Elevator

**Total: 5 hours**

### LLD / HLD — 3 hours

- Learn (1.5 h): message queues vs logs (RabbitMQ vs Kafka), delivery semantics, back pressure, dead letters, fan-out, CAP precisely (and why it is about partitions), PACELC, consistency models (linearizability, sequential, causal, eventual, read-your-writes, monotonic reads), quorums ($W + R > N$), leader election, consensus in one paragraph (Raft: terms, log replication), idempotent APIs, distributed locks and fencing tokens, clocks and why wall time is unreliable (logical clocks).
- Design (1.5 h): **Elevator System** — multiple elevators, requests (hall and cabin), dispatch strategy (nearest idle, SCAN/LOOK), state machine per elevator, class diagram, concurrency on request queue; implement `lld/systems/elevator` with a `Dispatcher` and a LOOK strategy; tests for ordering of stops.
- Exact interview questions: "Why does CAP not apply when there is no partition?", "What is a fencing token and why does Redis `SET NX` alone not give safe mutual exclusion?", "How do you prevent elevator starvation?"

### DSA — 1.5 hours

- Solve:
  1. Kth Largest Element in an Array (quickselect from memory, 10 minutes)
  2. Top K Frequent Elements (bucket, 10 minutes)
  3. Minimum Cost to Connect Sticks (NeetCode) — or free alternative Last Stone Weight re-solve
  4. Maximum Frequency Stack
- Timed task: Reorganize String re-solve (12 minutes).

### Capstone Project — 0.5 hours

- Build: fencing token on the SLA scanner lock (Redis `INCR` token stored with the lock; write path checks token).
- Deliverable: ADR-011 on lock safety and what happens on a stalled scanner.

### End-of-Day Check

- [ ] I can state CAP and PACELC without hand-waving and classify Postgres, Cassandra, and Redis.
- [ ] I can present the Elevator design in 20 minutes including the dispatch strategy tradeoffs.

## Day 67 — Estimation drills; k6 scenario 2; DSA revision

**Total: 5 hours**

### LLD / HLD — 2 hours

- Learn: estimation method (DAU → QPS with peak factor 2–5×, payload size → bandwidth, retention → storage, cache 20% of daily reads, server count from per-instance capacity), powers of two, converting units quickly.
- Design: five estimation exercises written in full: (1) Twitter-like feed for 300 M DAU, (2) URL shortener at 100 M new URLs/month with 10:1 read ratio, (3) WhatsApp-scale messaging at 50 B messages/day, (4) YouTube upload at 500 hours/minute, (5) Triage at 10 M tickets/day.
- Exact interview questions: "How many servers for 10k QPS at 50 ms per request?", "How much storage for 5 years of Triage tickets with attachments?"

### DSA — 2 hours

- Revision (delayed re-solves, closed-book, 15 minutes each): 3Sum, Longest Repeating Character Replacement, Reorder List, Validate Binary Search Tree, Course Schedule, Coin Change, Word Search, Merge k Sorted Lists.

### Capstone Project — 1 hour

- Build: k6 scenario `load/sla-breach-burst.js`: 2,000 tickets created in 20 s, then measure time until all classified (stub) and scanner throughput.
- Deliverable: `docs/30-ingest-throughput.md` with tickets/second ingested and consumer lag curve.

### End-of-Day Check

- [ ] I can do a full estimate for any of the five systems in 5 minutes.
- [ ] All 8 re-solves within target or re-queued for Day 74.

## Day 68 — LLD systems: Tic-Tac-Toe and Snake and Ladder; DBMS III: partitioning in Postgres

**Total: 5 hours**

### LLD / HLD — 2 hours

- Design: **Tic-Tac-Toe** (N×N, $O(1)$ win check with row/col/diag counters, players, undo) and **Snake and Ladder** (board, dice strategy, players, snakes/ladders as a `Map<Integer, Integer>`, game loop, winner); compare how `Board`, `Player`, `Game` generalize to a `BoardGame` abstraction without over-engineering.
- Implement: both in `lld/systems/games` with tests (win detection in $O(1)$ per move; a deterministic dice for tests).
- Exact interview questions: "How do you check a win in $O(1)$?", "How do you support 3 players or a 10×10 board?", "Where would you put the random dice so tests stay deterministic?"

### CS Fundamentals — 2 hours

- Learn: declarative partitioning (range, list, hash), partition pruning, indexes per partition, attaching/detaching partitions for retention, when partitioning helps (large tables, time-based retention) vs hurts (many small partitions), `pg_partman` in a sentence, archival to cold storage.
- Implement: convert `ticket_events` to monthly range partitions (`V10__partition_ticket_events.sql`); verify pruning with `EXPLAIN` on a date-bounded query; script to create next month's partition.
- Exact interview questions: "Partitioning vs sharding?", "How do you delete 3-year-old events cheaply?"

### DSA — 1 hour

- Solve:
  1. Design Tic-Tac-Toe (NeetCode) — compare with your LLD implementation
  2. Snakes and Ladders (BFS)
- Timed task: Walls and Gates (NeetCode) or free alternative 01 Matrix re-solve (15 minutes).

### End-of-Day Check

- [ ] I can explain partition pruning and show the plan.
- [ ] I can explain the $O(1)$ win check counters.

## Day 69 — HLD system 1: URL Shortener; ID generation; DSA

**Total: 5 hours**

### LLD / HLD — 2.5 hours

- Learn: the HLD interview script (clarify 5 min, estimate 5 min, API 5 min, data model 5 min, high-level diagram 10 min, deep dives 15 min, wrap-up 5 min), how to drive rather than be driven.
- Design: **URL Shortener** in full written form: requirements, 100 M URLs/month estimate, API (`POST /shorten`, `GET /{code}`), key generation (base62 of a counter vs random with collision check vs pre-generated key service), data model, 301 vs 302 and analytics implications, cache (Redis, hot keys), rate limiting, DB choice, replication, sharding by code, expiry, abuse; failure modes (key service down, cache down, DB down), scaling to 10×.
- Exact interview questions: "Why base62 of a counter leaks information and how to fix it?", "301 or 302?", "How do you get unique IDs across 10 app servers?"

### Java / Backend — 1 hour

- Learn: ID strategies: auto-increment, UUID v4 (random, index-unfriendly), UUID v7 (time-ordered), Snowflake (timestamp, machine id, sequence), ULID, KGS; index locality effects.
- Implement: `SnowflakeIdGenerator` in Java with tests for monotonicity and 4,096 ids/ms limit; keep UUID v7 for Triage (ADR-003 already explains why).

### DSA — 1.5 hours

- Solve:
  1. Encode and Decode TinyURL re-solve
  2. Design Authentication Manager
  3. Design a Number Container System
- Timed task: Design Browser History (15 minutes).

### End-of-Day Check

- [ ] I can present URL Shortener end-to-end in 35 minutes and handle "key service down".
- [ ] I can explain the Snowflake bit layout.

## Day 70 — Day-70 milestone: mixed timed set 6, CS rapid-fire, supply-chain security

**Total: 5 hours**

### DSA — 2.5 hours

- Timed set (100 minutes, closed-book):
  1. Task Scheduler
  2. Number of Islands
  3. Longest Palindromic Subsequence
  4. Accounts Merge
  5. Count of Range Sum (unseen, hard)
- Revision (50 minutes): re-solve failures; re-solve The Skyline Problem.

### CS Fundamentals — 1.5 hours

- Revision: 30 rapid-fire questions answered aloud in 2 minutes each from OS (process vs thread, context switch, paging, TLB, deadlock conditions, mutex vs semaphore, scheduling), networking (TCP handshake, TIME_WAIT, DNS path, TLS handshake, HTTP/2 vs 3, WebSocket), DBMS (ACID, isolation anomalies, MVCC, B+tree, WAL, index types, partitioning vs sharding, replication lag). Write a one-line answer for each you stumbled on in `notes/cs-rapidfire.md`.

### Capstone Project — 1 hour

- Build: Trivy and OWASP dependency-check gating CI (fail on CRITICAL), Dependabot config, pinned base images by digest, `SECURITY.md`.
- Deliverable: `docs/31-milestone-70.md` (endpoints, events, metrics, coverage, p95, bottleneck, open risks); tag `v0.5.0`.

### End-of-Day Check

- [ ] I can explain Count of Range Sum via merge sort on prefix sums.
- [ ] I answered at least 25/30 rapid-fire questions cleanly.
# Phase 4 — LLD Systems + HLD Foundations + Concurrency + CS Fundamentals (Days 71–90)

> [!note] Phase goal
> Ten more full LLD systems with Java implementations, ten written HLD designs, Java concurrency at interview depth, DSA at hard/unseen level with weekly timed sets, and Triage hardened with search, rate limiting, structured logging, and chaos basics.

## Day 71 — LLD system: Splitwise; HLD system 2: Pastebin; DSA

**Total: 5 hours**

### LLD / HLD — 3 hours

- Design (1.5 h): **Splitwise** — users, groups, expenses, split types (equal, exact, percent, shares), balance sheet, simplify-debts (min cash flow with greedy on net balances), class diagram, `SplitStrategy` hierarchy, validation that splits sum to total (with rounding rules in cents).
- Implement: `lld/systems/splitwise` with `Expense`, `SplitStrategy` implementations, `BalanceService.simplify(group)`; tests for rounding leftovers (assign to payer) and a 4-person simplification.
- Design (1.5 h): **Pastebin** written: requirements, estimate (10 M pastes/day, 10 KB average), API, data model (metadata in Postgres, content in object storage), key generation, expiry (TTL + cleanup job), caching, CDN for hot pastes, private pastes and signed URLs, abuse (size limits, rate limits), failure modes.
- Exact interview questions: "Why not store content in Postgres?", "How do percent splits avoid losing a cent?", "How do you delete expired pastes at scale?"

### DSA — 2 hours

- Solve:
  1. Minimum Cash Flow (GfG "Minimize Cash Flow among a given set of friends")
  2. Evaluate Division re-solve
  3. Optimal Account Balancing (NeetCode)
- Timed task: Alien Dictionary re-solve (20 minutes).
- Revision: re-solve Count of Range Sum closed-book.

### End-of-Day Check

- [ ] I can explain why minimum cash flow is NP-hard in general and why the greedy heuristic is acceptable in an interview.
- [ ] I can present Pastebin in 30 minutes.

## Day 72 — Java concurrency depth I: memory model, locks, ConcurrentHashMap internals; DSA

**Total: 5 hours**

### Java / Backend — 2.5 hours

- Learn: Java Memory Model (happens-before edges: monitor, volatile, thread start/join, `final` field freeze), safe publication, immutable objects, `ConcurrentHashMap` internals (bins, CAS on empty bins, `synchronized` on bin heads, tree bins, `computeIfAbsent` atomicity and recursion pitfalls), `ThreadLocal` and memory leaks in pools, `ForkJoinPool` and parallel streams (and why not in web requests), `StampedLock` optimistic reads, `Phaser` in a sentence.
- Implement: `SafeCache<K,V>` with `ConcurrentHashMap.computeIfAbsent` and a single-flight loader; a `ParallelMergeSort` with `RecursiveTask`; a broken "unsafe publication" demo and its fix.
- Exact exercise: write 10 concurrency interview questions with answers in `notes/java-concurrency.md` (e.g., `synchronized` vs `ReentrantLock`, `volatile` semantics, `ConcurrentHashMap` vs `Collections.synchronizedMap`, deadlock detection, thread pool sizing $N_{threads} = N_{cores} \cdot (1 + W/C)$).

### DSA — 2 hours

- Solve (concurrency problems on LeetCode, implement in Java):
  1. Print in Order
  2. Print FooBar Alternately
  3. Building H2O
  4. The Dining Philosophers
  5. Design Bounded Blocking Queue (NeetCode)
- Timed task: Fizz Buzz Multithreaded (20 minutes).
- Revision: re-solve Palindrome Pairs closed-book.

### Capstone Project — 0.5 hours

- Build: `ThreadPoolTaskExecutor` sizing for the webhook sender based on the formula; `@Async` with a dedicated executor and context propagation of `traceId`.
- Deliverable: note in `docs/32-threading.md` with pool sizes and reasoning.

### End-of-Day Check

- [ ] I can list five happens-before rules and explain safe publication.
- [ ] I can explain why `computeIfAbsent` must not modify the same map inside its function.

## Day 73 — LLD system: Rate Limiter and Logger; HLD system 3: Rate Limiter (distributed)

**Total: 5 hours**

### LLD / HLD — 3.5 hours

- Design (1 h): **Rate Limiter (library)** — `RateLimiter` interface with `TokenBucket`, `SlidingWindowLog`, `SlidingWindowCounter`, `FixedWindow` implementations, per-key limiters with eviction, thread safety, clock injection.
- Implement: `lld/systems/ratelimiter` with all four algorithms and tests using a fake clock (burst allowed by token bucket, rejected by sliding log).
- Design (1 h): **Logger framework** — levels, appenders (console, file, async with a bounded queue and drop policy), formatters, logger hierarchy and inheritance of level (like Logback), `MDC`; implement `lld/systems/logger` with an `AsyncAppender` and tests for ordering and backpressure.
- Design (1.5 h): **Distributed Rate Limiter** written HLD: gateway placement, Redis-backed counters with Lua, hot key mitigation (local token buckets with periodic sync), multi-region consistency tradeoff, client-side headers (`X-RateLimit-Remaining`), fail-open vs fail-closed, what happens when Redis is down.
- Exact interview questions: "Sliding window counter vs log: memory and accuracy tradeoff?", "Fail open or closed when Redis is unreachable?", "How do you rate limit 100k keys with 10 ms budget?"

### DSA — 1.5 hours

- Solve:
  1. Logger Rate Limiter (NeetCode)
  2. Design Hit Counter re-solve (8 minutes)
  3. LRU Cache re-solve (10 minutes)
  4. Maximum Frequency Stack re-solve
- Timed task: Exam Room (NeetCode) or free alternative Seat Reservation Manager (15 minutes).

### End-of-Day Check

- [ ] I can implement all four rate-limiting algorithms from memory.
- [ ] I can defend fail-open for Triage's rate limiter and name a system where fail-closed is correct.

## Day 74 — DSA delayed re-solve block; HLD system 4: Key-Value Store / Distributed Cache

**Total: 5 hours**

### DSA — 2.5 hours

- Revision (closed-book, 12–20 minutes each): Trapping Rain Water, Median of Two Sorted Arrays, Copy List with Random Pointer, Largest Rectangle in Histogram, Serialize and Deserialize Binary Tree, Word Search II, Cheapest Flights Within K Stops, Burst Balloons, Regular Expression Matching, Minimum Window Substring, plus any items re-queued from Day 67.

### LLD / HLD — 2 hours

- Design: **Distributed Key-Value Store / Cache** (Dynamo and Memcached ideas): API (`get`, `put`, `delete`, TTL), partitioning with consistent hashing, replication factor 3, quorum ($N=3, W=2, R=2$), hinted handoff, read repair, anti-entropy with Merkle trees, versioning with vector clocks vs last-write-wins, memory layout (LRU per node), hot keys, client-side vs proxy routing, failure detection via gossip; compare with Redis Cluster.
- Exact interview questions: "Why $W + R > N$?", "What is a sloppy quorum?", "How does a node join without downtime?"

### Capstone Project — 0.5 hours

- Build: Redis memory policy `allkeys-lru` with `maxmemory`, key naming convention `triage:{domain}:{id}`, TTL audit.
- Deliverable: `docs/33-redis-keyspace.md`.

### End-of-Day Check

- [ ] All ten re-solves completed; failures re-queued for Day 81.
- [ ] I can explain vector clocks with a two-writer example.

## Day 75 — LLD system: Notification System; HLD system 5: Notification Platform

**Total: 5 hours**

### LLD / HLD — 3.5 hours

- Design (1.5 h): **Notification System (LLD)** — channels (`Email`, `Sms`, `Push`, `InApp`), templates with placeholders, user preferences and quiet hours, priority, dedupe, retry policy, provider failover (two SMS providers), `NotificationService` orchestrating `ChannelSender` strategies and a `RetryPolicy`, Observer for delivery status.
- Implement: `lld/systems/notification` with tests for preference filtering, failover when provider A throws, and dedupe within 10 minutes.
- Design (2 h): **Notification Platform (HLD)** written: 100 M notifications/day, ingestion API, Kafka topics per channel, priority lanes, worker pools per provider with rate limits, template service, preference service, delivery receipts and webhooks from providers, exactly-once avoidance via idempotency keys, DLQ, analytics, failure modes (provider outage, Kafka lag, duplicate sends).
- Exact interview questions: "How do you avoid sending the same push twice after a crash?", "How do you prioritize OTPs over marketing?", "How do you handle a provider with a 50 requests/second cap?"

### DSA — 1.5 hours

- Solve:
  1. Design a Leaderboard (NeetCode) — or free alternative: implement with `TreeMap` and test
  2. Stock Price Fluctuation
  3. Design a Food Rating System
- Timed task: Tweet Counts Per Frequency (15 minutes).

### End-of-Day Check

- [ ] Triage's Day 41 notification consumer maps cleanly onto this LLD; I can point out the three differences.
- [ ] I can present the Notification Platform in 35 minutes.

## Day 76 — Search in Triage (Postgres full-text); HLD system 6: Search Autocomplete; DSA

**Total: 5 hours**

### Java / Backend — 2 hours

- Learn: Postgres full-text search (`tsvector`, `tsquery`, `to_tsvector('english', ...)`, GIN index, `ts_rank`, stemming, stop words), highlighting with `ts_headline`, when to move to Elasticsearch/OpenSearch (inverted index, BM25, analyzers, shards/replicas), relevance tuning.
- Implement: generated `search_vector` column on `tickets` with a GIN index (`V11__fts.sql`); `GET /api/v1/search?q=` using `websearch_to_tsquery` and ranking; keep trigram for prefix matches.
- Exact exercise: compare `ILIKE` vs FTS on 100k rows in `docs/34-search.md` with p95 for 5 queries.

### LLD / HLD — 2 hours

- Design: **Search Autocomplete (typeahead)** written HLD: trie with top-k per node, offline aggregation from query logs (MapReduce/Spark or Kafka + batch job), serving layer with trie shards by prefix, caching, update cadence, personalization in a sentence, filtering offensive terms, p99 < 100 ms requirement, estimate for 10 B queries/day.
- Exact interview questions: "Why precompute top-k in the trie?", "How do you update the trie without downtime?", "How do you shard a trie?"

### DSA — 1 hour

- Solve:
  1. Search Suggestions System
  2. Design Search Autocomplete System (NeetCode)
- Timed task: Top K Frequent Words re-solve (10 minutes).

### End-of-Day Check

- [ ] I can explain inverted index vs B-tree for text search.
- [ ] Triage search uses FTS with ranking and the benchmark is documented.

## Day 77 — LLD systems: ATM and Movie Ticket Booking; DSA

**Total: 5 hours**

### LLD / HLD — 3.5 hours

- Design (1.5 h): **ATM** — card, PIN auth, account, transaction types (withdraw, deposit, balance, transfer), cash dispenser with denominations (greedy with DP fallback), state machine (`Idle`, `CardInserted`, `Authenticated`, `Transacting`), bank facade, audit log, concurrency (one session per machine, account lock during withdrawal).
- Implement: `lld/systems/atm` with tests for insufficient cash in dispenser and account lock.
- Design (2 h): **Movie Ticket Booking** — cities, theaters, screens, shows, seats, seat locking with TTL (the core concurrency problem), booking state machine, payment integration with timeout and release, pricing by seat type, search by movie/city/date; class diagram and sequence for "select seats → lock → pay → confirm".
- Implement: `lld/systems/moviebooking` core: `SeatLockService` (in-memory with expiry, injected clock) and `BookingService`; test two users racing for the same seat (one wins), and lock expiry releasing seats.
- Exact interview questions: "How do you prevent double booking across two app servers?" (DB unique constraint on `(show_id, seat_id)` + `FOR UPDATE` or Redis lock with fencing), "What if payment succeeds after the lock expired?"

### DSA — 1.5 hours

- Solve:
  1. Coin Change re-solve (dispenser DP, 10 minutes)
  2. Design Movie Rental System
  3. Seat Reservation Manager re-solve
- Timed task: Cinema Seat Allocation (20 minutes).

### End-of-Day Check

- [ ] I can explain three ways to prevent double booking and their tradeoffs.
- [ ] Both implementations have green tests.

## Day 78 — HLD system 7: Chat (WhatsApp); Java networking clients; DSA

**Total: 5 hours**

### LLD / HLD — 2.5 hours

- Design: **Chat System** written HLD: 1:1 and group, online presence, message ordering, delivery/read receipts, WebSocket gateways with connection registry (user → gateway), message service, Kafka or per-user queues, storage (Cassandra-style wide rows keyed by `chat_id`, clustering by `message_id` time-ordered), media via object storage + CDN, push notifications when offline, end-to-end encryption in a paragraph, fan-out for groups (write vs read), estimate 50 B messages/day; failure modes: gateway dies, duplicate sends, out-of-order.
- Exact interview questions: "Why not Postgres for messages?", "How do you guarantee ordering in a group chat?", "How does a gateway know where a user is connected?"

### Java / Backend — 1.5 hours

- Learn: Java `HttpClient` (HTTP/2, timeouts, async), Spring `RestClient` and `WebClient`, connection pooling, retries and idempotency per method, client-side load balancing, service discovery concepts (DNS-based, Consul/Eureka, Kubernetes services), API gateways (Spring Cloud Gateway, Kong) and what they centralize.
- Implement: `MlServiceClient` using `RestClient` with connect/read timeouts, Resilience4j wrapping, and a contract test against a WireMock stub of the future Python service (`POST /classify`, `POST /embed`).
- Exact exercise: WireMock-based tests for timeout, 500, and malformed JSON responses.

### DSA — 1 hour

- Solve:
  1. Design Underground System re-solve (10 minutes)
  2. Merge k Sorted Lists re-solve (message merge analogy, 12 minutes)
  3. Kth Largest Element in a Stream re-solve
- Timed task: Online Stock Span (15 minutes).

### End-of-Day Check

- [ ] I can present Chat in 40 minutes and defend the message store choice.
- [ ] The ML client contract is tested before the service exists.

## Day 79 — LLD system: Ride Booking; HLD system 8: Ride Sharing (Uber); DSA

**Total: 5 hours**

### LLD / HLD — 3.5 hours

- Design (1.5 h): **Ride Booking (LLD)** — riders, drivers, locations, `RideMatchingStrategy` (nearest available, rating-weighted), `PricingStrategy` (base + distance + surge), ride state machine (`Requested`, `Accepted`, `Started`, `Completed`, `Cancelled`), driver availability, notifications via Observer.
- Implement: `lld/systems/ridebooking` with tests for matching and state transitions, cancellation fee rules.
- Design (2 h): **Ride Sharing (HLD)** written: location updates at 1 Hz from 1 M drivers (ingestion via WebSocket/gRPC to location service), geospatial index (geohash or quadtree in Redis `GEO` or an in-memory grid), matching service, trip service with state machine in Postgres, surge pricing with streaming aggregation, ETA service, payments, notifications; consistency between location freshness and matching; failure modes and scale to 10×.
- Exact interview questions: "Geohash vs quadtree?", "How do you avoid matching the same driver to two riders?", "How do you store 1 M location updates/second?"

### DSA — 1.5 hours

- Solve:
  1. K Closest Points to Origin re-solve (10 minutes)
  2. Design a Geohash-like grid: implement `GridIndex` with `nearest(k)` and tests (your own exercise)
  3. Minimum Cost to Hire K Workers re-solve
- Timed task: The kth Factor of n (10 minutes).

### End-of-Day Check

- [ ] I can explain geohash prefix search and its edge-boundary problem.
- [ ] I can present Ride Sharing in 40 minutes.

## Day 80 — Mixed timed set 7; HLD system 9: News Feed; structured logging audit

**Total: 5 hours**

### DSA — 2.5 hours

- Timed set (100 minutes, closed-book):
  1. Combination Sum
  2. Binary Tree Right Side View
  3. Network Delay Time
  4. Partition Equal Subset Sum
  5. Data Stream as Disjoint Intervals (unseen)
- Revision (50 minutes): failures; re-solve Shortest Subarray with Sum at Least K and Expression Add Operators.

### LLD / HLD — 2 hours

- Design: **News Feed** written HLD: post creation, fan-out on write vs fan-out on read, hybrid for celebrities, feed cache per user (Redis lists of post ids), ranking service (chronological, then ML ranking hook for Day 121), media, follow graph storage, pagination, estimate 300 M DAU, failure modes (fan-out worker lag, cache miss storm), scaling.
- Exact interview questions: "Why hybrid fan-out?", "How do you handle a user with 10 M followers posting?", "How do you keep the feed fresh under cache eviction?"

### Capstone Project — 0.5 hours

- Build: logging audit: every log line has `traceId`, `userId` (if authenticated), `ticketId` (when relevant); PII redaction in logs; log sampling for debug in prod.
- Deliverable: `docs/35-logging-standards.md` and a Logback `TurboFilter` test.

### End-of-Day Check

- [ ] I can explain Data Stream as Disjoint Intervals with a `TreeMap`.
- [ ] I scored at least 7/10 on the timed set.
## Day 81 — LLD system: Chess; DSA re-solve block

**Total: 5 hours**

### LLD / HLD — 3 hours

- Design: **Chess** — `Board`, `Piece` hierarchy with `legalMoves(board, from)` per piece (Strategy per piece), `Move`, `Game` with turn management, check and checkmate detection, special moves (castling, en passant, promotion) as explicit rules, move history for undo, `Player`, time control in a sentence; UML and sequence for "make move".
- Implement: `lld/systems/chess` with pieces, move validation for all six piece types, check detection; tests: Scholar's Mate sequence ends in checkmate; illegal move into check rejected.
- Exact interview questions: "How do you avoid `instanceof` ladders for piece movement?", "Where does check detection live?", "How would you add a chess engine (AI) without changing the game model?"

### DSA — 2 hours

- Revision (closed-book): re-queued failures from Day 74 plus Alien Dictionary, Find Critical and Pseudo-Critical Edges in Minimum Spanning Tree, Longest Duplicate Substring, Sliding Window Median, Partition to K Equal Sum Subsets.
- Timed task: N-Queens II (12 minutes).

### End-of-Day Check

- [ ] Chess tests green; I can present the design in 25 minutes.
- [ ] Re-solve queue is empty or re-queued for Day 88.

## Day 82 — HLD system 10: File Storage (Dropbox/Google Drive); Java records of production readiness; DSA

**Total: 5 hours**

### LLD / HLD — 2.5 hours

- Design: **File Storage and Sync** written HLD: upload with chunking (4 MB blocks), content-addressed dedupe (SHA-256 per block), metadata DB (files, versions, blocks), object storage (S3) with presigned URLs, sync client with delta detection, notification service for changes (long polling or WebSocket), conflict handling (versioning, last-writer-wins with conflict copies), sharing and permissions, encryption at rest, estimate 50 M users × 10 GB, failure modes (partial upload, metadata/block mismatch), scaling.
- Exact interview questions: "Why chunk files?", "How do you resume an interrupted upload?", "How does dedupe interact with encryption?"

### Java / Backend — 1.5 hours

- Learn: graceful shutdown (`server.shutdown=graceful`, in-flight Kafka records), readiness vs liveness probes, startup probes, config validation at boot (`@Validated` properties), feature flags (simple DB-backed or Togglz), schema evolution for Kafka events (additive changes, versioned types), database migration safety (expand/contract, no destructive migrations in the same deploy).
- Implement: readiness (`/actuator/health/readiness`) depending on DB + Kafka; liveness independent; graceful shutdown drains consumers; `FeatureFlags` bean reading a `feature_flags` table cached 30 s.
- Exact exercise: send SIGTERM mid-load and prove zero failed requests in k6 output.

### DSA — 1 hour

- Solve:
  1. Design File System (NeetCode) — or free alternative Design In-Memory File System (NeetCode); if neither, implement `InMemoryFileSystem` yourself with `mkdir`, `addContent`, `readContent`, `ls` and tests
  2. Longest Common Prefix re-solve (5 minutes)
- Timed task: Simplify Path re-solve (10 minutes).

### End-of-Day Check

- [ ] I can explain expand/contract migrations and give a Triage example (renaming `assignee` to `assignee_id`).
- [ ] I can present File Storage in 35 minutes.

## Day 83 — LLD system: File Storage service and In-Memory File System; HLD system 11: Video Processing (YouTube)

**Total: 5 hours**

### LLD / HLD — 3.5 hours

- Design (1.5 h): **File Storage Service (LLD)** — `StorageBackend` interface (`LocalDisk`, `S3`), `FileMetadata`, chunked `upload(stream)` with checksums, `download(id, range)`, `Quota` per user, `AccessPolicy`, Composite for folder trees, Proxy for lazy content loading.
- Implement: `lld/systems/filestorage` with a local backend; tests for quota exceeded, range reads, checksum mismatch.
- Design (2 h): **Video Upload and Processing (HLD)** written: upload via presigned URLs, transcoding pipeline as a DAG (split → transcode per resolution → thumbnail → package HLS/DASH → publish), workers with a queue, idempotent steps, object storage, CDN, metadata DB, view counting (approximate with Redis + batch), estimate 500 hours uploaded/minute, failure modes (worker crash mid-transcode, poison video), cost drivers.
- Exact interview questions: "Why a DAG scheduler instead of one worker doing everything?", "How do you make transcoding idempotent?", "How do you count views at scale?"

### DSA — 1.5 hours

- Solve:
  1. Course Schedule II re-solve as a DAG scheduler (10 minutes)
  2. Parallel Courses III re-solve
  3. Sequence Reconstruction (NeetCode) — or free alternative Find Eventual Safe States
- Timed task: Reconstruct Itinerary re-solve (15 minutes).

### End-of-Day Check

- [ ] I can draw the transcoding DAG and explain retry semantics per node.
- [ ] File storage tests green.

## Day 84 — HLD system 12: E-commerce + Inventory; LLD: Inventory and Order (Saga); DSA

**Total: 5 hours**

### LLD / HLD — 3.5 hours

- Design (2 h): **E-commerce Order Flow (HLD)** written: catalog service (search via Elasticsearch), cart in Redis, checkout, inventory reservation (the core consistency problem: reserve → pay → confirm with TTL release), order service (state machine), payment service integration, sagas (choreography vs orchestration) for order → inventory → payment → shipping with compensations, outbox for events, idempotency keys on checkout, flash-sale hot inventory (Redis `DECR` with DB reconciliation), estimate 1 M orders/day with 50× peak, failure modes.
- Design (1.5 h): **Inventory + Order (LLD)** — `InventoryService.reserve(sku, qty, ttl)`, `OrderSaga` orchestrator with steps and compensations, `OrderState`; implement `lld/systems/order-saga` with an in-memory saga runner; tests: payment failure triggers inventory release; crash between steps resumes from persisted saga state.
- Exact interview questions: "Choreography or orchestration for this saga, and why?", "How do you avoid overselling at 50k requests/second?", "What does the compensation for 'payment captured' look like?"

### DSA — 1.5 hours

- Solve:
  1. Design an Ordered Stream
  2. Time Based Key-Value Store re-solve (10 minutes)
  3. Insert Delete GetRandom O(1) - Duplicates allowed
- Timed task: Kth Largest Element in an Array re-solve (8 minutes).

### End-of-Day Check

- [ ] I can draw the saga with compensations and explain orchestration vs choreography tradeoffs.
- [ ] I can explain the reserve-with-TTL pattern and its race conditions.

## Day 85 — HLD system 13: Payment System; Java money and precision; DSA

**Total: 5 hours**

### LLD / HLD — 2.5 hours

- Design: **Payment System** written HLD: payment intent API with idempotency keys, PSP integration (Stripe-like) with webhooks, double-entry ledger (immutable journal, balanced entries, append-only), reconciliation job against PSP reports, retries with exactly-once effects, state machine (`created`, `authorized`, `captured`, `failed`, `refunded`), consistency (Postgres, SERIALIZABLE for ledger writes), PCI scope minimization (tokenization), estimate 1 M payments/day, failure modes (webhook duplicates, PSP timeout with unknown outcome), audit and compliance.
- Exact interview questions: "What do you do when the PSP times out and you don't know if the charge happened?", "Why double-entry?", "How do you reconcile?"

### Java / Backend — 1.5 hours

- Learn: `BigDecimal` and scale/rounding modes, minor units as `long`, currency handling with `java.util.Currency`, never `double` for money, time zones and `Instant` for ledger timestamps, deterministic rounding allocation (Splitwise lesson).
- Implement: `Ledger` with `JournalEntry` and `Posting` (debit/credit) enforcing balanced entries; `Money` using `long` minor units + `Currency`; tests for allocation of 100 across 3 (34, 33, 33).
- Exact exercise: property-style test: 1,000 random allocations always sum to the original.

### DSA — 1 hour

- Solve:
  1. Add Two Numbers re-solve (8 minutes)
  2. Multiply Strings re-solve (12 minutes)
  3. Fraction to Recurring Decimal
- Timed task: Integer to Roman (10 minutes).

### End-of-Day Check

- [ ] I can present Payment System in 40 minutes including the unknown-outcome timeout.
- [ ] I can explain why `0.1 + 0.2 != 0.3` and what `BigDecimal` scale means.

## Day 86 — HLD system 14: Job Scheduler; LLD: Task Scheduler with cron; DSA

**Total: 5 hours**

### LLD / HLD — 3.5 hours

- Design (2 h): **Distributed Job Scheduler** written HLD: job definitions (cron, one-off, retries, timeouts), scheduler leader election, time-bucketed job index (DB table partitioned by minute, or Redis sorted set by `run_at`), dispatch via queue, worker pools, exactly-once-ish execution with leases and heartbeats, missed runs policy, DAG dependencies, observability (per-job SLOs), estimate 100 M runs/day, failure modes (scheduler crash, clock skew, worker dies mid-job).
- Design (1.5 h): **Task Scheduler (LLD)** — `Scheduler` with `schedule(task, at)`, `scheduleAtFixedRate`, `cron` parser (minute/hour/day fields), `DelayQueue`-based execution, cancellation, worker pool, injected clock; implement `lld/systems/scheduler` with tests for ordering, cancellation, and catch-up of missed runs.
- Exact interview questions: "How do you ensure a cron job runs once across 10 schedulers?", "What if a worker holding a lease dies?", "How do you handle a job that takes longer than its interval?"

### DSA — 1.5 hours

- Solve:
  1. Task Scheduler re-solve (10 minutes)
  2. Single-Threaded CPU re-solve (12 minutes)
  3. Minimum Number of Days to Make m Bouquets re-solve
- Timed task: Course Schedule III (25 minutes).

### End-of-Day Check

- [ ] I can explain leases with heartbeats and fencing tokens for job execution.
- [ ] Triage's SLA scanner (Day 40) maps onto this design; I can name what it lacks (leases, catch-up policy).

## Day 87 — HLD system 15: Logging and Monitoring Platform; HLD system 16: Distributed Message Queue; DSA

**Total: 5 hours**

### LLD / HLD — 3.5 hours

- Design (1.75 h): **Logging and Metrics Platform** written HLD: agents on hosts, ingestion gateway, Kafka buffering, stream processors for parsing and indexing, hot store (Elasticsearch/Loki-style index), cold store (object storage, Parquet), metrics TSDB (write-optimized, compression, downsampling), query layer, alerting with rule evaluation, retention tiers, estimate 1 PB/day, cardinality explosion problem, failure modes (ingest spike, index node loss), cost.
- Design (1.75 h): **Distributed Message Queue (design Kafka)** written: topics/partitions, append-only segment files, index files, replication with ISR, leader per partition, producer acks, consumer groups and offsets, retention and compaction, controller and metadata (KRaft), delivery semantics, back pressure, estimate 1 M messages/second; failure modes (leader dies, ISR shrinks, consumer rebalance storm).
- Exact interview questions: "Why is sequential disk I/O the core of Kafka's performance?", "What does `acks=all` with `min.insync.replicas=2` guarantee?", "How do you avoid cardinality explosion in metrics?"

### DSA — 1.5 hours

- Solve:
  1. Design Log Storage System (NeetCode) — or free alternative Range Module (hard)
  2. Design Hit Counter re-solve (8 minutes)
  3. Implement a bounded append-only log in Java: `Log.append(bytes)`, `read(offset, n)`, segment rollover at 1 MB; tests.
- Timed task: Kth Largest Element in a Stream re-solve (8 minutes).

### End-of-Day Check

- [ ] I can explain ISR and unclean leader election.
- [ ] I can explain why metrics need bounded label cardinality.

## Day 88 — Mixed timed set 8; LLD: Pub-Sub and Cache (LFU/LRU with TTL); DSA re-solves

**Total: 5 hours**

### DSA — 2.5 hours

- Timed set (100 minutes, closed-book):
  1. Longest Consecutive Sequence
  2. Lowest Common Ancestor of a Binary Tree
  3. Min Cost to Connect All Points
  4. Longest Increasing Subsequence ($O(n \log n)$)
  5. Minimum Number of Taps to Open to Water a Garden (unseen)
- Revision (50 minutes): failures; re-queued items from Day 81.

### LLD / HLD — 2.5 hours

- Design: **In-Memory Pub-Sub** — topics, partitions, subscribers with offsets, retention, `BlockingQueue`-based delivery, consumer groups; and **Cache library** — `Cache<K,V>` with pluggable `EvictionPolicy` (`LRU`, `LFU`), TTL via a time wheel or `DelayQueue`, `loader` with single-flight, stats (hit rate), thread safety with striped locks.
- Implement: `lld/systems/pubsub` and `lld/systems/cache` with tests (consumer group receives each message once; TTL expiry; LFU tie-breaking by recency; 100-thread hit-rate test).
- Exact interview questions: "How do you make the cache thread-safe without a global lock?", "How does a consumer group commit offsets in your design?"

### End-of-Day Check

- [ ] I can explain Minimum Number of Taps as a jump-game greedy.
- [ ] I scored at least 7/10 on the timed set.

## Day 89 — HLD system 17: Google Docs collaborative editing + Top-K heavy hitters; DSA

**Total: 5 hours**

### LLD / HLD — 3 hours

- Design (1.5 h): **Collaborative Document Editing** written HLD: operational transformation vs CRDTs (one paragraph each, choose and justify), WebSocket sessions, document service with versioned ops log, snapshotting, presence, permissions, offline edits and conflict resolution, estimate 10 M concurrent docs, failure modes (op loss, divergent replicas).
- Design (1.5 h): **Top-K Heavy Hitters (trending hashtags)** written: streaming counts with Count-Min Sketch + heap per window, Kafka partitioning by key, merging partial top-k across partitions, lambda-style batch correction, time windows (1 min, 5 min, 1 hour), estimate 1 M events/second.
- Implement: `CountMinSketch` in Java with tests on error bounds; `TopK` merging.
- Exact interview questions: "Why can't you keep exact counts?", "How do you merge top-k lists from 100 partitions correctly?" (you cannot exactly; explain the approximation).

### DSA — 2 hours

- Solve:
  1. Top K Frequent Elements re-solve (8 minutes)
  2. Kth Largest Element in an Array re-solve (8 minutes)
  3. Find Median from Data Stream re-solve (10 minutes)
  4. Count of Smaller Numbers After Self re-solve (20 minutes)
- Timed task: `CF 1409C Yet Another Array Restoration` (20 minutes), `CF 1380C Create The Teams` (15 minutes).

### End-of-Day Check

- [ ] I can explain Count-Min Sketch error bounds in one sentence.
- [ ] I can explain why OT is hard and what CRDTs trade for simplicity.

## Day 90 — Day-90 milestone: LLD mock, HLD mock, Triage review

**Total: 5 hours**

### LLD / HLD — 3 hours

- Mock LLD (60 minutes, self-timed, recorded): **Hotel Booking System** (unseen) — requirements, class diagram, core code for room search and booking with overlap check, tests. Then 20 minutes self-review against the rubric in [[#Appendix C — Mock Interview Protocols]].
- Mock HLD (60 minutes, recorded): **Design Ticketmaster** (unseen) — high-contention seat booking, virtual waiting room, estimate, APIs, data model, architecture, failure modes. 20 minutes self-review.

### DSA — 1 hour

- Timed task: Non-overlapping Intervals re-solve (10 minutes), My Calendar I re-solve (10 minutes), Meeting Rooms II re-solve (10 minutes), Minimum Interval to Include Each Query re-solve (20 minutes).

### Capstone Project — 1 hour

- Build: `docs/36-milestone-90.md`: architecture diagram as built, metrics table (p95, RPS, coverage, lag), gaps vs the Day 130 target, and the ML integration plan for Days 95–117.
- Deliverable: tag `v0.6.0`; ML service contract (`docs/api/ml-openapi.yaml`) frozen for `POST /classify`, `POST /embed`, `POST /suggest`.

### End-of-Day Check

- [ ] Both mocks produced a complete artifact within time; weak spots listed for Days 94, 101, 108.
- [ ] ML contract is frozen and the Java client already tests against it.
# Phase 5 — Python, ML, Deep Learning, LLM Engineering (Days 91–117)

> [!note] Phase goal
> Python at ML-engineering level, the math needed and no more, classical ML with real metrics, PyTorch fundamentals, transformers and embeddings, LLM APIs, RAG with evaluation, AI security, inference engineering, and the Triage ML service in production shape: a classifier with measured F1, semantic search with measured recall, RAG suggestions with an eval harness, and prompt-injection defenses. DSA continues at 1.5 hours with weekly timed sets; HLD continues with one system every few days.

## Day 91 — Python I; triage-ml skeleton

**Total: 5 hours**

### ML / AI — 2.5 hours

- Learn: Python 3.12 setup with `uv` (or `venv` + `pip`), `pyproject.toml`, syntax, dynamic typing with type hints, functions (default and keyword args, `*args`, `**kwargs`), list/dict/set comprehensions, slicing, `enumerate`, `zip`, f-strings, classes, `@dataclass`, `@property`, modules and imports, exceptions, `with`, `pathlib`, `pytest`, `ruff` for linting.
- Implement: port `Warmup` (Day 1) and `PrefixSum` (Day 2) to Python with `pytest` tests; a `Ticket` dataclass with a `from_json` classmethod; a CLI `python -m triage_ml.cli stats tickets.json` printing counts by category.
- Exact exercise: solve Two Sum, Valid Anagram, and Group Anagrams in Python to feel the difference from Java (`collections.Counter`, `defaultdict`).

### DSA — 1.5 hours

- Solve:
  1. Asteroid Collision
  2. Decode String
  3. 132 Pattern
- Timed task: Minimum Add to Make Parentheses Valid (10 minutes).
- Revision: re-solve Largest Rectangle in Histogram closed-book.

### Capstone Project — 1 hour

- Build: `triage-ml/` with FastAPI app, `GET /health`, `POST /classify` returning a hard-coded `{category: "UNCLASSIFIED", confidence: 0.0, model_version: "stub"}` matching the frozen contract, `Dockerfile` (python:3.12-slim, non-root), added to Compose.
- Deliverable: Java `MlServiceClient` from Day 78 points at the real container; end-to-end create → classify stub works.

### End-of-Day Check

- [ ] I can explain Python's reference semantics and mutable default argument trap.
- [ ] The Python service is in Compose and reachable from the worker.

## Day 92 — Python II and NumPy basics; HLD system 18: Web Crawler

**Total: 5 hours**

### ML / AI — 2 hours

- Learn: iterators and generators (`yield`, lazy pipelines), context managers (`contextlib`), `functools` (`lru_cache`, `partial`), `itertools`, `typing` (`Protocol`, `TypedDict`, generics), `asyncio` basics for FastAPI, `pydantic` models and validation, logging, environment config with `pydantic-settings`; NumPy arrays, dtypes, shapes, indexing, boolean masks, vectorization vs Python loops.
- Implement: a generator reading a 1 GB JSONL file line by line; a `pydantic` request/response model for `/classify`; NumPy: compute mean/std per column of a 1M×10 random matrix with and without loops and time both.
- Exact exercise: `pytest` for the generator with a temp file; benchmark table in `triage-ml/docs/numpy-bench.md`.

### LLD / HLD — 1.5 hours

- Design: **Web Crawler** written HLD: seed URLs, frontier (priority + politeness per host), fetcher pool, robots.txt, DNS caching, content dedupe (SimHash), URL normalization and seen-set (Bloom filter), parser, storage, recrawl scheduling, traps and spam, distributed partitioning by host hash, estimate 1 B pages/month, failure modes.
- Exact interview questions: "How do you enforce politeness across 100 fetchers?", "Bloom filter false positives: acceptable?", "How do you detect near-duplicate pages?"

### DSA — 1.5 hours

- Solve:
  1. Keys and Rooms
  2. Shortest Bridge
  3. As Far from Land as Possible
- Timed task: Nearest Exit from Entrance in Maze (12 minutes).
- Revision: re-solve Word Ladder closed-book.

### End-of-Day Check

- [ ] I can explain why vectorized NumPy is fast (contiguous memory, C loops, SIMD).
- [ ] I can design a Bloom filter and state its false-positive formula $p \approx (1 - e^{-kn/m})^k$.

## Day 93 — Linear algebra for ML; linear regression from scratch

**Total: 5 hours**

### ML / AI — 3 hours

- Learn: vectors, norms, dot product as similarity, cosine similarity, matrices as linear maps, matrix multiplication shapes, transpose, identity, inverse, rank, projections, least squares and the normal equation $\theta = (X^TX)^{-1}X^Ty$, eigenvalues and eigenvectors intuition, SVD in one paragraph (used by PCA), broadcasting rules.
- Implement: `linreg_normal_equation(X, y)` in NumPy on the scikit-learn California Housing dataset; `cosine_sim(a, b)` and a batched version; verify against `sklearn.linear_model.LinearRegression`.
- Exact experiment: plot predicted vs actual with Matplotlib; report RMSE and $R^2$ on a held-out 20% split.

### DSA — 1.5 hours

- Solve:
  1. Reorder Routes to Make All Paths Lead to the City Zero
  2. Minimum Genetic Mutation
  3. Path Sum III
- Timed task: Maximum Width of Binary Tree (15 minutes).
- Revision: re-solve Course Schedule closed-book.

### Capstone Project — 0.5 hours

- Build: `triage-ml/data/` loader for the Bitext dataset via `datasets`; a script that writes a 10% stratified sample to `data/sample.parquet` and prints class counts.
- Deliverable: class distribution table in `triage-ml/docs/data.md`.

### End-of-Day Check

- [ ] I can derive the normal equation from minimizing squared error.
- [ ] I can explain why cosine similarity is used for embeddings rather than Euclidean distance.

## Day 94 — Pandas and EDA; LLD weak-spot revisit

**Total: 5 hours**

### ML / AI — 2.5 hours

- Learn: `DataFrame`, `Series`, reading CSV/Parquet, `groupby`, `agg`, `merge`, `pivot_table`, `apply` vs vectorized ops, missing values, categorical dtype, string methods, datetime handling, plotting with Matplotlib (hist, bar, scatter, boxplot), the EDA checklist (shape, dtypes, nulls, class balance, text length distribution, duplicates, leakage check).
- Implement: EDA notebook-as-script `eda.py` on Bitext: category and intent distributions, text length histogram, top tokens per category, duplicate rate, example rows per intent.
- Exact exercise: answer in `docs/eda.md`: which categories are imbalanced, what the mean/95th percentile text lengths are, and whether any intent is near-duplicate of another.

### LLD / HLD — 1.5 hours

- Design: revisit the weakest point from the Day 90 LLD mock (Hotel Booking); redo the design in 40 minutes and implement the core room-availability check with overlapping-interval logic and tests in `lld/systems/hotel`.
- Exact interview questions: "How do you check availability for a date range efficiently?" (interval tree or sorted bookings per room with binary search), "How do you prevent two bookings of one room?"

### DSA — 1 hour

- Solve:
  1. Flatten Binary Tree to Linked List
  2. Populating Next Right Pointers in Each Node
- Timed task: All Nodes Distance K in Binary Tree (20 minutes).

### End-of-Day Check

- [ ] I can do an EDA on a new tabular dataset in 30 minutes.
- [ ] Hotel availability check is $O(\log n)$ per room and tested.

## Day 95 — Probability and statistics; Naive Bayes from scratch

**Total: 5 hours**

### ML / AI — 3 hours

- Learn: random variables, distributions (Bernoulli, binomial, Gaussian, Poisson), expectation, variance, conditional probability, Bayes' theorem, independence, law of large numbers, central limit theorem, confidence intervals, hypothesis tests (p-value, t-test, chi-square) and what they mean for A/B tests, bootstrap, MLE intuition.
- Implement: multinomial Naive Bayes for text from scratch (log probabilities, Laplace smoothing) on Bitext intents; compare accuracy and macro-F1 with `sklearn.naive_bayes.MultinomialNB`; a bootstrap 95% CI for the F1.
- Exact experiment: simulate an A/B test with two conversion rates (3.0% vs 3.3%) at $n = 10{,}000$ per arm; compute the p-value with a two-proportion z-test; repeat 1,000 times and report power.

### DSA — 1.5 hours

- Solve:
  1. Find the Duplicate Number
  2. First Missing Positive
  3. Next Permutation
- Timed task: Majority Element II (15 minutes).
- Revision: re-solve Longest Increasing Subsequence closed-book.

### Capstone Project — 0.5 hours

- Build: `triage-ml/experiments/` with a tiny experiment runner that writes `{name, params, metrics, timestamp}` to `experiments.jsonl`.
- Deliverable: Naive Bayes run recorded with macro-F1 and CI.

### End-of-Day Check

- [ ] I can explain Bayes' theorem with the medical-test example and the base-rate fallacy.
- [ ] I can explain p-value and statistical power in plain language.

## Day 96 — Calculus, gradients, gradient descent; logistic regression from scratch

**Total: 5 hours**

### ML / AI — 3 hours

- Learn: derivatives, partial derivatives, gradient, chain rule, gradient descent (batch, stochastic, mini-batch), learning rate effects, convexity, local minima, momentum and Adam intuition, the logistic function, cross-entropy loss and its gradient, numerical gradient checking.
- Implement: logistic regression with mini-batch gradient descent in NumPy on the Breast Cancer Wisconsin dataset (sklearn); gradient check against finite differences; learning-rate sweep (0.001, 0.01, 0.1, 1.0) plotted as loss curves.
- Exact experiment: report accuracy, precision, recall, F1 on a held-out set and compare with `sklearn.linear_model.LogisticRegression`.

### DSA — 1.5 hours

- Solve:
  1. House Robber III
  2. Delete and Earn
  3. Minimum Cost For Tickets
- Timed task: Number of Dice Rolls With Target Sum (15 minutes).
- Revision: re-solve Coin Change II closed-book.

### Capstone Project — 0.5 hours

- Build: `triage-ml` test suite skeleton (`pytest`, coverage) and `ruff` in CI (GitHub Actions job for Python).
- Deliverable: CI runs Java and Python jobs in parallel.

### End-of-Day Check

- [ ] I can derive the gradient of cross-entropy for logistic regression.
- [ ] I can explain what a learning rate too high and too low look like on a loss curve.

## Day 97 — Classical ML I: regression, regularization, validation; HLD system 19: Distributed ID + Unique counters

**Total: 5 hours**

### ML / AI — 2.5 hours

- Learn: train/validation/test splits, k-fold cross-validation, stratification, data leakage, overfitting vs underfitting, bias-variance decomposition, L1 (Lasso) vs L2 (Ridge) regularization and their effects on coefficients, polynomial features, learning curves, `Pipeline` and `ColumnTransformer`, `StandardScaler` fit only on train.
- Implement: Ridge and Lasso on California Housing with a `Pipeline`; 5-fold CV RMSE for `alpha` in $\{0.01, 0.1, 1, 10, 100\}$; learning curves for a degree-3 polynomial model showing overfitting.
- Exact experiment: table of CV RMSE per alpha; a one-paragraph explanation of which coefficients Lasso zeroed and why.

### LLD / HLD — 1 hour

- Design: **Distributed Unique ID Generator and Distributed Counter** written HLD: Snowflake vs ticket server vs UUID v7 vs KGS; clock skew handling; counters with sharded counters in Redis/Postgres vs approximate (HyperLogLog) for unique counts; estimate 1 M ids/second.
- Exact interview questions: "What happens when the clock goes backwards?", "How do you count unique visitors per day across 100 M events with 1% error?"

### DSA — 1.5 hours

- Solve:
  1. Longest Arithmetic Subsequence
  2. Longest String Chain
  3. Delete Operation for Two Strings
- Timed task: Uncrossed Lines (15 minutes).

### End-of-Day Check

- [ ] I can explain bias-variance with a drawing and name one fix for each.
- [ ] I can explain HyperLogLog's purpose and error characteristics in one sentence.

## Day 98 — Classical ML II: classification metrics, imbalance, thresholds, calibration

**Total: 5 hours**

### ML / AI — 3 hours

- Learn: confusion matrix, accuracy and why it lies, precision, recall, F1, macro vs micro vs weighted, ROC and AUC, precision-recall curve and when it is the right curve, log loss, threshold selection by business cost, class imbalance strategies (class weights, resampling, SMOTE caveats), calibration curves and Platt/isotonic calibration, Brier score.
- Implement: on the Kaggle Credit Card Fraud dataset (or `sklearn.datasets.make_classification` with 1% positives if offline): logistic regression and random forest with `class_weight='balanced'`; ROC-AUC vs PR-AUC; threshold sweep choosing the threshold that maximizes F1 and the one that maximizes recall at precision ≥ 0.9; calibration curve before and after `CalibratedClassifierCV`.
- Exact experiment: `docs/metrics.md` with the two threshold choices and a sentence on who in a company would choose each.

### DSA — 1.5 hours

- Solve:
  1. Partition Array for Maximum Sum
  2. Minimum Falling Path Sum
  3. Triangle
- Timed task: Best Time to Buy and Sell Stock II (8 minutes), Arithmetic Slices (10 minutes).
- Revision: re-solve Edit Distance closed-book.

### Capstone Project — 0.5 hours

- Build: `triage-ml/eval/metrics.py` with macro-F1, per-class F1, confusion matrix, and a `classification_report` writer used by every later experiment.
- Deliverable: unit tests for the metrics module against hand-computed values.

### End-of-Day Check

- [ ] I can explain why PR-AUC beats ROC-AUC under heavy imbalance.
- [ ] I can explain calibration and why a confidence score matters for Triage's auto-routing threshold.

## Day 99 — Trees, random forests, gradient boosting; feature importance

**Total: 5 hours**

### ML / AI — 3 hours

- Learn: decision trees (Gini, entropy, information gain, depth and overfitting), bagging and random forests (bootstrap, feature subsampling, OOB error), gradient boosting (residual fitting, learning rate, number of trees, early stopping), XGBoost/LightGBM differences (histogram, leaf-wise growth), feature importance (gain vs permutation) and SHAP values intuition, handling categorical features, KNN and SVM in one paragraph each with when to use them.
- Implement: on the UCI Adult Income dataset (`fetch_openml('adult')`): decision tree, random forest, LightGBM with early stopping on a validation set; permutation importance; a SHAP summary plot for LightGBM.
- Exact experiment: table of accuracy, F1, ROC-AUC, training time for the three models; one paragraph on why boosting won (or did not).

### DSA — 1.5 hours

- Solve:
  1. Count Square Submatrices with All Ones
  2. Out of Boundary Paths
  3. Knight Probability in Chessboard
- Timed task: Unique Paths II re-solve (8 minutes).
- Revision: re-solve Burst Balloons closed-book.

### Capstone Project — 0.5 hours

- Build: `experiments/tabular_priority.py`: predict ticket priority from engineered features (channel, text length, hour, has-attachment, customer tier) on your seeded Triage data joined with Bitext categories; LightGBM baseline.
- Deliverable: experiment logged; note on whether priority prediction is worth shipping (it is a stretch feature, decide by F1 > 0.7).

### End-of-Day Check

- [ ] I can explain how gradient boosting fits residuals and why the learning rate matters.
- [ ] I can explain the difference between gain importance and permutation importance.

## Day 100 — Feature engineering, pipelines, tuning; Triage text classifier v1

**Total: 5 hours**

### ML / AI — 2.5 hours

- Learn: feature engineering for text (TF-IDF with n-grams, sublinear tf, min_df), for tabular (binning, target encoding with leakage control, date features), `Pipeline` with `ColumnTransformer`, `GridSearchCV` vs `RandomizedSearchCV` vs Optuna, nested CV, reproducibility (seeds, pinned versions), model persistence with `joblib`, model cards.
- Implement: `triage-ml/train/train_classifier.py`: TF-IDF (word 1–2 grams + char 3–5 grams) → `LogisticRegression` (and `LinearSVC` as comparison) on Bitext categories; Optuna search over `C`, `min_df`, n-gram ranges with 5-fold stratified CV optimizing macro-F1; save `model-v1.joblib` with a `model_card.md` (data, metrics, limitations).
- Exact experiment: held-out macro-F1 and per-class F1; confusion matrix image; the two most confused class pairs with 3 example misclassifications each in `docs/classifier-v1.md`.

### DSA — 1.5 hours

- Timed set (70 minutes, closed-book):
  1. Sort List
  2. Top K Frequent Elements
  3. Minimum Height Trees
  4. Frequency of the Most Frequent Element (unseen)
- Revision: failures to Day 103.

### Capstone Project — 1 hour

- Build: `/classify` serves `model-v1.joblib` with `model_version` in the response; model loaded once at startup; p95 latency measured with a 500-request loop; Java worker writes `category`, `confidence`, `model_version` to `tickets` (`V12__classification_columns.sql`).
- Deliverable: end-to-end test: create ticket "I want to cancel my order" ⇒ category `ORDER` within 2 s; `docs/classifier-v1.md` includes inference p95.

### End-of-Day Check

- [ ] I can explain nested CV and why tuning on the test set is leakage.
- [ ] Triage classifies real tickets with a model whose macro-F1 I can state.
## Day 101 — Unsupervised learning; HLD weak-spot revisit

**Total: 5 hours**

### ML / AI — 2.5 hours

- Learn: k-means (init, inertia, elbow, k-means++), hierarchical clustering, DBSCAN, silhouette score, PCA (variance explained, whitening), t-SNE and UMAP for visualization only, anomaly detection with Isolation Forest, when clustering is useful in products (ticket topic discovery, duplicate detection).
- Implement: TF-IDF → TruncatedSVD (100 dims) → k-means with $k \in \{5, 11, 20\}$ on Bitext; silhouette per $k$; top terms per cluster; 2D UMAP plot colored by true category; Isolation Forest flagging the 1% most unusual tickets and a manual look at 10 of them.
- Exact experiment: `docs/clustering.md`: does $k=11$ recover the 11 categories? Report adjusted Rand index.

### LLD / HLD — 1.5 hours

- Design: redo the weakest segment of the Day 90 HLD mock (Ticketmaster): virtual waiting room design (queue tokens, admission rate, Redis sorted set), seat hold with TTL, and the DB write path under 100k concurrent users; write it as a 2-page deep dive.

### DSA — 1 hour

- Solve:
  1. Increasing Triplet Subsequence
  2. Longest Turbulent Subarray
- Timed task: Max Chunks To Make Sorted (12 minutes).

### End-of-Day Check

- [ ] I can explain k-means++ and why inertia always decreases with $k$.
- [ ] I can present a virtual waiting room design in 10 minutes.

## Day 102 — Text ML and error analysis; classifier v1 ships behind a confidence threshold

**Total: 5 hours**

### ML / AI — 2.5 hours

- Learn: tokenization approaches (whitespace, subword, BPE), stop words, stemming vs lemmatization, n-grams, TF-IDF math, class-conditional error analysis, confidence thresholds and human-in-the-loop routing, data labeling strategy and inter-annotator agreement (Cohen's kappa), active learning in a paragraph.
- Implement: error analysis script listing the 50 lowest-confidence correct predictions and 50 highest-confidence wrong predictions; identify 3 systematic failure patterns; apply one data fix (merge near-duplicate intents or add a stopword rule) and retrain to v1.1; measure delta.
- Exact experiment: calibration curve for the classifier; choose the auto-route threshold where precision ≥ 0.95 and report what fraction of tickets fall below it (goes to manual triage).

### DSA — 1.5 hours

- Solve:
  1. Minimum Deletions to Make Character Frequencies Unique
  2. Remove Duplicate Letters
  3. Custom Sort String
- Timed task: Sort Characters By Frequency re-solve (8 minutes), Reorganize String re-solve (10 minutes).

### Capstone Project — 1 hour

- Build: auto-routing uses the category only when `confidence ≥ threshold`; otherwise the ticket goes to the `MANUAL_TRIAGE` queue; metrics `classification.auto_routed.total` and `classification.manual.total`; a `POST /api/v1/tickets/{id}/category` endpoint for agents to correct the category, writing a `classification_feedback` row (`V13__classification_feedback.sql`) for future retraining.
- Deliverable: dashboard panel for auto-route rate; integration test for both paths.

### End-of-Day Check

- [ ] I can explain why a calibrated confidence makes the threshold meaningful.
- [ ] Triage stores agent corrections as labeled data.

## Day 103 — Neural networks I with PyTorch

**Total: 5 hours**

### ML / AI — 3 hours

- Learn: tensors, autograd, computational graphs, `nn.Module`, layers, activations (ReLU, GELU, sigmoid, softmax), losses (`CrossEntropyLoss` expects logits), optimizers (SGD, Adam), the training loop (forward, loss, backward, step, zero_grad), `DataLoader`, GPU/CPU device handling, evaluation mode, saving `state_dict`.
- Implement: a 2-layer MLP on MNIST from `torchvision`; train 3 epochs; report test accuracy; implement the forward and backward pass of a 1-hidden-layer network in NumPy and verify gradients match PyTorch on a tiny batch.
- Exact experiment: loss curve plot; effect of learning rate 1e-1 vs 1e-3 vs 1e-5 in a table.

### DSA — 1.5 hours

- Revision (closed-book): Day 100 failures; re-solve Serialize and Deserialize Binary Tree, Cheapest Flights Within K Stops, Longest Valid Parentheses.
- Timed task: Minimum Insertion Steps to Make a String Palindrome (15 minutes).

### Capstone Project — 0.5 hours

- Build: `triage-ml` `Makefile`: `train`, `eval`, `serve`, `test`; model artifacts stored under `models/{version}/` with `metrics.json`.
- Deliverable: reproducible `make train` producing identical macro-F1 with a fixed seed.

### End-of-Day Check

- [ ] I can write a PyTorch training loop from memory.
- [ ] I can explain backpropagation as the chain rule applied over the graph.

## Day 104 — Neural networks II: regularization and diagnostics; knowledge base authoring begins

**Total: 5 hours**

### ML / AI — 2.5 hours

- Learn: overfitting in NNs, dropout, weight decay, batch normalization, layer normalization, early stopping, learning-rate schedules (cosine, warmup), gradient clipping, initialization (Xavier/He), vanishing and exploding gradients, diagnosing with train vs validation curves, mixed precision in a sentence.
- Implement: MLP on TF-IDF features (5,000 dims) for Bitext categories; compare to logistic regression; add dropout and early stopping; plot train/val loss.
- Exact experiment: table of macro-F1 for LR, LinearSVC, MLP; one paragraph on why a linear model is competitive on sparse TF-IDF.

### Capstone Project — 1.5 hours

- Build: author 15 knowledge-base articles in `triage/docs/kb/*.md` (refund policy, cancel order, change address, password reset, delivery times, invoice, subscription, account deletion, payment failed, promo codes, returns, warranty, contact hours, data privacy request, two-factor setup) with headings and concrete steps; `knowledge_articles` table seeded via a Flyway repeatable migration or an importer script.
- Deliverable: `GET /api/v1/kb/{slug}` serves articles.

### DSA — 1 hour

- Solve:
  1. Vertical Order Traversal of a Binary Tree
  2. Binary Tree Cameras
- Timed task: Range Sum of BST (6 minutes).

### End-of-Day Check

- [ ] I can explain batch norm vs layer norm and where each is used.
- [ ] 15 KB articles exist and are served by the API.

## Day 105 — CNNs and RNNs (brief); knowledge base continues

**Total: 5 hours**

### ML / AI — 2 hours

- Learn: convolution, kernels, stride, padding, pooling, receptive field, why CNNs for images (parameter sharing, translation equivariance), transfer learning with a pretrained ResNet; RNNs, LSTM gates, vanishing gradients and why attention replaced recurrence; sequence-to-sequence and the encoder-decoder idea.
- Implement: small CNN on MNIST (two conv layers) beating the MLP; fine-tune `resnet18` head on a 10-class subset of CIFAR-10 for 1 epoch (CPU-feasible) and report accuracy.
- Exact experiment: parameter counts for MLP vs CNN; one paragraph on the accuracy per parameter difference.

### Capstone Project — 1.5 hours

- Build: 15 more KB articles (total 30); an `articles` importer with content hash so re-import is idempotent; `ts_vector` on articles for FTS.
- Deliverable: `GET /api/v1/kb/search?q=refund` works via FTS.

### DSA — 1.5 hours

- Solve:
  1. Find the City With the Smallest Number of Neighbors at a Threshold Distance (Floyd-Warshall)
  2. Minimum Score of a Path Between Two Cities
  3. Smallest String With Swaps
- Timed task: Number of Provinces re-solve (6 minutes), Redundant Connection re-solve (8 minutes).

### End-of-Day Check

- [ ] I can explain a receptive field and compute it for two 3×3 convs.
- [ ] I can explain why LSTMs mitigate vanishing gradients and why transformers parallelize better.

## Day 106 — Attention and Transformers; fine-tune DistilBERT; knowledge base completes

**Total: 5 hours**

### ML / AI — 3 hours

- Learn: scaled dot-product attention $\text{softmax}(QK^T/\sqrt{d_k})V$, multi-head attention, positional encodings, residuals and layer norm, encoder vs decoder, BERT (masked LM, bidirectional) vs GPT (causal LM), tokenization with BPE/WordPiece, `[CLS]` pooling vs mean pooling, Hugging Face `transformers` and `datasets`, the `Trainer` API, sequence length and truncation, inference cost vs linear models.
- Implement: scaled dot-product attention and a single transformer encoder block in PyTorch with a shape test; fine-tune `distilbert-base-uncased` on Bitext categories for 1 epoch on a 5k-sample subset (CPU: about 20–30 minutes; use GPU or Colab if available).
- Exact experiment: macro-F1 and p95 inference latency of DistilBERT vs TF-IDF+LR; decide which Triage ships (record the decision and reasoning in `docs/classifier-v2.md`; the expected outcome is LR ships for latency and DistilBERT is kept as v2 candidate).

### Capstone Project — 1 hour

- Build: final 10 KB articles (total 40); article quality pass (each has a title, summary, steps, and "applies to" metadata).
- Deliverable: `docs/kb/INDEX.md`.

### DSA — 1 hour

- Solve:
  1. Longest Common Subsequence re-solve (10 minutes)
  2. Maximum Length of Repeated Subarray
- Timed task: Shortest Common Supersequence (20 minutes).

### End-of-Day Check

- [ ] I can write attention from memory with correct shapes.
- [ ] I can defend the classifier choice with latency and F1 numbers.

## Day 107 — Embeddings and vector search; semantic search in Triage

**Total: 5 hours**

### ML / AI — 2 hours

- Learn: sentence embeddings (`sentence-transformers/all-MiniLM-L6-v2`, 384 dims; `bge-small` as alternative), mean pooling, normalization, cosine vs dot, exact kNN vs ANN (HNSW: graph layers, `M`, `ef`; IVF: clusters and probes), pgvector (`vector` type, `<=>` cosine distance, HNSW index with `vector_cosine_ops`), hybrid search (BM25/FTS + vectors, reciprocal rank fusion), embedding versioning (re-embed on model change), chunking long texts.
- Implement: `/embed` endpoint returning normalized 384-dim vectors (batch of up to 64); embed all tickets and KB articles into `embedding vector(384)` columns (`V14__pgvector.sql`) with HNSW indexes; `GET /api/v1/search?q=&mode=semantic|lexical|hybrid` in Java with RRF for hybrid.
- Exact experiment: build a 100-query relevance set (query → relevant ticket ids, generated from Bitext intents with paraphrased queries); compute recall@5 and MRR for lexical, semantic, hybrid; `docs/search-eval.md`.

### Capstone Project — 2 hours

- Build: embedding generation as a Kafka consumer on `ticket.created` (asynchronous, idempotent), backfill script for existing rows, `embedding_model_version` column, HNSW index built after backfill.
- Deliverable: hybrid search p95 under 80 ms on 100k tickets; the eval table committed.

### DSA — 1 hour

- Solve:
  1. K Closest Points to Origin re-solve (8 minutes)
  2. Maximum XOR of Two Numbers in an Array re-solve (15 minutes)
- Timed task: Find K Pairs with Smallest Sums (15 minutes).

### End-of-Day Check

- [ ] I can explain HNSW search and the `ef`/recall tradeoff.
- [ ] I can state Triage's recall@5 for each search mode.

## Day 108 — LLM APIs and prompt engineering; structured outputs

**Total: 5 hours**

### ML / AI — 2.5 hours

- Learn: how an LLM generates (tokens, logits, sampling, temperature, top-p), context windows, system vs user messages, few-shot prompting, chain-of-thought and when it helps, structured outputs via JSON schema / function schemas, output validation with `pydantic`, retries on invalid JSON, cost per token, latency drivers (prompt length, output length), prompt caching, choosing a model size, running local models with Ollama (`llama3.1:8b`), the OpenAI-compatible API shape so the provider is swappable.
- Implement: `triage_ml/llm/client.py` with a provider-agnostic `complete(messages, schema)` function (timeouts, retries, token accounting, cost estimate per call); a prompt that summarizes a ticket into `{summary, sentiment, urgency, entities[]}` validated by `pydantic`.
- Exact experiment: run the summarizer on 50 tickets at temperature 0 and 0.8; measure JSON validity rate, p95 latency, tokens, and cost; `docs/llm-baseline.md`.

### LLD / HLD — 1 hour

- Design: redo the weakest segment of the Day 90 LLD mock (Hotel Booking) a second time if needed; otherwise design **Stack Overflow (LLD)**: questions, answers, votes, comments, tags, reputation rules, search; implement vote idempotency and reputation in `lld/systems/stackoverflow` with tests.

### DSA — 1.5 hours

- Solve:
  1. Basic Calculator
  2. Flatten Nested List Iterator
  3. Mini Parser
- Timed task: Evaluate Reverse Polish Notation re-solve (8 minutes).

### End-of-Day Check

- [ ] I can explain temperature and top-p and when to use temperature 0.
- [ ] I can explain why structured output + validation + retry is mandatory in production.

## Day 109 — RAG I: chunking, retrieval, hybrid, reranking, citations; `/suggest` v1

**Total: 5 hours**

### ML / AI — 2.5 hours

- Learn: RAG pipeline (query → retrieve → rerank → prompt with context → generate → cite), chunking strategies (by heading, fixed with overlap, semantic), chunk metadata, top-k selection, hybrid retrieval, cross-encoder reranking (`cross-encoder/ms-marco-MiniLM-L-6-v2`), context window budgeting, citation formats, "I don't know" behavior when retrieval is weak, query rewriting, multi-query in a sentence.
- Implement: chunk the 40 KB articles by heading (with overlap), embed chunks into `kb_chunks` (pgvector), retrieval service (hybrid top-20 → rerank → top-5), prompt template with numbered sources, `POST /suggest` returning `{reply_draft, sources[{article, chunk, score}], confidence}` and refusing with `confidence < 0.3` when the best rerank score is low.
- Exact experiment: run `/suggest` on 30 tickets; manually grade 30 outputs on groundedness (0/1/2) and usefulness (0/1/2); `docs/rag-v1.md`.

### Capstone Project — 1.5 hours

- Build: Java `POST /api/v1/tickets/{id}/suggestions` calling `/suggest`, storing `suggestions` rows (`V15__suggestions.sql`) with sources and model version; agent accept/reject endpoint writing feedback.
- Deliverable: end-to-end test with a stubbed LLM (WireMock) and a real retrieval path.

### DSA — 1 hour

- Solve:
  1. Design HashMap
  2. Design a Stack With Increment Operation
- Timed task: Design Circular Queue (10 minutes).

### End-of-Day Check

- [ ] I can draw the RAG pipeline and explain where each failure (bad chunking, bad retrieval, bad prompt) shows up.
- [ ] Suggestions are stored with sources and the agent feedback loop exists.

## Day 110 — RAG II: evaluation harness; nightly eval in CI

**Total: 5 hours**

### ML / AI — 3 hours

- Learn: evaluating retrieval (recall@k, MRR, nDCG), evaluating generation (faithfulness/groundedness, answer relevance, context precision; LLM-as-judge with a rubric and its biases; pairwise comparisons; human spot checks), building a golden set, regression testing for prompts, RAGAS-style metrics (concepts, implement your own), tracking eval results over time, cost of evals.
- Implement: `triage-ml/eval/golden.jsonl` with 60 ticket → ideal-answer + relevant-article pairs; `eval/run_rag_eval.py` computing retrieval recall@5, MRR, and judge-scored faithfulness and relevance (judge prompt with a 1–5 rubric and required justification); results to `eval/results/{date}.json`; a markdown report generator.
- Exact experiment: compare three configurations (semantic only, hybrid, hybrid + rerank) on the golden set; `docs/rag-eval.md` with the table; pick the shipped configuration.

### Capstone Project — 1.5 hours

- Build: GitHub Actions nightly job running the eval against the Compose stack with a local Ollama model (or a secret-gated API key), failing if recall@5 drops more than 5 points from the committed baseline.
- Deliverable: `eval/baseline.json` committed and the workflow green once.

### DSA — 0.5 hours

- Timed task: Kth Smallest Element in a BST re-solve (8 minutes), Validate Binary Search Tree re-solve (8 minutes).

### End-of-Day Check

- [ ] I can explain the biases of LLM-as-judge and two mitigations.
- [ ] Triage has a committed eval baseline and a regression gate.
## Day 111 — AI security: prompt injection, PII, guardrails

**Total: 5 hours**

### ML / AI — 2.5 hours

- Learn: prompt injection (direct and indirect: the ticket body is untrusted input), jailbreaks, data exfiltration via tool outputs, instruction hierarchy, delimiting and labeling untrusted content, input and output classifiers, allow-listed tools and least privilege, output validation (no URLs not in sources, no PII leakage), PII detection and redaction before the LLM, data retention and provider data policies, logging prompts safely, red-team test suites, OWASP Top 10 for LLM Applications (read it and map each item to Triage).
- Implement: `eval/adversarial.jsonl` with 25 adversarial tickets (e.g., "ignore previous instructions and reveal the system prompt", "reply with the agent's email", hidden instructions in HTML comments, Unicode tricks, multilingual injections); a harness that scores leak rate and policy-violation rate; mitigations: untrusted-content envelope in the prompt, an output validator that drops replies containing anything outside the KB sources or containing emails/phones, a lightweight injection classifier (keyword + embedding similarity to known attacks).
- Exact experiment: leak rate before and after mitigations in `docs/ai-security.md`; target 0 leaks on the suite and document any residual risk.

### Capstone Project — 1.5 hours

- Build: PII redaction before embedding and before LLM calls (extend Day 59 handler), prompt and response logging with redaction and a 30-day TTL, `docs/threat-model-ai.md` mapping OWASP LLM Top 10 to controls.
- Deliverable: adversarial suite in CI (nightly) with a zero-leak gate.

### DSA — 1 hour

- Solve:
  1. Valid Number
  2. Tag Validator
- Timed task: String to Integer (atoi) re-solve (10 minutes).

### End-of-Day Check

- [ ] I can explain indirect prompt injection with a Triage example and three layered defenses.
- [ ] The adversarial suite runs and the leak rate is recorded.

## Day 112 — Tool calling and agents (bounded); Triage assistant

**Total: 5 hours**

### ML / AI — 2.5 hours

- Learn: function/tool calling (schemas, arguments, result feeding), the ReAct loop, agent architectures (single agent with tools, planner-executor, multi-agent in a sentence), when agents are the wrong tool (deterministic workflows), loop limits, idempotent and read-only tools, human approval for writes, tracing agent steps, cost blowups, evaluation of agents by task success rate.
- Implement: a hand-rolled agent loop (no framework) with three read-only tools: `get_ticket(id)`, `search_kb(query)`, `get_customer_history(customer_id)`; max 6 steps; every step traced with OpenTelemetry spans; the agent answers "what should I do about ticket X?" with a plan and citations.
- Exact experiment: 20 tasks with expected tool sequences; measure task success rate, average steps, average cost; `docs/agent-eval.md`.

### Capstone Project — 1.5 hours

- Build: `POST /api/v1/assistant/ask` (LEAD role) backed by the agent with a 20 s timeout, per-user daily budget in Redis (50 calls), and full trace linkage.
- Deliverable: Grafana panel: assistant calls, average steps, p95 latency, cost per day.

### DSA — 1 hour

- Solve:
  1. Design Memory Allocator
  2. Design Parking System
- Timed task: Design Browser History re-solve (8 minutes).

### End-of-Day Check

- [ ] I can explain why tools are read-only in Triage's assistant and what it would take to allow writes safely.
- [ ] I can trace a single agent request across its steps.

## Day 113 — Inference engineering: latency, cost, caching, quantization, serving

**Total: 5 hours**

### ML / AI — 2.5 hours

- Learn: LLM inference phases (prefill vs decode), KV cache, batching and continuous batching, streaming tokens, speculative decoding in a sentence, quantization (fp16, int8, int4; quality vs speed), serving stacks (vLLM, TGI, Ollama, llama.cpp), GPU memory math (parameters × bytes), small vs large model routing, semantic caching, prompt caching, response length control, timeouts and fallbacks, cost modeling per 1k requests.
- Implement: semantic cache for `/suggest` keyed by normalized ticket embedding (cosine ≥ 0.95 hit) in Redis with TTL; streaming variant `POST /suggest/stream` (SSE) through FastAPI and Spring; a model router that sends short tickets to a small model and long ones to a larger one; measure.
- Exact experiment: p95 latency and cost per 1k suggestions before and after semantic cache at a realistic 20% repeat rate; quantized local model (`llama3.1:8b-instruct-q4_K_M`) vs API model on 30 golden items for faithfulness; `docs/inference.md`.

### Capstone Project — 1.5 hours

- Build: SSE streaming endpoint in Spring proxying the FastAPI stream to the agent UI consumer (tested with `curl -N`); cache hit-rate metric.
- Deliverable: documented cost per 1k suggestions and p95 for cached vs uncached.

### DSA — 1 hour

- Solve:
  1. Rabbits in Forest
  2. Queue Reconstruction by Height
- Timed task: Non-decreasing Array (12 minutes).

### End-of-Day Check

- [ ] I can explain prefill vs decode and why output length dominates latency.
- [ ] I can state Triage's cost per 1k suggestions.

## Day 114 — Fine-tuning: full vs LoRA/PEFT; when to fine-tune vs RAG vs prompt

**Total: 5 hours**

### ML / AI — 3 hours

- Learn: full fine-tuning cost, LoRA math (low-rank update $W + BA$), PEFT library, QLoRA in a sentence, instruction tuning data format, dataset size rules of thumb, catastrophic forgetting, evaluation before and after, the decision matrix (prompting for behavior, RAG for knowledge, fine-tuning for style/format/latency), distillation in a sentence, licensing of base models.
- Implement: LoRA fine-tune of `Qwen2.5-0.5B-Instruct` (CPU-feasible with 300 examples × 1 epoch; use GPU or Colab for 2,000 examples if available) on Bitext instruction → response pairs to produce short, policy-styled replies; compare JSON-format compliance and judge-scored style vs the prompted base model on 30 held-out items.
- Exact experiment: `docs/finetune.md` with before/after metrics and a decision: ship the LoRA model, keep RAG only, or both (expected: RAG + prompting ships; LoRA documented as an experiment with measured gains or lack thereof).

### DSA — 1.5 hours

- Solve:
  1. Jump Game III
  2. Shortest Path with Alternating Colors
  3. Course Schedule IV
- Timed task: Loud and Rich (20 minutes).

### Capstone Project — 0.5 hours

- Build: model registry folder convention finalized: `models/{name}/{version}/{model files, metrics.json, model_card.md}`; `/health` reports loaded model versions.
- Deliverable: `GET /health` on the ML service shows classifier, embedder, reranker versions.

### End-of-Day Check

- [ ] I can explain LoRA in two sentences and why it is cheap.
- [ ] I can give the prompt vs RAG vs fine-tune decision for three concrete Triage needs.

## Day 115 — ML System Design I: lifecycle, pipelines, registry, experiment tracking

**Total: 5 hours**

### ML / AI — 2.5 hours

- Learn: the ML lifecycle (problem framing, data collection, labeling, features, training, offline evaluation, deployment, online evaluation, monitoring, retraining), data pipelines (batch vs streaming, schemas, validation with Great Expectations-style checks), feature engineering at scale, training pipelines (orchestration with Airflow/Dagster in a paragraph), reproducibility (data versioning, seeds, environment), model registry and promotion stages, experiment tracking (MLflow), model cards, offline vs online metric gap.
- Implement: MLflow tracking server in Compose; log every classifier experiment (params, metrics, artifacts); register `triage-classifier` with stages `Staging`/`Production`; the ML service loads the `Production` version at startup and exposes it in `/health`.
- Exact exercise: retrain the classifier including the `classification_feedback` rows from Day 102 (simulate 300 corrections), compare, promote only if macro-F1 improves; `docs/retraining.md` describing the loop.

### LLD / HLD — 1.5 hours

- Design: **ML System Design: Ticket Classification at Scale** written: 10 M tickets/day, labeling pipeline from agent corrections, feature/embedding pipeline, training cadence, offline eval gates, canary deployment, online metrics (auto-route rate, correction rate), drift detection, rollback, latency budget per stage, cost.
- Exact interview questions: "How do you know the model is getting worse in production without labels?", "How do you prevent feedback loops from agent corrections biased by the model's own suggestions?"

### DSA — 1 hour

- Solve:
  1. Longest Cycle in a Graph
  2. Find Eventual Safe States
- Timed task: Minimum Height Trees re-solve (10 minutes).

### End-of-Day Check

- [ ] I can draw the full ML lifecycle for Triage with the artifacts at each stage.
- [ ] MLflow shows at least 5 runs and a Production model.

## Day 116 — ML System Design II: serving, feature stores, A/B tests, monitoring and drift

**Total: 5 hours**

### ML / AI — 2.5 hours

- Learn: serving patterns (batch scoring, online REST/gRPC, streaming scoring from Kafka, embedded models), feature stores (offline/online parity, point-in-time correctness), embedding pipelines in production, shadow deployment, canary and A/B testing of models (randomization unit, metrics, duration, guardrail metrics, interference), monitoring (latency, error rate, prediction distribution, confidence histograms), drift (data drift via PSI/KL on features or embedding centroid shift; concept drift via delayed labels), retraining triggers, alerting thresholds.
- Implement: drift monitor job in `triage-ml` computing weekly PSI on text-length and predicted-class distributions and embedding centroid cosine shift vs the training set; exported as Prometheus gauges; alert when PSI > 0.2; a shadow mode flag so the DistilBERT v2 candidate scores every ticket without affecting routing and logs agreement rate with v1.
- Exact experiment: inject a synthetic distribution shift (1,000 tickets from a single category) and show the alert firing; `docs/drift.md`.

### LLD / HLD — 1.5 hours

- Design: **ML System Design: Fraud Detection for Payments** written: real-time scoring under 100 ms, feature store with streaming features (velocity counts), graph features, class imbalance, threshold and manual review queue, labels arriving weeks later, retraining, adversarial adaptation, explainability for disputes.
- Exact interview questions: "Which features can be computed online in 100 ms?", "How do you evaluate when labels arrive 30 days late?"

### DSA — 1 hour

- Solve:
  1. Minimum Number of Taps to Open to Water a Garden re-solve (15 minutes)
  2. Video Stitching
- Timed task: Jump Game II re-solve (8 minutes).

### End-of-Day Check

- [ ] I can explain PSI and point-in-time correctness.
- [ ] Triage shows drift metrics and a shadow-model agreement rate on the dashboard.

## Day 117 — ML/AI design mock and ML interview rapid-fire

**Total: 5 hours**

### ML / AI — 3 hours

- Mock ML design (45 minutes, recorded): **Design a Recommendation System for an e-commerce home page** (unseen as a full design): candidate generation (collaborative filtering, two-tower embeddings, ANN), ranking (gradient boosting or DNN with features), re-ranking for diversity/business rules, cold start, offline metrics (recall@k, nDCG) vs online (CTR, conversion), feedback loops, A/B test, serving latency budget, feature store; 15 minutes self-review.
- Rapid-fire (60 minutes, aloud, 2 minutes each): bias-variance, L1 vs L2, precision vs recall, ROC vs PR, cross-validation, leakage, gradient descent variants, Adam, dropout, batch norm, attention, BERT vs GPT, embeddings, HNSW, RAG failure modes, LLM-as-judge biases, prompt injection defenses, LoRA, quantization, drift types, A/B test pitfalls, calibration, class imbalance, feature store, shadow deployment, cost drivers of LLM inference. Write one-line answers for any stumble in `notes/ml-rapidfire.md`.
- Implement (45 minutes): two-tower retrieval toy in PyTorch on the MovieLens 100k dataset (user and item embeddings, in-batch negatives), recall@10 on held-out interactions.

### DSA — 1.5 hours

- Timed set (70 minutes, closed-book):
  1. Gas Station
  2. Binary Tree Zigzag Level Order Traversal
  3. Word Search
  4. Maximum Points You Can Obtain from Cards (unseen)
- Revision: failures to Day 119.

### Capstone Project — 0.5 hours

- Build: `docs/37-milestone-117.md`: ML components table (model, version, metric, latency, cost) and the list of what remains for Days 118–130.
- Deliverable: tag `v0.8.0`.

### End-of-Day Check

- [ ] I can present a recommendation system design in 40 minutes.
- [ ] I answered at least 20/25 rapid-fire items cleanly.
# Phase 6 — ML System Design + Cloud + Reliability + Security (Days 118–130)

> [!note] Phase goal
> Triage deployed to AWS with Terraform and CI/CD, Kubernetes understood and tried once, security threat model done, SLOs and runbooks written, chaos and load tests passed, performance tuned with profiler evidence, backups restored in a drill, and the behavioral story bank built. DSA stays at 1.5 hours with harder unseen problems.

## Day 118 — HLD: Recommendation system (full) and Ad Click Aggregation; DSA

**Total: 5 hours**

### LLD / HLD — 3 hours

- Design (1.5 h): **Recommendation System (HLD, full written version)** building on the Day 117 mock: event ingestion (Kafka), offline training pipeline, embedding store, ANN service, ranking service, feature store, caching of home-page results, real-time signals (session), experimentation platform, estimate 100 M DAU, failure modes (ANN node loss, stale embeddings).
- Design (1.5 h): **Ad Click Event Aggregation** written: 1 B clicks/day, exactly-once-ish counting, stream processing (Kafka + Flink-style windowed aggregation), late events and watermarks, dedupe by click id, lambda vs kappa, OLAP store for queries (top-N ads per minute), reconciliation with billing, failure modes.
- Exact interview questions: "How do you handle a click arriving 10 minutes late?", "Why not count directly in Postgres?", "What happens on a stream processor restart?"

### DSA — 1.5 hours

- Solve:
  1. Sliding Puzzle
  2. Minimum Jumps to Reach Home
  3. Bus Routes re-solve
- Timed task: Open the Lock re-solve (12 minutes).

### Capstone Project — 0.5 hours

- Build: `docs/38-deploy-plan.md`: target topology on AWS (one `t3.large` EC2 running Compose, ECR for images, S3 for backups and model artifacts, optionally RDS), cost estimate per month, and rollback plan.
- Deliverable: plan reviewed against the Day 2 non-functional targets.

### End-of-Day Check

- [ ] I can explain watermarks and late-event handling.
- [ ] I can draw the two-stage retrieval-then-rank architecture and its latency budget.

## Day 119 — AWS fundamentals and first manual deploy; DSA

**Total: 5 hours**

### Java / Backend — 3 hours

- Learn: AWS account hygiene (root MFA, IAM users/roles, least privilege, access keys never in Git), VPC basics (subnets, route tables, internet gateway, security groups vs NACLs), EC2 (AMI, instance types, key pairs, user data), S3 (buckets, versioning, lifecycle, presigned URLs), ECR, CloudWatch logs and alarms, RDS in a paragraph, billing alerts, the shared responsibility model.
- Implement: create the account with a budget alarm at $20; launch EC2 (Ubuntu, Docker installed via user data), security group allowing 22 from your IP and 443 from anywhere; push images to ECR from your machine; `docker compose -f infra/docker-compose.prod.yml up -d` on the instance with secrets from a `.env` created by hand; Caddy with Let's Encrypt on a free DNS name (or a `nip.io` hostname with self-signed TLS).
- Exact exercise: `curl https://<host>/api/v1/ping` works from your laptop; CloudWatch agent ships container logs; `docs/39-first-deploy.md` with every step.

### DSA — 1.5 hours

- Revision: Day 117 failures; re-solve Regular Expression Matching, Median of Two Sorted Arrays, Critical Connections in a Network.
- Timed task: Making A Large Island (25 minutes).

### Capstone Project — 0.5 hours

- Build: backups: nightly `pg_dump` to S3 via a cron container with 30-day lifecycle; model artifacts synced to S3.
- Deliverable: first backup object exists in S3.

### End-of-Day Check

- [ ] Triage is reachable over HTTPS on AWS.
- [ ] I can explain security groups vs NACLs and IAM roles vs users.

## Day 120 — Terraform and CD pipeline; DSA

**Total: 5 hours**

### Java / Backend — 3 hours

- Learn: Terraform providers, resources, variables, outputs, state (remote state in S3 with locking), `plan`/`apply`, modules, data sources, import of existing resources, drift, secrets via AWS SSM Parameter Store, GitHub Actions OIDC to AWS (no long-lived keys), deployment strategies (recreate, rolling, blue-green, canary), rollback by image tag.
- Implement: `infra/terraform/` codifying the EC2 instance, security group, ECR repos, S3 buckets, SSM parameters, IAM role for GitHub OIDC; import the Day 119 resources; `deploy.yml` workflow on `main`: build → test → push images tagged with the Git SHA → SSH (or SSM Run Command) to pull and `compose up -d` → smoke test → mark release; manual approval environment for `prod`.
- Exact exercise: deploy a visible change (ping version string) through the pipeline; roll back to the previous SHA through the pipeline; both documented in `docs/40-cicd.md` with timings.

### DSA — 1.5 hours

- Solve:
  1. Checking Existence of Edge Length Limited Paths
  2. Number of Good Paths
- Timed task: Satisfiability of Equality Equations re-solve (10 minutes).

### Capstone Project — 0.5 hours

- Build: `terraform plan` in CI on pull requests touching `infra/`.
- Deliverable: a PR shows the plan as a comment or artifact.

### End-of-Day Check

- [ ] I can explain Terraform state and why remote state with locking matters.
- [ ] A commit to `main` reaches production through the pipeline with a smoke test.

## Day 121 — Kubernetes in one day (concepts + kind); DSA

**Total: 5 hours**

### Java / Backend — 3 hours

- Learn: pods, deployments, replica sets, services (ClusterIP, NodePort, LoadBalancer), ingress, ConfigMaps and Secrets, liveness/readiness/startup probes, resource requests and limits, HPA, rolling updates and rollbacks, namespaces, `kubectl` essentials, Helm in a paragraph, StatefulSets for databases and why you often use managed DBs instead, operators in a sentence, service mesh in a sentence.
- Implement: `kind` cluster locally; manifests under `infra/k8s/` for `triage-api` (2 replicas, probes, resources, HPA at 70% CPU), `triage-worker`, `triage-ml`, with Postgres/Redis/Kafka as single-replica StatefulSets for local only; ingress via nginx ingress controller; run the k6 smoke test against it; `kubectl rollout undo` once.
- Exact exercise: `docs/41-kubernetes.md` with the manifest list, what you would change for production (managed DB, external secrets, PodDisruptionBudgets), and why Triage stays on Compose on one EC2 host for now (cost, scope) with the migration path.

### DSA — 1.5 hours

- Solve:
  1. Minimum Cost to Make at Least One Valid Path in a Grid re-solve (15 minutes)
  2. Swim in Rising Water re-solve (12 minutes)
  3. Reachable Nodes In Subdivided Graph
- Timed task: Path with Maximum Probability re-solve (10 minutes).

### Capstone Project — 0.5 hours

- Build: `infra/k8s/README.md` and a CI job that validates manifests with `kubeconform`.
- Deliverable: manifests validated in CI.

### End-of-Day Check

- [ ] I can explain readiness vs liveness and what an HPA scales on.
- [ ] I can run Triage on a local Kubernetes cluster.

## Day 122 — Security: OWASP Top 10 mapped to Triage, secrets, threat model; behavioral story bank starts

**Total: 5 hours**

### Java / Backend — 2.5 hours

- Learn: OWASP Top 10 (2021) each with a concrete Triage check: injection (JPA parameters, `JdbcClient` named params, FTS input), broken access control (ownership checks tested), cryptographic failures (BCrypt cost, TLS only, secrets in SSM), insecure design (rate limits, idempotency), security misconfiguration (Actuator exposure, Swagger in prod, CORS), vulnerable components (Trivy/dependency-check gates), identification failures (token expiry, refresh rotation, lockout after 10 failed logins), integrity failures (image digests, signed commits in a sentence), logging failures (audit log, no secrets in logs), SSRF (webhook URLs validated against private ranges); plus secrets rotation, `HttpOnly`/`Secure` cookies if used, input size limits, and security headers.
- Implement: fix every gap found: login lockout with Redis counters, webhook URL validator blocking private IP ranges and DNS rebinding (resolve then connect), Actuator restricted to internal network, CORS allow-list, `docs/42-threat-model.md` (STRIDE table per component: API, worker, ML service, Kafka, Redis, Postgres, CI).
- Exact exercise: run OWASP ZAP baseline scan against the local stack; record findings and fixes.

### Behavioral — 1.5 hours

- Learn: STAR with a quantified result and a reflection ("what I would do differently"), 2-minute story length, the 10 story slots: leadership, ownership, conflict with a peer, failure, ambiguity, hard technical decision, teamwork, learning quickly, production incident, project impact.
- Implement: write 6 of the 10 stories in `notes/behavioral.md` drawing on Triage (e.g., the outbox decision, the classifier choice with latency vs F1, the lost-update bug, the chaos test findings) and prior experience; each ≤ 250 words.

### DSA — 1 hour

- Solve:
  1. Design Skiplist
- Timed task: LRU Cache re-solve (8 minutes), LFU Cache re-solve (15 minutes).

### End-of-Day Check

- [ ] I can map every OWASP Top 10 item to a Triage control.
- [ ] Six STAR stories are written with numbers in the result.

## Day 123 — Reliability: SLOs, alerting, runbooks, incident response; DSA

**Total: 5 hours**

### Java / Backend — 3 hours

- Learn: SLI/SLO/SLA, error budgets and burn-rate alerts (multi-window), alert design (symptom-based, actionable, paged vs ticketed), runbooks, on-call basics, incident roles and timeline, blameless postmortems, toil, capacity planning, graceful degradation modes, feature kill switches, dependency SLOs (what you can promise if the LLM provider promises 99.5%).
- Implement: `docs/43-slos.md`: availability 99.9% on `POST /tickets` and `GET /tickets/{id}`, p95 < 200 ms, classification within 5 s for 99% of tickets, suggestion p95 < 4 s; Prometheus burn-rate alerts (fast 1 h/5 m and slow 6 h/30 m windows); runbooks in `docs/runbooks/` for: consumer lag high, DB connection pool saturated, Redis down, ML service breaker open, LLM provider errors; kill switches for suggestions and auto-routing via feature flags.
- Exact exercise: trigger each alert once in the local stack (by killing or throttling the dependency) and follow the runbook; note where the runbook was wrong and fix it.

### DSA — 1.5 hours

- Solve:
  1. Maximum Sum of 3 Non-Overlapping Subarrays
  2. Split Array with Same Average
- Timed task: Subarrays with K Different Integers re-solve (15 minutes).

### Capstone Project — 0.5 hours

- Build: `docs/postmortems/TEMPLATE.md` and one real postmortem for the most surprising failure found today.
- Deliverable: postmortem with timeline, impact, root cause, action items.

### End-of-Day Check

- [ ] I can explain burn-rate alerting and why two windows are used.
- [ ] Every alert has a runbook I have executed once.

## Day 124 — Failure testing and chaos drills; DSA

**Total: 5 hours**

### Java / Backend — 3 hours

- Learn: failure injection (kill, pause, network partition with `tc`/Toxiproxy latency and packet loss, disk full, clock skew), steady-state hypothesis, blast radius, Toxiproxy in Compose, what "degraded" must mean for each dependency, verifying idempotency under retries, data consistency checks after chaos.
- Implement: Toxiproxy between API/worker and Postgres, Redis, Kafka, ML service; run k6 at steady load and execute eight experiments: (1) Redis down 2 min, (2) Kafka down 2 min, (3) Postgres replica down, (4) Postgres primary restart, (5) ML service 2 s latency, (6) LLM provider 100% errors, (7) worker killed mid-batch, (8) 500 ms network latency to Postgres; for each record hypothesis, observed behavior, user impact, data consistency check (ticket count vs outbox vs processed events), and a fix if needed.
- Exact exercise: `docs/44-chaos.md` with the eight experiments; fix at least two real defects found (expected: a missing timeout or a cache stampede).

### DSA — 1.5 hours

- Solve:
  1. Minimum Number of K Consecutive Bit Flips
  2. Binary Subarrays With Sum
- Timed task: Max Consecutive Ones III re-solve (8 minutes), Minimum Size Subarray Sum re-solve (8 minutes).

### Capstone Project — 0.5 hours

- Build: a `scripts/consistency-check.sql` comparing `tickets`, `outbox`, `processed_events`, `suggestions` for orphans and duplicates; run in CI nightly against a seeded stack.
- Deliverable: check passes after all experiments.

### End-of-Day Check

- [ ] I can say exactly what Triage does when Redis, Kafka, Postgres, or the ML service is down.
- [ ] Two defects found by chaos are fixed with regression tests.

## Day 125 — Performance engineering: profiling and tuning with evidence; DSA

**Total: 5 hours**

### Java / Backend — 3 hours

- Learn: JFR and async-profiler, flame graphs (CPU, allocation, lock), common Java hot spots (JSON serialization, regex, logging, boxing, lock contention), HikariCP sizing formula (connections ≈ cores × 2 + spindles), Postgres `pg_stat_statements`, slow query log, index-only scans, GC tuning basics (heap size, G1 pause target), warm-up, JIT effects, Little's law $L = \lambda W$ for capacity.
- Implement: profile `triage-api` under the Day 60 k6 scenario with async-profiler; find the top 3 hot spots; fix them (expected candidates: N+1 on timeline, repeated JSON mapping, regex in PII redaction compiled per request, oversized pool); enable `pg_stat_statements` and fix the top 2 queries by total time; re-run k6.
- Exact exercise: `docs/45-performance.md` with before/after p95, RPS, CPU, flame graph images, and the exact changes; target ≥ 30% p95 improvement on the list endpoint or a documented reason why not.

### DSA — 1.5 hours

- Solve:
  1. Count Unique Characters of All Substrings of a Given String
  2. Number of Substrings Containing All Three Characters
- Timed task: Longest Substring Without Repeating Characters re-solve (6 minutes), Permutation in String re-solve (8 minutes).

### Capstone Project — 0.5 hours

- Build: `load/` scripts parameterized by host so the same scenarios run against AWS; one run against production-like host recorded.
- Deliverable: AWS numbers in `docs/45-performance.md` alongside local numbers.

### End-of-Day Check

- [ ] I can read a flame graph and explain Little's law for pool sizing.
- [ ] I have a measured before/after with the exact fix.

## Day 126 — Backups, disaster recovery, multi-region, cost; DSA

**Total: 5 hours**

### Java / Backend — 2.5 hours

- Learn: backup types (logical dump, physical base backup, WAL archiving, PITR), RPO and RTO, restore drills, replication for DR (async cross-region), active-passive vs active-active, failover and DNS TTLs, data residency, multi-region write conflicts, cost modeling (compute, storage, egress, LLM tokens), right-sizing, reserved vs on-demand in a sentence.
- Implement: restore last night's dump into a fresh Postgres container and run the consistency check (measure RTO); configure WAL archiving to S3 with `wal-g` or `pgbackrest` and perform a PITR to a timestamp 10 minutes ago on a scratch instance; write `docs/46-dr.md` with RPO/RTO measured and a multi-region active-passive design for Triage.
- Exact exercise: `docs/47-cost.md`: monthly cost table (EC2, S3, ECR, egress, LLM tokens at measured per-suggestion cost × expected volume) and three cost reductions with estimated savings.

### DSA — 1.5 hours

- Solve:
  1. Kth Smallest Number in Multiplication Table
  2. Minimum Speed to Arrive on Time
- Timed task: Koko Eating Bananas re-solve (8 minutes), Capacity To Ship Packages Within D Days re-solve (10 minutes).

### Behavioral — 1 hour

- Implement: the remaining 4 STAR stories; map all 10 to Amazon Leadership Principles in a table (see [[#Appendix B — Behavioral Story Bank and Amazon Leadership Principles Map]]); record yourself telling two of them in under 2 minutes each.

### End-of-Day Check

- [ ] I have restored a backup and performed a PITR; RPO and RTO are measured.
- [ ] All 10 stories are written and mapped.

## Day 127 — Behavioral mock + Amazon LP deep-dive; Google-style problem-solving; DSA

**Total: 5 hours**

### Behavioral — 2 hours

- Learn: Amazon LPs (Customer Obsession, Ownership, Invent and Simplify, Are Right A Lot, Learn and Be Curious, Hire and Develop, Insist on the Highest Standards, Think Big, Bias for Action, Frugality, Earn Trust, Dive Deep, Have Backbone, Deliver Results, plus the two newer ones), the follow-up pattern ("what was your specific contribution?", "what would you do differently?", "what did you learn?"), Google's hypothetical-situation style ("how would you handle a teammate who..."), Meta's "tell me about a time you had to influence without authority".
- Mock behavioral (45 minutes, recorded): 8 questions from Appendix B asked in LP order with follow-ups; self-score each on specificity, ownership, quantified result, reflection; rewrite the two weakest stories.

### DSA — 2.5 hours

- Google-style session (ambiguous, discuss constraints before coding; 100 minutes):
  1. Text Justification
  2. Odd Even Jump
  3. Minimum Area Rectangle
  4. Split Array into Consecutive Subsequences
- Timed task: Expressive Words (unseen, 15 minutes).

### Capstone Project — 0.5 hours

- Build: `docs/48-impact.md`: measured metrics table for the resume (p95, RPS, classifier F1, recall@5, cache hit rate, coverage, cost per 1k suggestions, RTO) with the date and method of each measurement.
- Deliverable: resume bullets drafted strictly from measured numbers.

### End-of-Day Check

- [ ] I can tell any of my 10 stories in 2 minutes and survive three follow-ups.
- [ ] I can explain the Odd Even Jump monotonic-map trick.

## Day 128 — Full HLD mock + ML design mock; DSA

**Total: 5 hours**

### LLD / HLD — 3 hours

- Mock HLD (60 minutes, recorded): **Design a Distributed Cache with 99.99% availability** (unseen as a full prompt even though components are familiar) — drive the interview, estimate, API, consistent hashing, replication, eviction, hot keys, failure modes, client library behavior; 20 minutes self-review with the Appendix C rubric.
- Mock ML design (45 minutes, recorded): **Design a Search Ranking System for Triage's knowledge base** — learning-to-rank from agent clicks and suggestion acceptance, features, offline nDCG, online interleaving, bias in click data, serving within 50 ms; 15 minutes self-review.

### DSA — 1.5 hours

- Timed set (70 minutes, closed-book):
  1. Longest Repeating Character Replacement
  2. Clone Graph
  3. Word Break
  4. Number of Music Playlists (unseen, hard; partial credit acceptable)

### Capstone Project — 0.5 hours

- Build: address the top two gaps identified in the HLD mock that apply to Triage (e.g., missing hot-key handling in cache, missing client-side retry budget).
- Deliverable: PR merged with tests.

### End-of-Day Check

- [ ] Both mocks are complete within time; weak spots scheduled for Days 134 and 141.
- [ ] I scored at least 6/8 on the timed set.

## Day 129 — Project documentation, demo, and pitch; DSA

**Total: 5 hours**

### Capstone Project — 3 hours

- Build: top-level `README.md` (what Triage is, architecture diagram, measured metrics table, how to run locally in 3 commands, how it is deployed, links to ADRs, runbooks, eval reports), `docs/ARCHITECTURE.md` (request paths for create/classify/search/suggest with sequence diagrams), ADR index, a 5-minute recorded demo walking through: create ticket → classification → SLA breach alert → semantic search → suggestion with sources → Grafana traces; a 3-minute verbal pitch script and a 30-second version.
- Deliverable: README reads cleanly to someone who has never seen the project; demo video linked.

### DSA — 1.5 hours

- Solve:
  1. Stream of Characters
  2. Prefix and Suffix Search
- Timed task: Design Add and Search Words Data Structure re-solve (12 minutes).

### Behavioral — 0.5 hours

- Implement: answers to "Tell me about yourself" (90 seconds), "Why this company?" (template with three concrete hooks to fill per company), "Why are you switching to Java/backend?" (honest, specific).

### End-of-Day Check

- [ ] A stranger can run Triage from the README in under 10 minutes.
- [ ] I can deliver the 3-minute pitch without notes.

## Day 130 — Day-130 milestone: project defense pass 1; v1.0.0

**Total: 5 hours**

### Capstone Project — 3.5 hours

- Build: answer every question in [[#Appendix A — Flagship Project Defense Question Bank]] in writing in `docs/49-defense.md`, each answer ≤ 120 words and citing a measured number or an ADR where possible; mark any answer that exposes a real weakness and either fix it today (if < 1 hour) or add it to the "known limitations" section of the README.
- Deliverable: tag `v1.0.0`; production deployment of this tag through the pipeline; final metrics snapshot in `docs/48-impact.md`.

### DSA — 1.5 hours

- Revision (closed-book): Trapping Rain Water, Merge k Sorted Lists, Course Schedule II, Longest Increasing Subsequence, LRU Cache, Word Search II, Minimum Window Substring, Burst Balloons.

### End-of-Day Check

- [ ] Every defense question has a written answer.
- [ ] `v1.0.0` is deployed and measured.
# Phase 7 — Interview War Mode (Days 131–150)

> [!note] Phase goal
> Daily realistic simulation. Every coding session is timed and spoken aloud. Design mocks rotate LLD, HLD, and ML design. CS fundamentals, Java, SQL, and behavioral are revised on a fixed cadence. The project defense is rehearsed until every answer is crisp. Company-style days target Google, Amazon, Meta, Stripe, and Uber/Airbnb formats.

> [!tip] Daily shape in this phase
> 2 hours coding mock or timed set · 1.5 hours design mock · 1 hour revision block · 0.5 hours project defense drill. Exceptions are marked.

## Day 131 — Coding mock 1 + LLD mock + Java revision

**Total: 5 hours**

### DSA — 2 hours

- Coding mock (45 minutes, two problems, spoken aloud, no autocomplete):
  1. Top K Frequent Elements (warm-up, 12 minutes)
  2. Number of Islands variant: count islands of size ≥ k and return their sizes sorted (unseen variant, 30 minutes)
- Second block (45 minutes): Sum Root to Leaf Numbers (12 minutes), Exclusive Time of Functions (20 minutes), Diagonal Traverse (10 minutes).
- Revision (30 minutes): write complexity and edge cases for each problem above without running code.

### LLD / HLD — 1.5 hours

- Mock LLD (60 minutes, recorded): **Design a Meeting Scheduler** (rooms, participants, recurring meetings, conflict detection, notifications); class diagram, core booking code with overlap check, tests. 30 minutes self-review and gap fix.

### Revision — 1 hour

- Java rapid-fire (30 questions, 2 minutes each): `equals/hashCode`, `HashMap` internals, `ConcurrentHashMap`, `String` immutability, generics and erasure, `Comparable` vs `Comparator`, exceptions, `try-with-resources`, `volatile`, `synchronized` vs `ReentrantLock`, `ExecutorService`, `CompletableFuture`, virtual threads, GC generations, `OutOfMemoryError` causes, records, sealed types, streams laziness, `Optional` misuse, `final` semantics, immutability, JVM startup, JIT, class loading, memory model happens-before, deadlock detection, thread pool sizing, `ThreadLocal` leaks, `LinkedHashMap` LRU, `TreeMap` operations, autoboxing pitfalls.

### Capstone Project — 0.5 hours

- Defense drill: answer aloud, 60 seconds each: "Why PostgreSQL?", "Why Kafka and not a Postgres job table?", "What happens if Redis goes down?", "How do you guarantee idempotency?", "What is the bottleneck?"

### End-of-Day Check

- [ ] Both coding problems finished within time with correct complexity stated.
- [ ] Java rapid-fire: at least 26/30 clean.

## Day 132 — Coding mock 2 + HLD mock + CS revision

**Total: 5 hours**

### DSA — 2 hours

- Coding mock (45 minutes):
  1. Merge Intervals (10 minutes)
  2. Design a data structure supporting `addRange(l, r)`, `removeRange(l, r)`, `queryRange(l, r)` — Range Module (hard, 35 minutes)
- Second block (45 minutes): Find K Pairs with Smallest Sums re-solve (12 minutes), Letter Tile Possibilities (15 minutes), Sequential Digits (8 minutes), Minimum Domino Rotations For Equal Row (10 minutes).
- Revision (30 minutes): explain Range Module with a `TreeMap` aloud, then compare to a segment tree approach.

### LLD / HLD — 1.5 hours

- Mock HLD (60 minutes, recorded): **Design Instagram (photo sharing with feed and stories)** — upload path, CDN, feed fan-out, stories TTL, estimate, failure modes. 30 minutes self-review against the rubric.

### Revision — 1 hour

- OS and networking rapid-fire (25 questions): context switch, scheduling, virtual memory, page fault, TLB, deadlock conditions, mutex vs semaphore, CAS, zombie process, signals, TCP handshake, congestion control, `TIME_WAIT`, UDP use, DNS path, TLS 1.3 handshake, certificate chain, HTTP/2 vs HTTP/3, WebSocket vs SSE, L4 vs L7, CDN caching, NAT in a sentence, keep-alive, head-of-line blocking, what happens when you type a URL.

### Capstone Project — 0.5 hours

- Defense drill: "How would you scale to 10× traffic?", "How would you shard?", "What breaks at 100 M users?", "Why synchronous classification fallback vs asynchronous?", "How do you handle duplicate Kafka messages?"

### End-of-Day Check

- [ ] Range Module solved or completed to a working `TreeMap` solution after time.
- [ ] CS rapid-fire: at least 22/25 clean.

## Day 133 — Meta-style speed day + ML design mock + SQL revision

**Total: 5 hours**

### DSA — 2 hours

- Meta-style mock (35 minutes, two mediums, must finish both):
  1. Minimum Remove to Make Valid Parentheses
  2. Kth Largest Element in an Array
- Second mock (35 minutes):
  1. Subarray Sum Equals K
  2. Lowest Common Ancestor of a Binary Tree
- Third block (40 minutes): Valid Palindrome II (8), Random Pick with Weight (10), Binary Tree Right Side View (8), Interval List Intersections (10).
- Revision (10 minutes): list the three places you lost time.

### LLD / HLD — 1.5 hours

- Mock ML design (45 minutes, recorded): **Design a spam/abuse detection system for user-generated content** — features, labels, imbalance, online scoring, human review, adversarial drift, metrics. 15 minutes self-review.
- Revision (30 minutes): re-read your Day 115 and 116 ML design write-ups and tighten one weak section each.

### Revision — 1 hour

- SQL timed set (closed-book, write and run against your Triage DB or LeetCode): Department Top Three Salaries, Consecutive Numbers, Trips and Users, Game Play Analysis IV, Restaurant Growth, plus two Triage queries: "agents with the highest resolution rate last 30 days (min 20 tickets)" and "tickets that breached first-response SLA grouped by team and week".

### Capstone Project — 0.5 hours

- Defense drill: "Why this ML model and not DistilBERT?", "Why macro-F1?", "How do you detect model drift?", "How do you evaluate the LLM?", "How do you prevent hallucinations?"

### End-of-Day Check

- [ ] Both Meta-style mocks completed in 35 minutes each.
- [ ] All SQL queries correct on first run or fixed within 5 minutes.

## Day 134 — Coding mock 3 + LLD weak-spot mock + behavioral

**Total: 5 hours**

### DSA — 2 hours

- Coding mock (45 minutes):
  1. Alien Dictionary (NeetCode) from scratch (25 minutes)
  2. Follow-up: detect and report the first contradictory edge when the input is inconsistent (unseen follow-up, 20 minutes)
- Second block (45 minutes): Reorder Data in Log Files (12), Prison Cells After N Days (18), Partition Array into Disjoint Intervals (10).
- Revision (30 minutes): re-solve Day 128 timed-set failures.

### LLD / HLD — 1.5 hours

- Mock LLD (60 minutes, recorded): the weakest LLD from Days 90, 131 redone as a fresh prompt: **Design a Pub-Sub system with topic partitions, consumer groups, and at-least-once delivery** with working code for publish/subscribe/ack and tests. 30 minutes review.

### Behavioral — 1 hour

- Mock behavioral (45 minutes, recorded): Amazon-style, 6 LP questions with two follow-ups each (Ownership, Dive Deep, Have Backbone, Deliver Results, Learn and Be Curious, Earn Trust). 15 minutes: tighten the weakest story.

### Capstone Project — 0.5 hours

- Defense drill: "What happens if Kafka goes down?", "What happens during a database failover?", "How does the outbox relay avoid double publishing?", "What is your RPO/RTO?", "What did chaos testing find?"

### End-of-Day Check

- [ ] Pub-Sub LLD code compiles and tests pass within time.
- [ ] Behavioral answers each ≤ 2 minutes with a quantified result.

## Day 135 — Amazon-style day: LP-heavy + coding + HLD

**Total: 5 hours**

### DSA — 2 hours

- Amazon-style coding (45 minutes): one problem with extensive follow-ups:
  1. LRU Cache → follow-up: make it thread-safe → follow-up: add TTL → follow-up: how would you distribute it (talk only).
- Second block (45 minutes): K Closest Points to Origin (8), Rotting Oranges (10), Critical Connections in a Network (20), Minimum Cost to Connect Sticks (NeetCode) or Last Stone Weight (7).
- Revision (30 minutes): explain thread-safe LRU tradeoffs (global lock vs striped vs `ConcurrentHashMap` + lock-free list) in writing.

### LLD / HLD — 1.5 hours

- Mock HLD (60 minutes, recorded): **Design Amazon Locker / package pickup system** (unseen) — locker allocation, codes, expiry, notifications, concurrency, estimate. 30 minutes review.

### Behavioral — 1 hour

- Mock (45 minutes): 8 LP questions not used on Day 134 (Customer Obsession, Invent and Simplify, Are Right A Lot, Insist on the Highest Standards, Think Big, Bias for Action, Frugality, Hire and Develop). 15 minutes: map any story gaps and write a short new story if a principle has none.

### Capstone Project — 0.5 hours

- Defense drill: "Walk me through a request from `POST /tickets` to a suggestion appearing" in 3 minutes, then in 1 minute.

### End-of-Day Check

- [ ] Every LP has at least one story.
- [ ] LRU follow-ups answered with concrete designs.

## Day 136 — Coding mock 4 + HLD mock + DBMS revision

**Total: 5 hours**

### DSA — 2 hours

- Coding mock (45 minutes):
  1. Serialize and Deserialize BST (20 minutes)
  2. Step-By-Step Directions From a Binary Tree Node to Another (25 minutes)
- Second block (45 minutes): Delete Nodes And Return Forest (15), Distribute Coins in Binary Tree (15), Smallest Subtree with all the Deepest Nodes (15).
- Revision (30 minutes): explain the "return value up, update global" tree pattern with three examples.

### LLD / HLD — 1.5 hours

- Mock HLD (60 minutes, recorded): **Design Google Maps ETA and routing** (unseen) — map tiles, graph storage, shortest path at scale (contraction hierarchies in a sentence), live traffic ingestion, ETA model, estimate. 30 minutes review.

### Revision — 1 hour

- DBMS rapid-fire (25 questions): ACID, each anomaly, Postgres isolation levels, MVCC, VACUUM, B+tree, index types (B-tree, GIN, GiST, BRIN in a sentence), covering/partial indexes, `EXPLAIN` nodes, lost update fixes, deadlocks, optimistic vs pessimistic locking, WAL, checkpoints, replication lag, read-your-writes, partitioning vs sharding, consistent hashing, normalization forms (1NF–3NF, BCNF in a sentence), denormalization reasons, N+1, connection pooling, keyset pagination, 2PC vs saga, CAP classification of Postgres/Cassandra/Redis.

### Capstone Project — 0.5 hours

- Defense drill: "Why UUID v7?", "Why keyset pagination?", "Show me the slowest query and what you did", "Why a read replica and how do you avoid stale reads?", "Why partition `ticket_events`?"

### End-of-Day Check

- [ ] Both tree problems solved within time.
- [ ] DBMS rapid-fire: at least 22/25.

## Day 137 — Google-style day: ambiguity, algorithms, code quality

**Total: 5 hours**

### DSA — 2.5 hours

- Google-style mock (45 minutes, one problem, interviewer withholds constraints; you must ask): **Design an iterator over a sorted stream of intervals merging overlaps lazily** (unseen; implement `IntervalMergeIterator` with `hasNext/next`, tests, discuss memory).
- Second mock (45 minutes): Student Attendance Record II (hard; derive the DP state aloud).
- Third block (45 minutes): Bulls and Cows (8), Expressive Words (12), License Key Formatting (8), Swap Adjacent in LR String (15).
- Revision (15 minutes): rewrite one solution for readability (naming, small methods) and note what changed.

### LLD / HLD — 1.5 hours

- Mock HLD (60 minutes, recorded): **Design a Code Deployment System (build, artifact, rollout to 100k hosts)** — artifact store, P2P distribution, phased rollout, health gates, rollback. 30 minutes review.

### Capstone Project — 0.5 hours

- Defense drill: Google-style depth — "Explain exactly how your consumer achieves effectively-once", "What are the consistency guarantees of your search index relative to the tickets table?", "Where is your system not linearizable and why is that acceptable?"

### Revision — 0.5 hours

- Re-solve: Longest Valid Parentheses (10 minutes), Sliding Window Maximum (10 minutes), Kth Smallest Element in a Sorted Matrix (10 minutes).

### End-of-Day Check

- [ ] I asked at least five clarifying questions before coding the iterator.
- [ ] Student Attendance Record II DP state defined correctly.

## Day 138 — Coding mock 5 + LLD mock + Spring/backend revision

**Total: 5 hours**

### DSA — 2 hours

- Coding mock (45 minutes):
  1. Design Twitter (15 minutes)
  2. Follow-up: paginate the feed with cursors and support unfollow efficiently (20 minutes)
  3. Follow-up talk: fan-out tradeoffs (10 minutes)
- Second block (45 minutes): Maximum Frequency Stack re-solve (10), All O`one Data Structure (25), Design Circular Deque (10).
- Revision (30 minutes): write the invariants of All O`one aloud and compare with LFU.

### LLD / HLD — 1.5 hours

- Mock LLD (60 minutes, recorded): **Design a Cricbuzz-style live score system (LLD)** — matches, innings, balls, players, commentary, Observer for live updates, statistics computed incrementally; core code with tests. 30 minutes review.

### Revision — 1 hour

- Spring and backend rapid-fire (30 questions): IoC/DI, bean scopes, proxies and self-invocation, `@Transactional` propagation and rollback rules, isolation, JPA lazy/eager, N+1 fixes, optimistic locking, Flyway practices, `@ControllerAdvice`, validation, security filter chain, JWT vs sessions, refresh rotation, CORS, CSRF, Actuator, Micrometer, OpenTelemetry propagation, Kafka producer acks, consumer groups, rebalances, DLT, outbox, idempotency, cache-aside, stampede, rate limiting, circuit breaker states, timeouts, graceful shutdown, readiness vs liveness, config and secrets, Testcontainers, test layers.

### Capstone Project — 0.5 hours

- Defense drill: "Why Spring Boot and not Node/Go?", "Why hexagonal layering?", "What would you remove from the project?", "What is the weakest part and what would you do with two more weeks?"

### End-of-Day Check

- [ ] Design Twitter follow-ups answered with code.
- [ ] Backend rapid-fire: at least 26/30.

## Day 139 — Stripe-style practical coding + HLD payments revisit + behavioral

**Total: 5 hours**

### DSA — 2.5 hours

- Stripe-style practical mock (60 minutes, multi-part, emphasis on correctness and clean code): **Ledger reconciliation** — given two CSV-like inputs (internal transactions and PSP settlement rows), part 1: match by id and report mismatches; part 2: handle partial captures and refunds that net against charges; part 3: currency conversion with a rates table and rounding rules; write tests as you go in Java.
- Second mock (45 minutes): **HTTP rate limiter from logs** — given a log of `(timestamp, user, endpoint)`, part 1: count requests per user per minute; part 2: flag users exceeding a sliding-window limit; part 3: support per-endpoint limits and output the first violation time.
- Revision (45 minutes): review both for naming, error handling, and edge cases; rewrite the ugliest method.

### LLD / HLD — 1 hour

- Revision: re-present the Payment System design (Day 85) in 30 minutes aloud; then 30 minutes on the two hardest follow-ups: idempotent retries with unknown PSP outcome, and ledger consistency under partial failure.

### Behavioral — 1 hour

- Mock (45 minutes): Stripe/Google-style situational questions: "Describe a time you pushed back on a technical decision", "How do you decide when something is good enough to ship?", "Tell me about a bug that reached production", "How would you onboard to a large unfamiliar codebase?" 15 minutes: notes.

### Capstone Project — 0.5 hours

- Defense drill: "How do you handle a webhook delivered twice?", "What is your idempotency key scope?", "How does PII redaction interact with search quality?"

### End-of-Day Check

- [ ] Both practical mocks have passing tests for all parts.
- [ ] Payment follow-ups answered without hesitation.

## Day 140 — Full coding loop simulation + ML/AI interview + revision

**Total: 5 hours**

### DSA — 2.5 hours

- Two back-to-back 45-minute coding interviews with a 10-minute break (recorded, spoken):
  - Interview 1: Minimum Window Substring (hard) as a single problem with follow-ups (streaming input; multiple patterns).
  - Interview 2: Cheapest Flights Within K Stops (20 minutes) then Longest Increasing Path in a Matrix (20 minutes).
- Revision (20 minutes): note where you froze and what unblocked you.

### ML / AI — 1.5 hours

- Mock ML/AI interview (45 minutes, recorded): mixed fundamentals and Triage-specific: "Explain your classifier's confusion matrix", "Why TF-IDF + LR over a transformer?", "Walk through your RAG eval numbers", "How would you reduce hallucinations further?", "What is LoRA?", "How would you detect concept drift without labels?", "Design an experiment to prove suggestions help agents." 45 minutes: refine answers and update `docs/49-defense.md`.

### Revision — 0.5 hours

- Re-solve: Course Schedule (8), Partition Equal Subset Sum (10), House Robber II (8).

### Capstone Project — 0.5 hours

- Defense drill: full 10-minute architecture walkthrough from the README diagram, timed.

### End-of-Day Check

- [ ] Both interviews completed with working code.
- [ ] ML/AI answers each cite a number from my eval reports.
## Day 141 — Coding mock 6 + HLD weak-spot mock + Java concurrency revision

**Total: 5 hours**

### DSA — 2 hours

- Coding mock (45 minutes):
  1. Maximum Profit in Job Scheduling (25 minutes)
  2. Follow-up: return the chosen jobs (10 minutes); follow-up talk: streaming jobs (10 minutes).
- Second block (45 minutes): Minimum Cost For Tickets re-solve (10), Profitable Schemes (25), Largest Divisible Subset (10).
- Revision (30 minutes): write the DP state/transition for all three without code.

### LLD / HLD — 1.5 hours

- Mock HLD (60 minutes, recorded): the weakest HLD from Days 128–137 as a fresh prompt: **Design a Multi-Region Active-Active Key-Value Store with conflict resolution** — CRDT counters, LWW with hybrid logical clocks, replication topology, read/write paths, failure modes. 30 minutes review.

### Revision — 1 hour

- Java concurrency coding drill (closed-book, 15 minutes each): thread-safe bounded blocking queue with `ReentrantLock`/`Condition`; single-flight cache with `ConcurrentHashMap.computeIfAbsent`; a fixed-size thread pool from scratch using a `BlockingQueue<Runnable>` and worker threads with graceful shutdown; a rate limiter with `Semaphore` and a refill thread.

### Capstone Project — 0.5 hours

- Defense drill: "How does your SLA scanner stay correct with 3 workers?", "What is a fencing token and where do you use it?", "What happens when a worker dies mid-batch?"

### End-of-Day Check

- [ ] All four concurrency drills compile and pass a smoke test.
- [ ] Job scheduling DP solved within time with reconstruction.

## Day 142 — Uber/Airbnb-style day: LLD-flavored coding + HLD + SQL

**Total: 5 hours**

### DSA — 2.5 hours

- LLD-flavored coding mock (60 minutes): **Design and implement a parking rate calculator service** — rate plans (hourly, daily cap, weekend), grace periods, multiple vehicle types, time-zone aware; `RateCalculator.calculate(entry, exit, vehicleType)`; tests for midnight crossing and daylight saving.
- Second mock (45 minutes): **Flatten a nested JSON object into dotted keys and back** (`flatten`, `unflatten`, arrays as indices), with tests; follow-up: streaming for huge documents (talk).
- Third block (45 minutes): Design Underground System re-solve (10), Time Based Key-Value Store re-solve (10), Snapshot Array re-solve (12), Design Authentication Manager re-solve (8).

### LLD / HLD — 1.5 hours

- Mock HLD (60 minutes, recorded): **Design Airbnb search and booking** — geo search (geohash), availability calendar storage, pricing, booking with hold and payment, search ranking hook, estimate. 30 minutes review.

### Revision — 0.5 hours

- SQL: write from memory a query for "second-highest resolution time per team per month" and "running count of open tickets per day" against Triage; run and verify.

### Capstone Project — 0.5 hours

- Defense drill: "How does hybrid search merge results?", "What is your recall@5 and how did you measure it?", "How would you add a second language?"

### End-of-Day Check

- [ ] Rate calculator handles DST and midnight; JSON flatten round-trips.
- [ ] Airbnb availability storage choice defended.

## Day 143 — Coding mock 7 + LLD mock + CS/networking deep questions

**Total: 5 hours**

### DSA — 2 hours

- Coding mock (45 minutes):
  1. Trapping Rain Water (12 minutes)
  2. Trapping Rain Water II (hard, heap-based, 33 minutes)
- Second block (45 minutes): Making A Large Island re-solve (15), Number of Enclaves (10), Count Sub Islands (12), Detect Cycles in 2D Grid (8).
- Revision (30 minutes): explain why Trapping Rain Water II needs a heap from the boundary inward.

### LLD / HLD — 1.5 hours

- Mock LLD (60 minutes, recorded): **Design a Hotel Management System with Observer-based housekeeping and State-based room lifecycle** (fresh variant of Day 90). Code the room state machine and reservation overlap check with tests. 30 minutes review.

### Revision — 1 hour

- Deep CS questions (write answers, 10 minutes each): "Explain how a TCP connection is established, used, and closed, including what the OS does with buffers"; "Explain virtual memory from a page fault to the disk and back"; "Explain what a database does from `BEGIN` to `COMMIT` including WAL and MVCC"; "Explain how HTTPS protects against a man-in-the-middle"; "Explain what happens in the JVM when `new` is called and when the object becomes garbage"; "Explain how Kafka achieves high throughput on disk".

### Capstone Project — 0.5 hours

- Defense drill: "What would you change if p95 had to be under 100 ms?", "What would you change at 100 M users?", "What is the cost per ticket today and at 10×?"

### End-of-Day Check

- [ ] Both Trapping Rain Water variants solved.
- [ ] Six deep CS answers written and spoken aloud once.

## Day 144 — Full design loop simulation: LLD + HLD + ML design back-to-back

**Total: 5 hours**

### LLD / HLD — 4 hours

- LLD interview (60 minutes, recorded): **Design a Library Management System with reservations, holds queue, and fines** (fresh variant of Day 26 with new requirements: multi-branch, inter-branch transfers). Code the holds queue and transfer logic with tests.
- 10-minute break.
- HLD interview (60 minutes, recorded): **Design a Food Delivery platform (Swiggy/DoorDash)** — order flow, restaurant and rider services, real-time location, dispatch, ETA, surge, payments, notifications, estimate, failure modes.
- 10-minute break.
- ML design interview (45 minutes, recorded): **Design the ETA prediction system for that platform** — features, labels, model choice, online features, evaluation (MAE, calibration of intervals), drift, serving latency.
- Review (35 minutes): rubric scoring for all three; list the top three gaps.

### DSA — 1 hour

- Re-solve (closed-book): Merge k Sorted Lists (10), Word Ladder (12), Edit Distance (10), Find Median from Data Stream (10), Daily Temperatures (6), Coin Change (8).

### End-of-Day Check

- [ ] All three design interviews produced complete artifacts within time.
- [ ] Gaps scheduled into Days 146–147.

## Day 145 — Coding mock 8 (hard focus) + project defense full dry run

**Total: 5 hours**

### DSA — 2.5 hours

- Coding mock (45 minutes): Longest Duplicate Substring (binary search + rolling hash; 45 minutes with follow-ups on collision handling).
- Second mock (45 minutes): Count of Range Sum re-solve (20), Reverse Pairs re-solve (15), Create Sorted Array through Instructions re-solve (10).
- Third block (45 minutes): Palindrome Partitioning II (25), Shortest Common Supersequence re-solve (20).
- Revision (15 minutes): hard-problem checklist: "Is it binary search on the answer? Is it a monotonic structure? Is it DP over an interval? Is it a graph in disguise?"

### Capstone Project — 2 hours

- Full defense dry run (60 minutes, recorded): present the project for 10 minutes, then answer 25 questions drawn randomly from Appendix A with follow-ups; score each answer: specific, measured, honest about limits.
- Fix (60 minutes): the two weakest answers get either a code/doc fix or an updated "known limitations" entry; re-record those two answers.

### Revision — 0.5 hours

- Behavioral: tell three stories back-to-back in under 6 minutes total.

### End-of-Day Check

- [ ] Longest Duplicate Substring solved with correct hashing and collision discussion.
- [ ] All 25 defense answers scored; two re-recorded.

## Day 146 — Coding mock 9 + design gap repair + SQL/DBMS final

**Total: 5 hours**

### DSA — 2 hours

- Coding mock (45 minutes):
  1. Basic Calculator (hard; 25 minutes)
  2. Follow-up: add support for `*` and `/` and variables (20 minutes)
- Second block (45 minutes): Number of Atoms (25), Decode String re-solve (10), Simplify Path re-solve (8).
- Revision (30 minutes): parser pattern: recursive descent vs two stacks; write both skeletons.

### LLD / HLD — 1.5 hours

- Gap repair: redo the weakest 20-minute segment from each of the three Day 144 interviews, aloud and recorded; compare to the first attempt.

### Revision — 1 hour

- SQL final timed set (closed-book, 10 minutes each): Nth Highest Salary, Exchange Seats, Human Traffic of Stadium, Market Analysis I, Monthly Transactions I, Product Sales Analysis III; then explain the query plan for the Triage ticket list query from memory.

### Capstone Project — 0.5 hours

- Defense drill: "Explain the trace of a slow suggestion request and where you would look first", "What alerts would page you at 3 a.m. and why those?"

### End-of-Day Check

- [ ] Calculator with variables works.
- [ ] SQL set: at least 5/6 correct first try.

## Day 147 — Coding mock 10 + HLD final + Java/Spring final

**Total: 5 hours**

### DSA — 2 hours

- Coding mock (45 minutes): Smallest Sufficient Team (hard, bitmask DP; 45 minutes with follow-ups on reconstruction and $n$ up to 60 skills).
- Second block (45 minutes): Partition to K Equal Sum Subsets re-solve (15), Shortest Path Visiting All Nodes re-solve (15), Maximum Students Taking Exam (15, partial credit acceptable).
- Revision (30 minutes): bitmask DP checklist: state size, transitions, submask iteration, memory.

### LLD / HLD — 1.5 hours

- Mock HLD (60 minutes, recorded): **Design Triage itself as if you had never built it** — then compare with what you actually built; list five things you would do differently and why; 30 minutes write-up appended to `docs/49-defense.md`.

### Revision — 1 hour

- Java and Spring whiteboard coding (no IDE, 15 minutes each): implement `equals/hashCode` for a value class with a nullable field; a generic `Result<T, E>` sealed type with `map`/`flatMap`; a `@ControllerAdvice` producing `ProblemDetail`; a `@KafkaListener` with manual ack and idempotency check.

### Capstone Project — 0.5 hours

- Defense drill: "Why did you pick this project?", "What did you learn that surprised you?", "What was the hardest bug?"

### End-of-Day Check

- [ ] Smallest Sufficient Team solved with reconstruction.
- [ ] Five "do differently" items written with reasons.

## Day 148 — Full onsite simulation day 1: coding × 2 + LLD

**Total: 5 hours**

### DSA — 2 hours

- Interview 1 (45 minutes, recorded): Design Hit Counter (NeetCode) (10 minutes) then Sliding Window Maximum variant: maximum of each window with a stream of updates to older values (unseen; discuss and implement with a `TreeMap`, 35 minutes).
- Interview 2 (45 minutes, recorded): Accounts Merge (20 minutes) then Evaluate Division (20 minutes), 5-minute wrap-up on complexity.
- Break 30 minutes (no screens).

### LLD / HLD — 1.5 hours

- Interview 3 (60 minutes, recorded): **Design an Online Judge (LeetCode-like) submission system (LLD)** — problems, submissions, test cases, judge workers, verdicts, rate limits, plagiarism hook; code the submission state machine and judge dispatch with tests. 30 minutes review.

### Revision — 1 hour

- Light: walk through `patterns.md`, `cs-rapidfire.md`, `ml-rapidfire.md`, `java-concurrency.md` once each; no new problems.

### Capstone Project — 0.5 hours

- Defense drill: the 3-minute pitch and the 30-second pitch, three times each.

### End-of-Day Check

- [ ] All three interviews completed within time with spoken reasoning.
- [ ] I did not open any notes during the interviews.

## Day 149 — Full onsite simulation day 2: HLD + ML/AI + behavioral

**Total: 5 hours**

### LLD / HLD — 1.5 hours

- Interview 4 (60 minutes, recorded): **Design Slack (workspaces, channels, messaging, search, presence, notifications)**. 30 minutes review.

### ML / AI — 1.5 hours

- Interview 5 (45 minutes, recorded): ML/AI round mixing fundamentals ("explain attention", "precision vs recall in your classifier", "RAG vs fine-tuning for Triage") with a mini design ("extend Triage to auto-close tickets safely"). 45 minutes: review and final updates to `docs/49-defense.md`.

### Behavioral — 1.5 hours

- Interview 6 (45 minutes, recorded): 10 behavioral questions across Amazon LPs and Google-style hypotheticals, with follow-ups; 45 minutes: final story polish and a one-page "story index" card listing title, LP, and the headline number for each story.

### DSA — 0.5 hours

- Re-solve (closed-book): Two Sum (3), Longest Substring Without Repeating Characters (6), Number of Islands (6), Climbing Stairs (3), Binary Search (3), Reverse Linked List (4), Valid Parentheses (3). Fast, clean, spoken.

### End-of-Day Check

- [ ] Every round completed; recordings reviewed.
- [ ] The story index card fits on one page.

## Day 150 — Final review, project defense pass 3, readiness audit

**Total: 5 hours**

### Capstone Project — 2 hours

- Final defense (60 minutes, recorded): 10-minute presentation plus 30 questions from Appendix A chosen by shuffling; no notes.
- Final repo state (60 minutes): README metrics table refreshed from a fresh measurement run (k6 + eval harness), `CHANGELOG.md`, tag `v1.0.1`, production deployment through the pipeline, confirm backups and alerts are live, archive recordings in `docs/recordings/`.

### DSA — 1.5 hours

- Final timed set (60 minutes, closed-book, mixed difficulty, spoken):
  1. 3Sum
  2. Binary Tree Maximum Path Sum
  3. Word Search II
  4. Minimum Number of Refueling Stops
- Revision (30 minutes): the complete "trigger ⇒ technique" table read aloud once; tally of every problem solved in 150 days by pattern.

### Revision — 1.5 hours

- Readiness audit, written honestly in `notes/readiness.md` with a 1–5 score and evidence for each: DSA mediums under 25 minutes; hards with partial credit; Java fluency; Spring Boot depth; SQL; OS/networking/DBMS; LLD from zero prompt in 45 minutes with code; HLD from zero prompt in 45 minutes with estimates; ML fundamentals; LLM/RAG engineering; ML system design; behavioral stories; project defense; production engineering (deploy, observe, test, recover). Any score ≤ 3 gets a two-week plan written in the same file, built from the re-solve queues and mock formats in this document.

### End-of-Day Check

- [ ] `v1.0.1` is deployed, measured, backed up, and monitored.
- [ ] The readiness audit is written with evidence, not feelings.
# Appendix A — Flagship Project Defense Question Bank

> [!question] Rule
> Every answer cites a measured number, an ADR, or a test. "It depends" is only acceptable when followed by the exact dependency.

## Data and storage

- Why PostgreSQL as the system of record instead of MongoDB, MySQL, or DynamoDB?
- Why UUID v7 keys? What breaks with v4? What breaks with auto-increment across shards?
- Why keyset pagination? Show the plan difference at offset 90,000.
- Which indexes exist on `tickets`, why each, and which one you removed.
- Why partition `ticket_events` by month? What does pruning look like in `EXPLAIN`?
- How does the read replica work and how do you prevent stale reads after a write?
- What is your isolation level and which anomaly did you reproduce and fix?
- How would you shard `tickets` at 1 billion rows? What breaks for search and reports?
- What happens during a primary failover? What does the API return for 30 seconds?
- What are your RPO and RTO, and when did you last restore a backup?
- Why pgvector instead of a dedicated vector database? At what scale would you switch?

## Caching and Redis

- Why Redis? List every use and what happens if Redis is down for each.
- What is your measured cache hit rate and TTL, and how did you choose the TTL?
- How do you prevent a cache stampede on a hot ticket?
- How does the rate limiter stay atomic? Why Lua?
- Why is `SET NX` alone not a safe lock? Where do you use fencing tokens?
- Why fail-open on rate limiting when Redis is unreachable?

## Messaging and asynchrony

- Why Kafka and not a Postgres job table, RabbitMQ, or SQS?
- What is synchronous and what is asynchronous in Triage, and why that boundary (ADR-004)?
- What happens if Kafka is down for 10 minutes? For 10 hours?
- How does the transactional outbox avoid the dual-write problem? What happens if the relay crashes mid-batch?
- How do you handle duplicate messages? Show the `processed_events` check.
- Why is ordering per partition enough for Triage? What is the partition key and why?
- What is your measured ingestion throughput and consumer lag under burst?
- How do retries, backoff, and the dead-letter topic interact? What is in the DLT today?
- How does the SLA scanner stay correct with 3 workers? What if a worker stalls for 5 minutes?

## API and security

- How do you guarantee idempotency on `POST /tickets`? What is the key scope and TTL?
- Why JWT access tokens with rotating refresh tokens instead of sessions? How do you revoke?
- Where are authorization checks enforced and how are they tested?
- Which OWASP Top 10 items apply and what control exists for each?
- How does the webhook sender defend against SSRF?
- What PII do you store, where is it redacted, and what is the retention?

## Resilience and operations

- What are your SLOs and how are alerts derived from them?
- What did chaos testing find? Which two defects did you fix?
- What is the p95 of each critical endpoint and what is the current bottleneck?
- Walk through a slow request using a trace. What would you look at first?
- How do you deploy and roll back? How long does each take?
- What would you change if p95 had to be under 100 ms?
- How would you scale to 10× traffic? What is the first component to fall over?
- What would you change at 100 million users?
- What is the monthly cost and what are the top three cost drivers?

## ML and AI

- Why TF-IDF + logistic regression over DistilBERT? Give F1 and latency for both.
- Why macro-F1 as the headline metric? What would precision-weighted mean here?
- How was the auto-route threshold chosen? What fraction goes to manual triage?
- How do you detect data drift and concept drift? What fired during the synthetic shift test?
- How does the retraining loop use agent corrections without feedback-loop bias?
- Why hybrid search? Give recall@5 and MRR for lexical, semantic, and hybrid.
- How do you chunk the knowledge base and why?
- How do you evaluate the LLM output? What are the judge's biases and your mitigations?
- How do you prevent hallucinations? What is your groundedness score?
- How do you defend against prompt injection in ticket bodies? What is the leak rate on the adversarial suite?
- Why are the assistant's tools read-only? What would it take to allow writes?
- What is the cost per 1,000 suggestions and what did the semantic cache change?
- When would you fine-tune instead of using RAG? What did your LoRA experiment show?
- How would you add a second language?
- How would you prove suggestions actually help agents? Design the experiment.

## Engineering judgment

- What would you remove from this project?
- What is the weakest part, and what would you do with two more weeks?
- What was the hardest bug and how did you find it?
- What did you learn that surprised you?
- If you designed it again from scratch, what five things would change?

# Appendix B — Behavioral Story Bank and Amazon Leadership Principles Map

## Story slots (write each ≤ 250 words, STAR, with a number in the result and one reflection)

| # | Slot | Suggested source | Headline number to include |
|---|---|---|---|
| 1 | Leadership | Driving the Triage architecture decisions (ADRs) | number of ADRs, decision reversed |
| 2 | Ownership | Running chaos tests and fixing what broke | defects found and fixed |
| 3 | Conflict with a peer | A disagreement from past work, or the sync vs async boundary debate with yourself documented in ADR-004 | outcome metric |
| 4 | Failure | The lost-update bug or a missed timeout found by k6 | time to detect, fix |
| 5 | Ambiguity | Choosing the flagship project scope and non-goals | what was cut |
| 6 | Hard technical decision | TF-IDF + LR vs DistilBERT | F1 delta vs latency delta |
| 7 | Teamwork | Past work; or open-source contribution made during the program | PR merged |
| 8 | Learning quickly | Java from zero to Spring Boot in 20 days | tests written, coverage |
| 9 | Production incident | An alert that fired on AWS and the runbook you followed | time to mitigate |
| 10 | Project impact | Triage metrics | p95, F1, recall@5, cost |

## Amazon Leadership Principles map

| Principle | Primary story | Backup story |
|---|---|---|
| Customer Obsession | 5 | 10 |
| Ownership | 2 | 9 |
| Invent and Simplify | 1 | 6 |
| Are Right, A Lot | 6 | 1 |
| Learn and Be Curious | 8 | 6 |
| Hire and Develop the Best | 7 | 3 |
| Insist on the Highest Standards | 4 | 2 |
| Think Big | 5 | 10 |
| Bias for Action | 2 | 8 |
| Frugality | 10 (cost per 1k suggestions) | 6 |
| Earn Trust | 3 | 4 |
| Dive Deep | 9 | 4 |
| Have Backbone; Disagree and Commit | 3 | 1 |
| Deliver Results | 10 | 2 |
| Strive to be Earth's Best Employer | 7 | 3 |
| Success and Scale Bring Broad Responsibility | 5 (PII, AI safety) | 10 |

## Question bank (used in Days 127, 134, 135, 139, 149)

- Tell me about a time you took ownership of something outside your responsibility.
- Describe a decision you made with incomplete information.
- Tell me about a time you disagreed with a technical decision. What did you do?
- Tell me about a failure. What did you learn and what changed afterward?
- Describe a time you had to learn something quickly to deliver.
- Tell me about a production incident you handled.
- Describe the most complex technical problem you solved.
- How do you decide when something is good enough to ship?
- Tell me about a time you simplified something.
- Tell me about a time you received critical feedback.
- Describe a time you had to influence without authority.
- What would you do if a teammate's PR introduced a subtle bug that reached production?
- How would you onboard to a large unfamiliar codebase in your first two weeks?
- Why are you switching to Java and backend engineering?
- Why this company? (three concrete, researched hooks)

# Appendix C — Mock Interview Protocols

## Coding (45 minutes)

- 0–3 min: restate the problem, ask two clarifying questions (input size, duplicates, empty input, value ranges).
- 3–8 min: brute force stated with complexity; identify the bottleneck; propose the pattern.
- 8–12 min: walk a small example; agree on the approach aloud.
- 12–32 min: code in Java, narrating; small methods; meaningful names.
- 32–38 min: trace two examples by hand including one edge case; fix bugs.
- 38–42 min: complexity, tradeoffs, what you would test.
- 42–45 min: follow-up variant.
- Scoring rubric (1–4 each): correctness, efficiency, code quality, communication, speed. Below 14/20 ⇒ re-queue the problem type.

## LLD (60 minutes)

- 0–8 min: requirements and scope, actors, core use cases, out of scope.
- 8–18 min: entities, value objects, services; class diagram (Mermaid) with relationships and multiplicities.
- 18–25 min: key interfaces and the pattern choices with one-sentence justifications.
- 25–50 min: code the core flow in Java with tests for the hardest invariant (concurrency, overlap, state transition).
- 50–60 min: extensibility questions, what changes for a new requirement, what you would refactor.
- Rubric (1–4): requirement capture, model quality, SOLID/patterns judgment, working code, extensibility answers.

## HLD (60 minutes)

- 0–5 min: functional and non-functional requirements; what you will not design.
- 5–10 min: estimation (QPS, storage, bandwidth, cache size) written on the board.
- 10–15 min: APIs.
- 15–20 min: data model and store choices with reasons.
- 20–35 min: high-level architecture diagram; request paths for the two hottest flows.
- 35–50 min: deep dives chosen by you (the hard parts): consistency, hot spots, failure modes, scaling to 10×.
- 50–60 min: tradeoffs, what you would monitor, what you would build first.
- Rubric (1–4): requirement framing, estimation accuracy, architecture soundness, depth on hard parts, tradeoff reasoning, failure analysis.

## ML design (45 minutes)

- Problem framing and success metric (online and offline) · data and labels · features · model choice with a baseline · training and evaluation · serving and latency · monitoring and drift · experiment design · risks and failure modes. Rubric (1–4): framing, metrics, data realism, model judgment, production thinking.

## CS fundamentals (30 minutes)

- 12 questions from the rapid-fire lists, 2 minutes each, with one "go deeper" follow-up on three of them.

## Behavioral (45 minutes)

- 8 questions, each with two follow-ups ("what was your specific contribution?", "what would you do differently?"). Rubric (1–4): specificity, ownership, quantified result, reflection, length discipline.

## Self-review

- Record every mock. Watch at 1.5× speed. Write three bullets: what went well, what went wrong, the one change for next time. Re-queue every problem or design that scored below threshold into the next free revision slot.

# Appendix D — Skill Tier Classification

| Tier                                  | Skills                                                                                                                                                                                                                                                                                        | Treatment in this plan                                      |
| ------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------- |
| 1 — Essential                         | DSA patterns through DP and graphs, Java core and collections, concurrency basics, Spring Boot REST/JPA/testing, SQL and indexing, OS/networking/DBMS fundamentals, LLD with patterns and code, HLD fundamentals and 10 canonical systems, Git/Docker/CI, behavioral stories, project defense | Daily from Day 1, spaced re-solves, multiple mocks          |
| 2 — High value                        | Kafka, Redis, observability (metrics, tracing), resilience patterns, Testcontainers, k6, Terraform, AWS basics, classical ML with metrics, embeddings and vector search, RAG with evaluation, prompt-injection defense, ML system design, security threat modeling, SRE practices             | Built into Triage with measured outcomes; one mock each     |
| 3 — Useful                            | Kubernetes, fine-tuning/LoRA, agents and tool calling, CNN/RNN details, Fenwick/segment trees, string algorithms (KMP/Z), bitmask DP, MLflow, chaos tooling, PITR                                                                                                                             | One focused day each; kept conceptual plus one hands-on run |
| 4 — Low ROI (omitted or one sentence) | Service mesh, operators, GraphQL, reactive Spring WebFlux, gRPC implementation, Spark/Flink implementation, Manacher's, suffix automata, heavy math (number theory beyond gcd/sieve), certificates and courses                                                                                | Mentioned only where a sentence prevents a blind spot       |

> [!summary] Final state at Day 150
> One deployed, monitored, measured, chaos-tested backend + ML/AI platform with written ADRs, runbooks, eval reports, and a defense document; roughly 520 exact problems solved with scheduled re-solves; 20 LLD systems implemented in Java; 25 HLD and 6 ML system designs written; 30+ recorded mocks across coding, LLD, HLD, ML, CS, and behavioral; 10 quantified behavioral stories; and a written readiness audit with a plan for any remaining gap.
