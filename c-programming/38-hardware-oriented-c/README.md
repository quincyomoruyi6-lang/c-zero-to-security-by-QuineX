# 38. Hardware-Oriented C

**Previous:** [← 37. Bit Manipulation](../37-bit-manipulation/README.md) | **Home:** [README](../README.md) | **Next:** [39. Embedded Security →](../39-embedded-security/README.md)

This chapter ties together structs (24), unions (27), bit manipulation
(37), and memory-mapped I/O (36) into the actual techniques used to model
real hardware in C.

## Fixed-width types — stdint.h

Chapter 03 mentioned that `int`'s exact size isn't guaranteed by the
language, just its minimum range. On hardware, this ambiguity is
unacceptable — if a sensor's register is documented as exactly 16 bits,
your code needs a type that's *exactly* 16 bits, on every platform you
might compile for.

```c
#include <stdint.h>

uint8_t  small_value;    // exactly 8 bits, unsigned
int16_t  signed_value;   // exactly 16 bits, signed
uint32_t register_value; // exactly 32 bits, unsigned
```

| Type | Exact size | Signed? |
|------|-----------|---------|
| `uint8_t` | 8 bits | No |
| `int8_t` | 8 bits | Yes |
| `uint16_t` | 16 bits | No |
| `int16_t` | 16 bits | Yes |
| `uint32_t` | 32 bits | No |
| `int32_t` | 32 bits | Yes |

Get in the habit of using these instead of plain `int`/`char` whenever
exact size actually matters — which, in embedded and protocol work, is
most of the time.

## Bit-fields — packing multiple values into a struct

```c
#include <stdint.h>

struct StatusRegister {
    uint8_t power_on   : 1;   // 1 bit
    uint8_t error_flag : 1;   // 1 bit
    uint8_t mode       : 2;   // 2 bits (values 0-3)
    uint8_t reserved   : 4;   // 4 unused bits, padding to a full byte
};
```

The `: N` syntax after a member tells the compiler to use only `N` bits
for that field, packing several fields into shared storage — this whole
struct fits in a single byte, matching how a real hardware status
register is typically documented (bit 0 = power state, bit 1 = error,
bits 2-3 = mode, and so on). Bit-fields make register-mapping code read
much closer to the datasheet than manual shifting and masking would.

One caveat worth knowing: the exact bit ordering within a bit-field is
implementation-defined by the C standard — it can differ between
compilers/platforms. For code that has to match a specific documented
hardware layout exactly, some embedded codebases use manual
shift/mask (chapter 37) instead of bit-fields specifically to guarantee
the bit order, or carefully verify their compiler's behavior. Know that
this caveat exists before you rely on bit-field ordering for something
safety-critical.

## Representing a hardware register with a struct

```c
#include <stdint.h>

typedef struct {
    volatile uint32_t control;
    volatile uint32_t status;
    volatile uint32_t data;
} UART_TypeDef;

#define UART0 ((UART_TypeDef *)0x40001000)

void uart_send(uint8_t byte) {
    UART0->data = byte;
}
```

This pattern — a struct whose members line up exactly with a hardware
peripheral's memory layout, pointed at via a fixed address — is exactly
how real microcontroller vendor header files are written (STM32, ESP32,
and similar platforms all use this style, generated directly from the
chip's datasheet). Notice `volatile` on every member — chapter 36
explained why: these values can change due to hardware, not just your
own code, so the compiler must never assume it can skip re-reading them.

## Endianness — why it matters for protocols

Multi-byte values can be stored in memory in two different byte orders:

- **Big-endian** — most significant byte first (how humans normally write
  numbers)
- **Little-endian** — least significant byte first (what x86 and most ARM
  configurations use internally)

```c
#include <stdint.h>
#include <stdio.h>

int main(void) {
    uint32_t value = 0x12345678;
    uint8_t *bytes = (uint8_t *)&value;

    printf("%02x %02x %02x %02x\n", bytes[0], bytes[1], bytes[2], bytes[3]);
    // On a little-endian system: 78 56 34 12
    return 0;
}
```

This matters constantly in real embedded/protocol work: if a drone's
flight controller sends telemetry over a radio link using big-endian
values (very common in network protocols, following a convention often
called "network byte order"), and the receiving device is little-endian
internally, reading the raw bytes without converting produces completely
wrong numbers — not a crash, just silently incorrect data, which is
often worse. Functions like `htons`/`ntohs` (host-to-network,
network-to-host) exist specifically to handle this conversion reliably.

## Try it yourself

- Write the endianness-check example above yourself, run it, and confirm
  which byte order your own machine actually uses.
- Define a `struct StatusRegister` with bit-fields matching some made-up
  but plausible hardware register layout, set a few fields, and print the
  whole struct's raw byte value using a technique like the endianness
  example (reinterpreting it as a `uint8_t *`).
- Look up how a specific real sensor (an accelerometer or GPS module is a
  good, well-documented example) structures its data registers in its
  datasheet, and sketch out (on paper is fine) what a C struct modeling
  that register layout might look like.

---
**Next:** [39. Embedded Security →](../39-embedded-security/README.md)
