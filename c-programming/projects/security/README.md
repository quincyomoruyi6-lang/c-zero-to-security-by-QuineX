# Security Projects

**Home:** [README](../../README.md)

Built on **chapters 15, 22-23, and 31-35** — memory bugs, buffer
overflows, and the classic vulnerable patterns. These projects are about
building the "spot it, break it, fix it" instinct through repetition, in
a legal, self-contained way (everything here runs against your own code,
on your own machine).

## 1. A Deliberately Vulnerable CLI Tool
Write a small tool with 3-4 intentional bugs mixed in from chapter 35's
catalog (a format string bug, an off-by-one, a missing bounds check).
Then, separately, write a second version that fixes every one of them.
Run both under `-fsanitize=address` and `valgrind` and compare the
reports.

**Practices:** the full bug catalog from chapters 23 and 35, sanitizer
tooling.

## 2. A Simple Input Fuzzer
Write a small program that generates random/malformed strings (varying
lengths, unexpected characters, missing null terminators where relevant)
and feeds them into one of your own functions from earlier chapters
(your custom `strcpy`, for instance) to see what breaks. This is a tiny,
from-scratch taste of what real fuzzing tools (like AFL) do at a much
larger scale.

**Practices:** input generation, defensive programming, sanitizers.

## 3. A Basic Static Bug Scanner
Write a program that reads a `.c` file as plain text and flags lines
containing known-dangerous function calls (`gets`, `strcpy`, `sprintf`,
`system`) with a warning message and the line number — a tiny, simplified
version of what real static analysis tools do.

**Practices:** file I/O, string searching, command-line tools.

## 4. Buffer Overflow Practice, Properly Scoped
Take the vulnerable example from chapter 32, and practice observing (not
weaponizing) its behavior: confirm the stack-protector catches it by
default, then deliberately disable protections (`-fno-stack-protector -z
execstack`) in a disposable VM/container and observe the raw crash
behavior change. Document what you see.

**Practices:** compiler flags, stack layout, mitigations.

## Where to go deeper from here

For structured, legal practice specifically on exploitation once you've
got this foundation solid, look into:
- **OverTheWire** — wargames like "Narnia" and "Behemoth" cover exactly
  this material, hands-on, in a legal sandboxed environment.
- **pwn.college** — a full, free, structured curriculum on binary
  exploitation, built from first principles.
- **PicoCTF** — beginner-friendly CTF challenges with a solid binary
  exploitation track.

These pick up exactly where this repo's security chapters leave off —
this repo builds the C foundation; those build the offensive-security
depth on top of it.

---
See also [`embedded/`](../embedded/README.md) for where this overlaps
with IoT-specific security.
