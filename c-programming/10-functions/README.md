# 10. Functions

**Previous:** [← 09. Practical Logic](../09-practical-logic/README.md) | **Home:** [README](../README.md) | **Next:** [11. Return Values →](../11-return-values/README.md)

## Why functions exist

Without them, every program is one giant `main`. Functions let you break a
problem into named, reusable pieces — write the logic once, call it as
many times as you need. This is the "don't repeat yourself" idea, and in C
it's not optional style advice, it's how any program bigger than a school
exercise stays manageable.

## Basic structure

```c
#include <stdio.h>

int add(int a, int b) {
    return a + b;
}

int main(void) {
    int result = add(3, 4);
    printf("%d\n", result);
    return 0;
}
```

`int add(int a, int b)` is the **function definition**: return type
(`int`), name (`add`), parameter list (`int a, int b`). Inside `main`,
`add(3, 4)` is a **function call** — it hands control to `add`, runs its
body with `a = 3` and `b = 4`, then returns control (and a value) back to
wherever it was called from.

## Declaration vs definition vs prototype

C reads your file top to bottom. If you call a function before the
compiler has seen it defined, you'll get an error (or, worse, a warning
that's easy to ignore). Two ways to fix this:

**Option 1: Define it before you use it**
```c
int add(int a, int b) {
    return a + b;
}

int main(void) {
    printf("%d\n", add(3, 4));
    return 0;
}
```

**Option 2: Forward-declare it with a prototype**
```c
int add(int a, int b);   // prototype — just the signature, ending in ;

int main(void) {
    printf("%d\n", add(3, 4));   // compiler now knows this call is valid
    return 0;
}

int add(int a, int b) {   // actual definition, can come later in the file
    return a + b;
}
```

A **prototype** is just the function's signature (return type, name,
parameter types) without a body, ending in a semicolon. Header files
(`.h`) are essentially collections of prototypes — that's how one `.c`
file can call functions defined in a different `.c` file.

## void functions

Not every function needs to return something:

```c
void greet(const char *name) {
    printf("Hello, %s!\n", name);
}

int main(void) {
    greet("Quincy");
    return 0;
}
```

`void` as a return type means "this function doesn't hand back a value."
You can still `return;` early from inside it (with no value) if you need
to exit before reaching the end.

## Parameters are copies (pass by value)

```c
void increment(int x) {
    x = x + 1;
    printf("Inside function: %d\n", x);
}

int main(void) {
    int num = 5;
    increment(num);
    printf("Outside function: %d\n", num);   // still 5!
    return 0;
}
```

C passes arguments **by value** — the function gets its own copy of
`num`, not the original. Changes inside `increment` never touch the
caller's variable. This surprises a lot of people at first, and it's
exactly the problem pointers solve — chapter 18 shows you how to actually
let a function modify the caller's variable, using addresses instead of
copies.

## Try it yourself

- Write a function `int square(int n)` and use it inside a loop to print
  the squares of 1 through 10.
- Write a `void` function that prints a horizontal line of dashes, and use
  it to separate sections of output in a program.
- Predict, then verify, what happens if you try to call a function that's
  defined *below* `main` with no prototype above it. Read the compiler's
  warning or error carefully.

---
**Next:** [11. Return Values →](../11-return-values/README.md)
