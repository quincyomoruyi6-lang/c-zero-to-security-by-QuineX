# 27. Unions

**Previous:** [← 26. Enums](../26-enums/README.md) | **Home:** [README](../README.md) | **Next:** [28. File Handling →](../28-file-handling/README.md)

## A union looks like a struct but behaves completely differently

```c
#include <stdio.h>

union Value {
    int i;
    float f;
    char c;
};

int main(void) {
    union Value v;
    v.i = 65;
    printf("%d\n", v.i);   // 65

    v.f = 3.14f;
    printf("%f\n", v.f);   // 3.14 — but now v.i is garbage!

    return 0;
}
```

A `struct` gives every member its own separate space — a struct with an
`int` and a `float` takes up at least 8 bytes (4 + 4). A `union` gives
**all** members the *same* shared space, sized to fit the largest member.
Writing to one member overwrites whatever was in any other member — a
union can only meaningfully hold **one** of its members' values at a
time, whichever was written most recently.

## Why unions are useful

**Saving memory** — if you know only one of several possible types is
ever needed at a given moment, a union avoids reserving space for all of
them simultaneously. This matters more on memory-constrained systems
(embedded devices) than on a desktop with gigabytes of RAM to spare.

**Type punning** — reinterpreting the same bytes as a different type,
useful in some low-level and embedded contexts:

```c
union Converter {
    float f;
    unsigned int bits;
};

int main(void) {
    union Converter c;
    c.f = 3.14f;
    printf("Raw bits: %u\n", c.bits);   // the float's bytes, read as an unsigned int
    return 0;
}
```

This lets you inspect the raw bit pattern behind a float — genuinely
useful when working close to hardware, and something you'll see again
properly in the embedded chapters (36-39), where interpreting the same
memory as different types is common when talking to registers.

## The gotcha: you have to track which member is "active" yourself

```c
union Value v;
v.i = 65;
printf("%f\n", v.f);   // reading the WRONG member — undefined/meaningless result
```

The union itself has no memory of which member you last wrote — it's
just a block of bytes being reinterpreted based on however you choose to
read it. It's entirely on you to track which interpretation is currently
valid. A common, practical pattern is to pair a union with a separate
"tag" field (often an enum) that records which member is currently in
use:

```c
enum ValueType { TYPE_INT, TYPE_FLOAT };

struct TaggedValue {
    enum ValueType type;
    union {
        int i;
        float f;
    } data;
};

void print_value(struct TaggedValue v) {
    if (v.type == TYPE_INT) {
        printf("%d\n", v.data.i);
    } else {
        printf("%f\n", v.data.f);
    }
}
```

This "tagged union" pattern is extremely common in real systems code — it
gives you memory efficiency where it matters, with a safe, explicit way
to know how to interpret the shared memory at any given moment.

## sizeof a union

```c
union Value {
    int i;      // 4 bytes
    float f;    // 4 bytes
    char c;     // 1 byte
};

printf("%zu\n", sizeof(union Value));   // 4 — the size of the LARGEST member
```

A `struct` with the same members would be at least 9 bytes (likely padded
to 12); a `union` with them is just 4 — the size of its biggest member,
since everything shares that one space.

## Try it yourself

- Write the `Converter` example above, and try it with a `double` and
  `unsigned long long` instead of `float`/`unsigned int` — check that the
  sizes line up correctly with `sizeof`.
- Build a small "tagged union" that can hold either an `int`, a `float`,
  or a short string (`char[20]`), with an enum tag tracking which one is
  active, and a function that prints the value correctly based on the
  tag.
- Compare `sizeof` on an equivalent `struct` and `union` with the exact
  same members, and confirm the size difference matches your expectation.

---
**Next:** [28. File Handling →](../28-file-handling/README.md)
