# 32. Buffer Overflows

**Previous:** [← 31. Memory Layout](../31-memory-layout/README.md) | **Home:** [README](../README.md) | **Next:** [33. Debugging with GDB →](../33-debugging-gdb/README.md)

This is probably the chapter you've been most curious about if security
is your goal, so let's be precise about what it does and doesn't cover:
this explains **why** buffer overflows are dangerous and **how to prevent
and detect them**, using the same classic illustrative example taught in
virtually every systems security course. It is not a guide to building a
working exploit against a real target — that's a separate, much deeper
skill built on top of this foundation (and best learned in a legal,
sandboxed environment like the CTF/wargame platforms mentioned in the
security project ideas). What follows is the "understand the mechanism
and defend against it" half, which is the half every C programmer
actually needs.

## The classic vulnerable pattern

```c
#include <stdio.h>
#include <string.h>

void vulnerable_function(char *input) {
    char buffer[16];
    strcpy(buffer, input);   // no bounds check — copies however much input actually is
    printf("You entered: %s\n", buffer);
}

int main(int argc, char *argv[]) {
    if (argc > 1) {
        vulnerable_function(argv[1]);
    }
    return 0;
}
```

`buffer` has room for 16 bytes. `strcpy` doesn't know that, or care — it
copies bytes from `input` until it hits a null terminator, full stop. If
`argv[1]` is longer than 16 bytes, `strcpy` keeps writing past the end of
`buffer`, into whatever memory sits next to it on the stack.

## What actually sits next to that buffer

Recall the stack frame concept from chapter 21. A rough picture of
`vulnerable_function`'s stack frame, with addresses growing upward toward
the top of this diagram:

```
Higher addresses
┌───────────────────────┐
│   Return address        │  <- where execution resumes after this function returns
├───────────────────────┤
│   Saved base pointer     │  <- used to restore the caller's stack frame
├───────────────────────┤
│   buffer[16]             │  <- our vulnerable array, growing UPWARD from here
└───────────────────────┘
Lower addresses
```

An overflow that writes past `buffer`'s 16 bytes doesn't just corrupt
random memory — it writes into the saved base pointer next, and beyond
that, the **return address**: the exact memory address the CPU jumps to
when this function returns. If an attacker controls the overflow's
content precisely enough, they control what value ends up in that return
address slot — meaning they can influence *where the program's execution
continues* after the function returns. That's the conceptual core of why
this bug class has historically been so serious: it's not just "the
program crashes," it's "the program's control flow can potentially be
redirected." Actually pulling that off reliably against a modern,
protected binary involves a lot more (precise offsets, defeating the
mitigations below, and more) — real exploit development is its own deep
field, well beyond a single README.

## Reproducing this safely, to see it for yourself

```bash
gcc -g -fno-stack-protector -z execstack overflow.c -o overflow
./overflow "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA"
```

Note the flags: `-fno-stack-protector` and `-z execstack` deliberately
*disable* two of the modern protections described below, purely so you
can observe the raw, unprotected behavior in a controlled learning
environment. Never disable these on anything you're actually deploying.
On a default modern build (protections on), this same overflow is far
more likely to simply crash cleanly — which is the protections doing
their job.

## Mitigations — what actually stops this in practice today

- **Bounds-checked functions.** Use `strncpy`, `snprintf`, `fgets` instead
  of `strcpy`, `sprintf`, `gets` — you've already been building this habit
  since chapters 14 and 15.
- **Stack canaries.** The compiler places a random, secret value right
  before the return address; before returning, it checks whether that
  value has changed. An overflow big enough to reach the return address
  almost always overwrites the canary first, and the program aborts with
  `*** stack smashing detected ***` instead of continuing with corrupted
  state. On by default with modern `gcc` (that's what `-fno-stack-
  protector` above was disabling).
- **ASLR (Address Space Layout Randomization).** Randomizes where a
  process's stack, heap, and libraries are loaded each run, making it much
  harder for an attacker to know what address to aim for even if they can
  influence the return address.
- **NX / DEP (No-Execute / Data Execution Prevention).** Marks stack and
  heap memory as non-executable, so even if an attacker gets their own
  bytes into memory, the CPU refuses to *run* them as instructions. The
  `-z execstack` flag above disables exactly this, for demonstration
  purposes only.
- **Compiler warnings.** `-Wall -Wextra -Wformat-security`, and treating
  warnings about deprecated/unsafe functions seriously, catch a lot of
  this before the program ever runs.

## Detecting overflows during development

```bash
gcc -Wall -Wextra -g -fsanitize=address program.c -o program
./program
```

AddressSanitizer (from chapter 23) catches stack buffer overflows
immediately, with a precise report — this is genuinely the single most
useful tool for catching this bug class while you're writing code, well
before it ever reaches anyone else.

## The takeaway

The defensive side of this is genuinely not complicated: use
bounds-checked functions, validate lengths before copying, compile with
protections on (they're on by default — the danger is turning them off,
not forgetting to turn them on), and test with sanitizers. The offensive
side — building a working, targeted exploit — is a legitimate, deep
specialty that assumes everything in this chapter as a starting point,
not a shortcut around it.

## Try it yourself

- Compile the vulnerable example with default flags (protections on) and
  pass it a long argument — observe the stack-smashing detection kick in.
- Then compile it with `-fno-stack-protector -z execstack` as shown above,
  in a disposable VM or container you don't mind crashing, and compare the
  behavior.
- Fix `vulnerable_function` properly using `strncpy` with a correct bound
  and manual null-termination, then confirm a long input no longer causes
  any problem at all, protections on or off.

---
**Next:** [33. Debugging with GDB →](../33-debugging-gdb/README.md)
