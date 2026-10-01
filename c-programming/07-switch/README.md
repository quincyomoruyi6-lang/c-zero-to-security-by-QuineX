# 07. Switch

**Previous:** [← 06. Conditions](../06-conditions/README.md) | **Home:** [README](../README.md) | **Next:** [08. Loops →](../08-loops/README.md)

## The basic structure

```c
int day = 3;

switch (day) {
    case 1:
        printf("Monday\n");
        break;
    case 2:
        printf("Tuesday\n");
        break;
    case 3:
        printf("Wednesday\n");
        break;
    default:
        printf("Unknown day\n");
        break;
}
```

`switch` compares a single value against a list of possible matches. It's
often cleaner than a long `if/else if` chain when you're checking one
variable against many exact values.

## Fallthrough — a feature, not a bug (usually)

```c
int day = 6;

switch (day) {
    case 6:
    case 7:
        printf("Weekend\n");
        break;
    default:
        printf("Weekday\n");
        break;
}
```

Without a `break`, execution "falls through" into the next case. Here,
`case 6` has no `break`, so it falls into `case 7`'s code — both 6 and 7
print "Weekend." This is intentional and a genuinely useful pattern for
grouping multiple values under one action.

The catch: **forgetting a `break` when you didn't mean to** is a very
common bug. If you leave it off by accident, execution keeps running
through every case below it until it hits a `break` or the end of the
switch. Always ask yourself, for every `case`, "do I actually want this to
fall through?" If not, put the `break` there.

## default

`default` catches any value that didn't match a listed `case`. It's not
strictly required, but leaving it out means unexpected inputs get silently
ignored — usually not what you want. Include it, even if it just prints an
error or does nothing explicit.

## switch vs if-else — when to use which

Use `switch` when:
- You're comparing one variable against several exact, known values
  (especially integers or characters)
- You want grouped cases (like the weekend example)

Use `if/else` when:
- You need range checks (`score >= 90`) — `switch` can't do ranges
- Your conditions involve multiple different variables
- You need `&&` / `||` logic

## A practical example: a simple menu

```c
#include <stdio.h>

int main(void) {
    int choice;

    printf("1. Add\n2. Subtract\n3. Exit\nChoice: ");
    scanf("%d", &choice);

    switch (choice) {
        case 1:
            printf("You chose Add\n");
            break;
        case 2:
            printf("You chose Subtract\n");
            break;
        case 3:
            printf("Goodbye\n");
            break;
        default:
            printf("Invalid choice\n");
            break;
    }

    return 0;
}
```

## Try it yourself

- Build a switch statement that converts a numeric grade (1-5) into a
  letter grade, with a sensible `default`.
- Deliberately remove one `break` in the middle of a multi-case switch and
  observe the fallthrough behavior — predict the output before you run it.
- Rewrite the weekend example using `if/else` instead, and decide for
  yourself which version reads more clearly.

---
**Next:** [08. Loops →](../08-loops/README.md)
