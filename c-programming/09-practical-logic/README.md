# 09. Practical Logic

**Previous:** [← 08. Loops](../08-loops/README.md) | **Home:** [README](../README.md) | **Next:** [10. Functions →](../10-functions/README.md)

You've now got variables, operators, conditions, and loops — which is
actually enough to solve a surprising number of real problems. This chapter
is just practice: taking those building blocks and combining them without
any new syntax getting introduced. If pointers are the "hard" part of C
later on, this kind of problem-decomposition is the hard part right now,
and it's worth taking seriously.

## Worked example 1: Is it prime?

```c
#include <stdio.h>

int main(void) {
    int n = 29;
    int is_prime = 1;   // assume true until proven otherwise

    if (n < 2) {
        is_prime = 0;
    }

    for (int i = 2; i * i <= n; i++) {
        if (n % i == 0) {
            is_prime = 0;
            break;   // no point checking further, we already know
        }
    }

    if (is_prime) {
        printf("%d is prime\n", n);
    } else {
        printf("%d is not prime\n", n);
    }

    return 0;
}
```

Notice the loop only checks up to `i * i <= n`, not all the way to `n`.
If `n` has a factor larger than its square root, it must also have a
matching factor smaller than its square root — so checking beyond that
point is wasted work. Small optimization, but it's the kind of thinking
that matters once your programs do real amounts of computation.

## Worked example 2: Reversing digits

```c
#include <stdio.h>

int main(void) {
    int number = 12345;
    int reversed = 0;

    while (number != 0) {
        int digit = number % 10;      // peel off the last digit
        reversed = reversed * 10 + digit;
        number = number / 10;         // drop the last digit
    }

    printf("Reversed: %d\n", reversed);
    return 0;
}
```

This pattern — `% 10` to grab the last digit, `/ 10` to remove it — shows
up constantly in beginner problems. It's worth understanding deeply rather
than memorizing, because you'll reconstruct it from scratch in slightly
different forms for the rest of your programming life.

## Worked example 3: A simple pattern

```c
#include <stdio.h>

int main(void) {
    int rows = 5;

    for (int i = 1; i <= rows; i++) {
        for (int j = 0; j < i; j++) {
            printf("*");
        }
        printf("\n");
    }

    return 0;
}
```

Output:
```
*
**
***
****
*****
```

Pattern-printing problems like this are less about the patterns themselves
and more about training you to think in terms of "outer loop controls
rows, inner loop controls what happens within a row" — a mental model
you'll reuse constantly, including well outside of printing triangles
(image processing, grid-based games, matrix math).

## How to approach problems like these on your own

1. **Work out the logic on paper first**, with actual numbers, before
   writing any code. If you can't do it by hand for a small example, you
   won't be able to code it either.
2. **Identify what needs to repeat** (that's your loop) and **what needs to
   change each time** (that's your loop variable or accumulator).
3. **Write the simplest possible version first**, get it compiling and
   producing *some* output, then refine it. Don't try to write the perfect
   version in one pass.

## Try it yourself

- Compute the sum of digits of a number (similar structure to the reversal
  example above).
- Print a right-aligned triangle (spaces before the stars, so it lines up
  against the right edge).
- Write a simple calculator: ask for two numbers and an operator
  (`+`, `-`, `*`, `/`) using `switch`, and print the result.

---
**Next:** [10. Functions →](../10-functions/README.md)
