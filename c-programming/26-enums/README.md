# 26. Enums

**Previous:** [← 25. Typedef](../25-typedef/README.md) | **Home:** [README](../README.md) | **Next:** [27. Unions →](../27-unions/README.md)

## The problem with magic numbers

```c
int status = 2;   // what does 2 even mean here? You'd have to go check.
```

Bare numbers scattered through code with implicit meaning ("magic
numbers") make code hard to read and easy to get wrong. `enum` gives
meaningful names to a set of related constant values.

## Basic enum

```c
#include <stdio.h>

enum Status { PENDING, ACTIVE, COMPLETED, CANCELLED };

int main(void) {
    enum Status s = ACTIVE;

    if (s == ACTIVE) {
        printf("Currently active\n");
    }

    return 0;
}
```

By default, enum values are just sequential integers starting at 0:
`PENDING = 0`, `ACTIVE = 1`, `COMPLETED = 2`, `CANCELLED = 3`. This is
exactly what `#define`-ing a pile of constants would give you manually —
`enum` just does it more cleanly, and gives the compiler a chance to
sanity-check your usage in ways plain integers can't.

## Custom values

```c
enum HttpStatus {
    OK = 200,
    NOT_FOUND = 404,
    SERVER_ERROR = 500
};
```

You can assign specific values where they matter (like matching real
protocol codes), and unassigned members after that continue counting up
from the last explicit value.

## Why prefer enum over #define for this

```c
#define PENDING 0
#define ACTIVE 1
```

`#define` works, technically, but it's handled by the preprocessor before
the compiler ever sees real types — the compiler has zero awareness that
`PENDING` and `ACTIVE` are related or represent a closed set of options.
An `enum` groups them as one named type, which means some compilers and
static analysis tools can warn you about things like a `switch` statement
that doesn't handle every enum value — a genuinely useful safety net that
`#define` can't give you.

## Using enums with switch — a natural pairing

```c
#include <stdio.h>

enum Status { PENDING, ACTIVE, COMPLETED, CANCELLED };

void print_status(enum Status s) {
    switch (s) {
        case PENDING:
            printf("Pending\n");
            break;
        case ACTIVE:
            printf("Active\n");
            break;
        case COMPLETED:
            printf("Completed\n");
            break;
        case CANCELLED:
            printf("Cancelled\n");
            break;
    }
}
```

## The underlying type is still just an int

```c
enum Status s = ACTIVE;
printf("%d\n", s);   // 1 — enums print as their underlying integer value by default
```

There's no built-in way to print an enum's *name* (like "ACTIVE")
directly — `printf` only knows about the underlying integer. If you want
readable output, you write your own mapping (an array of strings indexed
by the enum value, or a `switch` like the one above).

## Try it yourself

- Define an `enum Day { SUNDAY, MONDAY, ..., SATURDAY }` and write a
  function that takes a `Day` and returns `1` if it's a weekend, `0`
  otherwise.
- Give an enum a mix of explicit and default values (like the
  `HttpStatus` example) and print out every member to confirm you
  understand how the "counting up" behavior works after an explicit
  value.
- Write a small array of strings that maps each `enum Status` value to
  its readable name, and use it to actually print "Active" instead of `1`.

---
**Next:** [27. Unions →](../27-unions/README.md)
