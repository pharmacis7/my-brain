## Day 1 — Java setup, syntax, Big-O, first array problems

**Total: 5 hours**

### Java / Backend — 2.5 hours

- Learn: JDK 21 install, IntelliJ IDEA Community, Maven project layout (`src/main/java`, `src/test/java`), `public static void main`, primitive types and ranges (`int` overflow at $2^{31}-1$, `long`), `var`, operators, integer division, `Math` class, `if/else`, `switch` expressions, `for`, enhanced `for`, `while`, methods, parameters, return types, `final`.
- Implement: `sde-prep/dsa` Maven project. Write `Warmup.java` with methods `isPrime(int n)`, `gcd(long a, long b)` (Euclid), `reverseDigits(int n)` (handle overflow by returning 0), `fizzBuzz(int n)` returning a `String[]`, `countBits(int n)`.
- Exact exercise: write a JUnit 5 test class `WarmupTest` with at least 2 tests per method, including `reverseDigits(1534236469)` returning 0.

### DSA — 2 hours

- Learn: Big-O, Big-Omega, Big-Theta; amortized cost; $O(1), O(\log n), O(n), O(n \log n), O(n^2), O(2^n)$ with concrete loop examples; why $10^8$ simple operations ≈ 1 second; reading constraints to guess the intended complexity ($n \le 10^5 \Rightarrow O(n \log n)$, $n \le 20 \Rightarrow O(2^n)$).
- Implement: `int[]` basics in Java, `Arrays.sort`, `Arrays.fill`, `Arrays.toString`, 2D arrays.
- Solve:
  1. Two Sum
  2. Contains Duplicate
  3. Best Time to Buy and Sell Stock
  4. Maximum Subarray
- Timed task: none today.
- Revision: none.

### Capstone Project — 0.5 hours

- Build: `triage/` GitHub repository with `README.md`, `.gitignore`, `docs/` folder.
- Deliverable: `docs/00-vision.md` — one page: who uses Triage (support agents, team leads, customers via API), the 6 core capabilities (ingest, classify, route, SLA, search, suggest), the 3 non-goals (no live chat UI, no billing, no multi-tenancy in v1).

### End-of-Day Check

- [ ] I can explain why Two Sum is $O(n)$ with a hash map and $O(n^2)$ brute force.
- [ ] I can explain Kadane's invariant: best sum ending at index $i$.
- [ ] I can write a Java method with a JUnit test without looking anything up.
