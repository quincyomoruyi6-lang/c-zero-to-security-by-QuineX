# 34. C to Assembly

**Previous:** [← 33. Debugging with GDB](../33-debugging-gdb/README.md) | **Home:** [README](../README.md) | **Next:** [35. Vulnerable Programs →](../35-vulnerable-programs/README.md)

Chapter 02 mentioned that compiling goes through an assembly stage. Let's
actually look at it. You don't need to become fluent in assembly to
benefit from this — even a rough ability to recognize what's happening
makes you noticeably better at debugging, reverse engineering, and
understanding *why* certain C code patterns are faster or slower than
others.

## Generating assembly output

```c
int add(int a, int b) {
    return a + b;
}
```

```bash
gcc -S add.c -o add.s
```

The `-S` flag stops the compiler right after the assembly stage, instead
of continuing on to produce a full executable. Open `add.s` in any text
editor.

## Reading a simple function's assembly

A simplified, representative version of what you'll see (exact output
varies by compiler version and optimization level):

```asm
add:
    push   %rbp           ; save the caller's base pointer
    mov    %rsp, %rbp     ; set up this function's own base pointer
    mov    %edi, -4(%rbp) ; store parameter 'a' onto the stack
    mov    %esi, -8(%rbp) ; store parameter 'b' onto the stack
    mov    -4(%rbp), %eax ; load 'a' into a register
    add    -8(%rbp), %eax ; add 'b' to it
    pop    %rbp           ; restore the caller's base pointer
    ret                   ; return, using the address on the stack
```

A few instructions worth recognizing on sight:

| Instruction | Rough meaning |
|-------------|---------------|
| `mov src, dst` | Copy a value from src to dst |
| `push` / `pop` | Add/remove a value from the top of the stack |
| `add`, `sub`, `mul` | Arithmetic, directly on registers/memory |
| `call` | Call a function (pushes the return address, then jumps) |
| `ret` | Return (pops the return address, jumps back to it) |
| `cmp` | Compare two values (sets flags used by conditional jumps) |
| `je`, `jne`, `jg`, `jl` | Conditional jumps ("jump if equal," etc) |

This connects directly back to chapter 32 — the `push %rbp` /
`pop %rbp` / `ret` sequence you see here is exactly the stack frame
mechanics that buffer overflow attacks target: `push %rbp` is setting up
the saved base pointer, and the return address `ret` uses is sitting
right there on the stack too.

## Why this is worth knowing, practically

- **Debugging** — when GDB shows you a crash with no source line info
  (like inside a library you don't have symbols for), being able to
  glance at the assembly and recognize "oh, this is doing a function
  call" or "this is a comparison" is genuinely useful.
- **Understanding compiler optimizations** — comparing `gcc -S` output at
  `-O0` (no optimization) vs `-O2` (optimized) shows you, concretely, what
  the compiler actually does to make your code faster — dead code
  elimination, loop unrolling, keeping values in registers instead of
  memory.
- **Reverse engineering and security research** — most tools in that
  space (disassemblers, decompilers) present you with output that looks
  exactly like this, just without the friendly comments.
- **Embedded work** — on resource-constrained devices, being able to spot
  wasteful generated code matters more than it does when you've got
  gigabytes of RAM to spare.

## objdump — disassembling a compiled binary directly

```bash
gcc add.c -o add
objdump -d add
```

`objdump -d` disassembles an already-compiled binary — useful when you
don't have the source at all, only the executable. This is the actual
tool reverse engineers reach for constantly, alongside more advanced
ones like Ghidra or IDA once you go deeper into that field.

## Try it yourself

- Generate assembly for a simple `if/else` function and try to spot the
  `cmp` and conditional jump instructions corresponding to your condition.
- Compile the same small function with `-O0` and `-O2`, generate assembly
  for both with `-S`, and compare — even without understanding every
  instruction, you should be able to see the `-O2` version is noticeably
  shorter or more compact.
- Run `objdump -d` on one of your own compiled programs and try to locate
  the disassembly for `main` — look for the `call` instructions
  corresponding to functions you know it calls.

---
**Next:** [35. Vulnerable Programs →](../35-vulnerable-programs/README.md)
