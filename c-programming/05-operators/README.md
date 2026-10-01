# 05. Operators

**Previous:** [← 04. Input & Output](../04-input-output/README.md) | **Home:** [README](../README.md) | **Next:** [06. Conditions →](../06-conditions/README.md)

## Arithmetic operators

```c
int a = 10, b = 3;

printf("%d\n", a + b);   // 13
printf("%d\n", a - b);   // 7
printf("%d\n", a * b);   // 30
printf("%d\n", a / b);   // 3  <- integer division truncates!
printf("%d\n", a % b);   // 1  <- remainder (modulo)
```

**Integer division is the one that trips people up.** `10 / 3` in C is `3`,
not `3.333...`, because both operands are integers, so the result is
forced back into an integer. If you want a decimal result, at least one
operand needs to be a float:

```c
float result = (float)a / b;   // 3.333333
```

That `(float)` is a **cast** — an explicit instruction to treat `a` as a
float before the division happens. Without it, C does integer division
first and *then* you'd be assigning a truncated `3` into a float, which
technically compiles but gives you the wrong answer.

## Relational operators

```c
a == b   // equal to
a != b   // not equal to
a > b    // greater than
a < b    // less than
a >= b   // greater than or equal to
a <= b   // less than or equal to
```

These return `1` (true) or `0` (false) — C doesn't have a dedicated boolean
type in older standards; it just uses integers where `0` is false and
anything nonzero is true. (C99 introduced `_Bool` / `<stdbool.h>` if you
want `true`/`false` keywords — purely cosmetic, works the same underneath.)

## The classic beginner bug: `=` vs `==`

```c
int x = 5;

if (x = 10) {     // BUG: assignment, not comparison!
    printf("This always runs\n");
}
```

`x = 10` assigns `10` to `x` *and evaluates to `10`*, which is truthy, so
the `if` block runs every single time, regardless of what you meant to
check. This compiles cleanly and will absolutely burn you at some point.
Always compile with `-Wall` — GCC will usually flag this specific mistake
with a warning like "suggest parentheses around assignment."

## Logical operators

```c
a && b   // logical AND — true if both are true
a || b   // logical OR — true if at least one is true
!a       // logical NOT — flips true/false
```

```c
int age = 20;
int has_id = 1;

if (age >= 18 && has_id) {
    printf("Allowed in\n");
}
```

C uses **short-circuit evaluation**: in `a && b`, if `a` is false, `b` is
never even evaluated (the whole expression can't be true anyway). This
matters when the second condition has side effects or could crash:

```c
if (ptr != NULL && ptr->value > 0) {
    // safe: if ptr IS NULL, the left side is false,
    // so ptr->value is never touched — no crash
}
```

## Increment / decrement

```c
int x = 5;
x++;   // x is now 6 (post-increment)
x--;   // x is now 5 again (post-decrement)
++x;   // pre-increment: x is now 6
```

Post (`x++`) vs pre (`++x`) only actually matters when you use the
expression's *value* in the same statement:

```c
int a = 5;
int b = a++;   // b = 5, then a becomes 6
int c = 5;
int d = ++c;   // c becomes 6 first, then d = 6
```

Outside of that specific case (using the result inline), `x++;` and `++x;`
as standalone statements behave identically.

## Operator precedence — the quiet source of bugs

```c
int result = 2 + 3 * 4;   // 14, not 20 — multiplication binds tighter
int check = 1 || 0 && 0;  // && binds tighter than ||, so this is 1 || (0 && 0) = 1
```

You don't need to memorize the entire precedence table. You need to
memorize one habit instead: **when in doubt, add parentheses.** It costs
nothing, and it makes your intent obvious to anyone reading the code later
— including you, in six months.

## Try it yourself

- Predict the output of `7 / 2` and `7.0 / 2` before running them, then
  check yourself.
- Write an `if` statement using `=` instead of `==` on purpose, compile
  with `-Wall`, and read the warning GCC gives you.
- Write a short-circuit example where the second condition would crash the
  program if it were ever evaluated (like dividing by a variable that's
  zero), and prove the crash never happens because of the ordering.

---
**Next:** [06. Conditions →](../06-conditions/README.md)
