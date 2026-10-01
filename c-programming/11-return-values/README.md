# 11. Return Values

**Previous:** [← 10. Functions](../10-functions/README.md) | **Home:** [README](../README.md) | **Next:** [12. Scope →](../12-scope/README.md)

## The basics

```c
int square(int n) {
    return n * n;
}
```

`return` does two things at once: it hands a value back to the caller,
*and* it immediately exits the function — any code after a `return`
statement in the same execution path never runs.

## A function can return from multiple points

```c
int classify(int n) {
    if (n < 0) {
        return -1;
    }
    if (n == 0) {
        return 0;
    }
    return 1;
}
```

This is completely normal and often clearer than forcing every function to
have a single exit point with nested conditions. Just make sure every
possible path through the function actually reaches a `return` — the
compiler won't always catch it if you leave one out (though `-Wall` often
warns about "control reaching end of non-void function").

## Using an int as a boolean

Older C doesn't have a real boolean type, so returning "yes/no" answers as
`int` (`1` for true, `0` for false) is standard:

```c
int is_even(int n) {
    return n % 2 == 0;
}

int main(void) {
    if (is_even(4)) {
        printf("Even\n");
    }
    return 0;
}
```

`n % 2 == 0` already evaluates to `1` or `0`, so you can return it
directly — no need for `if (n % 2 == 0) return 1; else return 0;`. If you
want the actual `true`/`false` keywords for readability, `#include
<stdbool.h>` and use the `bool` type — it's the same mechanism underneath,
just nicer to read.

## "Returning" more than one value

A C function can only return a single value directly. When you need to
hand back multiple results, you've got two real options, both of which get
their own proper treatment later:

- **Return a struct** containing multiple fields (chapter 24)
- **Use pointer parameters** so the function writes results directly into
  variables owned by the caller (chapter 18) — this is the far more common
  approach in real C code, especially when one of your "results" is
  actually a success/failure status

```c
// preview — you'll fully understand this after chapter 18
int divide(int a, int b, int *result) {
    if (b == 0) {
        return 0;   // failure
    }
    *result = a / b;
    return 1;       // success
}
```

The function returns a simple success/failure flag, while the actual
computed value gets written into memory the caller already owns. This
pattern — return an int status, write real data through a pointer — is
everywhere in real-world C, including the standard library itself.

## Ignoring return values is a real source of bugs

```c
FILE *f = fopen("data.txt", "r");
// what if fopen failed and returned NULL? Using f without checking is
// a crash waiting to happen — you'll see this properly in chapter 28
```

Many standard library functions return a status specifically so you can
check whether something went wrong. Ignoring that return value is one of
the most common reasons real C programs crash unpredictably — not because
the language is unstable, but because the caller skipped a check the
function was explicitly offering.

## Try it yourself

- Write a function `int max(int a, int b)` that returns the larger of the
  two, then extend it to `int max3(int a, int b, int c)`.
- Write a function that returns `1` if a number is prime and `0`
  otherwise, reusing the logic from chapter 09.
- Write a function with an early `return` for an invalid input (like a
  negative number where only positives make sense), and think about why
  checking that first, before the "real" logic, tends to make functions
  easier to read.

---
**Next:** [12. Scope →](../12-scope/README.md)
