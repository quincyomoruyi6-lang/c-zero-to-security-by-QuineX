# 23. Memory Bugs

**Previous:** [← 22. Dynamic Memory](../22-dynamic-memory/README.md) | **Home:** [README](../README.md) | **Next:** [24. Structures →](../24-structures/README.md)

Manual memory management is powerful and, honestly, unforgiving. This
chapter is a catalog of the classic ways it goes wrong — recognizing these
patterns on sight is one of the most valuable skills you'll build in this
entire repo, especially if security work is where you're headed.

## Memory leak

Covered briefly in chapter 22 — allocated memory that's never freed.

```c
void leaky(void) {
    int *p = malloc(sizeof(int));
    *p = 5;
    // no free(p) — leaked
}
```

**Why it's dangerous:** in a long-running program, leaks accumulate.
Eventually the system runs out of memory, and things start failing in
ways that have nothing obviously to do with the actual leak.

**Detect it with:** `valgrind --leak-check=full`.

## Double free

```c
int *p = malloc(sizeof(int));
free(p);
free(p);   // BUG: freeing the same memory twice
```

**Why it's dangerous:** the memory allocator's internal bookkeeping gets
corrupted. This can crash immediately, or — worse — silently corrupt
unrelated data that a *different* allocation later reuses that same
memory for. Historically, double-free bugs have been directly exploitable
by attackers to manipulate the allocator's internal structures.

**Fix:** set pointers to `NULL` immediately after freeing them (from
chapter 22) — calling `free(NULL)` is explicitly defined to do nothing, so
this makes an accidental second `free` harmless instead of catastrophic.

## Use-after-free

```c
int *p = malloc(sizeof(int));
*p = 5;
free(p);
printf("%d\n", *p);   // BUG: reading memory that's already been freed
```

**Why it's dangerous:** once freed, that memory can be reused by any
subsequent allocation, anywhere in the program. Reading it might give you
garbage, might give you old data that happens to still be there, or might
give you data belonging to something completely different that got
allocated in that same spot afterward. This exact bug class — accessing
memory through a pointer after it's been freed — is one of the most
common sources of serious, exploitable security vulnerabilities in
real-world C and C++ software to this day.

**Fix:** same as double-free — `p = NULL;` right after `free(p);`, and
never touch a pointer you're not certain is still valid.

## Dangling pointer

A more general version of use-after-free — any pointer that still holds
an address, but the data at that address is no longer valid. You already
saw one form of this in chapter 21:

```c
int *make_dangling(void) {
    int local = 42;
    return &local;   // local's stack frame is gone the instant this returns
}
```

**Why it's dangerous:** identical risk profile to use-after-free — the
pointer looks completely valid (it holds a real address), but what's
actually there is undefined.

## Uninitialized memory read

```c
int arr[5];
printf("%d\n", arr[2]);   // BUG: reading memory that was never set
```

**Why it's dangerous:** the value could be anything, including
security-sensitive leftover data from whatever previously occupied that
memory. This is a real, documented vulnerability class — uninitialized
memory disclosure has leaked private data (like encryption keys or
passwords from a previous process) in real software.

**Fix:** always initialize variables, and prefer `calloc` over `malloc`
when you specifically need zeroed memory.

## Buffer overrun (a preview — full chapter coming)

```c
int arr[5];
arr[10] = 1;   // writes past the allocated block
```

You've technically seen this since chapter 13. It gets its own dedicated,
much deeper treatment in chapter 32, because it's specifically the bug
class behind stack-smashing and remote code execution vulnerabilities.

## Tools that catch these for you

You don't need to spot every one of these by eye every time — real tools
exist specifically for this:

```bash
# Compile with AddressSanitizer — catches overflows, use-after-free, and more, at runtime
gcc -Wall -Wextra -g -fsanitize=address program.c -o program
./program

# Catch leaks and invalid memory access
valgrind --leak-check=full ./program
```

`-fsanitize=address` (often called "ASan") instruments your binary to
detect memory errors the instant they happen, with a detailed report
including the exact line of code responsible. Get in the habit of
compiling with it while developing anything that touches raw pointers —
it turns silent corruption into a loud, immediate, readable error.

## A summary table worth remembering

| Bug | What happens | Primary tool to catch it |
|---|---|---|
| Memory leak | Allocated, never freed | `valgrind --leak-check=full` |
| Double free | `free()` called twice on the same pointer | ASan, valgrind |
| Use-after-free | Access after `free()` | ASan, valgrind |
| Dangling pointer | Points at memory that's no longer valid | ASan, careful review |
| Uninitialized read | Using a variable before assigning it | `-Wall -Wextra`, valgrind |
| Buffer overrun | Reading/writing past an allocation's bounds | ASan |

## Try it yourself

- Deliberately write a double-free and a use-after-free in two small,
  separate test programs. Compile both with `-fsanitize=address` and read
  the reports carefully — they're genuinely well-written and tell you
  exactly what went wrong and where.
- Write the uninitialized-read example, run it several times in a row,
  and see whether the "garbage" value changes between runs.
- Go back to any program you've written earlier in this repo that uses
  `malloc`, and run it under `valgrind --leak-check=full` just to build
  the habit of checking.

---
**Next:** [24. Structures →](../24-structures/README.md)
