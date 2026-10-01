# Beginner Projects

**Home:** [README](../../README.md)

Build these using chapters **00 through 15** — variables, loops,
functions, arrays, and strings. No pointers required yet. The goal here
is fluency with the basics, not cleverness.

## 1. Simple Calculator
Take two numbers and an operator from the user, print the result.
Extend it: reject division by zero with a proper message instead of
crashing.

**Practices:** `switch`, input validation, `scanf`/`fgets`.

## 2. Number Guessing Game
Pick a random number (look up `rand()` and `srand()` for this one — you
haven't formally covered them, which is intentional; reading a small
piece of unfamiliar documentation and figuring out how to use it is a
real skill). Let the user guess, telling them "too high" / "too low"
each time, and count how many guesses it took.

**Practices:** loops, conditions, basic library research.

## 3. Temperature Converter
Convert between Celsius, Fahrenheit, and Kelvin. Use a menu (`switch`)
to pick the conversion direction.

**Practices:** functions, `switch`, floating-point math.

## 4. Grade Calculator
Take a list of scores (fixed-size array), compute the average, and
assign a letter grade. Print each score alongside whether it's above or
below the average.

**Practices:** arrays, loops, functions.

## 5. Palindrome Checker
Check whether a word or short phrase reads the same forwards and
backwards. Handle spaces and capitalization sensibly (decide for
yourself what "sensibly" means, and be ready to explain your choice).

**Practices:** strings, loops, `string.h` functions.

## 6. Simple Text-Based Tic-Tac-Toe
Two players, one 3x3 grid (a 2D array), alternating turns, detect a
win or a draw.

**Practices:** 2D arrays, nested loops, functions, game-state logic.

---
Once these feel comfortable, move on to [`intermediate/`](../intermediate/README.md).
