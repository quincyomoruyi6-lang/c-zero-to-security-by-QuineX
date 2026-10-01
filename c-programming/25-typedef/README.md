# 25. Typedef

**Previous:** [← 24. Structures](../24-structures/README.md) | **Home:** [README](../README.md) | **Next:** [26. Enums →](../26-enums/README.md)

## The problem typedef solves

```c
struct Student {
    char name[50];
    int age;
};

struct Student s1;   // have to write "struct Student" every single time
```

Typing `struct Student` repeatedly gets tedious, and it's not how you'd
want your types to read in a large codebase. `typedef` lets you give an
existing type a new, shorter name.

## Typedef with structs — the common pattern

```c
#include <stdio.h>

typedef struct {
    char name[50];
    int age;
} Student;

int main(void) {
    Student s1;   // no "struct" keyword needed anymore
    s1.age = 18;
    printf("%d\n", s1.age);
    return 0;
}
```

This is by far the most common use of `typedef` in real C code — an
anonymous struct definition combined with a typedef, giving you a clean
type name to use everywhere else.

## Typedef with a named struct too

```c
typedef struct Student {
    char name[50];
    int age;
} Student;
```

This version keeps the tag name (`struct Student`) *and* creates the
alias (`Student`) — useful if the struct needs to reference itself (like
in a linked list node, where a struct needs a pointer to another instance
of its own type) since you can't self-reference through an anonymous
struct.

```c
typedef struct Node {
    int value;
    struct Node *next;   // must use "struct Node" here — Node isn't defined yet at this point
} Node;
```

## Typedef for non-struct types

```c
typedef unsigned int uint;
typedef char* String;

uint count = 5;
String name = "Quincy";
```

This works, but use it sparingly for basic types like this — hiding that
`String` is really just `char *` can actually make code *harder* to
follow for someone reading it later, since pointer behavior (and its
sharp edges) is now disguised behind a friendlier-looking name. `typedef`
is most valuable when it removes real repetition (like `struct Student`
everywhere) rather than just renaming something that was already clear.

## A genuinely useful typedef pattern: function pointers

```c
typedef int (*Operation)(int, int);

int add(int a, int b) { return a + b; }

int main(void) {
    Operation op = add;
    printf("%d\n", op(3, 4));
    return 0;
}
```

Recall the function pointer syntax from chapter 18 — `int (*Operation)(int,
int)` was genuinely hard to read repeated everywhere. Typedef-ing it once
as `Operation` makes every later declaration far more readable.

## When it helps vs when it obscures

**Helps:** structs (removes real repetition), function pointer types
(genuinely hard-to-read syntax), platform-specific fixed-width types
(you'll see this properly in chapter 38 with `uint8_t`, `uint32_t`, etc.)

**Obscures:** renaming a plain type just to make it shorter, especially
if it hides that something is a pointer. `String name` looking like a
"real" string type, when it's actually just a raw `char *` with none of
the safety that name implies, can genuinely mislead someone reading your
code.

## Try it yourself

- Take your `struct Student` from the previous chapter and convert it to
  use `typedef`, dropping the `struct` keyword everywhere you can.
- Write a simple singly-linked list `Node` typedef (like the self-
  referencing example above), and build a tiny 3-node chain by hand.
- Typedef a function pointer type for a comparison function
  (`int (*Comparator)(int, int)`), and write two functions matching that
  signature (like "ascending" and "descending") that you can swap in and
  out.

---
**Next:** [26. Enums →](../26-enums/README.md)
