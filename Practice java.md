# Practice: Java Language (4-hour drill)

> [!abstract] What this is
> **22 exercises + 1 recall test** that cover **every section** of `Core/java language & collections.md`.
> - Total time: **~238 min (just under 4 hours)**. Each time box **includes ~2 min of re-reading**.
> - Every exercise starts with **📖 Re-read first**: open the notes, read only that part, then close them.
> - Answers are hidden in **collapsed callouts** (click to open). Open them **only after** you've written your answer.

> [!warning] Rules that turn this into second nature
> 1. **Predict first, run second.** Write your answer as a comment (`// predict: 98`) *before* you run. A wrong guess is where the learning happens.
> 2. **Type every line.** No copy-paste. Your fingers have to learn the syntax too.
> 3. **Notes closed while coding.** If you get stuck, re-read the section, close it again, then continue.
> 4. **Mark your misses.** For each wrong prediction, add a `❌` next to it here. Those are what you redo.

## Setup (once, ~2 min, already counted)

- **JDK 21+** (pattern `switch` and record patterns in Ex 20 and Ex 22 need 21).
- One IntelliJ project `java-practice`, one file per exercise: `Ex01.java`, `Ex02.java` …
- Start every file with this template:

```java
import java.math.*;
import java.util.*;

public class Ex01 {
    public static void main(String[] args) {

    }
}
```

> [!tip] "Does it compile?" questions
> Type the line, look at IntelliJ's red underline (or the build error), then **delete or comment it out** so the rest of the file still runs.

## Time plan & progress

| #   | Exercise                              | Notes section           | ⏱       | Done |
| --- | ------------------------------------- | ----------------------- | ------- | ---- |
| 01  | Primitive defaults & arithmetic traps | Primitives §1           | 12      | [ ]  |
| 02  | Integer overflow & casting down       | Primitives §2           | 15      | [ ]  |
| 03  | Integer cache & the `==` trap         | Primitives §3           | 8       | [ ]  |
| 04  | Autoboxing cost & traps               | Primitives §4           | 10      | [ ]  |
| 05  | `double` precision                    | Primitives §5           | 6       | [ ]  |
| 06  | Money as `long` paise                 | Primitives §6 A         | 10      | [ ]  |
| 07  | Money as `BigDecimal`                 | Primitives §6 B         | 15      | [ ]  |
| 08  | String immutability                   | Strings §1, §2          | 10      | [ ]  |
| 09  | String pool, `==`, `intern()`         | Strings §3              | 10      | [ ]  |
| 10  | StringBuilder drill                   | Strings §4              | 8       | [ ]  |
| 11  | Concatenation in a loop               | Strings §5              | 7       | [ ]  |
| 12  | Common String methods                 | Strings §6              | 13      | [ ]  |
| 13  | `char`, code points, `c - 'a'`        | Strings §7              | 12      | [ ]  |
| 14  | `var`                                 | Modern §1, §2           | 6       | [ ]  |
| 15  | Text blocks                           | Modern §3               | 6       | [ ]  |
| 16  | Switch expressions                    | Modern §4               | 10      | [ ]  |
| 17  | Records                               | Modern §5               | 13      | [ ]  |
| 18  | Records vs Lombok                     | Modern §6               | 4       | [ ]  |
| 19  | Sealed types                          | Modern §7               | 8       | [ ]  |
| 20  | Pattern matching                      | Modern §8               | 10      | [ ]  |
| 21  | Optional                              | Modern §9               | 13      | [ ]  |
| 22  | Capstone: Java 8 → modern rewrite     | Modern §10 + everything | 20      | [ ]  |
| ⚡   | Rapid-fire recall (no notes, no IDE)  | all                     | 12      | [ ]  |
|     | **Total**                             |                         | **238** |      |

> [!tip] Take a 5-minute break after Ex 07 and after Ex 13 (not counted in the time).

---

# Block 1: Primitives, Overflow, Boxing & Money (~76 min)

## Ex 01 · Primitive defaults & arithmetic traps · ⏱ 12 min

> [!abstract] 📖 Re-read first
> **Primitives → §1 Primitive types**: the 8-type table, the *"Defaults only apply to fields and array elements"* warning, and **Quick traps**.

**Part A: Predict the output** (write each answer as a comment, then run)

```java
public class Ex01 {
    static int field;
    static boolean flag;
    static char ch;
    static double d;

    public static void main(String[] args) {
        System.out.println(field);               // 1
        System.out.println(flag);                // 2
        System.out.println((int) ch);            // 3
        System.out.println(d);                   // 4
        long[] arr = new long[2];
        System.out.println(arr[1]);              // 5
        System.out.println(7 / 2);               // 6
        System.out.println(7 / 2.0);             // 7
        System.out.println(-7 / 2);              // 8
        System.out.println(1.0 / 0);             // 9
        System.out.println(-1.0 / 0);            // 10
        System.out.println('a' + 1);             // 11
        System.out.println((char) ('a' + 1));    // 12
        System.out.println('a' + 'b');           // 13
        System.out.println("" + 'a' + 'b');      // 14
        System.out.println('a' + 'b' + "");      // 15
        System.out.println(1 / 0);               // 16
    }
}
```

**Part B: Does it compile?** If yes, what is the value?

```java
// a
int x;
System.out.println(x);
// b
byte b1 = 10; b1 = b1 + 5;
// c
byte b2 = 10; b2 += 5;
// d
byte b3 = 10; b3 += 300;
// e
short s = 1; s = s * 2;
// f
char c = 'a'; c++;
// g
long l = 5; int i = l;
// h
int i2 = 5; long l2 = i2;
// i
float f = 3.14;
```

**Part C: Write**
- `static double average(int[] a)` that returns the **exact** average (not int division).
- Test: `{1, 2}` → `1.5`, and `{Integer.MAX_VALUE, Integer.MAX_VALUE}` → `2.147483647E9`.

> [!success]- Answers
> **A:** 1 `0` · 2 `false` · 3 `0` · 4 `0.0` · 5 `0` · 6 `3` · 7 `3.5` · 8 `-3` (decimals are cut off toward zero) · 9 `Infinity` · 10 `-Infinity` · 11 `98` · 12 `b` · 13 `195` (97 + 98, both chars become ints) · 14 `ab` (String first, so `+` means join) · 15 `195` (left to right: `'a' + 'b'` = 195 first, then join) · 16 `ArithmeticException: / by zero`
>
> **B:**
> - a ❌ `variable x might not have been initialized` (local, no default)
> - b ❌ `possible lossy conversion from int to byte` (`b1 + 5` is an `int`)
> - c ✅ `15` (compound operator adds a hidden cast)
> - d ✅ compiles! `54`: hidden cast `(byte) 310` = 310 − 256. The hidden cast also **hides overflow**.
> - e ❌ same as b, `s * 2` is an `int`
> - f ✅ `'b'` (`++` also has the hidden cast)
> - g ❌ long → int loses data, needs `(int) l`
> - h ✅ int → long is **widening**, always safe
> - i ❌ `3.14` is a `double` literal. Write `3.14f`.
>
> **C:**
> ```java
> static double average(int[] a) {
>     long sum = 0;                        // long: int sum would overflow
>     for (int v : a) sum += v;
>     return (double) sum / a.length;      // cast BEFORE dividing
> }
> ```

> [!check] You've got it when…
> you can say *why* `b += 300` compiles but `b = b + 300` doesn't, without thinking.

---

## Ex 02 · Integer overflow & casting down · ⏱ 15 min

> [!abstract] 📖 Re-read first
> **Primitives → §2 Integer overflow**: the wrap-around diagram, *"overflow happens before widening"*, **Where interviewers check this**, the `Math.*Exact` tip, and **Casting down**.

