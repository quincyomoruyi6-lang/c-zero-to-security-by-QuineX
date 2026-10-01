# 33. Debugging with GDB

**Previous:** [← 32. Buffer Overflows](../32-buffer-overflows/README.md) | **Home:** [README](../README.md) | **Next:** [34. C to Assembly →](../34-c-to-assembly/README.md)

Printing values with `printf` to figure out what's wrong works, until it
doesn't — some bugs need you to actually pause execution and poke around.
That's what a debugger is for, and GDB (the GNU Debugger) is the standard
one for C on Linux.

## Compiling with debug info

```bash
gcc -g -Wall -Wextra program.c -o program
```

The `-g` flag embeds debug symbols (variable names, line numbers) into
the binary. Without it, GDB can still technically attach, but you'll be
staring at raw addresses instead of your actual variable names and source
lines — always compile with `-g` while you're actively debugging.

## A buggy program to debug

```c
#include <stdio.h>

int divide(int a, int b) {
    return a / b;
}

int main(void) {
    int numbers[] = {10, 5, 0, 2};
    int total = 0;

    for (int i = 0; i < 4; i++) {
        total = total + divide(100, numbers[i]);
    }

    printf("Total: %d\n", total);
    return 0;
}
```

This crashes — division by zero when `numbers[2]` is `0`.

## Starting GDB

```bash
gdb ./program
```

You'll land at a `(gdb)` prompt. Key commands:

| Command | Does |
|---------|------|
| `run` | Starts the program |
| `break <location>` | Sets a breakpoint (e.g. `break divide` or `break 10`) |
| `next` | Runs the next line, stepping OVER function calls |
| `step` | Runs the next line, stepping INTO function calls |
| `print <var>` | Prints a variable's current value |
| `backtrace` (or `bt`) | Shows the call stack — how you got to where you are |
| `continue` | Resumes running until the next breakpoint or crash |
| `quit` | Exits GDB |

## A real debugging session

```
(gdb) break divide
Breakpoint 1 at 0x...: file program.c, line 4.
(gdb) run
Breakpoint 1, divide (a=100, b=10) at program.c:4
4	    return a / b;
(gdb) continue
Breakpoint 1, divide (a=100, b=5) at program.c:4
4	    return a / b;
(gdb) continue
Breakpoint 1, divide (a=100, b=0) at program.c:4
4	    return a / b;
(gdb) print b
$1 = 0
(gdb) continue
Program received signal SIGFPE, Arithmetic exception.
0x... in divide (a=100, b=0) at program.c:4
4	    return a / b;
```

Right there — `print b` confirms `b` is `0` on this call, right before
the crash. In a bigger program with dozens of function calls, this is
dramatically faster than scattering `printf` statements everywhere and
recompiling repeatedly.

## Examining a crash after the fact with backtrace

```
(gdb) backtrace
#0  divide (a=100, b=0) at program.c:4
#1  0x... in main () at program.c:11
```

This shows the exact call chain leading to the crash — `main` called
`divide` at line 11, and the crash happened inside `divide` at line 4.
For a deeply nested call chain, this is often the fastest way to see
exactly how you got into trouble.

## Watching a variable

```
(gdb) watch total
```

GDB will pause execution every time `total`'s value changes, anywhere in
the program — useful for tracking down exactly where a variable gets an
unexpected value, without having to guess which line is responsible.

## Examining raw memory

```
(gdb) x/4dw numbers
```

`x` (examine memory) with a format like `4dw` means "4 items, decimal,
word-sized (4 bytes)" — dumps the raw contents of the `numbers` array
directly from memory. Useful once you're debugging something lower-level
than a single variable, like confirming an array's actual contents match
what you expect.

## Try it yourself

- Reproduce the divide-by-zero example, set a breakpoint on `divide`, and
  step through all four calls using `continue`, printing `b` each time.
- Deliberately write an out-of-bounds array access (from chapter 13),
  compile with `-g`, and use `backtrace` after it crashes to see exactly
  where.
- Use `watch` on a variable that gets modified inside a loop, and observe
  GDB pausing on every single change.

---
**Next:** [34. C to Assembly →](../34-c-to-assembly/README.md)
