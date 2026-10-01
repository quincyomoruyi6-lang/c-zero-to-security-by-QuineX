# 14. Strings

**Previous:** [← 13. Arrays](../13-arrays/README.md) | **Home:** [README](../README.md) | **Next:** [15. Input Security →](../15-input-security/README.md)

## There's no real "string" type in C

A C string is just a `char` array with a special convention: it ends with
a **null terminator**, the byte `'\0'` (value zero), marking "the string
stops here."

```c
char name[6] = {'Q', 'u', 'i', 'n', 'c', 'y'};   // NOT a valid C string — no null terminator!
char name2[7] = {'Q', 'u', 'i', 'n', 'c', 'y', '\0'};   // correct
char name3[] = "Quincy";   // the compiler adds '\0' automatically — this is the way you'll actually write it
```

That third version is what you'll use in practice — string literals in
double quotes automatically get a null terminator appended. The array
needs to be at least one byte bigger than the visible text to hold it.

## Why the null terminator matters so much

Every string function (`strlen`, `printf("%s", ...)`, `strcpy`, etc.)
works by reading bytes one at a time **until it hits `'\0'`**. If that
terminator is missing or gets overwritten, these functions have no idea
where to stop — they'll keep reading (or writing) into whatever memory
happens to come next, which is undefined behavior and a real security
issue (this connects directly to the buffer overflow material in
chapter 32).

## Common string.h functions

```c
#include <string.h>
#include <stdio.h>

int main(void) {
    char greeting[50] = "Hello";

    printf("%zu\n", strlen(greeting));      // 5 — length, NOT counting '\0'

    strcat(greeting, ", world!");           // appends onto greeting
    printf("%s\n", greeting);               // "Hello, world!"

    char copy[50];
    strcpy(copy, greeting);                 // copies greeting into copy
    printf("%s\n", copy);

    if (strcmp(copy, greeting) == 0) {      // 0 means "equal"
        printf("They match\n");
    }

    return 0;
}
```

| Function | Does |
|----------|------|
| `strlen(s)` | Returns length, not including `'\0'` |
| `strcpy(dest, src)` | Copies `src` into `dest` |
| `strcat(dest, src)` | Appends `src` onto the end of `dest` |
| `strcmp(a, b)` | Returns `0` if equal, nonzero otherwise |
| `strncpy`, `strncat` | Bounded versions — see below |

## The problem with strcpy and strcat

Neither checks whether `dest` is actually big enough to hold the result.

```c
char small[5];
strcpy(small, "This is way too long");   // writes past the end of small. No warning. No crash guarantee. Just corruption.
```

This is exactly the kind of bug that shows up constantly in real-world
security vulnerabilities. The safer relatives — `strncpy`, `strncat` —
take an explicit maximum length:

```c
char small[5];
strncpy(small, "This is way too long", sizeof(small) - 1);
small[sizeof(small) - 1] = '\0';   // strncpy doesn't guarantee null-termination if it truncates — always do this
```

Notice the manual null-termination line. `strncpy` is safer against
overflow, but it has its own sharp edge: if the source is longer than the
limit, it won't add the `'\0'` for you. Get in the habit of setting the
last byte yourself whenever you use it.

## Comparing strings the wrong way

```c
char a[] = "hello";
char b[] = "hello";

if (a == b) {   // WRONG — compares addresses, not contents. Almost always false.
    printf("Equal\n");
}
```

`a` and `b` decay to pointers here, so `a == b` checks whether they point
to the *same memory location*, not whether the text matches. Always use
`strcmp` for comparing string contents.

## Stripping a trailing newline from fgets

Recall from chapter 04 that `fgets` keeps the newline if there's room for
it:

```c
char line[100];
fgets(line, sizeof(line), stdin);

size_t len = strlen(line);
if (len > 0 && line[len - 1] == '\n') {
    line[len - 1] = '\0';   // overwrite the newline with a terminator
}
```

This pattern is extremely common in real C programs — expect to write it
often.

## Try it yourself

- Write a program that reads a name with `fgets`, strips the trailing
  newline, then prints "Hello, `<name>`!" on one line.
- Write your own version of `strlen` from scratch (don't use `string.h`
  for this one) — loop through the array counting characters until you
  hit `'\0'`.
- Reproduce the `strcpy` overflow example above with a small buffer, using
  `-Wall -Wextra -fsanitize=address` when compiling, and read the error
  AddressSanitizer gives you. This is your first real look at the kind of
  tooling used to catch these bugs before they ship.

---
**Next:** [15. Input Security →](../15-input-security/README.md)
