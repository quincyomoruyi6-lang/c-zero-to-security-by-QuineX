# 12. Scope

**Previous:** [← 11. Return Values](../11-return-values/README.md) | **Home:** [README](../README.md) | **Next:** [13. Arrays →](../13-arrays/README.md)

## Local scope

Variables declared inside a function (or inside any `{ }` block) only
exist within that block. They're created when execution enters the block
and destroyed when it exits.

```c
void foo(void) {
    int x = 5;   // local to foo
    printf("%d\n", x);
}

int main(void) {
    foo();
    printf("%d\n", x);   // ERROR: x doesn't exist here
    return 0;
}
```

This is a good thing, not a limitation — it means functions can't
accidentally step on each other's variables just because they happen to
use the same name.

## Block scope, specifically

Scope isn't just per-function, it's per `{ }` block:

```c
int main(void) {
    int x = 10;

    if (x > 5) {
        int y = 20;   // y only exists inside this if-block
        printf("%d\n", y);
    }

    printf("%d\n", y);   // ERROR: y is out of scope here
    return 0;
}
```

## Global scope

Variables declared outside any function are global — visible to every
function in the file from the point they're declared onward.

```c
#include <stdio.h>

int counter = 0;   // global

void increment(void) {
    counter++;      // modifies the global directly
}

int main(void) {
    increment();
    increment();
    printf("%d\n", counter);   // 2
    return 0;
}
```

Globals are convenient, and also genuinely risky in anything beyond a
small program: any function can modify them, at any time, which makes it
hard to reason about *where* a value actually changed when you're
debugging something larger. Default to passing values through parameters
and return values; reach for a global only when you have a real reason
(shared state that truly belongs to the whole program, like a
configuration value set once at startup).

## static — scope vs lifetime

`static` on a local variable is a genuinely useful, slightly unusual
feature: the variable keeps its scope (still only visible inside that
function) but its **lifetime** extends for the entire life of the program
instead of being destroyed when the function returns.

```c
#include <stdio.h>

void counter(void) {
    static int count = 0;   // initialized ONCE, ever
    count++;
    printf("%d\n", count);
}

int main(void) {
    counter();   // 1
    counter();   // 2
    counter();   // 3
    return 0;
}
```

Without `static`, `count` would reset to `0` every time `counter()` is
called, and you'd see `1, 1, 1` instead. With `static`, the variable
persists between calls, remembering its value — while still being
completely inaccessible from outside `counter()`. This is genuinely handy
for things like call counters, caching a one-time computed value, or
simple state machines without reaching for a global.

`static` on a global variable (or a function) means something slightly
different — it restricts visibility to just that `.c` file, so other
files in a multi-file project can't see it. You'll bump into that usage
once projects grow beyond one file.

## Scope vs lifetime, summarized

| | Local | Local `static` | Global |
|---|---|---|---|
| **Visible from** | Just its own block | Just its own function | Whole file (from declaration onward) |
| **Exists for** | Just that block's execution | Entire program | Entire program |

## Try it yourself

- Write the `counter()` example above yourself, then remove `static` and
  compare the output — confirm you actually understand *why* it changes.
- Write a program with a global variable that two different functions both
  modify, and trace through by hand what its value is after each call.
- Try declaring two different variables with the same name in two nested
  blocks (an outer `{ }` and an inner `{ }`), and print both — this is
  called "shadowing," and it's legal but a genuine readability hazard.

---
**Next:** [13. Arrays →](../13-arrays/README.md)
