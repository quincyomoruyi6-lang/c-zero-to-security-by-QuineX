# 20. Pointer Strings

**Previous:** [← 19. Pointer Arrays](../19-pointer-arrays/README.md) | **Home:** [README](../README.md) | **Next:** [21. Stack & Heap →](../21-stack-heap/README.md)

Strings are `char` arrays, and arrays decay to pointers — so everything
from the last two chapters applies directly here. This chapter is mostly
about building the muscle memory to walk strings by hand, which matters a
lot once you start writing your own string-processing code instead of
just calling `string.h` functions.

## Walking a string with a pointer

```c
#include <stdio.h>

int main(void) {
    char *s = "Hello";

    while (*s != '\0') {
        printf("%c\n", *s);
        s++;
    }

    return 0;
}
```

`s` starts pointing at `'H'`. Each loop, we print the character it
currently points at, then advance `s` to the next byte. The loop stops the
moment we hit the null terminator — the exact same principle every
`string.h` function relies on internally.

## Writing strlen from scratch

```c
#include <stdio.h>

int my_strlen(const char *s) {
    int count = 0;
    while (*s != '\0') {
        count++;
        s++;
    }
    return count;
}

int main(void) {
    printf("%d\n", my_strlen("Hello, world!"));   // 13
    return 0;
}
```

Notice `s` is a *local copy* of the pointer — incrementing it inside the
function doesn't affect the caller's original pointer, only where this
function's own copy currently points. This is exactly the pass-by-value
rule from chapter 10, just applied to a pointer instead of an int.

## String literal vs char array — a real gotcha

```c
char *s1 = "Hello";        // s1 points at a string LITERAL — read-only memory
char s2[] = "Hello";       // s2 is a genuine, mutable array — a real copy

s1[0] = 'J';   // undefined behavior — modifying a string literal, may crash
s2[0] = 'J';   // perfectly fine — s2 is its own writable array
```

`char *s1 = "Hello";` makes `s1` point directly at the literal string
stored by the compiler, which is often placed in read-only memory.
Attempting to modify it is undefined behavior — on many systems it
segfaults immediately, which is the *safe* failure mode; on others it
might silently corrupt something. `char s2[] = "Hello";`, by contrast,
copies the characters into a genuine array on the stack that you own and
can freely modify. If you intend to modify a string, always use the array
form, never a raw `char *` pointing at a literal.

## Building your own strcpy

```c
#include <stdio.h>

void my_strcpy(char *dest, const char *src) {
    while (*src != '\0') {
        *dest = *src;
        dest++;
        src++;
    }
    *dest = '\0';   // don't forget the terminator!
}

int main(void) {
    char buffer[20];
    my_strcpy(buffer, "Hello");
    printf("%s\n", buffer);
    return 0;
}
```

Notice this has exactly the same weakness as the real `strcpy` — no
bounds checking on `dest`. Writing this yourself is a great way to
*really* understand why the real one is dangerous with untrusted input
(chapter 15 and chapter 32 both circle back to this).

## Comparing your own way (don't do this, but understand it)

```c
int my_strcmp(const char *a, const char *b) {
    while (*a != '\0' && *b != '\0') {
        if (*a != *b) {
            return *a - *b;
        }
        a++;
        b++;
    }
    return *a - *b;   // handles one string ending before the other
}
```

Worth writing once so you understand *why* `strcmp` returns something
other than a plain `0`/`1` — the nonzero return value tells you which
string is "greater" alphabetically (based on character codes), which
occasionally matters for sorting.

## Try it yourself

- Write `my_strcat(char *dest, const char *src)` from scratch, appending
  `src` onto the end of `dest`.
- Write a function that counts how many times a specific character
  appears in a string, walking it with a pointer instead of indexing.
- Take the `char *s1 = "Hello"; s1[0] = 'J';` example, run it in a
  disposable test file, and see what actually happens on your system.

---
**Next:** [21. Stack & Heap →](../21-stack-heap/README.md)
