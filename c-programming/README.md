# C Programming — From First Line to Systems & Security

Hey there my name is Quincy and this is my own C study guide. I'm building it while I learn, mostly so future-me
has something better than scattered notes and half-finished PDFs — and if it
helps someone else along the way, even better.

I'm coming at C from a security/web-hacking background and heading toward IoT
and embedded work, so this guide leans that way in a few places (memory,
pointers, buffer overflows, embedded chapters). But the first ~25 chapters are
just solid general-purpose C — useful no matter what you're doing with the
language.

## How to actually use this

Don't just read it. Every chapter has code in it — type it out yourself
(don't copy-paste) My friend did this and gave up in just one week of starting, compile it, run it, break it on purpose, then fix it.
C punishes skimming harder than most languages because the compiler will
happily let you shoot yourself in the foot and say nothing about it.

Go in order the first time through. Chapters build on each other, especially
from `13-arrays` onward — pointers won't make sense if you don't already have
arrays and functions solid.

## Structure

### Phase 1 — Foundations
| # | Chapter | What it covers |
|---|---------|-----------------|
| 00 | [Introduction](00-introduction/README.md) | What C is, why it still matters |
| 01 | [Setup](01-setup/README.md) | Installing a compiler, compiling your first thing |
| 02 | [First Program](02-first-program/README.md) | Hello World, dissected properly |
| 03 | [Variables & Data Types](03-variables-data-types/README.md) | int, float, char, sizes, ranges |
| 04 | [Input & Output](04-input-output/README.md) | printf, scanf, format specifiers |
| 05 | [Operators](05-operators/README.md) | Arithmetic, logic, precedence traps |
| 06 | [Conditions](06-conditions/README.md) | if / else, common bugs |
| 07 | [Switch](07-switch/README.md) | switch/case, fallthrough |
| 08 | [Loops](08-loops/README.md) | for, while, do-while |
| 09 | [Practical Logic](09-practical-logic/README.md) | Putting it together on real problems |

### Phase 2 — Functions & Program Structure
| # | Chapter | What it covers |
|---|---------|-----------------|
| 10 | [Functions](10-functions/README.md) | Declaring, defining, calling |
| 11 | [Return Values](11-return-values/README.md) | What functions hand back and how |
| 12 | [Scope](12-scope/README.md) | Local, global, static |

### Phase 3 — Data Handling
| # | Chapter | What it covers |
|---|---------|-----------------|
| 13 | [Arrays](13-arrays/README.md) | Fixed-size collections, indexing |
| 14 | [Strings](14-strings/README.md) | char arrays, the null terminator |
| 15 | [Input Security](15-input-security/README.md) | Why raw input handling is where things go wrong |

### Phase 4 — Memory & Pointers (the real heart of C)
| # | Chapter | What it covers |
|---|---------|-----------------|
| 16 | [Memory](16-memory/README.md) | How data actually lives in RAM |
| 17 | [Pointers](17-pointers/README.md) | Addresses, `&` and `*`, the big one |
| 18 | [Pointer Functions](18-pointer-functions/README.md) | Pass-by-reference, function pointers |
| 19 | [Pointer Arrays](19-pointer-arrays/README.md) | How arrays and pointers relate |
| 20 | [Pointer Strings](20-pointer-strings/README.md) | Walking strings by hand |
| 21 | [Stack & Heap](21-stack-heap/README.md) | Where your variables physically live |
| 22 | [Dynamic Memory](22-dynamic-memory/README.md) | malloc, calloc, realloc, free |
| 23 | [Memory Bugs](23-memory-bugs/README.md) | Leaks, double-frees, use-after-free |

### Phase 5 — Custom Types
| # | Chapter | What it covers |
|---|---------|-----------------|
| 24 | [Structures](24-structures/README.md) | Grouping related data |
| 25 | [Typedef](25-typedef/README.md) | Naming your own types |
| 26 | [Enums](26-enums/README.md) | Named constants done right |
| 27 | [Unions](27-unions/README.md) | Shared memory between types |

### Phase 6 — Talking to the OS
| # | Chapter | What it covers |
|---|---------|-----------------|
| 28 | [File Handling](28-file-handling/README.md) | Reading and writing files |
| 29 | [Command Line](29-command-line/README.md) | argc, argv, building real CLI tools |
| 30 | [System Interaction](30-system-interaction/README.md) | system(), exit codes, environment |

### Phase 7 — Low-Level & Security
| # | Chapter | What it covers |
|---|---------|-----------------|
| 31 | [Memory Layout](31-memory-layout/README.md) | The full picture: text, data, bss, heap, stack |
| 32 | [Buffer Overflows](32-buffer-overflows/README.md) | How they happen, why they're dangerous, how to stop them |
| 33 | [Debugging with GDB](33-debugging-gdb/README.md) | Actually finding your bugs instead of guessing |
| 34 | [C to Assembly](34-c-to-assembly/README.md) | Seeing what the compiler really does |
| 35 | [Vulnerable Programs](35-vulnerable-programs/README.md) | Spot-the-bug practice with classic C flaws |

### Phase 8 — Embedded & Hardware
| # | Chapter | What it covers |
|---|---------|-----------------|
| 36 | [Embedded C](36-embedded-c/README.md) | C without an OS underneath you |
| 37 | [Bit Manipulation](37-bit-manipulation/README.md) | Working a byte at a time |
| 38 | [Hardware-Oriented C](38-hardware-oriented-c/README.md) | Registers, structs, endianness |
| 39 | [Embedded Security](39-embedded-security/README.md) | Where IoT devices actually get broken |

### Projects
Once you've got the concepts, [`projects/`](projects/) has ideas grouped by
level — `beginner`, `intermediate`, `security`, `embedded` — each with a
`README.md` listing project ideas and what they'll force you to practice. No
solutions, on purpose. Building it yourself is the point.

## Requirements

- A C compiler (`gcc` or `clang`)
- A terminal
- That's genuinely it. No IDE required, though one makes life easier.

If you don't have a compiler yet, start at [`01-setup`](01-setup/README.md).

## Contributing

Found an error, or think a chapter is missing something? Open an issue or a
PR. This is a living document — I'll keep updating it as I learn more myself.

## License

Do whatever you want with this. If you fork it, a mention is appreciated but
not required.

## Author
# Quincy .O. Omoruyi AKA QuineX
# Dont forget to leave a star 
