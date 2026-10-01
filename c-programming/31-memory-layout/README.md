# 31. Memory Layout

**Previous:** [← 30. System Interaction](../30-system-interaction/README.md) | **Home:** [README](../README.md) | **Next:** [32. Buffer Overflows →](../32-buffer-overflows/README.md)

You've met the stack and the heap already (chapter 21). This chapter puts
the whole picture together — every region a running C program's memory is
divided into, and what lives where. This is the mental model chapter 32
builds directly on, so take the time to actually get it straight.

## The full layout

A typical process's virtual address space, from low addresses to high
(the exact addresses vary, but the ordering below is standard on Linux):

```
High addresses
┌─────────────────────┐
│        Stack         │  <- local variables, function calls, grows DOWNWARD
│          ↓            │
│                       │
│          ↑            │
│         Heap          │  <- malloc'd memory, grows UPWARD
├─────────────────────┤
│   BSS segment         │  <- uninitialized global/static variables (zeroed)
├─────────────────────┤
│   Data segment        │  <- initialized global/static variables
├─────────────────────┤
│   Text (code) segment │  <- your compiled instructions — typically read-only
└─────────────────────┘
Low addresses
```

Notice the stack and heap grow **toward each other**, from opposite ends
of the available space. This is deliberate — it lets both regions grow as
needed without a fixed boundary between them, right up until they'd
theoretically collide (which, on a real system, means you've run out of
usable memory).

## What lives in each region

**Text (code) segment** — the actual machine instructions the CPU
executes. Typically marked read-only and executable — the OS won't let
you write to it, which is a genuine security measure (more on this in
chapter 32).

**Data segment** — global and `static` variables that have an explicit
initial value.

```c
int global_counter = 5;   // lives in the data segment
```

**BSS segment** — global and `static` variables with no explicit
initializer (they default to zero). Kept separate from the data segment
mainly as a size optimization — the executable file doesn't need to store
a block of zeros, it just records "reserve this much space and zero it."

```c
int global_flag;   // lives in BSS, automatically zeroed
```

**Heap** — memory from `malloc`/`calloc`/`realloc`, as covered in
chapter 22.

**Stack** — local variables and function call bookkeeping, as covered in
chapter 21.

## Seeing this yourself with `size`

```bash
gcc -Wall -Wextra program.c -o program
size program
```

The `size` command reports the sizes of the text, data, and BSS segments
in your compiled binary directly — a small, concrete way to confirm this
isn't just a diagram, it's really how your program is laid out on disk
and in memory.

## Why this chapter matters for what's next

Every buffer overflow discussed in chapter 32 is really a story about
this exact layout — specifically, what sits *next to* a buffer on the
stack, and what happens when a write goes past that buffer's boundary
into neighboring stack memory. Understanding this layout isn't optional
background for that chapter; it's the entire foundation the explanation
rests on.

## Try it yourself

- Declare one initialized global, one uninitialized global, and one local
  variable in the same program, and use `%p` to print all three
  addresses. Compare the ordering against the diagram above (note:
  security features like ASLR randomize the *base* addresses on modern
  systems, but the *relative* ordering of the regions still holds).
- Run `size` on a few different compiled programs of your own and compare
  how the segment sizes change as you add more global variables.
- Look up what ASLR (Address Space Layout Randomization) does at a
  conceptual level, and connect it back to why the "high addresses" and
  "low addresses" in the diagram above aren't the same specific numbers
  every time you run a program.

---
**Next:** [32. Buffer Overflows →](../32-buffer-overflows/README.md)
