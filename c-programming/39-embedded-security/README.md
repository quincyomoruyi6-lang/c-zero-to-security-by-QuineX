# 39. Embedded Security

**Previous:** [← 38. Hardware-Oriented C](../38-hardware-oriented-c/README.md) | **Home:** [README](../README.md)

The last chapter, and in a lot of ways the one that ties everything else
in this repo together: IoT and embedded devices sit at the intersection
of low-level C programming and real security consequences — a
vulnerability here isn't abstract, it can mean an attacker physically
controlling a device, intercepting a camera feed, or hijacking a drone
mid-flight. This is exactly the territory where general-purpose C
knowledge and security thinking meet.

## Why embedded/IoT security is its own hard problem

Everything from chapters 32, 23, and 35 (buffer overflows, memory bugs,
classic vulnerable patterns) applies here at full force — embedded C is
still C, with the exact same sharp edges. But it's compounded by extra
factors:

- **Limited resources mean fewer protections.** Stack canaries, ASLR,
  and other modern mitigations from chapter 32 aren't always available
  (or fully enabled) on constrained microcontrollers — the CPU or
  toolchain may simply not support them, or a team disables them to save
  the (real) performance and memory cost.
- **Devices run for a long time, often unattended,** and frequently never
  get security updates after they ship — a vulnerability discovered years
  later may never actually get patched on devices already in the field.
- **Physical access changes the threat model.** An attacker who can hold
  the device can attempt things impossible remotely — reading flash
  memory directly, probing debug interfaces, glitching power to bypass
  checks.

## Hardcoded credentials

```c
#define ADMIN_PASSWORD "admin123"   // BUG: compiled directly into the firmware

int check_login(const char *input) {
    return strcmp(input, ADMIN_PASSWORD) == 0;
}
```

**The problem:** anyone who extracts the firmware (often trivial — many
devices don't encrypt or sign it) can find this string with a basic
search, no reverse engineering skill required. Hardcoded credentials are
one of the single most common real IoT vulnerabilities and a recurring
theme in security advisories for consumer routers, cameras, and smart
home devices.

**Better approaches:** unique per-device credentials set at
manufacturing time, credentials the user must set on first boot, or
proper cryptographic authentication rather than a fixed shared secret at
all.

## Unverified firmware updates

```c
void apply_update(uint8_t *update_data, size_t len) {
    write_to_flash(update_data, len);   // BUG: no signature check at all
}
```

**The problem:** if a device accepts any firmware image handed to it,
with no cryptographic verification that it actually came from the
legitimate vendor, an attacker who can get data onto the device (over
the network, over a debug port, sometimes even physically) can install
completely arbitrary code with full control over the hardware.

**Better approach:** verify a cryptographic signature on any update
image before flashing it, using a public key baked into the device at
manufacturing time — reject anything that doesn't verify.

## Unencrypted communication

A drone or IoT sensor transmitting telemetry (location, camera feed,
sensor readings, or command-and-control data) in plaintext means anyone
within radio range can passively intercept it — or worse, actively
inject spoofed commands if there's no authentication on the receiving
end either. This is a genuinely common finding in real IoT security
assessments: encryption gets skipped for cost, complexity, or
performance reasons on constrained hardware, and the consequences show up
later.

## Debug interfaces left enabled

Hardware debug ports — UART serial consoles, JTAG, SWD — are
enormously useful during development, letting you flash firmware and
inspect running state directly. Left enabled and accessible on a shipped
production device, they frequently provide a direct path to a root
shell or full memory dump, bypassing every software-level protection
entirely. A huge number of real embedded device compromises start with
someone physically finding an exposed UART header on a circuit board.

## A practical checklist for anything you build

- No hardcoded secrets — ever, not even "just for testing" (testing code
  has a way of shipping).
- Validate every input coming from outside the device, exactly as
  chapter 15 covers — network packets and sensor data deserve the same
  suspicion as keyboard input.
- Sign and verify firmware updates.
- Encrypt and authenticate any meaningful communication.
- Disable or physically remove debug interfaces before shipping, or at
  minimum gate them behind real authentication.
- Compile with every available protection from chapter 32 that your
  target platform actually supports — don't disable them for convenience.

## Where this connects back to everything else

This chapter is really the practical destination this whole repo has
been walking toward for anyone headed into IoT/embedded security
specifically: solid C fundamentals (0-15), a real grasp of memory (16-23),
comfort debugging and reasoning about low-level behavior (31-35), and now
the hardware-specific knowledge (36-38) to actually apply all of that to
real devices — with security as the lens tying it together, rather than
an afterthought bolted on at the end.

## Try it yourself

- Take the hardcoded-password example, and rewrite `check_login` to
  compare against a value read from a (simulated) secure storage location
  instead of a compiled-in constant.
- Sketch out, in comments or pseudocode, what a basic firmware update
  signature check would need to do, step by step, even without
  implementing real cryptography.
- Research one real, publicly disclosed IoT vulnerability (router, smart
  camera, or similar) and identify which of the categories above it falls
  into — this is a good habit to build generally: connect the abstract
  categories back to real, specific incidents.

---
**Home:** [README](../README.md)
