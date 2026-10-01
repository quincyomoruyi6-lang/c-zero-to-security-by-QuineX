# 36. Embedded C

**Previous:** [← 35. Vulnerable Programs](../35-vulnerable-programs/README.md) | **Home:** [README](../README.md) | **Next:** [37. Bit Manipulation →](../37-bit-manipulation/README.md)

Everything up to this point ran on a desktop/laptop with an OS underneath
it, handling memory protection, process scheduling, and a filesystem for
you. Embedded C throws most of that away — you're often writing code that
runs directly on a microcontroller, with none of those comforts. If
you're aiming at IoT devices, drones, or any kind of hardware hacking,
this is the shift in mindset that actually matters most — more than any
single new syntax rule.

## What's fundamentally different

**No operating system (usually).** On many microcontrollers, your `main`
function *is* the entire program — there's no OS to hand control back to
if it returns, no process isolation, no virtual memory. This is called a
"freestanding" environment, versus the "hosted" environment (with a full
OS and standard library) everything earlier in this repo assumed.

**Extremely limited resources.** A typical desktop has gigabytes of RAM.
A common microcontroller might have a few kilobytes — not gigabytes,
not megabytes, *kilobytes*. Every `malloc`, every large local array,
every unnecessary struct field costs you in a way that simply doesn't
register on a laptop. This is exactly why chapter 21's stack-size
warnings matter so much more here.

**Direct hardware access.** You're often reading and writing specific
memory addresses that correspond to physical hardware — a GPIO pin, a
sensor's data register, a radio module's control bits — not just
abstract variables.

## The volatile keyword

```c
#define STATUS_REGISTER (*(volatile unsigned int *)0x40000000)

while ((STATUS_REGISTER & 0x1) == 0) {
    // wait for a hardware flag to be set
}
```

`volatile` tells the compiler: "this value can change for reasons outside
this code's control — don't optimize away repeated reads of it." Without
`volatile`, an aggressive compiler might notice you're reading
`STATUS_REGISTER` in a loop without ever writing to it *in this code*,
conclude "this never changes," and optimize the loop into an infinite
one (or read it only once) — completely breaking your program's ability
to actually wait for hardware. This is one of the most important
embedded-specific keywords, and one that genuinely doesn't come up in
normal desktop programming.

## Memory-mapped I/O, conceptually

On many microcontrollers, hardware peripherals are controlled by writing
to specific, fixed memory addresses — this is called memory-mapped I/O.

```c
#define LED_PIN_REGISTER (*(volatile unsigned char *)0x50000010)

void led_on(void) {
    LED_PIN_REGISTER = 0x01;
}

void led_off(void) {
    LED_PIN_REGISTER = 0x00;
}
```

Writing `0x01` to that specific address isn't "just setting a variable" —
on real hardware, it's physically toggling voltage on a pin connected to
an LED. Chapter 38 goes much deeper into representing this kind of thing
properly with structs and registers.

## No standard library (or a much smaller one)

Functions like `printf`, `malloc`, and `fopen` typically depend on an
underlying OS to actually do their work (writing to a terminal, managing
a heap, talking to a filesystem). On bare-metal embedded targets, these
either don't exist at all, or exist as much smaller, custom
implementations tailored to that specific hardware (a `printf` that
writes over a UART serial connection instead of a terminal, for example).

## Why this matters for your direction specifically

Drones and IoT devices are, at their core, embedded systems — a flight
controller reading sensor data and driving motors, or a smart device
reading a sensor and reporting over Wi-Fi, is running code with exactly
these constraints: no OS (or a minimal real-time OS at most), tight
memory, and direct hardware register access. Everything from here through
chapter 39 is building the specific foundation that kind of work actually
requires — this isn't a tangent from web/security work, it's the next
layer down.

## Try it yourself

- Look up the datasheet for a common, beginner-friendly microcontroller
  (the ATmega328P used in classic Arduino boards is a good, well-
  documented starting point) and find its GPIO register addresses — you
  don't need actual hardware yet, just get comfortable reading a
  datasheet's register map.
- Write a `volatile`-based busy-wait loop simulating waiting for a
  hardware flag (you can fake the "hardware" with a global variable set
  by a signal handler, just to see the compiler-optimization difference
  `volatile` makes without needing real hardware).
- Compile the same small function with and without `-O2`, with and
  without `volatile` on a variable it repeatedly reads, and compare the
  generated assembly (chapter 34) to see the optimization difference
  directly.

---
**Next:** [37. Bit Manipulation →](../37-bit-manipulation/README.md)
