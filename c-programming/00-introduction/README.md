# 00. Introduction

**Previous:** — | **Home:** [README](../README.md) | **Next:** [01. Setup →](../01-setup/README.md)

## Why C, in 2026?

Reasonable question. You've got Python for scripting, JavaScript for the web,
Rust for "safe C." So why bother with a language from 1972?

Because almost everything else is *built on it*, or built the way it is
*because of it*. The Linux kernel is C. Most device drivers are C. The
Python interpreter you'd use instead of C — written in C. Most microcontroller
firmware, routers, the software running inside your phone's modem, industrial
control systems, exploit toolkits, and probably the drone flight controller
you'll eventually want to poke at — C, or C with an accent.

Higher-level languages give you a lot for free: garbage collection, bounds
checking, safe strings. That's great for productivity. It's also exactly why
they hide what's actually happening in memory. If you want to understand how
software actually breaks — buffer overflows, use-after-free bugs, why a
"random" memory corruption crash happens — you need a language that doesn't
protect you from yourself. That language is C.

## What you'll actually get out of this

By the end of chapter 35, you'll be able to read and write real C: manage
memory manually, understand how a program is laid out when it runs, debug a
segfault instead of just staring at it, and recognize the classic bug
patterns that show up in CVEs to this day. Chapters 36–39 take that
foundation into embedded/IoT territory — where C isn't just an option, it's
usually the *only* option.

## A little history, because it explains a lot of C's design

C was created by **Dennis Ritchie** at Bell Labs in the early 1970s, mainly
to rewrite Unix in something more portable than assembly. That origin story
matters: C was built by systems programmers, for systems programming. It
gives you direct access to memory and hardware, and it trusts you not to
mess it up. It will not stop you from writing `array[1000]` on a 10-element
array. It assumes you know what you're doing. Sometimes you won't — and
that's fine, that's how you learn where the edges are.

## What C is used for today

- Operating system kernels (Linux, parts of Windows/macOS)
- Embedded systems and firmware (microcontrollers, IoT devices, drones)
- Device drivers
- Databases (SQLite, parts of PostgreSQL/MySQL)
- Performance-critical libraries that other languages call into
- Security tooling and research — a huge amount of exploit development and
  vulnerability research assumes you can read and write C comfortably

## How this repo is structured

Each numbered folder is one topic, in the order you should learn it. Every
`README.md` has explanations, real compilable code, common mistakes people
actually make, and a couple of things to try yourself. Don't skip the "try
it yourself" bits — reading about pointers and actually debugging a pointer
bug are very different experiences.

Next up: getting a compiler installed so you can actually run any of this.

---
**Next:** [01. Setup →](../01-setup/README.md)
