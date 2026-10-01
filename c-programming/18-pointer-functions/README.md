# 18. Pointer Functions

**Previous:** [← 17. Pointers](../17-pointers/README.md) | **Home:** [README](../README.md) | **Next:** [19. Pointer Arrays →](../19-pointer-arrays/README.md)

Back in chapter 10, you saw that C passes arguments by value — a function
only gets a copy of whatever you pass it. This chapter is the payoff for
learning pointers: they're how you work around that limitation.

## The problem, restated

```c
void increment(int x) {
    x = x + 1;   // modifies the LOCAL copy only
}

int main(void) {
    int num = 5;
    increment(num);
    printf("%d\n", num);   // still 5
    return 0;
}
```

## The fix: pass the address instead

```c
#include <stdio.h>

void increment(int *x) {
    *x = *x + 1;   // dereference: modify whatever x points at
}

int main(void) {
    int num = 5;
    increment(&num);           // pass num's ADDRESS
    printf("%d\n", num);       // 6 — actually changed!
    return 0;
}
```

By passing `&num` (an address) instead of `num` (a value), `increment`
receives a pointer that points directly at the caller's actual variable.
Dereferencing it with `*x` reaches into that same memory and modifies it
for real. This is called **pass by reference**, and it's how C achieves
something similar to what other languages give you automatically.

## The classic example: swap

```c
#include <stdio.h>

void swap(int *a, int *b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

int main(void) {
    int x = 5, y = 10;
    swap(&x, &y);
    printf("x = %d, y = %d\n", x, y);   // x = 10, y = 5
    return 0;
}
```

Without pointers, `swap` would be impossible to write correctly — any
version taking plain `int` parameters could only ever swap its own local
copies, never the caller's actual variables.

## Returning multiple values through pointers

```c
#include <stdio.h>

void min_max(int arr[], int size, int *min, int *max) {
    *min = arr[0];
    *max = arr[0];

    for (int i = 1; i < size; i++) {
        if (arr[i] < *min) *min = arr[i];
        if (arr[i] > *max) *max = arr[i];
    }
}

int main(void) {
    int numbers[] = {5, 3, 9, 1, 7};
    int smallest, largest;

    min_max(numbers, 5, &smallest, &largest);
    printf("Min: %d, Max: %d\n", smallest, largest);
    return 0;
}
```

A function can only `return` one value directly, but it can write results
into as many pointer parameters as you give it. This pattern — pointer
"out parameters" — is everywhere in real C code.

## const pointers — protecting data you don't own

If a function receives a pointer but should only *read* through it, say
so:

```c
void print_value(const int *p) {
    printf("%d\n", *p);
    // *p = 5;   // compile error — can't modify through a const pointer
}
```

This is good practice specifically because pointers give a function power
over memory it doesn't own — `const` documents (and enforces) "I promise
not to change this," which matters a lot once functions get passed
pointers to large structs or arrays where a copy would be wasteful but
you still don't want accidental mutation.

## A first look at function pointers

Functions themselves have addresses too — you can store a pointer to a
function and call it indirectly:

```c
#include <stdio.h>

int add(int a, int b) { return a + b; }
int subtract(int a, int b) { return a - b; }

int main(void) {
    int (*operation)(int, int);   // a pointer to a function taking (int, int), returning int

    operation = add;
    printf("%d\n", operation(3, 4));   // 7

    operation = subtract;
    printf("%d\n", operation(3, 4));   // -1

    return 0;
}
```

This looks intimidating mostly because of the syntax. Function pointers
are how C implements things like callbacks (passing a function as a
parameter to another function) and simple plugin-style dispatch — you
won't need them everywhere, but they show up often enough to be worth
recognizing on sight.

## Try it yourself

- Write a function `void square(int *x)` that squares whatever `x` points
  to, in place.
- Extend the `min_max` example to also compute the sum via a third pointer
  parameter.
- Write an array of function pointers (`add`, `subtract`, maybe
  `multiply`) and call each one in a loop — a small preview of how you
  might build a simple calculator's dispatch logic.

---
**Next:** [19. Pointer Arrays →](../19-pointer-arrays/README.md)
