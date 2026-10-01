# 06. Conditions

**Previous:** [← 05. Operators](../05-operators/README.md) | **Home:** [README](../README.md) | **Next:** [07. Switch →](../07-switch/README.md)

## if / else — the basics

```c
int age = 20;

if (age >= 18) {
    printf("Adult\n");
} else {
    printf("Minor\n");
}
```

If the condition inside `if (...)` evaluates to nonzero (true), the block
runs. Otherwise, control falls to `else` if one exists.

## else if — chaining conditions

```c
int score = 75;

if (score >= 90) {
    printf("Grade: A\n");
} else if (score >= 80) {
    printf("Grade: B\n");
} else if (score >= 70) {
    printf("Grade: C\n");
} else {
    printf("Grade: F\n");
}
```

C checks each condition in order and stops at the first one that's true —
so ordering matters. If you put `score >= 70` before `score >= 90`, a
score of 95 would incorrectly match the `70` branch first, because 95 is
also `>= 70` and that check comes first.

## Combining conditions

```c
int age = 25;
int has_license = 1;

if (age >= 18 && has_license) {
    printf("Can drive\n");
}

int is_weekend = 0;
int is_holiday = 1;

if (is_weekend || is_holiday) {
    printf("No work today\n");
}
```

## Nested conditions

```c
int age = 20;
int has_ticket = 1;

if (age >= 18) {
    if (has_ticket) {
        printf("Entry allowed\n");
    } else {
        printf("Need a ticket\n");
    }
} else {
    printf("Too young\n");
}
```

This works, but nesting more than 2-3 levels deep usually means you should
restructure — either combine conditions with `&&`, or pull logic into a
separate function (chapter 10). Deeply nested `if` blocks are one of the
most common reasons C code becomes hard to read.

## The ternary operator

```c
int age = 20;
const char *status = (age >= 18) ? "adult" : "minor";
printf("%s\n", status);
```

`condition ? value_if_true : value_if_false` — a compact `if/else` that
produces a *value* rather than executing a block. Great for simple cases,
a readability nightmare if you try to chain several together. Use it for
one clean decision, not for complex branching logic.

## The `=` vs `==` bug, revisited

Worth repeating here because conditions are exactly where it bites:

```c
int flag = 0;

if (flag = 1) {    // BUG — always true, and it just silently set flag to 1
    printf("Uh oh\n");
}
```

Compile with `-Wall -Wextra` and GCC will flag this. Get in the habit of
reading compiler warnings, not just errors — a warning like this one is
often more damaging than an outright error, because errors stop you but
warnings let broken code run.

## Try it yourself

- Write a program that categorizes a number as negative, zero, or positive
  using `if/else if/else`.
- Write the same grading logic shown above but deliberately put the
  conditions in the wrong order, then see how the results become wrong for
  high scores.
- Rewrite one of your `if/else` blocks using the ternary operator instead.

---
**Next:** [07. Switch →](../07-switch/README.md)
