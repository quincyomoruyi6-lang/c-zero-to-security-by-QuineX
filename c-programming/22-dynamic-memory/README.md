# 22. Dynamic Memory

**Previous:** [← 21. Stack & Heap](../21-stack-heap/README.md) | **Home:** [README](../README.md) | **Next:** [23. Memory Bugs →](../23-memory-bugs/README.md)

Sometimes you don't know how much memory you need until the program is
actually running — the size of an array might depend on user input, a
file's contents, or a network response. `malloc` and friends let you
request exactly the memory you need, when you need it.

## malloc — requesting memory

```c
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    int *p = malloc(sizeof(int));

    if (p == NULL) {
        printf("Allocation failed\n");
        return 1;
    }

    *p = 42;
    printf("%d\n", *p);

    free(p);
    return 0;
}
```

`malloc(n)` requests `n` bytes from the heap and returns a pointer to the
start of that block — or `NULL` if the request couldn't be satisfied
(rare on a desktop with plenty of RAM, much more realistic on a
constrained embedded device). **Always check for `NULL` before using the
result.** It costs one `if` statement and prevents a crash on the failure
path.

Notice `sizeof(int)` rather than a hardcoded `4` — always compute sizes
with `sizeof`, so your code stays correct across platforms where type
sizes might differ.

## Allocating an array dynamically

```c
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    int n = 5;
    int *arr = malloc(n * sizeof(int));

    if (arr == NULL) {
        return 1;
    }

    for (int i = 0; i < n; i++) {
        arr[i] = i * i;
    }

    for (int i = 0; i < n; i++) {
        printf("%d\n", arr[i]);
    }

    free(arr);
    return 0;
}
```

Once allocated, `arr` behaves exactly like a normal array for indexing
purposes (`arr[i]`) — the difference is entirely in how its memory was
obtained and how long it lives.

## calloc — allocate and zero-initialize

```c
int *arr = calloc(n, sizeof(int));   // n elements, all set to 0
```

`malloc` gives you uninitialized memory — it could contain anything left
over from whatever used it before. `calloc` takes the element count and
element size as two separate arguments and guarantees every byte starts
at zero. Slightly slower than `malloc` because of that zeroing, but often
worth it for the safety.

## realloc — resizing an existing allocation

```c
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    int *arr = malloc(3 * sizeof(int));
    arr[0] = 1; arr[1] = 2; arr[2] = 3;

    int *bigger = realloc(arr, 5 * sizeof(int));
    if (bigger == NULL) {
        free(arr);   // realloc failed — original block is still valid, free it
        return 1;
    }
    arr = bigger;   // always reassign — realloc may have moved the block

    arr[3] = 4;
    arr[4] = 5;

    for (int i = 0; i < 5; i++) {
        printf("%d\n", arr[i]);
    }

    free(arr);
    return 0;
}
```

Two details that matter a lot here:
- **`realloc` might return a different address** than the one you passed
  in — it may need to move your data to find a large enough free block.
  Always assign the result to a variable and use *that* going forward.
- **If `realloc` fails, it returns `NULL` but leaves the original block
  untouched.** Never overwrite your only pointer to the original block
  with the (possibly `NULL`) result directly — that would leak the
  original memory with no way to free it. Use a temporary variable, like
  `bigger` above.

## free — giving memory back

```c
free(p);
p = NULL;   // good practice — makes accidental reuse an obvious NULL dereference, not a silent bug
```

Every successful `malloc`/`calloc`/`realloc` needs exactly one matching
`free`, once you're done with it. Setting the pointer to `NULL`
immediately after isn't required by the language, but it's cheap
insurance: if you accidentally try to use it again, you get an obvious,
loud crash instead of the much sneakier bugs covered in the next chapter.

## Memory leaks, briefly

```c
void leaky(void) {
    int *p = malloc(sizeof(int));
    *p = 5;
    // function ends without calling free(p) — that memory is now unreachable AND unfreed
}
```

Every time `leaky()` runs, it claims a little more memory that never gets
returned. Call it in a loop and your program's memory usage climbs
steadily until the system runs out — a real, common failure mode in
long-running programs (servers, embedded devices that run for months at
a time). The rule that prevents this is simple to state and easy to
forget in practice: **every `malloc` needs a `free`, on every code path,
including error-handling paths that return early.**

## valgrind — catching leaks automatically

```bash
gcc -g program.c -o program
valgrind --leak-check=full ./program
```

`valgrind` runs your program under close supervision and reports memory
leaks, invalid reads/writes, and use of uninitialized memory, with
surprising precision — including the exact line where the leaked memory
was allocated. It's slow (your program runs noticeably slower under it)
but invaluable during development. If it's not installed:
`sudo apt install valgrind`.

## Try it yourself

- Write a program that reads how many numbers the user wants to enter,
  `malloc`s exactly that many ints, fills them from input, then correctly
  `free`s the array.
- Deliberately write the `leaky()` function above, call it in a loop 1000
  times, and run it under `valgrind --leak-check=full` to see the reports
  pile up.
- Practice the `realloc` pattern by building a small dynamic array that
  starts at capacity 2 and doubles its capacity every time it fills up.

---
**Next:** [23. Memory Bugs →](../23-memory-bugs/README.md)
