# 16. Memory

**Previous:** [← 15. Input Security](../15-input-security/README.md) | **Home:** [README](../README.md) | **Next:** [17. Pointers →](../17-pointers/README.md)

Everything from here through chapter 23 is really one continuous topic:
memory. This short chapter is the mental model you need before pointers
actually click — if you understand this, pointers stop being magic and
just become "addresses."

## Memory is one giant array of bytes

Forget variables for a second. At the hardware level, your computer's RAM
is just an enormous sequence of numbered storage slots, each holding one
byte. Every one of those slots has a unique number — its **address**.

```
Address:  1000  1001  1002  1003  1004  1005 ...
Content:  [ ? ] [ ? ] [ ? ] [ ? ] [ ? ] [ ? ] ...
```

When you write `int x = 5;`, the compiler picks some address (say, 1000),
reserves 4 consecutive bytes starting there (since `int` is 4 bytes), and
from then on, "x" is just a friendly name your source code uses to refer
to "whatever's stored at address 1000, interpreted as an int."

## Variables are a convenience layer over addresses

```c
#include <stdio.h>

int main(void) {
    int x = 5;
    printf("Value of x: %d\n", x);
    printf("Address of x: %p\n", (void *)&x);
    return 0;
}
```

The `&` operator gives you the actual address where a variable lives —
you'll use this constantly starting in the next chapter. `%p` is the
format specifier for printing addresses, and the `(void *)` cast is
standard practice to keep `printf` happy across different pointer types.

## Every variable has both a value and an address

This is the core idea underlying pointers: any variable you declare
occupies real memory, at a real address, and C gives you the tools to work
with either the *value* (`x`) or the *address* (`&x`) depending on what
you need.

## Different data lives in different regions

You'll get the full picture in chapter 31, but the short version: a
running program's memory isn't one undifferentiated blob — it's organized
into distinct regions with different lifetimes and purposes:

- **Stack** — local variables, function call information; automatically
  managed, very fast, limited in size (chapter 21)
- **Heap** — memory you request manually at runtime, that persists until
  you explicitly free it (chapter 22)
- **Global/static storage** — global and `static` variables, existing for
  the whole program's lifetime
- **Code (text) segment** — the actual compiled instructions of your
  program

You don't need to fully internalize this yet — just know it's coming, and
that "where" a piece of data lives affects how long it lasts and how you
should manage it.

## sizeof, revisited with intent

```c
int x = 5;
printf("%zu\n", sizeof(x));   // 4 (on virtually every modern system)
```

`sizeof` tells you exactly how many bytes a variable occupies — which
directly tells you how many memory addresses it spans (an `int` at
address 1000 occupies bytes 1000, 1001, 1002, and 1003).

## Try it yourself

- Print the addresses of three different local variables declared next to
  each other, and look at how close together the addresses are (they're
  often, though not always, sequential — a small, direct look at how the
  stack lays things out).
- Print the size and address of an `int`, a `char`, and a `double` in the
  same program, and connect the size difference to how many address slots
  each one occupies.
- Ask yourself: if a variable's *value* can change while the program runs,
  can its *address* change too? (Answer: for a normal local variable, no —
  the address is fixed for the variable's entire lifetime. This fact is
  exactly what makes pointers reliable.)

---
**Next:** [17. Pointers →](../17-pointers/README.md)
