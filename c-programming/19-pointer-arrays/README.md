# 19. Pointer Arrays

**Previous:** [← 18. Pointer Functions](../18-pointer-functions/README.md) | **Home:** [README](../README.md) | **Next:** [20. Pointer Strings →](../20-pointer-strings/README.md)

Chapter 17 gave you a first taste of this. Now let's actually nail down
how arrays and pointers relate — because they're close enough to look
interchangeable, and different enough that conflating them causes real
bugs.

## Array names decay to pointers

```c
int numbers[5] = {1, 2, 3, 4, 5};
int *p = numbers;   // no & needed — numbers already "decays" to &numbers[0]
```

In most expressions, an array's name is automatically converted to a
pointer to its first element. This is why you never write `&numbers` when
passing an array to a function expecting a pointer.

## Pointer arithmetic on arrays

```c
int numbers[5] = {10, 20, 30, 40, 50};
int *p = numbers;

for (int i = 0; i < 5; i++) {
    printf("%d\n", *(p + i));   // identical to numbers[i]
}
```

`p[i]` and `*(p + i)` are exactly the same operation in C — array
indexing is literally defined in terms of pointer arithmetic. This is
also why `numbers[2]` and `2[numbers]` both compile and do the same thing
(genuinely — try it once, purely as a curiosity, never write it in real
code).

## Passing arrays to functions — they always decay

```c
void print_array(int arr[], int size) {
    for (int i = 0; i < size; i++) {
        printf("%d ", arr[i]);
    }
    printf("\n");
}
```

Even though this looks like `arr` is an array parameter, it's actually
received as a plain pointer — `sizeof(arr)` inside this function would
give you the size of a pointer (8 bytes on most 64-bit systems), *not*
the size of the original array. This is exactly why you always pass the
size explicitly as a separate parameter — the function has no way to know
it otherwise.

```c
void print_array(int *arr, int size) {   // equivalent to the version above
    // ...
}
```

Both function signatures compile to the exact same thing. Use whichever
reads more clearly to you — `arr[]` documents "this is meant to be an
array" even though, mechanically, it's a pointer.

## Where the analogy breaks: `sizeof`

```c
int numbers[5] = {1, 2, 3, 4, 5};
int *p = numbers;

printf("%zu\n", sizeof(numbers));   // 20 (5 ints * 4 bytes)
printf("%zu\n", sizeof(p));         // 8 (just the size of a pointer)
```

This is the single most common place people get burned: `sizeof` on an
actual array gives you the full array's size, but the moment that array
has decayed into a pointer (which happens the instant you assign it to a
pointer variable, or pass it into a function), `sizeof` only sees "a
pointer," not "the array it used to point to." The classic
`sizeof(arr)/sizeof(arr[0])` trick from chapter 13 only works on the
actual array in the scope where it was declared — never inside a function
that received it as a parameter.

## Array of pointers

Different from a pointer to an array — this is an array where *each
element itself is a pointer*:

```c
#include <stdio.h>

int main(void) {
    int a = 1, b = 2, c = 3;
    int *arr[3] = {&a, &b, &c};   // an array of 3 int pointers

    for (int i = 0; i < 3; i++) {
        printf("%d\n", *arr[i]);
    }

    return 0;
}
```

A very common real use of this pattern: an array of strings, since a
string in C is itself represented as a `char *`:

```c
const char *names[3] = {"Alice", "Bob", "Charlie"};

for (int i = 0; i < 3; i++) {
    printf("%s\n", names[i]);
}
```

## Try it yourself

- Write a function that takes an array and its size, and reverses the
  array in place using pointer arithmetic instead of `arr[i]` indexing.
- Print `sizeof` on an array both inside `main` (where it's declared) and
  inside a function you pass it to — confirm the difference for yourself.
- Build an array of pointers to 5 different integers (not necessarily
  related values) and print all 5 values by dereferencing each pointer in
  a loop.

---
**Next:** [20. Pointer Strings →](../20-pointer-strings/README.md)
