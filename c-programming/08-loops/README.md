# 08. Loops

**Previous:** [← 07. Switch](../07-switch/README.md) | **Home:** [README](../README.md) | **Next:** [09. Practical Logic →](../09-practical-logic/README.md)

## for loops

```c
for (int i = 0; i < 5; i++) {
    printf("%d\n", i);
}
```

Three parts, separated by semicolons:
1. **Initialization** (`int i = 0`) — runs once, before the loop starts
2. **Condition** (`i < 5`) — checked before every iteration; loop stops
   when this becomes false
3. **Update** (`i++`) — runs after every iteration

`for` is the go-to loop when you know roughly how many times you need to
repeat something (iterating an array, counting up/down, etc).

## while loops

```c
int count = 0;

while (count < 5) {
    printf("%d\n", count);
    count++;
}
```

Same behavior as the `for` loop above, just structured differently. Use
`while` when the number of iterations isn't known ahead of time — like
reading input until the user types a sentinel value.

```c
int number;
printf("Enter numbers (0 to stop): ");
scanf("%d", &number);

while (number != 0) {
    printf("You entered: %d\n", number);
    scanf("%d", &number);
}
```

## do-while loops

```c
int number;

do {
    printf("Enter a positive number: ");
    scanf("%d", &number);
} while (number <= 0);
```

The difference from `while`: the body runs **at least once**, before the
condition is ever checked. Use this specifically when you need something
to happen before you can even evaluate whether to repeat — like prompting
for input the first time.

## break and continue

```c
for (int i = 0; i < 10; i++) {
    if (i == 5) {
        break;      // exits the loop entirely
    }
    printf("%d\n", i);
}
```

```c
for (int i = 0; i < 10; i++) {
    if (i % 2 == 0) {
        continue;   // skips the rest of this iteration, goes to the next one
    }
    printf("%d\n", i);   // only prints odd numbers
}
```

`break` exits the loop completely. `continue` skips just the current
iteration and moves on to the next one. Both are useful, but overusing
them (especially `break` scattered across many conditions) can make a
loop's exit logic hard to follow — sometimes a cleaner condition in the
loop header is the better fix.

## Infinite loops — intentional and accidental

Intentional (very common in real programs, like servers or embedded main
loops — you'll see this again in chapter 36):

```c
while (1) {
    // runs forever, until something inside calls break or exits
}
```

Accidental (a genuine, extremely common bug):

```c
int i = 0;
while (i < 5) {
    printf("%d\n", i);
    // forgot i++  — this never terminates
}
```

If your program hangs and you have no idea why, "did I forget to update my
loop variable?" should be one of the first things you check.

## Nested loops

```c
for (int row = 0; row < 3; row++) {
    for (int col = 0; col < 3; col++) {
        printf("(%d,%d) ", row, col);
    }
    printf("\n");
}
```

`break` and `continue` only affect the *innermost* loop they're written in
— there's no built-in way to break out of multiple nested loops at once in
C (some people use a `goto` for this specific case, which is one of the
few situations where `goto` is genuinely considered acceptable style).

## Try it yourself

- Print the numbers 1 to 100, but replace multiples of 3 with "Fizz" and
  multiples of 5 with "Buzz" (yes, this is FizzBuzz — everyone writes it
  eventually, might as well be now).
- Write a `do-while` loop that keeps asking for a password until the user
  gets it right.
- Print a multiplication table (1-10) using nested `for` loops.

---
**Next:** [09. Practical Logic →](../09-practical-logic/README.md)
