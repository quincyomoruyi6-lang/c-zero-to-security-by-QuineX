# 03. Variables & Data Types

**Previous:** [← 02. First Program](../02-first-program/README.md) | **Home:** [README](../README.md) | **Next:** [04. Input & Output →](../04-input-output/README.md)

## Variables are just labeled memory

A variable in C is a name attached to a chunk of memory of a specific size.
Unlike Python or JavaScript, C wants to know *up front* what type of data
you're storing, because that determines exactly how many bytes to reserve
and how to interpret the bits sitting there.

```c
int age = 18;
float price = 9.99f;
char grade = 'A';
```

That's it — declare the type, give it a name, optionally assign a value.

## The core types

| Type | Typical size | Holds |
|------|-------------|-------|
| `char` | 1 byte | A single character (or a small integer, -128 to 127) |
| `int` | 4 bytes | Whole numbers, roughly ±2.1 billion |
| `float` | 4 bytes | Decimal numbers, ~6-7 significant digits of precision |
| `double` | 8 bytes | Decimal numbers, much higher precision than float |
| `unsigned int` | 4 bytes | Whole numbers, 0 to ~4.2 billion (no negatives) |

"Typical" is doing some work in that table — C only guarantees minimum
sizes, not exact ones. On virtually every modern desktop/laptop system
(x86-64, ARM64) the sizes above hold, but embedded platforms can differ.
This is exactly why chapter 38 introduces `stdint.h` types like `uint8_t`
that guarantee an exact size regardless of platform — important once you're
writing code for a microcontroller instead of a laptop.

## Checking sizes yourself

Never memorize sizes when you can just ask the compiler:

```c
#include <stdio.h>

int main(void) {
    printf("int: %zu bytes\n", sizeof(int));
    printf("float: %zu bytes\n", sizeof(float));
    printf("double: %zu bytes\n", sizeof(double));
    printf("char: %zu bytes\n", sizeof(char));
    return 0;
}
```

`sizeof` is a compile-time operator (not a function, even though it looks
like one) that returns the size in bytes of a type or variable. `%zu` is
the correct format specifier for the `size_t` type that `sizeof` returns —
using `%d` here technically works on most systems but is undefined
behavior; get in the habit of using the right specifier.

## Signed vs unsigned

By default, numeric types are **signed** — they can hold negative values,
splitting their range roughly in half between negative and positive.
`unsigned` types give up negative numbers in exchange for double the
positive range.

```c
int a = -5;            // fine
unsigned int b = -5;   // "works" but wraps around to a huge positive number
```

That wraparound isn't a crash — it's silent, and it's a real source of
bugs (and a real source of security vulnerabilities: integer overflow bugs
are a whole category of CVEs). Be deliberate about when you use `unsigned`.
A good rule: only use it when a negative value would be genuinely
meaningless (like a size or a count), and even then, be careful with
subtraction.

## Declaring and initializing

```c
int x;          // declared, but uninitialized — contains garbage memory
int y = 0;      // declared and initialized
int a, b, c;    // multiple declarations in one line
int p = 1, q = 2;
```

**Uninitialized local variables do not default to zero.** They contain
whatever bits were already sitting in that memory location from whatever
used it last. Reading an uninitialized variable is undefined behavior —
sometimes it'll happen to be zero, sometimes it'll be garbage, and you
cannot rely on either. Always initialize your variables.

## `const` — a value that shouldn't change

```c
const float PI = 3.14159f;
```

Attempting to reassign a `const` variable is a compile error, not a
runtime surprise — which is exactly the point. If a value is meant to
stay fixed, say so, and let the compiler catch you if you (or someone
maintaining your code later) tries to change it by accident.

## Format specifiers, quickly (more in chapter 04)

| Type | Specifier |
|------|-----------|
| `int` | `%d` |
| `unsigned int` | `%u` |
| `float` | `%f` |
| `double` | `%f` (with printf; `%lf` with scanf) |
| `char` | `%c` |
| `char*` (string) | `%s` |

## Try it yourself

- Write a program declaring one variable of each core type, print all of
  them with the correct specifiers.
- Assign `-1` to an `unsigned int` and print it. Explain to yourself *why*
  you got the number you got (hint: it relates directly to the type's size
  in bits).
- Try reassigning a `const` variable and read the compiler error carefully.

---
**Next:** [04. Input & Output →](../04-input-output/README.md)
