# 01. Setup

**Previous:** [← 00. Introduction](../00-introduction/README.md) | **Home:** [README](../README.md) | **Next:** [02. First Program →](../02-first-program/README.md)

Before you write a single line, you need a compiler. C doesn't run directly —
you write source code, then a compiler turns it into a binary your CPU can
actually execute. Let's get that pipeline working.

## Option 1: Linux

You probably already have `gcc`. Check:

```bash
gcc --version
```

If it's missing:

```bash
sudo apt update
sudo apt install build-essential gdb
```

`build-essential` gets you gcc, make, and the standard headers. `gdb` is the
debugger you'll need in chapter 33 — grab it now.

## Option 2: macOS

Install Xcode Command Line Tools:

```bash
xcode-select --install
```

This gives you `clang`, which understands the same `gcc` command most of the
time (Apple aliases `gcc` to `clang` by default). Everything in this repo
compiles fine with either.

## Option 3: Windows

Don't fight Windows' native toolchain — use **WSL (Windows Subsystem for
Linux)**. Open PowerShell as admin:

```powershell
wsl --install
```

Reboot, open your new Ubuntu terminal, then follow the Linux instructions
above. This gives you a real Linux environment, which matters a lot once you
get to chapters on GDB, memory layout, and system calls — those concepts are
Unix-flavored and behave differently (or don't exist) on native Windows.

## A text editor

Any editor works, but **VS Code** with the "C/C++" extension (from
Microsoft) gives you syntax highlighting and basic autocomplete for free.
You don't need a heavyweight IDE for this — a plain editor and a terminal is
genuinely how most C is written day to day.

## Verifying everything works

Create a file called `test.c`:

```c
#include <stdio.h>

int main(void) {
    printf("Toolchain is working.\n");
    return 0;
}
```

Compile and run it:

```bash
gcc test.c -o test
./test
```

If you see `Toolchain is working.` printed, you're good. If you get
`command not found: gcc`, go back and check the install step for your OS.

## A quick word on `gcc` flags you'll use constantly

```bash
gcc -Wall -Wextra -g file.c -o output
```

- `-Wall -Wextra` — turn on almost all warnings. C will compile plenty of
  genuinely broken code without complaint unless you ask it to warn you.
  Always compile with these on. Always.
- `-g` — includes debug symbols, which GDB (chapter 33) needs to make sense
  of your program.

Get in the habit of using `-Wall -Wextra -g` from day one. It costs nothing
and catches a huge number of bugs before you even run the program.

## Optional: a basic Makefile

Once you're tired of typing the same `gcc` command repeatedly, a `Makefile`
automates it:

```make
CC = gcc
CFLAGS = -Wall -Wextra -g

all: program

program: main.c
	$(CC) $(CFLAGS) main.c -o program

clean:
	rm -f program
```

Run it with `make`. Not required for this repo, but worth knowing exists.

---
**Next:** [02. First Program →](../02-first-program/README.md)
