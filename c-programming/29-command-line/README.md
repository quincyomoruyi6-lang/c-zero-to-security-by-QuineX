# 29. Command Line

**Previous:** [← 28. File Handling](../28-file-handling/README.md) | **Home:** [README](../README.md) | **Next:** [30. System Interaction →](../30-system-interaction/README.md)

Real command-line tools take arguments — think `gcc file.c -o output`, or
`grep pattern file.txt`. This chapter is how you read those arguments in
your own C programs.

## argc and argv

```c
#include <stdio.h>

int main(int argc, char *argv[]) {
    printf("Number of arguments: %d\n", argc);

    for (int i = 0; i < argc; i++) {
        printf("argv[%d] = %s\n", i, argv[i]);
    }

    return 0;
}
```

Compile and run it with some arguments:

```bash
gcc args.c -o args
./args hello world 42
```

Output:
```
Number of arguments: 4
argv[0] = ./args
argv[1] = hello
argv[2] = world
argv[3] = 42
```

**`argc`** ("argument count") includes the program's own name, so it's
always at least 1. **`argv`** ("argument vector") is an array of
C-strings — note that even `42` above comes through as the string `"42"`,
not the integer `42`. You'll need `atoi` or `strtol` to convert it if you
need a real number.

## Converting argument strings to numbers

```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[]) {
    if (argc < 2) {
        printf("Usage: %s <number>\n", argv[0]);
        return 1;
    }

    int n = atoi(argv[1]);
    printf("You entered: %d\n", n);
    return 0;
}
```

`atoi` ("ascii to integer") is simple but has no error reporting — if the
argument isn't a valid number, it silently returns `0`, indistinguishable
from someone actually typing "0". For real programs where you need to
know if the conversion failed, `strtol` is the better choice — it can
report exactly where parsing stopped.

## Checking argument count before using them

```c
if (argc < 2) {
    printf("Usage: %s <filename>\n", argv[0]);
    return 1;
}
```

This should be one of the very first things a real CLI tool does. Reading
`argv[1]` when the user didn't actually provide one reads past the end of
the `argv` array — undefined behavior, and a genuine, common way small
command-line tools crash.

## Exit codes

```c
#include <stdlib.h>

int main(void) {
    // ...
    return EXIT_SUCCESS;   // == 0, defined in stdlib.h
    // or, on failure:
    // return EXIT_FAILURE;   // == 1
}
```

`EXIT_SUCCESS`/`EXIT_FAILURE` are just named versions of `0`/`1`, but
using the names documents intent clearly. This matters more than it might
seem: shell scripts, CI pipelines, and other programs check a process's
exit code to decide what happened and what to do next — a program that
always returns `0` regardless of whether it actually succeeded is
actively misleading anything that depends on it.

## Environment variables

```c
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    char *home = getenv("HOME");

    if (home != NULL) {
        printf("Home directory: %s\n", home);
    } else {
        printf("HOME not set\n");
    }

    return 0;
}
```

`getenv` reads a value from the process's environment (variables set in
the shell, like `PATH` or `HOME`). Always check for `NULL` — a variable
you expect to exist might not, especially across different systems or
CI environments.

## Why this matters beyond "toy CLI tools"

Most real security and systems tools — scanners, exploit frameworks,
network utilities, embedded flashing tools — are command-line programs
that take flags and arguments. Getting comfortable with `argc`/`argv`
parsing (and doing it defensively, checking counts before use) is a
directly transferable skill toward building that kind of tool yourself.

## Try it yourself

- Write a small tool that takes two numbers and an operator as three
  separate command-line arguments (`./calc 5 + 3`) and prints the result.
- Add a proper usage message that prints if `argc` doesn't match what
  you expect, and returns `EXIT_FAILURE`.
- Write a program that prints the value of an environment variable named
  on the command line (`./printenv PATH`), handling the case where it's
  not set.

---
**Next:** [30. System Interaction →](../30-system-interaction/README.md)
