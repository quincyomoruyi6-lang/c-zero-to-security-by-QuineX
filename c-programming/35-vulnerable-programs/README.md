# 35. Vulnerable Programs

**Previous:** [← 34. C to Assembly](../34-c-to-assembly/README.md) | **Home:** [README](../README.md) | **Next:** [36. Embedded C →](../36-embedded-c/README.md)

A collection of small, classic C bug patterns — spot-the-flaw practice.
Each one is a real, well-documented bug class that has shown up in actual
CVEs over the years. The goal here is pattern recognition: after this
chapter, you should be able to glance at code like this in a real
codebase and immediately feel suspicious.

## 1. Format string vulnerability

```c
#include <stdio.h>

void log_message(char *user_input) {
    printf(user_input);          // BUG: user input used AS the format string
}

int main(int argc, char *argv[]) {
    if (argc > 1) {
        log_message(argv[1]);
    }
    return 0;
}
```

**The flaw:** `printf` interprets `%` sequences in its first argument as
format specifiers. If user input becomes that first argument directly, an
attacker can pass something like `%x %x %x %x` to read arbitrary values
off the stack (wherever `printf` expects its missing arguments to be), or
`%n` on older/less-hardened systems to write to memory. Reading raw,
un-sanitized input as a format string is a well-known, serious bug class.

**The fix:** always supply your own format string explicitly:
```c
printf("%s", user_input);   // input is DATA, never the format string itself
```

## 2. Integer overflow before allocation

```c
#include <stdlib.h>

void *allocate_records(int count, int record_size) {
    int total = count * record_size;   // BUG: can silently overflow
    return malloc(total);
}
```

**The flaw:** if `count * record_size` exceeds what an `int` can hold, it
silently wraps around to a small (or negative) number. `malloc` then
allocates a much smaller block than the caller thinks it did — and any
subsequent code writing `count` records into that undersized buffer
overflows it, exactly like chapter 32.

**The fix:** check for overflow before multiplying, or use a wider type
and validate the bounds:
```c
if (count <= 0 || record_size <= 0 || count > SIZE_MAX / record_size) {
    return NULL;   // would overflow — refuse instead of allocating wrong
}
size_t total = (size_t)count * (size_t)record_size;
return malloc(total);
```

## 3. Off-by-one

```c
void print_first_n(int arr[], int n) {
    for (int i = 0; i <= n; i++) {   // BUG: should be i < n
        printf("%d\n", arr[i]);
    }
}
```

**The flaw:** `<=` instead of `<` reads one element past the intended
range — for an array of size `n`, valid indices are `0` through `n - 1`,
and `arr[n]` is out of bounds. This is one of the single most common bugs
in all of programming, in any language, and it's especially dangerous in
C because there's no automatic bounds check to turn it into a clean
error.

**The fix:** be deliberate about `<` vs `<=` every single time you write
a loop bound, and double check it against what the valid index range
actually is.

## 4. Use-after-free (revisited in a function-boundary context)

```c
#include <stdlib.h>

struct Buffer { char *data; };

void release(struct Buffer *b) {
    free(b->data);
    // BUG: doesn't set b->data = NULL
}

void process(struct Buffer *b) {
    release(b);
    // ... much later, elsewhere in a large codebase ...
    printf("%s\n", b->data);   // use-after-free — data was already freed
}
```

**The flaw:** exactly chapter 23's use-after-free, but shown here in the
more realistic shape it usually takes — the `free()` and the later
misuse are often far apart, in different functions, sometimes different
files, which is exactly why this bug class is so easy to introduce and so
hard to catch by eye in a large real codebase.

**The fix:** always null out a pointer immediately after freeing what it
points to, and treat any struct member that might get freed as
"potentially invalid" unless you can clearly trace that it wasn't.

## Practice approach

For each of these:
1. Copy the vulnerable version into a test file, and confirm you can
   trigger the actual bad behavior.
2. Compile with `-fsanitize=address` where relevant, and read what the
   sanitizer tells you.
3. Write and test your own fixed version, confirming the bug no longer
   reproduces.

This "break it, then fix it, then verify the fix" loop is genuinely one
of the best ways to internalize these patterns — much more effective than
just reading about them.

## Where to go from here

If you want structured practice specifically on this kind of bug-hunting
in a legal, purpose-built environment, look at established CTF/wargame
platforms designed for exactly this (OverTheWire's "Narnia" and
"Behemoth" wargames, `pwn.college`, and PicoCTF's binary exploitation
tracks are all solid, widely-used starting points). See also the
[`projects/security`](../projects/security/README.md) folder for more
structured project ideas building on this chapter.

## Try it yourself

- Reproduce all four bugs above, in separate files, and trigger each
  one's bad behavior at least once.
- Fix all four, and verify each fix under `-fsanitize=address` or
  `valgrind` where applicable.
- Pick one bug class here and search for a real, publicly disclosed CVE
  that matches the pattern — reading a real advisory connects this
  practice material to something that actually mattered in the real
  world.

---
**Next:** [36. Embedded C →](../36-embedded-c/README.md)
