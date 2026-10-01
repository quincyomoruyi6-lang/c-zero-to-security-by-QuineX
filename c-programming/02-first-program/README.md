# 02. First Program

**Previous:** [← 01. Setup](../01-setup/README.md) | **Home:** [README](../README.md) | **Next:** [03. Variables & Data Types →](../03-variables-data-types/README.md)

## The obligatory Hello World

```c
#include <stdio.h>

int main(void) {
    printf("Hello, world!\n");
    return 0;
}
```

Compile and run:

```bash
gcc -Wall -Wextra hello.c -o hello
./hello
```

You should see `Hello, world!` printed. Simple enough — but every single
piece of that program is doing something specific, and understanding each
piece now saves you confusion later.

## Breaking it down line by line

**`#include <stdio.h>`**
This is a preprocessor directive — it runs *before* actual compilation and
pastes in the contents of the "standard I/O" header. That header declares
`printf`, `scanf`, and other input/output functions. Without it, the
compiler has no idea what `printf` even is.

**`int main(void)`**
Every C program has exactly one `main` function — it's the entry point.
The operating system calls `main` when your program starts. `int` means
main returns an integer (used as the program's exit status). `void` inside
the parentheses means main takes no arguments here (you'll see `argc`/`argv`
versions in chapter 29).

**`printf("Hello, world!\n");`**
Calls the `printf` function with a string. `\n` is a newline character —
without it, your next terminal prompt would print right after your output
on the same line, which looks broken.

**`return 0;`**
Ends `main` and hands `0` back to the OS. By convention, `0` means "the
program succeeded." Any non-zero value signals an error occurred — you'll
use this properly in chapter 29.

## What "compiling" actually does

When you run `gcc hello.c -o hello`, four separate things happen behind the
scenes:

1. **Preprocessing** — handles `#include`, `#define`, and other directives,
   producing plain C source with everything pasted in.
2. **Compiling** — translates that C source into assembly language
   (chapter 34 shows you this step directly).
3. **Assembling** — turns the assembly into machine code (an object file).
4. **Linking** — combines your object file with the standard library code
   (like the actual implementation of `printf`) into one final executable.

`gcc` does all four automatically. Later you'll learn to run some of these
steps individually when it's useful (like inspecting assembly output).

## Mistakes beginners actually make here

- **Forgetting the semicolon.** Every statement needs one. Miss it and
  you'll get a confusing error, sometimes pointing at the *next* line
  instead of the one you actually messed up.
- **Case sensitivity.** `Main` is not `main`. `Printf` is not `printf`. C
  cares about every character.
- **Forgetting `#include <stdio.h>`** and then being confused why `printf`
  "doesn't exist" — the compiler will usually warn about an implicit
  declaration, but on older setups it might just fail outright.
- **Mismatched quotes or braces.** A missing closing `}` makes the compiler
  think the rest of your file (or the next file!) is still inside `main`.

## Try it yourself

- Print your name, then a separate line with your favorite number.
- Deliberately delete the semicolon after `printf` and read the compiler
  error. Get used to what these look like now, on a program you understand,
  before you meet them on something you don't.
- Change `return 0;` to `return 1;`, then run `echo $?` right after running
  your program in the terminal — that's how you check the exit status.

---
**Next:** [03. Variables & Data Types →](../03-variables-data-types/README.md)
