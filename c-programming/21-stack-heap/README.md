# 21. Stack & Heap

**Previous:** [← 20. Pointer Strings](../20-pointer-strings/README.md) | **Home:** [README](../README.md) | **Next:** [22. Dynamic Memory →](../22-dynamic-memory/README.md)

Chapter 16 mentioned that a running program's memory is split into
regions. This chapter zooms into the two you'll interact with directly and
constantly: the **stack** and the **heap**.

## The stack

Every time a function is called, the system sets up a **stack frame** for
it — a chunk of memory holding that function's local variables,
parameters, and information about where to return to when it's done.

```c
void b(void) {
    int y = 20;   // lives in b's stack frame
}

void a(void) {
    int x = 10;   // lives in a's stack frame
    b();
}

int main(void) {
    a();
    return 0;
}
```

When `a()` calls `b()`, a new frame is pushed on top of `a`'s frame. When
`b()` returns, its frame is popped off and its local variables simply
cease to exist — no cleanup code needed, it's automatic and extremely
fast, which is exactly why local variables are the default choice for
most data in C.

**Key property: stack memory only lives as long as the function call
that owns it.**

```c
int *dangerous(void) {
    int local = 42;
    return &local;   // BUG: returning the address of a variable that's about to be destroyed
}
```

The moment `dangerous()` returns, `local`'s stack frame is gone. The
returned pointer now points at memory that could be reused by literally
any subsequent function call. This is called a **dangling pointer**, and
it's one of the most common real bugs in C — the program might even
appear to work by coincidence sometimes, which makes it worse, not
better, because it can fail unpredictably later. You'll see this bug
category properly cataloged in chapter 23.

## Stack overflow

The stack has a fixed, limited size (often a few megabytes by default).
Recursion that never terminates — or terminates too late — exhausts it:

```c
void recurse(void) {
    int data[1000];         // eats stack space every call
    (void)data;              // suppress "unused variable" warning
    recurse();               // never stops!
}
```

This crashes with a stack overflow, distinct from the buffer overflow
you'll see in chapter 32 — same general idea (writing past a boundary),
different mechanism (uncontrolled recursion vs. an oversized write into a
fixed buffer).

## The heap

The heap is a separate region for memory you request manually, at
runtime, that persists **until you explicitly free it** — completely
independent of any function's call/return cycle.

```c
#include <stdlib.h>

int *make_heap_int(int value) {
    int *p = malloc(sizeof(int));   // request heap memory
    *p = value;
    return p;   // safe! this memory outlives the function call
}
```

Unlike the earlier `dangerous()` example, this is completely fine —
`malloc`'d memory doesn't disappear when the function returns. It lives
until something calls `free()` on it (chapter 22 covers `malloc`/`free`
properly).

## Stack vs heap, side by side

| | Stack | Heap |
|---|---|---|
| **Managed by** | Automatically (compiler/runtime) | You, manually |
| **Speed** | Very fast | Slower (bookkeeping overhead) |
| **Lifetime** | Ends when the function returns | Until you call `free()` |
| **Size** | Small, fixed limit | Large, limited mainly by system RAM |
| **Typical use** | Local variables, function calls | Data that needs to outlive a function, or whose size isn't known until runtime |

## Why this matters for embedded work

If you're heading toward embedded/IoT (as several later chapters in this
repo do), stack size is a much bigger concern than on a desktop — many
microcontrollers have only a few kilobytes of RAM total. A large local
array or deep recursion that's harmless on a laptop can genuinely crash a
constrained device. Being deliberate about stack usage isn't just good
practice there, it's often a hard requirement.

## Try it yourself

- Write the `dangerous()` function above, call it, and print the
  dereferenced result. It may "work" by coincidence — that's the whole
  point of why this bug is dangerous, not reassuring.
- Write a version of the same function that heap-allocates instead
  (using `malloc`, even before you've formally covered chapter 22 in
  depth), and confirm it behaves correctly and predictably.
- Write a deliberately unbounded recursive function and run it to trigger
  a real stack overflow on your own machine — see what the crash actually
  looks like.

---
**Next:** [22. Dynamic Memory →](../22-dynamic-memory/README.md)
