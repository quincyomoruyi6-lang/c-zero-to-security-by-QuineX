# 24. Structures

**Previous:** [← 23. Memory Bugs](../23-memory-bugs/README.md) | **Home:** [README](../README.md) | **Next:** [25. Typedef →](../25-typedef/README.md)

## Grouping related data

Up to now, related pieces of data have lived in separate variables —
awkward once you have several values that logically belong together.
`struct` fixes this by letting you define your own composite type.

```c
#include <stdio.h>
#include <string.h>

struct Student {
    char name[50];
    int age;
    float gpa;
};

int main(void) {
    struct Student s1;
    strcpy(s1.name, "Quincy");
    s1.age = 18;
    s1.gpa = 4.5f;

    printf("%s, %d, %.1f\n", s1.name, s1.age, s1.gpa);
    return 0;
}
```

The `.` (dot) operator accesses a member of a struct. Once defined,
`struct Student` behaves like any other type — you can declare variables
of it, arrays of it, pass it to functions, and so on.

## Initializing at declaration

```c
struct Student s1 = {"Quincy", 18, 4.5f};

// or, more explicitly (and more robust if field order changes later):
struct Student s2 = {.name = "Quincy", .age = 18, .gpa = 4.5f};
```

The second form (designated initializers) is generally the safer habit —
it's explicit about which value goes where, so reordering the struct's
fields later doesn't silently scramble your initializer list.

## Structs containing structs

```c
struct Date {
    int day, month, year;
};

struct Student {
    char name[50];
    struct Date enrolled;
};

int main(void) {
    struct Student s1 = {"Quincy", {1, 9, 2024}};
    printf("Enrolled: %d/%d/%d\n",
           s1.enrolled.day, s1.enrolled.month, s1.enrolled.year);
    return 0;
}
```

Chained `.` access (`s1.enrolled.day`) works exactly as you'd expect —
walk through each level of nesting.

## Arrays of structs

```c
struct Student class[3] = {
    {"Alice", 19, 3.8f},
    {"Bob", 20, 3.2f},
    {"Charlie", 18, 4.0f}
};

for (int i = 0; i < 3; i++) {
    printf("%s: %.1f\n", class[i].name, class[i].gpa);
}
```

This is an extremely common real pattern — a fixed-size roster, a table
of records, a list of configuration entries.

## Passing structs to functions — value vs pointer

```c
void print_student_by_value(struct Student s) {
    printf("%s\n", s.name);
}

void print_student_by_pointer(const struct Student *s) {
    printf("%s\n", s->name);   // arrow operator, not dot!
}
```

Passing `struct Student` **by value** copies the *entire struct*,
member by member — fine for a small struct, wasteful (and slow) for a
large one. Passing a **pointer** to the struct avoids the copy entirely.

Note the **arrow operator (`->`)**: when you have a pointer to a struct,
`s->name` is shorthand for `(*s).name` — "dereference the pointer, then
access the member." You'll use `->` constantly once you start passing
structs by pointer, which, for anything beyond a small struct, is the
normal way real C code does it.

```c
struct Student s1 = {"Quincy", 18, 4.5f};
print_student_by_pointer(&s1);
```

## Modifying a struct through a pointer

```c
void have_birthday(struct Student *s) {
    s->age = s->age + 1;   // modifies the caller's actual struct
}
```

Exactly the pass-by-reference idea from chapter 18, now applied to
structs. This is the standard way to let a function meaningfully update a
struct that belongs to its caller.

## Try it yourself

- Define a `struct Point { int x, y; }` and write a function
  `double distance(struct Point a, struct Point b)` that computes the
  distance between two points.
- Build an array of 5 `struct Student` records, then write a function
  that takes the array and its size and returns the student with the
  highest GPA (return a pointer to the winning struct, or a copy — try
  both and think about the tradeoff).
- Write a function that takes a `struct Student *` and updates the GPA
  in place, and confirm the change is visible in `main` afterward.

---
**Next:** [25. Typedef →](../25-typedef/README.md)
