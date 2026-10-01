# 30. System Interaction

**Previous:** [← 29. Command Line](../29-command-line/README.md) | **Home:** [README](../README.md) | **Next:** [31. Memory Layout →](../31-memory-layout/README.md)

C programs don't run in isolation — they run on top of an operating
system, and C gives you direct ways to talk to it. This chapter covers
the basics on a Unix-like system (Linux/macOS); some of this doesn't
translate to native Windows, which is one more reason chapter 01
recommended WSL if you're on Windows.

## system() — running a shell command

```c
#include <stdlib.h>

int main(void) {
    system("ls -la");
    return 0;
}
```

`system()` hands a string straight to the shell to execute. Simple, and
genuinely dangerous the moment any part of that string comes from
untrusted input — this is exactly the command injection risk covered in
chapter 15. If you only take one thing from this section: **never build a
`system()` command string using unvalidated user input.** Where you
control the entire string yourself (a fixed command, no external input
involved), it's low-risk; the danger is specifically about concatenating
in anything you didn't fully control.

## exit codes, properly

```c
#include <stdlib.h>

int main(void) {
    // ... program logic ...

    if (/* something failed */ 0) {
        exit(EXIT_FAILURE);   // exits immediately, from anywhere in the program
    }

    exit(EXIT_SUCCESS);
}
```

`exit()` terminates the program immediately from wherever it's called —
useful for bailing out deep inside nested function calls without
manually returning all the way back up through every caller. Unlike a
plain `return` from `main`, `exit()` works from any function.

## A conceptual look at fork() and processes

On Unix systems, `fork()` creates a near-exact copy of the currently
running process — after calling it, you briefly have two processes
running the same code from that point onward, distinguished only by
`fork()`'s return value (0 in the new child process, the child's process
ID in the original parent).

```c
#include <stdio.h>
#include <unistd.h>

int main(void) {
    pid_t pid = fork();

    if (pid == 0) {
        printf("I am the child process\n");
    } else {
        printf("I am the parent process, child PID is %d\n", pid);
    }

    return 0;
}
```

This is genuinely one of the more mind-bending concepts the first time
you see it — one function call, and suddenly your program is running
twice, independently, from that exact line onward. This is how Unix
shells launch every program you run, and it's a full topic in its own
right (process management, `exec()` to replace a process's code, `wait()`
to synchronize parent and child) that goes beyond what this chapter can
cover — treat this as a conceptual introduction, not a complete picture.

## Signals, briefly

Signals are the OS's way of interrupting a running process asynchronously
— Ctrl+C in your terminal sends `SIGINT`, for example.

```c
#include <stdio.h>
#include <signal.h>
#include <unistd.h>

void handle_sigint(int sig) {
    (void)sig;
    printf("\nCaught Ctrl+C, but not exiting!\n");
}

int main(void) {
    signal(SIGINT, handle_sigint);

    while (1) {
        printf("Running... (Ctrl+C to try interrupting)\n");
        sleep(1);
    }

    return 0;
}
```

`signal()` registers a handler function that runs when a specific signal
arrives, instead of the default behavior (which, for `SIGINT`, is usually
to terminate the process immediately). You won't need this constantly,
but recognizing what a signal handler looks like is useful — plenty of
real daemons and long-running tools use this exact pattern for graceful
shutdown.

## The unistd.h header

Several POSIX-specific (Unix-family) functions live here:
`fork`, `sleep`, `getpid`, `close`, and more. If you see `#include
<unistd.h>` in someone's code, it's a strong signal the program is meant
for Unix-like systems specifically, not portable C in the strictest
sense.

## Try it yourself

- Write a program that uses `system()` to run a fixed, safe command (like
  `date` or `whoami`), and print a message before and after the call.
- Write the `fork()` example above, run it, and try to reason through
  exactly what order the two print statements might appear in — then run
  it several times and see if the order is always the same.
- Write a program with a `SIGINT` handler that, on the first Ctrl+C,
  prints a warning, and on a second Ctrl+C, actually exits (hint: you'll
  need a variable tracking how many times the handler has already run).

---
**Next:** [31. Memory Layout →](../31-memory-layout/README.md)
