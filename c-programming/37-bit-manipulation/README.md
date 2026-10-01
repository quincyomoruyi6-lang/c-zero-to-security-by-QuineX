# 37. Bit Manipulation

**Previous:** [← 36. Embedded C](../36-embedded-c/README.md) | **Home:** [README](../README.md) | **Next:** [38. Hardware-Oriented C →](../38-hardware-oriented-c/README.md)

Working a byte at a time, or even a single bit at a time, is routine in
embedded and low-level work — hardware registers are frequently a pile of
individual on/off flags packed into one byte or word, and network
protocols (including the kind of telemetry a drone sends) often pack data
tightly to save bandwidth. This chapter is the toolkit for that.

## The bitwise operators

```c
unsigned char a = 0b00001100;   // binary literal (C23) — or 0x0C in hex
unsigned char b = 0b00001010;

a & b    // AND:  0b00001000 — 1 only where BOTH bits are 1
a | b    // OR:   0b00001110 — 1 where EITHER bit is 1
a ^ b    // XOR:  0b00000110 — 1 where the bits DIFFER
~a       // NOT:  flips every bit
a << 2   // left shift: 0b00110000 — shifts bits left, multiplies by 4
a >> 2   // right shift: 0b00000011 — shifts bits right, divides by 4
```

Note: binary literals (`0b...`) are a C23 feature and may not compile on
older compilers — `0x0C` (hex) is the traditional, universally portable
way to write the same value.

## Setting, clearing, toggling, and checking a bit

These four operations show up constantly, and they're worth memorizing
as patterns, not just understanding once:

```c
unsigned char flags = 0;

// Set bit 3 (make it 1, leave everything else alone)
flags = flags | (1 << 3);      // or: flags |= (1 << 3);

// Clear bit 3 (make it 0, leave everything else alone)
flags = flags & ~(1 << 3);     // or: flags &= ~(1 << 3);

// Toggle bit 3 (flip it)
flags = flags ^ (1 << 3);      // or: flags ^= (1 << 3);

// Check if bit 3 is set
if (flags & (1 << 3)) {
    printf("Bit 3 is set\n");
}
```

`1 << 3` produces a value with only bit 3 set (`0b00001000`) — this is
called a **bit mask**. AND-ing with it isolates that one bit; OR-ing with
it sets that one bit without disturbing any others; AND-ing with its
complement (`~`) clears just that one bit.

## Why not just use a bool array instead?

You could represent 8 independent flags as `int flags[8]` — but that's 8
full integers (32 bytes on most systems) to represent something that
fits in a single byte using bit flags. On a desktop, that waste is
invisible. On a microcontroller with 2KB of total RAM, packing related
boolean flags into individual bits of one byte is a real, meaningful
saving — and it's also just how most hardware registers are actually
documented and expected to be manipulated.

## A practical example: representing permissions

```c
#define PERM_READ    (1 << 0)   // 0b001
#define PERM_WRITE   (1 << 1)   // 0b010
#define PERM_EXECUTE (1 << 2)   // 0b100

unsigned char permissions = PERM_READ | PERM_WRITE;

if (permissions & PERM_WRITE) {
    printf("Write access granted\n");
}

permissions |= PERM_EXECUTE;     // grant execute too
permissions &= ~PERM_WRITE;      // revoke write
```

This exact pattern — named bit flags combined with `|`, checked with `&`
— is genuinely everywhere: Unix file permissions, network protocol flag
fields, and hardware configuration registers all work this way.

## Shifting and sign — a gotcha with signed types

```c
signed char x = -8;   // 0b11111000 in two's complement
x >> 1;               // implementation-defined for negative signed values on right shift!
```

Right-shifting a **negative signed** value is technically
implementation-defined behavior in C (most compilers do an "arithmetic"
shift that preserves the sign bit, but the standard doesn't strictly
guarantee it). For bit manipulation where you're treating a value as a
pure bit pattern rather than a number, always use `unsigned` types to
avoid this ambiguity entirely.

## Try it yourself

- Write a function `int count_set_bits(unsigned int n)` that counts how
  many bits are `1` in a number, using a loop and `&`/`>>`.
- Build the permissions example above into a tiny working program that
  prints "yes"/"no" for read, write, and execute access after a few
  grant/revoke operations.
- Write a function that packs four small values (each 0-15, fitting in 4
  bits) into a single 16-bit integer using shifts and OR, and a matching
  function that unpacks them back out using shifts and AND — this is
  precisely the kind of packing real compact protocols use.

---
**Next:** [38. Hardware-Oriented C →](../38-hardware-oriented-c/README.md)
