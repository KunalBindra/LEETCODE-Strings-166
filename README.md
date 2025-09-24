# LEETCODE-Strings-166
---

## Problem Reminder

We need to convert a fraction (numerator / denominator) into a string.

* If it terminates, return the decimal normally.
* If it repeats, put the repeating part in parentheses `"()"`.

Example:

```
1/2  → "0.5"
2/3  → "0.(6)"
-22/7 → "-3.(142857)"
```

---

## Code Recap (simplified explanation)

```java
public String fractionToDecimal(int numerator, int denominator) {
    if(numerator == 0) return "0";

    StringBuilder result = new StringBuilder();

    // Step 1: Handle negative
    if((long) numerator * (long) denominator < 0) result.append('-');

    // Step 2: Work with positive numbers
    long absNum = Math.abs((long) numerator);
    long absDen = Math.abs((long) denominator);

    // Step 3: Integer part
    long Intpart = absNum / absDen;
    result.append(Intpart);

    // Step 4: Remainder part
    long rempart = absNum % absDen;
    if(rempart == 0) return result.toString(); // no decimal

    result.append(".");

    // Step 5: Map to detect cycles
    Map<Long, Integer> mp = new HashMap<>();

    while(rempart != 0) {
        if(mp.containsKey(rempart)) {
            int pos = mp.get(rempart);
            result.insert(pos, "(");
            result.append(")");
            break;
        }
        mp.put(rempart, result.length());

        rempart *= 10;
        long digit = rempart / absDen;
        result.append(digit);
        rempart %= absDen;
    }

    return result.toString();
}
```

---

## Dry Run Example 1: `numerator = 1, denominator = 2`

Expected: `"0.5"`

1. Numerator not zero.
   Result = `""`.

2. Check sign: `(1*2 > 0)` → not negative.
   Result = `""`.

3. absNum = 1, absDen = 2.
   Integer part = `1/2 = 0`.
   Append → `"0"`.

4. Remainder = `1 % 2 = 1`. Not zero.
   Append `"."` → `"0."`.

5. Loop begins:

   * Map is empty. Put `(rem=1, pos=2)` → mp = `{1: 2}`.
   * rempart = 1 \* 10 = 10.
   * digit = 10 / 2 = 5. Append `"5"` → `"0.5"`.
   * rempart = 10 % 2 = 0. Exit loop.

✅ Answer = `"0.5"`

---

## Dry Run Example 2: `numerator = 2, denominator = 3`

Expected: `"0.(6)"`

1. Numerator not zero. Result = `""`.

2. Sign check: `2*3 > 0` → not negative.
   Result = `""`.

3. absNum = 2, absDen = 3.
   Integer part = `0`. Append `"0"`.

4. Remainder = `2`. Not zero.
   Append `"."` → `"0."`.

5. Loop:

   * **First iteration**:
     rem=2 not in map. Put `(2,2)`.
     rem=2\*10=20. digit=20/3=6. Append `"6"` → `"0.6"`.
     rem=20%3=2.
     Now rem=2 already in map!

   * Insert "(" at pos=2 → `"0.(6"`.

   * Append ")" → `"0.(6)"`. Break.

✅ Answer = `"0.(6)"`

---

## Dry Run Example 3: `numerator = -22, denominator = 7`

Expected: `"-3.(142857)"`

1. Numerator not zero.
   Result = `""`.

2. Sign check: -22 \* 7 < 0 → negative.
   Append `"-"` → `"-"`.

3. absNum = 22, absDen = 7.
   Integer part = `22/7 = 3`. Append `"3"` → `"-3"`.

4. Remainder = `22%7 = 1`.
   Append `"."` → `"-3."`.

5. Loop:

   * rem=1, store `(1,3)`. Multiply 10 → 10. digit=10/7=1. Append `"1"` → `"-3.1"`. rem=3.
   * rem=3, store `(3,4)`. Multiply 10 → 30. digit=30/7=4. Append `"4"` → `"-3.14"`. rem=2.
   * rem=2, store `(2,5)`. Multiply 10 → 20. digit=20/7=2. Append `"2"` → `"-3.142"`. rem=6.
   * rem=6, store `(6,6)`. Multiply 10 → 60. digit=60/7=8. Append `"8"` → `"-3.1428"`. rem=4.
   * rem=4, store `(4,7)`. Multiply 10 → 40. digit=40/7=5. Append `"5"` → `"-3.14285"`. rem=5.
   * rem=5, store `(5,8)`. Multiply 10 → 50. digit=50/7=7. Append `"7"` → `"-3.142857"`. rem=1.
   * rem=1 already in map! Insert "(" at pos=3 → `"-3.(142857"`. Append ")" → `"-3.(142857)"`.

✅ Answer = `"-3.(142857)"`

---

### Beginner Key Takeaways

* **Use `StringBuilder`** to build string step by step.
* **HashMap stores remainder → index in string** so we know when a cycle starts.
* **When remainder repeats → put "(" before that digit and ")" at the end**.
* Integer division gives **whole part**, modulo gives **remainder**.
* Multiplying remainder by 10 simulates "bringing down a zero" in long division.

---