**Part A: Predict**

```java
int max = Integer.MAX_VALUE;
System.out.println(max + 1);                         // 1
System.out.println(Integer.MIN_VALUE - 1);           // 2
long a = 1_000_000 * 1_000_000;
long b = 1_000_000L * 1_000_000;
long c = (long) (1_000_000 * 1_000_000);
System.out.println(a + " " + b + " " + c);           // 3
long big = Integer.MAX_VALUE + 1;
long ok  = Integer.MAX_VALUE + 1L;
System.out.println(big + " " + ok);                  // 4
System.out.println(Math.abs(Integer.MIN_VALUE));     // 5
System.out.println((byte) 200);                      // 6
System.out.println((byte) 128);                      // 7
System.out.println((int) 3.99);                      // 8
System.out.println((int) -3.99);                     // 9
System.out.println(Math.toIntExact(3_000_000_000L)); // 10
```

**Part B: Write**
1. `static int midBuggy(int lo, int hi)` using `(lo + hi) / 2`, and `static int midSafe(int lo, int hi)`. Call both with `lo = 2_000_000_000, hi = 2_100_000_000`. Print both.
2. `static int binarySearch(int[] a, int target)` using `midSafe`. Returns the index or `-1`. Test on `{1, 3, 5, 7, 9}` with `7` → `3` and `4` → `-1`.
3. `static long sum(int[] a)` and test with `{Integer.MAX_VALUE, Integer.MAX_VALUE}` → `4294967294`.
4. Call `Math.multiplyExact(100_000, 100_000)` inside `try/catch (ArithmeticException e)` and print `e.getMessage()`.

