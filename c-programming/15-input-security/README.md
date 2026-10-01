# 15. Input Security

**Previous:** [← 14. Strings](../14-strings/README.md) | **Home:** [README](../README.md) | **Next:** [16. Memory →](../16-memory/README.md)

This chapter gets its own spot in the curriculum for a reason: an enormous
share of real-world C vulnerabilities — the kind that end up as actual
CVEs — trace back to one root cause: **trusting input without checking
it.** If you're heading into security work of any kind (web, IoT,
wherever), this is one of the most important habits you can build early.

## Why user input is the #1 attack surface

Any data coming from outside your program's control — keyboard input,
network packets, a file someone else can edit, command-line arguments,
sensor readings on an embedded device — should be treated as *potentially
hostile* until you've validated it. Not because every user is malicious,
but because your program has no way to tell the difference between
"innocent typo" and "someone testing your bounds on purpose" unless you
check.

## Recap: why `gets()` doesn't even exist anymore

Older C code sometimes uses a function called `gets()` to read a line of
input. It has no way to limit how much it reads — it will happily write
past the end of any buffer you give it. It was so dangerous that it was
formally **removed from the C standard library** in C11. If you see it in
old code or tutorials, that's a sign the material is outdated. Never use
it.

## The scanf("%s", ...) problem, properly

```c
char name[10];
scanf("%s", name);   // no bound — a longer input overflows the buffer
```

Same core problem as `gets()`. `scanf("%s")` reads until it hits
whitespace, with no regard for how big `name` actually is. You *can* give
`scanf` a width limit:

```c
scanf("%9s", name);   // reads at most 9 chars, leaving room for '\0'
```

This works, but the limit has to be hardcoded and kept in sync with the
buffer size by hand — easy to forget when you resize the buffer later.
`fgets` (chapter 04) takes its limit from `sizeof`, which stays correct
automatically, so prefer it for anything beyond trivial single-token
input.

## Validating input, not just safely storing it

Safely reading input into a buffer is only half the job — the content
itself might still be wrong or dangerous for what you're about to do with
it.

```c
#include <stdio.h>

int main(void) {
    int age;

    printf("Enter your age: ");
    if (scanf("%d", &age) != 1) {
        printf("That wasn't a valid number.\n");
        return 1;
    }

    if (age < 0 || age > 150) {
        printf("That age doesn't make sense.\n");
        return 1;
    }

    printf("Age accepted: %d\n", age);
    return 0;
}
```

Two separate checks here worth noticing:
- **`scanf`'s own return value** — it returns the number of items it
  successfully read. If someone types letters where a number was expected,
  `scanf` doesn't crash, it just fails to fill `age`, leaving it
  uninitialized — checking the return value is how you catch that.
- **Range validation** — a syntactically valid number can still be
  semantically nonsense for your program. Both checks matter.

## A word on command injection

If you ever pass user input into `system()` (covered in chapter 30) or
build a shell command by string concatenation, you're opening the door to
**command injection** — where an attacker's input contains characters
that let them run arbitrary commands instead of just supplying data.

```c
char filename[100];
scanf("%s", filename);

char command[200];
sprintf(command, "cat %s", filename);   // DANGEROUS
system(command);
```

If `filename` contains something like `; rm -rf ~`, the resulting command
string does exactly what that implies. This exact class of bug shows up
constantly in real vulnerability reports for both web applications and
native tools. The fix isn't a clever escaping trick — it's avoiding
`system()` with user-controlled input entirely wherever you can, and using
safer APIs (like `execve` with an argument array instead of a shell
string) when you genuinely need to run an external program.

## A mental checklist for handling input

1. **Bound it** — never read into a fixed buffer without a size limit
   (`fgets`, or `scanf` with an explicit width).
2. **Check the return value** — did the read actually succeed?
3. **Validate the content** — is this value actually reasonable for what
   you're about to do with it?
4. **Never build shell commands or SQL/file paths by directly pasting in
   raw user input.**

## Try it yourself

- Take your `fgets`-based name/age program from chapter 04 and add proper
  validation: reject an age that isn't a positive number.
- Deliberately build the `sprintf` + `system()` example above in a
  disposable test file (never on a real system you care about), and type
  something with a semicolon in it to see the injection happen. This is a
  controlled, first-hand look at *why* the rule exists, not just a rule to
  memorize.
- Rewrite the input-checking example to also reject non-numeric junk
  trailing after a valid number (hint: look into checking `scanf`'s
  behavior when there's leftover unparsed input in the buffer).

---
**Next:** [16. Memory →](../16-memory/README.md)
