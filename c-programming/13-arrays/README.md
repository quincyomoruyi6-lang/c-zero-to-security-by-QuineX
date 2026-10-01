# 13. Arrays

**Previous:** [← 12. Scope](../12-scope/README.md) | **Home:** [README](../README.md) | **Next:** [14. Strings →](../14-strings/README.md)

## What an array actually is

An array is a fixed number of elements of the same type, sitting
back-to-back in memory. Once you declare its size, that size is locked in
— C arrays don't grow or shrink.

```c
int numbers[5];              // 5 ints, uninitialized
int scores[5] = {90, 85, 77, 60, 100};   // declared and initialized
```

Indexing starts at `0`, not `1`:

```c
printf("%d\n", scores[0]);   // 90 — the first element
printf("%d\n", scores[4]);   // 100 — the last element (index = size - 1)
```

## Why indexing starts at 0

This isn't arbitrary — an array index is really just an offset from the
start of the array in memory. `scores[0]` means "0 elements past the
start," which *is* the first element. This will matter a lot once you get
to pointers (chapter 17) — array indexing is really pointer arithmetic
wearing a friendlier syntax.

## Iterating an array

```c
int scores[5] = {90, 85, 77, 60, 100};

for (int i = 0; i < 5; i++) {
    printf("%d\n", scores[i]);
}
```

Never hardcode the size in two places if you can avoid it — use `sizeof`
to compute it:

```c
int size = sizeof(scores) / sizeof(scores[0]);
```

`sizeof(scores)` gives the total size in bytes of the whole array;
`sizeof(scores[0])` gives the size of one element. Dividing gives you the
element count, and it stays correct automatically if you ever change the
array's size.

## Arrays do NOT check bounds — this matters a lot

```c
int numbers[5] = {1, 2, 3, 4, 5};
numbers[10] = 99;   // compiles fine. Runs. Corrupts memory it has no business touching.
```

This is one of the most important things to internalize about C. There's
no runtime check stopping you from writing past the end of an array. It
might crash immediately, it might silently corrupt some unrelated
variable, or it might appear to work fine and break something completely
different three functions later. This exact category of bug — writing
past the end of a fixed-size buffer — is the foundation of the buffer
overflow vulnerabilities covered properly in chapter 32. Get used to
double-checking your bounds now, before it's a security-relevant habit
and not just a "my program crashed" habit.

## Multi-dimensional arrays

```c
int grid[3][3] = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};

for (int row = 0; row < 3; row++) {
    for (int col = 0; col < 3; col++) {
        printf("%d ", grid[row][col]);
    }
    printf("\n");
}
```

A 2D array is really just a grid laid out in memory row by row (this is
called "row-major order"). `grid[row][col]` is syntax sugar over a single
flat block of `3 * 3 = 9` integers sitting contiguously in memory.

## A quick preview: arrays and pointers

```c
int numbers[5] = {1, 2, 3, 4, 5};
printf("%p\n", (void *)numbers);      // prints an address
printf("%p\n", (void *)&numbers[0]);  // prints the SAME address
```

An array's name, in most expressions, "decays" into the address of its
first element. This is a big deal and gets its own full chapter
(19-pointer-arrays) — for now, just notice this and file it away.

## Try it yourself

- Declare an array of 10 integers, fill it using a loop (e.g., squares of
  1 through 10), then print it with another loop.
- Write a function that finds the maximum value in an array (pass the
  array and its size as parameters).
- Deliberately write one element past the end of a small array (like
  `int arr[3]; arr[3] = 99;`), compile with `-Wall -Wextra`, and see
  whether the compiler warns you (it often can for a fixed-size local
  array with a constant index — a nice example of what static analysis can
  catch and what it can't).

---
**Next:** [14. Strings →](../14-strings/README.md)