> [!success]- Answers
> **A:** 1 `-2147483648` · 2 `2147483647` · 3 `-727379968 1000000000000 -727379968` (casting **after** the overflow is too late) · 4 `-2147483648 2147483648` · 5 `-2147483648` · 6 `-56` · 7 `-128` · 8 `3` · 9 `-3` (cuts, doesn't round) · 10 `ArithmeticException: integer overflow`
>
> **B:**
> ```java
> static int midBuggy(int lo, int hi) { return (lo + hi) / 2; }        // -97483648 ❌
> static int midSafe(int lo, int hi)  { return lo + (hi - lo) / 2; }   // 2050000000 ✅
>
> static int binarySearch(int[] a, int target) {
>     int lo = 0, hi = a.length - 1;
>     while (lo <= hi) {
>         int mid = midSafe(lo, hi);
>         if (a[mid] == target) return mid;
>         if (a[mid] < target) lo = mid + 1;
>         else hi = mid - 1;
>     }
>     return -1;
> }
>
> static long sum(int[] a) {
>     long s = 0;
>     for (int v : a) s += v;
>     return s;
> }
>
> try {
>     Math.multiplyExact(100_000, 100_000);
> } catch (ArithmeticException e) {
>     System.out.println(e.getMessage());   // integer overflow
> }
> ```
> Why `-97483648`: `lo + hi` = 4,100,000,000 → wraps to 4,100,000,000 − 2³² = −194,967,296 → `/ 2`.

> [!check] You've got it when…
> you write `lo + (hi - lo) / 2` and `long sum` by reflex, and spot `int * int` stored into a `long` in any code review.

---

## Ex 03 · Integer cache & the `==` trap · ⏱ 8 min

> [!abstract] 📖 Re-read first
> **Primitives → §3 Wrapper classes, autoboxing & the Integer cache**: the cache flowchart, the `==` trap, the **Rule** box (which wrappers have a cache), and *"When `==` does unbox"*.

**Part A: Predict**

```java
Integer a = 127, b = 127;
Integer c = 128, d = 128;
Integer e = -128, f = -128;
Long l1 = 127L, l2 = 127L;
Long l3 = 128L, l4 = 128L;
Double x = 1.0, y = 1.0;
Character ch1 = 'a', ch2 = 'a';

System.out.println(a == b);                         // 1
System.out.println(c == d);                         // 2
System.out.println(c.equals(d));                    // 3
System.out.println(e == f);                         // 4
System.out.println(c == 128);                       // 5
System.out.println(l1 == l2);                       // 6
System.out.println(l3 == l4);                       // 7
System.out.println(x == y);                         // 8
System.out.println(ch1 == ch2);                     // 9
System.out.println(c.intValue() == d.intValue());   // 10
```

**Part B: Discover the cache yourself**
Loop `n` from `-200` to `200`. Count how many `n` make `Integer.valueOf(n) == Integer.valueOf(n)` true, and print the first and last such `n`.

**Part C: Write**
`static boolean sameScore(Integer s1, Integer s2)` that is correct for any values, **including `null`**.

> [!success]- Answers
> **A:** 1 `true` · 2 `false` · 3 `true` · 4 `true` (−128 is inside the cache) · 5 `true` (one side is primitive → unboxed) · 6 `true` (Long has a cache too) · 7 `false` · 8 `false` (Double has **no** cache) · 9 `true` (Character caches 0..127, `'a'` = 97) · 10 `true`
>
> **B:** count `256`, first `-128`, last `127`.
>
> **C:**
> ```java
> static boolean sameScore(Integer s1, Integer s2) {
>     return Objects.equals(s1, s2);   // null-safe .equals()
> }
> ```
> `s1.equals(s2)` is also right for values, but throws NPE if `s1` is `null`.

> [!check] You've got it when…
> seeing `==` between two wrappers makes you uncomfortable.

---

## Ex 04 · Autoboxing cost & traps · ⏱ 10 min

> [!abstract] 📖 Re-read first
> **Primitives → §4 Autoboxing cost and traps**: hidden objects, unboxing `null`, `equals` across types, `remove(int)` vs `remove(Object)`.

**Part A: Predict**

```java
Map<String, Integer> stock = new HashMap<>();
stock.put("car", 5);
System.out.println(stock.getOrDefault("bike", 0));   // 1

Long l = 127L;
System.out.println(l.equals(127));                   // 2
System.out.println(l.equals(127L));                  // 3
System.out.println(l == 127);                        // 4

Integer big = 1000;
int prim = 1000;
System.out.println(big == prim);                     // 5

List<Integer> list = new ArrayList<>(List.of(10, 20, 30, 1));
list.remove(1);
list.remove(Integer.valueOf(1));
System.out.println(list);                            // 6

int n = stock.get("bike");                           // 7
```

**Part B: Fix the bug**

```java
static int total(Map<String, Integer> stock, List<String> items) {
    int t = 0;
    for (String it : items) t += stock.get(it);
    return t;
}
// total(stock, List.of("car", "bike")) → ?
```

**Part C: Measure the cost**
Sum `0 .. 9_999_999` twice: once into `Long sum`, once into `long sum`. Time each with `System.nanoTime()` and print milliseconds.

> [!success]- Answers
> **A:** 1 `0` · 2 `false` (127 boxes to `Integer`, not `Long`) · 3 `true` · 4 `true` (unboxed, compared as numbers) · 5 `true` (unboxed) · 6 `[10, 30]` (first removes **index** 1 = 20, then **value** 1) · 7 💥 `NullPointerException` (`get` returned `null`, then `.intValue()`)
>
> **B:** `stock.get("bike")` is `null` → NPE when unboxing for `+=`. Fix:
> ```java
> for (String it : items) t += stock.getOrDefault(it, 0);
> ```
>
> **C:**
> ```java
> long t0 = System.nanoTime();
> Long boxed = 0L;
> for (long i = 0; i < 10_000_000; i++) boxed += i;   // unbox, add, box a NEW Long
> long t1 = System.nanoTime();
> long prim = 0L;
> for (long i = 0; i < 10_000_000; i++) prim += i;
> long t2 = System.nanoTime();
> System.out.println("Long: " + (t1 - t0) / 1_000_000 + " ms, long: " + (t2 - t1) / 1_000_000 + " ms");
> ```
> The `Long` loop is clearly slower (exact numbers vary by machine). Both print the same sum.

> [!check] You've got it when…
> `map.get(...)` assigned to an `int` instantly looks like an NPE to you.

---

## Ex 05 · `double` precision · ⏱ 6 min

> [!abstract] 📖 Re-read first
> **Primitives → §5 float/double precision**: the "Why?" bullets, the exact-in-binary table, and the comparing tip.

**Part A: Predict**

```java
System.out.println(0.1 + 0.2);                          // 1
System.out.println(0.1 + 0.2 == 0.3);                   // 2
System.out.println(0.5 + 0.25 == 0.75);                 // 3
double total = 0;
for (int i = 0; i < 10; i++) total += 0.1;
System.out.println(total);                              // 4
System.out.println(total == 1.0);                       // 5
System.out.println(100 * 1.1);                          // 6
```

**Part B: Write** `static boolean nearlyEqual(double a, double b)` and use it to make line 2 and line 5 print `true`.

**Part C: Say it out loud (one sentence)** why `0.5` is exact but `0.1` is not.

> [!success]- Answers
> **A:** 1 `0.30000000000000004` · 2 `false` · 3 `true` (1/2 and 1/4 are exact in binary) · 4 `0.9999999999999999` · 5 `false` · 6 `110.00000000000001` (that's why money is never a `double`)
>
> **B:**
> ```java
> static boolean nearlyEqual(double a, double b) {
>     return Math.abs(a - b) < 1e-9;
> }
> ```
> **C:** `double` stores numbers in binary; 0.5 = 1/2 is a finite binary fraction, but 1/10 repeats forever in binary, so it gets rounded.

---

## Ex 06 · Money as `long` paise · ⏱ 10 min

> [!abstract] 📖 Re-read first
> **Primitives → §6 Money → Option A: `long` in the smallest unit**, and the comparison table at the end of §6.

**Write**
1. `static String fmt(long paise)` → `fmt(5997)` = `"₹59.97"`, `fmt(5)` = `"₹0.05"`.
2. A car-loan document fee is ₹19.99 per document. Store it in paise, multiply by 3, print with `fmt`.
3. `static long[] split(long totalPaise, int people)`: split exactly, give the leftover paise to the first people one by one. The parts must **add up to the total**.
   - `split(1000, 3)` → `[334, 333, 333]`
   - `split(100, 6)` → `[17, 17, 17, 17, 16, 16]`

> [!success]- Answers
> ```java
> static String fmt(long paise) {
>     return "₹" + paise / 100 + "." + String.format("%02d", paise % 100);
> }
>
> long fee = 1999;                       // ₹19.99
> System.out.println(fmt(fee * 3));      // ₹59.97, exact
>
> static long[] split(long totalPaise, int people) {
>     long[] parts = new long[people];
>     long base = totalPaise / people;
>     long left = totalPaise % people;   // leftover paise
>     for (int i = 0; i < people; i++) {
>         parts[i] = base + (i < left ? 1 : 0);
>     }
>     return parts;
> }
> System.out.println(Arrays.toString(split(1000, 3)));   // [334, 333, 333]
> ```
> This is the "handle division yourself" cost of Option A: `long` never rounds for you.

---

## Ex 07 · Money as `BigDecimal` · ⏱ 15 min

> [!abstract] 📖 Re-read first
> **Primitives → §6 Money → Option B: `BigDecimal`**: the 4 traps table and the `HALF_EVEN` bullet.

**Part A: Predict**

```java
System.out.println(new BigDecimal(0.1));                                  // 1
System.out.println(BigDecimal.valueOf(0.1));                              // 2
System.out.println(new BigDecimal("0.1").add(new BigDecimal("0.2")));     // 3

BigDecimal p = new BigDecimal("2.0"), q = new BigDecimal("2.00");
System.out.println(p.equals(q));                                          // 4
System.out.println(p.compareTo(q) == 0);                                  // 5
System.out.println(new HashSet<>(List.of(p, q)).size());                  // 6
System.out.println(new TreeSet<>(List.of(p, q)).size());                  // 7

BigDecimal amt = new BigDecimal("100");
amt.add(new BigDecimal("50"));
System.out.println(amt);                                                  // 8

System.out.println(new BigDecimal("10").divide(new BigDecimal("3"), 2, RoundingMode.HALF_EVEN)); // 9
System.out.println(new BigDecimal("2.5").setScale(0, RoundingMode.HALF_EVEN));  // 10
System.out.println(new BigDecimal("3.5").setScale(0, RoundingMode.HALF_EVEN));  // 11
System.out.println(new BigDecimal("2.5").setScale(0, RoundingMode.HALF_UP));    // 12
System.out.println(BigDecimal.ONE.divide(new BigDecimal("3")));                  // 13
```

> [!note] `setScale(n, mode)` = "round to `n` digits after the point using this rounding mode".

**Part B: Write (car-loan math)**
1. **One month's interest**: principal ₹5,00,000 at 9.5% per year → `principal × rate / 1200`, scale 2, `HALF_EVEN`. Expect `3958.33`.
2. **Flat-rate EMI**: same principal, 9.5%, 5 years (60 months).
   - total interest = `principal × rate × years / 100`
   - EMI = `(principal + interest) / 60`, scale 2, `HALF_EVEN`. Expect `12291.67`.
3. **Last-EMI fix**: 60 × 12291.67 is 20 paise more than the real total. Compute the **last EMI** so the 60 payments add up exactly. Expect `12291.47`.

> [!success]- Answers
> **A:** 1 `0.1000000000000000055511151231257827021181583404541015625` · 2 `0.1` · 3 `0.3` · 4 `false` (scale differs) · 5 `true` · 6 `2` (`HashSet` uses `equals`) · 7 `1` (`TreeSet` uses `compareTo`) · 8 `100` (immutable, result thrown away) · 9 `3.33` · 10 `2` (.5 goes to the **even** neighbour) · 11 `4` · 12 `3` · 13 `ArithmeticException: Non-terminating decimal expansion`
>
> **B:**
> ```java
> BigDecimal principal = new BigDecimal("500000");
> BigDecimal rate = new BigDecimal("9.5");
>
> BigDecimal monthInterest = principal.multiply(rate)
>         .divide(new BigDecimal("1200"), 2, RoundingMode.HALF_EVEN);       // 3958.33
>
> BigDecimal interest = principal.multiply(rate)
>         .multiply(new BigDecimal("5"))
>         .divide(new BigDecimal("100"));                                   // 237500.0 (exact, ok)
> BigDecimal total = principal.add(interest);                               // 737500.0
> BigDecimal emi = total.divide(new BigDecimal("60"), 2, RoundingMode.HALF_EVEN);  // 12291.67
>
> BigDecimal lastEmi = total.subtract(emi.multiply(new BigDecimal("59")));  // 12291.47
> ```
> Dividing by 100 is safe without a scale because the result always ends; dividing by 60 or 1200 is not.

> [!check] You've got it when…
> you type `new BigDecimal("...")` with quotes automatically and never call `.equals` on two BigDecimals.

---

# Block 2: Strings (~60 min)

## Ex 08 · String immutability · ⏱ 10 min

> [!abstract] 📖 Re-read first
> **Strings → §1 What a String is** and **§2 Why String is immutable** (the 4-reason table + "How Java makes it immutable").

**Part A: Predict**

```java
String s = "hello";
s.toUpperCase();
s.concat(" world");
s.replace('l', 'L');
System.out.println(s);       // 1
String t = s;
s = s + "!";
System.out.println(t);       // 2
System.out.println(s);       // 3
```

**Part B: See the "hashing" reason break with a mutable key**

```java
Map<List<String>, Integer> map = new HashMap<>();
List<String> key = new ArrayList<>(List.of("a"));
map.put(key, 1);
key.add("b");                                   // change the key AFTER putting it
System.out.println(map.get(key));               // 4
System.out.println(map.get(List.of("a")));      // 5
System.out.println(map.size());                 // 6
```

**Part C: Write your own immutable class** using the same 3 tricks String uses.
- `Rupees` holds `long paise`, has `paise()` and `Rupees plus(Rupees other)` that returns a **new** object.
- `Tags` holds a `List<String>` that nobody can change from outside, not even through the list passed to the constructor.

**Part D: From memory**, write the 4 reasons String is immutable, one line each.

> [!success]- Answers
> **A:** 1 `hello` · 2 `hello` · 3 `hello!` (every method returned a new String; only `s = …` moves the variable)
>
> **B:** 4 `null` · 5 `null` · 6 `1`. The entry is **lost**: its stored hash belongs to `["a"]`, but its key now equals `["a","b"]`. Strings can never do this, which is why they make safe map keys.
>
> **C:**
> ```java
> final class Rupees {                         // 1. final: no subclass can add setters
>     private final long paise;                // 2. private final state
>     Rupees(long paise) { this.paise = paise; }
>     long paise() { return paise; }
>     Rupees plus(Rupees other) {              // 3. "change" = return a new object
>         return new Rupees(paise + other.paise);
>     }
> }
>
> final class Tags {
>     private final List<String> tags;
>     Tags(List<String> tags) { this.tags = List.copyOf(tags); }   // copy + unmodifiable
>     List<String> tags() { return tags; }
> }
> ```
> **D:** String pool (sharing is safe) · Security (value can't change after a check) · Hashing (`hashCode` cached, key never lost) · Thread safety (share with no locks).

---

## Ex 09 · String pool, `==`, `intern()` · ⏱ 10 min

> [!abstract] 📖 Re-read first
> **Strings → §3 String pool**: the pool diagram, *What does this print?*, the `final` tip, and the "how many objects" question.

**Part A: Predict** (new variable names on purpose, so you can't recall the notes line by line)

```java
String a = "java";
String b = "ja" + "va";
String c = new String("java");
String g = new String("java");
String d = c.intern();
String half = "ja";
String e = half + "va";
final String fixedHalf = "ja";
String f = fixedHalf + "va";

System.out.println(a == b);              // 1
System.out.println(a == c);              // 2
System.out.println(c == g);              // 3
System.out.println(c.equals(g));         // 4
System.out.println(a == d);              // 5
System.out.println(a == e);              // 6
System.out.println(a == f);              // 7
System.out.println(a == e.intern());     // 8
System.out.println(a.equals(e));         // 9

String n = null;
System.out.println("java".equals(n));    // 10
System.out.println(n.equals("java"));    // 11
```

**Part B: Answer**
1. How many objects does `new String("java")` create on line 3, given line 1 already ran?
2. How many if `"java"` had never appeared before?

> [!success]- Answers
> **A:** 1 `true` (joined at compile time) · 2 `false` · 3 `false` (two separate heap objects) · 4 `true` · 5 `true` · 6 `false` (built at run time) · 7 `true` (`final` → compile-time constant) · 8 `true` · 9 `true` · 10 `false` (literal first = null-safe) · 11 💥 `NullPointerException`
>
> **B:** 1 → **1** (pool copy already exists) · 2 → **2** (pool copy + heap object)

---

## Ex 10 · StringBuilder drill · ⏱ 8 min

> [!abstract] 📖 Re-read first
> **Strings → §4 String vs StringBuilder vs StringBuffer**: the table, the method chain example, and the `equals` warning.

**Part A: Trace it.** Write the content after each line.

```java
StringBuilder sb = new StringBuilder("pay");
sb.append(42).append('!');          // 1
sb.insert(3, "-");                  // 2
sb.reverse();                       // 3
sb.setCharAt(0, '#');               // 4
sb.deleteCharAt(sb.length() - 1);   // 5
System.out.println(sb + " " + sb.length());   // 6

StringBuilder x = new StringBuilder("a"), y = new StringBuilder("a");
System.out.println(x.equals(y));                         // 7
System.out.println(x.toString().equals(y.toString()));   // 8
```

**Part B: Write**
1. `static boolean isPalindrome(String s)` using `StringBuilder.reverse()`.
2. `static String reverseWords(String s)`: `"I love java"` → `"java love I"`.

**Part C: Pick the class** (String / StringBuilder / StringBuffer)
- a HashMap key · building a CSV row inside a loop · old code shared between threads that you can't change

> [!success]- Answers
> **A:** 1 `pay42!` · 2 `pay-42!` · 3 `!24-yap` · 4 `#24-yap` · 5 `#24-ya` · 6 `#24-ya 6` · 7 `false` (no `equals` override) · 8 `true`
>
> **B:**
> ```java
> static boolean isPalindrome(String s) {
>     return new StringBuilder(s).reverse().toString().equals(s);
> }
>
> static String reverseWords(String s) {
>     String[] words = s.split(" ");
>     StringBuilder sb = new StringBuilder();
>     for (int i = words.length - 1; i >= 0; i--) {
>         sb.append(words[i]);
>         if (i > 0) sb.append(' ');
>     }
>     return sb.toString();
> }
> ```
> **C:** String · StringBuilder · StringBuffer (legacy only)

---

## Ex 11 · Concatenation in a loop: O(n²) · ⏱ 7 min

> [!abstract] 📖 Re-read first
> **Strings → §5 Concatenation in a loop**: the "chars copied" table and the "single line is fine" bullet.

**Part A: Measure**
Write `buildWithPlus(int n)` (`s += "x"` in a loop) and `buildWithBuilder(int n)`. Time both for `n = 50_000` and `n = 100_000`.
- When `n` doubles, how much slower does each get?

**Part B: Answer**
1. How many characters in total does `+=` copy for `n = 1000`?
2. Is `String full = first + " " + last;` a performance problem? Why?

> [!success]- Answers
> ```java
> static String buildWithPlus(int n) {
>     String s = "";
>     for (int i = 0; i < n; i++) s += "x";
>     return s;
> }
> static String buildWithBuilder(int n) {
>     StringBuilder sb = new StringBuilder();
>     for (int i = 0; i < n; i++) sb.append("x");
>     return sb.toString();
> }
> ```
> **A:** `+=` gets about **4× slower** when n doubles (O(n²)). StringBuilder gets about **2×** (O(n)) and stays tiny.
>
> **B:** 1 → 1 + 2 + … + 1000 = 1000 × 1001 / 2 = **500,500**. 2 → **No.** A single expression is optimized by the compiler. Only `+=` **inside a loop** is the problem.

---

## Ex 12 · Common String methods · ⏱ 13 min

> [!abstract] 📖 Re-read first
> **Strings → §6 Common methods**: the whole table. Pay attention to the **Notes** column.

**Part A: Predict**

```java
String s = "  Car Loan,Home Loan,Gold Loan  ";
String t = s.trim();
System.out.println(t.length());                       // 1
System.out.println(t.charAt(4));                      // 2
System.out.println(t.substring(0, 3));                // 3
System.out.println(t.substring(9));                   // 4
System.out.println(t.indexOf("Loan"));                // 5
System.out.println(t.lastIndexOf("Loan"));            // 6
System.out.println(t.indexOf("Bike"));                // 7
System.out.println(t.split(",").length);              // 8
System.out.println("a.b.c".split(".").length);        // 9
System.out.println("a.b.c".split("\\.").length);      // 10
System.out.println("apple".compareTo("banana") < 0);  // 11
System.out.println("Car".compareTo("car") < 0);       // 12
System.out.println("".isEmpty() + " " + "   ".isEmpty() + " " + "   ".isBlank());  // 13
System.out.println("ab".repeat(3));                   // 14
System.out.println("banana".replace("a", "o"));       // 15
System.out.println("a1b22c".replaceAll("[0-9]", "")); // 16
System.out.println(String.join("-", List.of("x", "y", "z")));  // 17
System.out.println("Car".equalsIgnoreCase("CAR"));    // 18
char[] arr = "dcba".toCharArray();
Arrays.sort(arr);
System.out.println(new String(arr));                  // 19
System.out.println(String.valueOf(42) + 1);           // 20
System.out.println(t.charAt(28));                     // 21
```

**Part B: Fill the blank** (length of each)
`int[] nums` → `nums.____` · `String name` → `name.____` · `List<String> ids` → `ids.____`

**Part C: Write**
1. `static String initials(String fullName)`: `"  rahul   kumar sharma "` → `"RKS"`. (Hint: `trim()`, split on `"\\s+"` = one or more spaces.)
2. `static boolean isRotation(String a, String b)`: `"waterbottle"` / `"erbottlewat"` → `true`. (Hint: one line with `contains`.)

> [!success]- Answers
> **A:** 1 `28` · 2 `L` · 3 `Car` (end exclusive) · 4 `Home Loan,Gold Loan` · 5 `4` · 6 `24` · 7 `-1` · 8 `3` · 9 `0` (!! `.` is a regex that matches **every** char; all parts are empty and get dropped) · 10 `3` · 11 `true` · 12 `true` (`'C'` 67 < `'c'` 99: uppercase sorts first) · 13 `true false true` · 14 `ababab` · 15 `bonono` (replaces **all**) · 16 `abc` · 17 `x-y-z` · 18 `true` · 19 `abcd` · 20 `421` (String join, not addition) · 21 💥 `StringIndexOutOfBoundsException` (last index is 27)
>
> **B:** `nums.length` · `name.length()` · `ids.size()`
>
> **C:**
> ```java
> static String initials(String fullName) {
>     StringBuilder sb = new StringBuilder();
>     for (String w : fullName.trim().split("\\s+")) {
>         sb.append(Character.toUpperCase(w.charAt(0)));
>     }
>     return sb.toString();
> }
>
> static boolean isRotation(String a, String b) {
>     return a.length() == b.length() && (a + a).contains(b);
> }
> ```

---

## Ex 13 · `char`, code points, `c - 'a'` · ⏱ 12 min

> [!abstract] 📖 Re-read first
> **Strings → §7 Unicode: char vs code point**: the surrogate-pair picture and the interview tip box.

**Part A: Predict**

```java
String s = "a😀b";
System.out.println(s.length());                              // 1
System.out.println(s.codePointCount(0, s.length()));         // 2
System.out.println(s.codePoints().count());                  // 3
System.out.println((int) 'A');                               // 4
System.out.println('z' - 'a');                               // 5
System.out.println((char) ('a' + 2));                        // 6
System.out.println(Character.isDigit('7') + " " + Character.isLetterOrDigit('_'));  // 7
System.out.println(Character.toLowerCase('Q'));              // 8
```

**Part B: Write**
1. `static boolean isAnagram(String a, String b)` for lowercase a–z using an `int[26]` and `c - 'a'`.
   `"listen"/"silent"` → `true`, `"rat"/"car"` → `false`.
2. `static boolean isCleanPalindrome(String s)`: ignore case and everything that isn't a letter or digit, two pointers, no extra string.
   `"A man, a plan, a canal: Panama"` → `true`, `"race a car"` → `false`.
3. Print each **real character** of `"hi😀"` on its own line. First try a `charAt` loop and see the broken emoji, then fix it with `codePoints()`.

> [!success]- Answers
> **A:** 1 `4` · 2 `3` · 3 `3` · 4 `65` · 5 `25` · 6 `c` · 7 `true false` · 8 `q`
>
> **B:**
> ```java
> static boolean isAnagram(String a, String b) {
>     if (a.length() != b.length()) return false;
>     int[] count = new int[26];
>     for (int i = 0; i < a.length(); i++) {
>         count[a.charAt(i) - 'a']++;
>         count[b.charAt(i) - 'a']--;
>     }
>     for (int c : count) if (c != 0) return false;
>     return true;
> }
>
> static boolean isCleanPalindrome(String s) {
>     int i = 0, j = s.length() - 1;
>     while (i < j) {
>         char l = s.charAt(i), r = s.charAt(j);
>         if (!Character.isLetterOrDigit(l)) { i++; continue; }
>         if (!Character.isLetterOrDigit(r)) { j--; continue; }
>         if (Character.toLowerCase(l) != Character.toLowerCase(r)) return false;
>         i++; j--;
>     }
>     return true;
> }
>
> String e = "hi😀";
> for (int i = 0; i < e.length(); i++) System.out.println(e.charAt(i));   // h, i, ?, ?  (broken halves)
> e.codePoints().forEach(cp -> System.out.println(Character.toString(cp))); // h, i, 😀
> ```

---

# Block 3: Modern Java 17/21 (~90 min)

## Ex 14 · `var` · ⏱ 6 min

> [!abstract] 📖 Re-read first
> **Modern Java → §1 Which feature came in which version** (memorize which two are Java 21) and **§2 `var`** (the ❌ table + *When NOT to use `var`*).

**Does it compile?** (If yes, what's the real type?) Try 7 and 8 at class level, outside `main`.

```java
var names = new ArrayList<String>();             // 1
var x;                                           // 2
var y = null;                                    // 3
var f = () -> 1;                                 // 4
var arr = {1, 2};                                // 5
var arr2 = new int[]{1, 2};                      // 6
class A { var count = 0; }                       // 7
void m(var p) { }                                // 8
for (var e : Map.of("a", 1).entrySet()) { }      // 9
var list = new ArrayList<>(); list.add("x"); list.add(5);   // 10
var total = 0; total = 3_000_000_000L;           // 11
var s = "hi"; s = 5;                             // 12
```

> [!success]- Answers
> 1 ✅ `ArrayList<String>` · 2 ❌ no initializer · 3 ❌ `null` has no type · 4 ❌ lambda needs a target type · 5 ❌ array initializer needs the type · 6 ✅ `int[]` · 7 ❌ fields can't use `var` · 8 ❌ parameters can't · 9 ✅ `Map.Entry<String, Integer>` · 10 ✅ compiles, but it's `ArrayList<Object>` (diamond trap) · 11 ❌ `total` is `int` · 12 ❌ still statically typed (`String`)

---

## Ex 15 · Text blocks · ⏱ 6 min

> [!abstract] 📖 Re-read first
> **Modern Java → §3 Text blocks**: the 5 rules under the example.

**Part A: Predict the exact value.** Print each as `"[" + t.replace("\n", "\\n") + "]"`.

```java
String t = """
    Hi
      there
    """;                    // 1  (also: t.length() ?)

String u = """
        Hi
    """;                    // 2

String w = """
    Hi""";                  // 3

String v = """Hi""";        // 4
```

**Part B: Write**
A JSON template text block with `%s` and `%d` for a loan: `id`, `customer`, `amount`. Fill it with `.formatted("L-101", "Asha", 500000)` and print. Don't escape any `"`.

> [!success]- Answers
> **A:**
> 1. `[Hi\n  there\n]`, length **11** (the common 4 spaces are removed, the extra 2 stay)
> 2. `[    Hi\n]`: the closing `"""` is the least-indented line, so 4 spaces stay
> 3. `[Hi]`: closing `"""` on the same line → **no** trailing newline
> 4. ❌ compile error: the opening `"""` must be followed by a new line
>
> **B:**
> ```java
> String tpl = """
>         {
>           "id": "%s",
>           "customer": "%s",
>           "amount": %d
>         }
>         """;
> System.out.println(tpl.formatted("L-101", "Asha", 500000));
> ```

---

## Ex 16 · Switch expressions · ⏱ 10 min

> [!abstract] 📖 Re-read first
> **Modern Java → §4 Switch expressions**: arrow form, several labels in one case, `yield`, and **exhaustive**.

```java
enum LoanStatus { DRAFT, SUBMITTED, APPROVED, REJECTED, DISBURSED }
```

**Part A: Predict the fall-through bug.** What is `label` for `DRAFT`, `SUBMITTED`, `APPROVED`?

```java
String label = "";
switch (st) {
    case DRAFT:     label = "Draft";
    case SUBMITTED: label = "In review"; break;
    default:        label = "Closed";
}
```

**Part B: Write**
1. `static String label(LoanStatus st)` as a **switch expression**: DRAFT → "Draft", SUBMITTED → "In review", APPROVED and DISBURSED **on one case** → "Positive", REJECTED → "Closed". **No `default`.**
2. Add `CANCELLED` to the enum. Read the compile error. Fix it.
3. `static int processingFee(LoanStatus st)`: SUBMITTED → a `{ }` block that prints `"charging fee"` and gives `999` with `yield`; everything else → `0`.
4. Delete one case from `label` (keep no `default`). What does the compiler say?

> [!success]- Answers
> **A:** DRAFT → `"In review"` (no `break`, falls into the next case) · SUBMITTED → `"In review"` · APPROVED → `"Closed"`
>
> **B:**
> ```java
> static String label(LoanStatus st) {
>     return switch (st) {
>         case DRAFT                -> "Draft";
>         case SUBMITTED            -> "In review";
>         case APPROVED, DISBURSED  -> "Positive";
>         case REJECTED, CANCELLED  -> "Closed";     // added after step 2
>     };
> }
>
> static int processingFee(LoanStatus st) {
>     return switch (st) {
>         case SUBMITTED -> {
>             System.out.println("charging fee");
>             yield 999;
>         }
>         default -> 0;
>     };
> }
> ```
> Steps 2 and 4: `the switch expression does not cover all possible input values`. That error is the **feature**: no silent missing case.

---

## Ex 17 · Records · ⏱ 13 min

> [!abstract] 📖 Re-read first
> **Modern Java → §5 Records**: what the compiler generates, the can/cannot bullets, the compact constructor, and *shallowly immutable*.

**Part A: Predict**

```java
record Customer(String id, String name, int age) { }

var c1 = new Customer("C1", "Asha", 30);
var c2 = new Customer("C1", "Asha", 30);
System.out.println(c1);                              // 1
System.out.println(c1 == c2);                        // 2
System.out.println(c1.equals(c2));                   // 3
System.out.println(c1.hashCode() == c2.hashCode());  // 4
System.out.println(c1.name());                       // 5
System.out.println(c1.getName());                    // 6
```

**Part B: Does it compile?** (add each inside / onto `Customer` one at a time)
a. `private int score;` · b. `static int count = 0;` · c. `boolean isAdult() { return age >= 18; }` · d. `record Vip(String id) extends Customer { }` · e. `record Customer(...) implements Comparable<Customer>` with a `compareTo`

**Part C: Write**
1. `record Money(BigDecimal amount, String currency)` with a **compact constructor** that: rejects `null` currency, rejects negative amounts, and stores the currency in **upper case**. `new Money(new BigDecimal("10"), "inr")` → `Money[amount=10, currency=INR]`.
2. Show the shallow-immutability leak, then fix it:
   ```java
   record Order(List<String> items) { }
   var list = new ArrayList<>(List.of("a"));
   var order = new Order(list);
   list.add("b");
   System.out.println(order.items());   // ?
   ```
   After the fix, what does `order.items().add("c")` do?
3. Use a record as a map key: `record Point(int x, int y)`. Put `new Point(1, 2) → "A"` in a `HashMap`, then `get(new Point(1, 2))`.
4. Return two values: `static MinMax minMax(int[] a)` with `record MinMax(int min, int max)`.

> [!success]- Answers
> **A:** 1 `Customer[id=C1, name=Asha, age=30]` · 2 `false` · 3 `true` · 4 `true` · 5 `Asha` · 6 ❌ compile error (accessor has no `get` prefix)
>
> **B:** a ❌ no extra instance fields · b ✅ · c ✅ · d ❌ records are final and can't extend · e ✅ records can implement interfaces
>
> **C:**
> ```java
> record Money(BigDecimal amount, String currency) {
>     Money {
>         Objects.requireNonNull(currency, "currency");
>         if (amount.signum() < 0) throw new IllegalArgumentException("negative");
>         currency = currency.toUpperCase();   // reassign the parameter; the field is set after
>     }
> }
>
> record Order(List<String> items) {
>     Order { items = List.copyOf(items); }   // fix
> }
> // before fix: [a, b]  😬   after fix: [a]
> // order.items().add("c") → UnsupportedOperationException
>
> record Point(int x, int y) { }
> Map<Point, String> m = new HashMap<>();
> m.put(new Point(1, 2), "A");
> System.out.println(m.get(new Point(1, 2)));   // A (equals/hashCode from the fields)
>
> record MinMax(int min, int max) { }
> static MinMax minMax(int[] a) {
>     int min = Integer.MAX_VALUE, max = Integer.MIN_VALUE;
>     for (int v : a) { min = Math.min(min, v); max = Math.max(max, v); }
>     return new MinMax(min, max);
> }
> ```

---

## Ex 18 · Records vs Lombok · ⏱ 4 min

> [!abstract] 📖 Re-read first
> **Modern Java → §6 Records vs Lombok**: the table and the interview answer.

**Pick record or Lombok, and give the reason in a few words:**
1. `LoanApplication` JPA entity
2. API response `LoanSummaryDto`
3. A `Money` value object
4. A class with 12 optional fields, built in many different ways
5. A class that must extend `BaseAuditEntity`

Then **say the interview answer out loud** without looking.

> [!success]- Answers
> 1 Lombok (JPA needs no-arg constructor, setters, proxies) · 2 record (immutable data carrier) · 3 record (value object, correct `equals`/`hashCode`) · 4 Lombok `@Builder` (records have no builder) · 5 Lombok / normal class (records can't extend a class)

---

## Ex 19 · Sealed types · ⏱ 8 min

> [!abstract] 📖 Re-read first
> **Modern Java → §7 Sealed classes and interfaces**: the modifier table, *Why use it*, and *Sealed vs enum*.

Put all of this in one file `Ex19.java` (top-level types without `public`):

```java
sealed interface Vehicle permits Car, Bike, Truck { }
record Car(String regNo) implements Vehicle { }
```

**Part A: Does it compile?**
a. `final class Bike implements Vehicle { }`
b. `class Bike implements Vehicle { }` (no modifier)
c. `non-sealed class Truck implements Vehicle { }` and then `class MiniTruck extends Truck { }`
d. `class Scooter implements Vehicle { }`
e. Why does `Car` need no modifier?

**Part B: Write** `static int wheels(Vehicle v)` as a switch expression with **no `default`**: Car 4, Bike 2, Truck 6. What does `wheels(new MiniTruck())` return?

**Part C: Enum or sealed?**
`LoanStatus` · `Collateral` (Vehicle has regNo, Property has address, Gold has grams) · `Currency` (INR, USD) · `PaymentMethod` (Card has number, UPI has VPA)

> [!success]- Answers
> **A:** a ✅ · b ❌ `sealed, non-sealed or final modifiers expected` · c ✅ (`non-sealed` opens it again) · d ❌ not in `permits` · e records are already `final`
>
> **B:** (needs Java 21)
> ```java
> static int wheels(Vehicle v) {
>     return switch (v) {
>         case Car c   -> 4;
>         case Bike b  -> 2;
>         case Truck t -> 6;
>     };
> }
> ```
> `wheels(new MiniTruck())` → `6` (a MiniTruck **is a** Truck).
>
> **C:** enum · sealed · enum · sealed. Rule: same shape, single instances → enum; each kind carries **its own data** → sealed.

---

## Ex 20 · Pattern matching · ⏱ 10 min

> [!abstract] 📖 Re-read first
> **Modern Java → §8 Pattern matching**: `instanceof` scope rules, `switch` patterns with `null` and `when`, order matters, record patterns.

**Part A: Rewrite** this without the cast:

```java
if (obj instanceof String) {
    String s = (String) obj;
    if (s.length() > 3) System.out.println(s.toUpperCase());
}
```

**Part B: Does it compile?**
a. `if (obj instanceof String s || s.isEmpty()) { }`
b. `if (obj instanceof String s && s.isEmpty()) { }`
c. ```java
   static int len(Object o) {
       if (!(o instanceof String s)) return -1;
       return s.length();
   }
   ```

**Part C: Write and predict.** `static String describe(Object o)` exactly like the notes (null, guarded Integer > 100, Integer, String, default). Predict, then run, for: `null`, `500`, `7`, `"hi"`, `3.5`.
- Then **remove** `case null` and call `describe(null)`. What happens?
- Then **swap** the two Integer cases. What does the compiler say?

**Part D: Record patterns**
`record Point(int x, int y)`. Write `static String where(Object o)` with a switch:
`Point` with `x == y` → `"on diagonal"`, any other `Point` → `"point x,y"`, anything else → `"not a point"`.

> [!success]- Answers
> **A:**
> ```java
> if (obj instanceof String s && s.length() > 3) System.out.println(s.toUpperCase());
> ```
> **B:** a ❌ (`s` may not exist on the right of `||`) · b ✅ · c ✅ (after the early `return`, `o` **must** be a String, so `s` is in scope)
>
> **C:** `null` · `big number` · `number 7` · `text hi` · `other`. Without `case null` → 💥 `NullPointerException`. Swapped → `this case label is dominated by a preceding case label`.
>
> **D:**
> ```java
> static String where(Object o) {
>     return switch (o) {
>         case Point(int x, int y) when x == y -> "on diagonal";
>         case Point(int x, int y)             -> "point " + x + "," + y;
>         default                              -> "not a point";
>     };
> }
> ```

---

## Ex 21 · Optional · ⏱ 13 min

> [!abstract] 📖 Re-read first
> **Modern Java → §9 Optional**: the method table, the traps box, and *Where to use Optional*.

**Setup**

```java
record Customer(String id, String name) { }
record Address(String city) { }

static Map<String, Customer> db = Map.of(
        "C1", new Customer("C1", "Asha"),
        "C2", new Customer("C2", "  "));
static Map<String, Address> addresses = Map.of("C1", new Address("Pune"));

static Optional<Customer> findById(String id) { return Optional.ofNullable(db.get(id)); }
static Optional<Address> findAddress(Customer c) { return Optional.ofNullable(addresses.get(c.id())); }
static String loadDefault() { System.out.println("DB CALL"); return "Guest"; }
```

**Part A: Write one chain each**
1. Name of `id`, or `"Unknown"` if missing **or blank**. Check C1, C2, C9.
2. The customer, or throw `IllegalArgumentException("no customer " + id)`.
3. City of `id` (customer → address → city), or `"NA"`. Hint: `flatMap`. Check C1 and C2.
4. `ifPresentOrElse`: print `"Hi <name>"` or `"not found"`.
5. From `List.of("C1", "C9", "C2")`, collect only the customers that exist, using `findById(id).stream()`.

**Part B: Predict**

```java
System.out.println(Optional.of("Asha").orElse(loadDefault()));             // 1
System.out.println(Optional.of("Asha").orElseGet(() -> loadDefault()));    // 2
System.out.println(Optional.empty().isEmpty());                            // 3
Optional.of(null);                                                         // 4
Optional.empty().get();                                                    // 5
```

**Part C: Refactor**

```java
Optional<Customer> opt = findById(id);
if (opt.isPresent()) {
    return opt.get().name().toUpperCase();
}
return "N/A";
```

**Part D: What's wrong with each, and the fix?**
a. a field `private Optional<String> middleName;`
b. a method `void sendMail(Optional<String> email)`
c. a method `Optional<List<Order>> findOrders(String id)`

> [!success]- Answers
> **A:**
> ```java
> String name = findById(id).map(Customer::name).filter(n -> !n.isBlank()).orElse("Unknown");
> // C1 → Asha, C2 → Unknown (blank), C9 → Unknown
>
> Customer c = findById(id).orElseThrow(() -> new IllegalArgumentException("no customer " + id));
>
> String city = findById(id).flatMap(Ex21::findAddress).map(Address::city).orElse("NA");
> // C1 → Pune, C2 → NA
>
> findById(id).ifPresentOrElse(
>         cu -> System.out.println("Hi " + cu.name()),
>         () -> System.out.println("not found"));
>
> List<Customer> found = List.of("C1", "C9", "C2").stream()
>         .flatMap(i -> findById(i).stream())
>         .toList();                          // [C1 Asha, C2]
> ```
> `flatMap` (not `map`) because `findAddress` already returns an `Optional`. `map` would give `Optional<Optional<Address>>`.
>
> **B:** 1 prints `DB CALL` **then** `Asha` (orElse is always evaluated) · 2 prints only `Asha` · 3 `true` · 4 💥 `NullPointerException` → use `ofNullable` · 5 💥 `NoSuchElementException`
>
> **C:**
> ```java
> return findById(id).map(Customer::name).map(String::toUpperCase).orElse("N/A");
> ```
> **D:** a → fields: use a plain nullable `String` · b → parameters: overload or accept `null`/a plain `String` · c → collections: return an **empty list**, `List<Order>`.

---

## Ex 22 · Capstone: Java 8 → modern rewrite · ⏱ 20 min

> [!abstract] 📖 Re-read first
> **Modern Java → §10 Java 8 → modern Java**: the before/after, the "What changed" table, and the *On Java 17* box. Skim, don't study: this exercise uses everything you already practiced.

**Starting code (Java 8 style).** Type it only if you want; the goal is the rewrite.

```java
public abstract class Collateral {
    private final double value;
    protected Collateral(double value) { this.value = value; }
    public double getValue() { return value; }
}
public final class VehicleCollateral extends Collateral {
    private final String regNo;
    public VehicleCollateral(String regNo, double value) { super(value); this.regNo = regNo; }
    public String getRegNo() { return regNo; }
    // equals, hashCode, toString by hand…
}
public final class PropertyCollateral extends Collateral { /* address, same boilerplate */ }
public final class GoldCollateral extends Collateral { /* grams, same boilerplate */ }

public class LtvCalculator {
    public double maxLoan(Collateral c) {
        if (c instanceof VehicleCollateral) return c.getValue() * 0.85;
        else if (c instanceof PropertyCollateral) return c.getValue() * 0.70;
        else if (c instanceof GoldCollateral) return c.getValue() * 0.75;
        throw new IllegalArgumentException("Unknown: " + c);
    }
    public String label(CollateralStatus s) {
        String label;
        switch (s) {
            case PENDING_VALUATION: label = "Waiting for valuation"; break;
            case VERIFIED: label = "Verified"; break;
            default: label = "Rejected";
        }
        return label;
    }
}
```

**Rewrite it with all of these:**
1. `sealed interface Collateral permits Vehicle, Property, Gold` with `BigDecimal value()`.
2. Three **records**, each with a **compact constructor**: reject `null` fields and `value <= 0`.
3. `maxLoan` as a **pattern switch** with no `default`, result in **`BigDecimal`, scale 2, `HALF_EVEN`** (no `double` anywhere: it's money).
4. `label` as a **switch expression** (no `default`) over `enum CollateralStatus { PENDING_VALUATION, VERIFIED, REJECTED }`.
5. `static Optional<Collateral> find(Map<String, Collateral> db, String ref)`.
6. Print a summary with a **text block** + `.formatted(...)`:
   ```
   Collateral : Vehicle
   Value      : 800000
   Max loan   : 680000.00
   Status     : Verified
   ```
7. Use `var` where the type is obvious from the right side, and **only** there.

**Then:**
- Add `record FixedDeposit(String fdNumber, BigDecimal value)` (LTV 90%) to `permits`. Count the compile errors before you fix them.
- Write the **Java 17** version of `maxLoan` (instanceof chain). What protection do you lose?

**Test values:** Vehicle 800000 → `680000.00` · Property 5000000 → `3500000.00` · Gold 200000 → `150000.00`

> [!success]- Reference solution
> ```java
> import java.math.*;
> import java.util.*;
>
> sealed interface Collateral permits Vehicle, Property, Gold {
>     BigDecimal value();
> }
>
> record Vehicle(String regNo, BigDecimal value) implements Collateral {
>     Vehicle {
>         Objects.requireNonNull(regNo, "regNo");
>         if (value == null || value.signum() <= 0) throw new IllegalArgumentException("value must be > 0");
>     }
> }
> record Property(String address, BigDecimal value) implements Collateral {
>     Property {
>         Objects.requireNonNull(address, "address");
>         if (value == null || value.signum() <= 0) throw new IllegalArgumentException("value must be > 0");
>     }
> }
> record Gold(BigDecimal grams, BigDecimal value) implements Collateral {
>     Gold {
>         Objects.requireNonNull(grams, "grams");
>         if (value == null || value.signum() <= 0) throw new IllegalArgumentException("value must be > 0");
>     }
> }
>
> enum CollateralStatus { PENDING_VALUATION, VERIFIED, REJECTED }
>
> public class Ex22 {
>     static BigDecimal maxLoan(Collateral c) {
>         BigDecimal ltv = switch (c) {
>             case Vehicle v  -> new BigDecimal("0.85");
>             case Property p -> new BigDecimal("0.70");
>             case Gold g     -> new BigDecimal("0.75");
>         };
>         return c.value().multiply(ltv).setScale(2, RoundingMode.HALF_EVEN);
>     }
>
>     static String label(CollateralStatus s) {
>         return switch (s) {
>             case PENDING_VALUATION -> "Waiting for valuation";
>             case VERIFIED          -> "Verified";
>             case REJECTED          -> "Rejected";
>         };
>     }
>
>     static Optional<Collateral> find(Map<String, Collateral> db, String ref) {
>         return Optional.ofNullable(db.get(ref));
>     }
>
>     public static void main(String[] args) {
>         var db = new HashMap<String, Collateral>();
>         db.put("V1", new Vehicle("MH12AB1234", new BigDecimal("800000")));
>         db.put("P1", new Property("Baner, Pune", new BigDecimal("5000000")));
>         db.put("G1", new Gold(new BigDecimal("30"), new BigDecimal("200000")));
>
>         Collateral c = find(db, "V1").orElseThrow();
>         String report = """
>                 Collateral : %s
>                 Value      : %s
>                 Max loan   : %s
>                 Status     : %s
>                 """.formatted(c.getClass().getSimpleName(), c.value(), maxLoan(c),
>                               label(CollateralStatus.VERIFIED));
>         System.out.print(report);
>     }
> }
> ```
> **Adding `FixedDeposit`:** 1 error, in `maxLoan` (the switch no longer covers all types). Add `case FixedDeposit f -> new BigDecimal("0.90");`.
>
> **Java 17 `maxLoan`:**
> ```java
> BigDecimal ltv;
> if (c instanceof Vehicle) ltv = new BigDecimal("0.85");
> else if (c instanceof Property) ltv = new BigDecimal("0.70");
> else if (c instanceof Gold) ltv = new BigDecimal("0.75");
> else throw new IllegalStateException("Unknown: " + c);
> return c.value().multiply(ltv).setScale(2, RoundingMode.HALF_EVEN);
> ```
> You lose **exhaustiveness**: add `FixedDeposit` and it still compiles, then fails at **run time**.

---

# ⚡ Rapid-fire recall · ⏱ 12 min

> [!warning] No notes, no IDE. Answer all 20 in writing, then check. Every miss = re-read that section once more.

1. What's the default of a local `int`?
2. `long x = 1_000_000 * 1_000_000;`: value, and the fix?
3. Integer cache range? Which wrappers have no cache?
4. `Integer c = 128; c == 128`: result and why?
5. `list.remove(1)` vs `list.remove(Integer.valueOf(1))`?
6. Why is `0.1 + 0.2 != 0.3`? One sentence.
7. Two ways to store money, and when you'd pick each.
8. Three BigDecimal traps.
9. Four reasons String is immutable.
10. `"hel" + "lo" == "hello"`? With a variable `part + "lo"`? With `final part`?
11. String vs StringBuilder vs StringBuffer: one line each.
12. Why is `+=` in a loop O(n²)?
13. `"a😀".length()`?
14. Five places `var` is not allowed.
15. What does the compiler generate for a record? Three things a record can't do.
16. `final` / `sealed` / `non-sealed` on a permitted subtype?
17. Sealed vs enum: when each?
18. `orElse` vs `orElseGet`? Three places not to use Optional.
19. Which two features in the notes need Java 21?
20. `"a.b.c".split(".").length`?

> [!success]- Answers
> 1. None: using it is a compile error.
> 2. `-727379968`; write `1_000_000L * 1_000_000`.
> 3. −128..127; `Double`, `Float`.
> 4. `true`: one side is primitive, so `c` is unboxed.
> 5. Removes index 1 vs removes the value 1.
> 6. `double` is binary, and 0.1 / 0.2 can't be stored exactly in binary, so the rounding errors add up.
> 7. `long` paise: fast, high-volume ledgers/payments. `BigDecimal`: interest, tax, EMI (rounding control).
> 8. `new BigDecimal(0.1)` (use the String constructor), `equals` checks scale (use `compareTo`), endless `divide` throws (pass scale + RoundingMode), immutable (assign the result). Any three.
> 9. Pool sharing, security, cached hashCode / safe map keys, thread safety.
> 10. `true`; `false`; `true`.
> 11. Immutable · mutable and fastest · mutable, synchronized, legacy.
> 12. Each `+=` copies all old characters into a new String: 1 + 2 + … + n.
> 13. `3`.
> 14. No initializer, `= null`, fields, parameters/return types, lambda, array initializer `{…}`. Any five.
> 15. private final fields, canonical constructor, accessors, `equals`/`hashCode`, `toString`. Can't extend a class, can't add instance fields, can't be extended (final), can't be mutated.
> 16. Closed / lists its own subtypes / open to anyone.
> 17. Enum = fixed single instances. Sealed = fixed set of types, each with its own data.
> 18. `orElse` always runs its argument, `orElseGet` only when empty. Not for fields, parameters, collections (also serialized types).
> 19. Pattern matching for `switch`, record patterns.
> 20. `0`.

> [!tip] Make it stick
> In **1 day**, **3 days** and **7 days**, redo only the **Predict** parts of exercises with a ❌ plus this recall test (~30 min). When a full round has no ❌, it's second nature.
