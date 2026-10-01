# 04. Input & Output

**Previous:** [← 03. Variables & Data Types](../03-variables-data-types/README.md) | **Home:** [README](../README.md) | **Next:** [05. Operators →](../05-operators/README.md)

## printf — formatted output

You've already used `printf`. Here's the fuller picture:

```c
#include <stdio.h>

int main(void) {
    int age = 18;
    float gpa = 4.5f;
    char grade = 'A';

    printf("Age: %d, GPA: %.2f, Grade: %c\n", age, gpa, grade);
    return 0;
}
```

`%.2f` means "float, 2 digits after the decimal point" — precision control
like this is genuinely useful, not just decoration.

### Common format specifiers

| Specifier | Type |
|-----------|------|
| `%d` | int |
| `%u` | unsigned int |
| `%f` | float / double |
| `%.Nf` | float/double with N decimal places |
| `%c` | char |
| `%s` | string (char array) |
| `%p` | pointer address |
| `%x` | unsigned int, hexadecimal |
| `%%` | a literal `%` character |

## scanf — formatted input

```c
#include <stdio.h>

int main(void) {
    int age;
    printf("Enter your age: ");
    scanf("%d", &age);
    printf("You entered: %d\n", age);
    return 0;
}
```

Notice the `&` before `age`. `scanf` needs the *address* of the variable so
it can write directly into it — not the value. Forgetting the `&` is one of
the single most common beginner mistakes in C, and the compiler often won't
even warn you clearly (with `-Wall` it usually will, another reason to
always use that flag).

## Where scanf gets dangerous — and why chapter 15 exists

```c
char name[10];
scanf("%s", name);   // no & needed here — arrays already decay to an address
```

This looks fine, but it has zero bounds checking. If someone types more
than 9 characters, `scanf` will happily keep writing past the end of
`name`, corrupting whatever memory comes next. This isn't a hypothetical —
it's one of the most classic security bugs in C's history, and it's exactly
why chapter 15 exists as its own dedicated topic. For now, just know: raw
`%s` with `scanf` into a fixed buffer is something you should be
suspicious of the moment you see it.

## scanf's other common trap: the leftover newline

```c
int age;
char initial;

printf("Age: ");
scanf("%d", &age);

printf("Initial: ");
scanf(" %c", &initial);   // note the leading space!
```

When you type `18` and hit Enter, `scanf("%d", ...)` reads `18` but leaves
the newline character sitting in the input buffer. The next `scanf("%c",
...)` will immediately read that leftover newline instead of waiting for
your actual input. The fix is the leading space before `%c` — it tells
`scanf` to skip any whitespace (including leftover newlines) before
reading the character. This one trips up almost everyone at least once.

## fgets — the safer way to read a line

```c
#include <stdio.h>

int main(void) {
    char name[50];
    printf("Enter your name: ");
    fgets(name, sizeof(name), stdin);
    printf("Hello, %s", name);
    return 0;
}
```

`fgets` takes an explicit maximum size (`sizeof(name)`), so it *cannot*
write past the end of your buffer no matter what the user types. This is
the safer default for reading strings, and you should prefer it over
`scanf("%s", ...)` in almost every real program. One quirk: `fgets` keeps
the trailing newline character in the string if there's room for it, which
occasionally needs stripping out (you'll see how in chapter 14).

## Try it yourself

- Write a program that asks for your name (with `fgets`) and age (with
  `scanf`), then prints them both back in one sentence.
- Deliberately trigger the leftover-newline bug above, see the broken
  output, then fix it with the leading space.
- Try typing way more characters than a `scanf("%s", buffer)` buffer can
  hold, using a small buffer like `char buf[5]`. Watch what happens (this
  is a controlled preview of chapter 32 — don't worry yet about *why* it
  behaves the way it does, just notice that C didn't stop you).

---
**Next:** [05. Operators →](../05-operators/README.md)
