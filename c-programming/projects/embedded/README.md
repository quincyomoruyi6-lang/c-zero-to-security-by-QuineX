# Embedded Projects

**Home:** [README](../../README.md)

Built on **chapters 36-39** — no real hardware required for most of
these; you can simulate "hardware" with plain structs and functions on
your own machine, then port the concepts to a real microcontroller
(Arduino/ESP32 are both good, cheap, well-documented starting points)
once you're ready.

## 1. Simulated GPIO / Blinking LED
Simulate a memory-mapped GPIO register with a global `volatile uint8_t`,
and write `led_on()`/`led_off()`/`led_toggle()` functions that manipulate
specific bits of it (chapter 37). Add a fake "hardware" thread or timer
loop that prints the LED's current state every second, so you can see it
actually toggling over simulated time.

**Practices:** bit manipulation, `volatile`, register-style struct
design.

## 2. Fixed-Size Packet Parser
Define a struct representing a simple, fixed-format telemetry packet
(think: a drone sending altitude, speed, and battery level as packed
fields), including correct use of `stdint.h` types. Write functions to
serialize it into a raw byte buffer and parse it back out, handling
endianness explicitly (chapter 38).

**Practices:** structs, bit-fields, endianness, fixed-width types — this
one maps almost directly onto real drone/IoT telemetry work.

## 3. Simple Register-Based "Fake Hardware" Simulator
Build a small set of simulated hardware registers (control, status,
data) as a struct, and write functions that behave like a real peripheral
driver would — writing to "control" changes what "status" reports,
reading "data" only works if a status flag is set, etc. This lets you
practice the exact patterns from chapter 38 without needing a physical
board yet.

**Practices:** memory-mapped I/O patterns, `volatile`, state machines.

## 4. A Minimal Command Protocol
Design a tiny binary protocol (a 1-byte command ID, a 1-byte length, then
that many data bytes) for sending simple commands to a simulated device
over a buffer (standing in for a real serial/radio link). Write both the
encoder and decoder, including basic validation (reject a claimed length
longer than the buffer actually has).

**Practices:** bit/byte manipulation, input validation (chapter 15,
applied to a binary protocol instead of text), buffer safety.

## 5. Secure Update Checker (Concept-Level)
Simulate a firmware update process: given a byte buffer representing
"update data" and a separately provided "expected checksum," verify the
data matches before "flashing" it (just print success/failure — you
don't need real cryptography for this exercise, a simple checksum is
enough to practice the *pattern* of "verify before you trust and act").

**Practices:** the embedded security mindset from chapter 39, applied
concretely.

---
See also [`security/`](../security/README.md) for the general security
practice this builds on.
