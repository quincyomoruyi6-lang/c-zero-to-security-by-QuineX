# 28. File Handling

**Previous:** [← 27. Unions](../27-unions/README.md) | **Home:** [README](../README.md) | **Next:** [29. Command Line →](../29-command-line/README.md)

## Opening a file

```c
#include <stdio.h>

int main(void) {
    FILE *f = fopen("data.txt", "w");

    if (f == NULL) {
        printf("Could not open file\n");
        return 1;
    }

    fprintf(f, "Hello, file!\n");
    fclose(f);

    return 0;
}
```

`fopen` returns a `FILE *` — a handle representing an open file — or
`NULL` if opening failed (wrong permissions, disk full, path doesn't
exist for a read, etc). **Always check for `NULL`.** A huge share of real
crashes involving files come from skipping this one check and later
dereferencing a `NULL` file handle.

## Common modes

| Mode | Meaning |
|------|---------|
| `"r"` | Read (fails if the file doesn't exist) |
| `"w"` | Write (creates the file, **overwrites if it already exists**) |
| `"a"` | Append (creates the file if needed, writes go to the end) |
| `"rb"`, `"wb"`, `"ab"` | Same as above, but binary mode (no text translation) |
| `"r+"` | Read and write, file must already exist |

`"w"` silently destroying existing content is a genuinely common mistake
— double-check you actually want to overwrite before using it on
anything that matters.

## Writing to a file

```c
FILE *f = fopen("log.txt", "a");   // append, so we don't wipe previous logs
if (f != NULL) {
    fprintf(f, "Event logged\n");
    fclose(f);
}
```

`fprintf` works exactly like `printf`, just writing to a file stream
instead of standard output.

## Reading a file, line by line

```c
#include <stdio.h>

int main(void) {
    FILE *f = fopen("data.txt", "r");
    if (f == NULL) {
        printf("Could not open file\n");
        return 1;
    }

    char line[256];
    while (fgets(line, sizeof(line), f) != NULL) {
        printf("%s", line);
    }

    fclose(f);
    return 0;
}
```

`fgets` on a file works identically to how you used it on `stdin` back in
chapter 04 — bounded, safe, and it returns `NULL` when there's nothing
left to read, which is exactly what drives the loop's exit condition
here.

## Reading formatted data

```c
FILE *f = fopen("scores.txt", "r");
int score;

while (fscanf(f, "%d", &score) == 1) {
    printf("Score: %d\n", score);
}
```

`fscanf` is `scanf`'s file-reading sibling — same format specifiers, same
return-value convention (number of items successfully read), same
general caveats about validating that return value.

## Always close what you open

```c
fclose(f);
```

Every `fopen` needs a matching `fclose`. Forgetting it isn't usually
catastrophic for a short-lived program (the OS typically cleans up open
handles when a process exits), but it's still a real resource leak in
any long-running program, and unflushed writes can be lost if the program
doesn't exit cleanly. Treat it the same way you treat `malloc`/`free` —
one open, one matching close, on every code path.

## Binary files, briefly

```c
#include <stdio.h>

struct Record { int id; float value; };

int main(void) {
    struct Record r = {1, 99.5f};

    FILE *f = fopen("data.bin", "wb");
    fwrite(&r, sizeof(struct Record), 1, f);
    fclose(f);

    struct Record loaded;
    f = fopen("data.bin", "rb");
    fread(&loaded, sizeof(struct Record), 1, f);
    fclose(f);

    printf("%d %.1f\n", loaded.id, loaded.value);
    return 0;
}
```

`fwrite`/`fread` write and read raw bytes directly — the exact in-memory
representation of a struct, dumped straight to disk. Fast, but not
portable between systems with different byte ordering or struct padding
(you'll meet both of those concepts properly in the embedded chapters) —
fine for a program's own private data files, risky as a format for
sharing data between different machines or architectures.

## Try it yourself

- Write a program that appends a new line to a log file every time it
  runs, with a counter tracking how many times it's been executed
  (reading the current count from the file first, if it exists).
- Write a program that reads a list of numbers from a file, one per line,
  and prints their sum and average.
- Reproduce the binary struct example above, then open the resulting
  `.bin` file in a plain text editor — notice it looks like garbage, since
  it's raw bytes, not human-readable text.

---
**Next:** [29. Command Line →](../29-command-line/README.md)
