# 17. Pointers

**Previous:** [← 16. Memory](../16-memory/README.md) | **Home:** [README](../README.md) | **Next:** [18. Pointer Functions →](../18-pointer-functions/README.md)

This is the chapter everyone warns you about. Pointers have a reputation
for being "the hard part of C," and honestly, that reputation is a little
overblown — the core idea is simple. What takes real practice is building
the habit of tracking, at every point in your code, exactly what a pointer
is pointing at, and whether that's still valid.

## A pointer is just a variable that holds an address

```c
#include <stdio.h>

int main(void) {
    int x = 42;
    int *p = &x;   // p now holds the address of x

    printf("Value of x: %d\n", x);
    printf("Address of x: %p\n", (void *)&x);
    printf("Value of p (an address): %p\n", (void *)p);
    printf("Value AT the address p points to: %d\n", *p);

    return 0;
}
```

Two operators are doing all the work here:
- **`&`** ("address-of") — turns a variable into its address
- **`*`** ("dereference") — turns an address (a pointer) into the value
  stored there

`int *p` declares `p` as "a pointer to an int" — meaning it's a variable
that holds an address, and whatever's at that address should be treated
as an int. The `*` in the declaration and the `*` used to dereference
later look identical but mean different things depending on context — it
trips people up at first, and then stops mattering once you've written
enough of both.

## Modifying a value through a pointer

```c
int x = 42;
int *p = &x;

*p = 100;   // "go to the address p holds, and store 100 there"

printf("%d\n", x);   // 100 — x itself changed!
```

This is the whole point of pointers: they let you reach into and modify
memory indirectly, through an address, rather than only through a
variable's own name. This becomes essential the moment you need a
function to modify something that belongs to its caller — full treatment
in chapter 18.

## NULL pointers

```c
int *p = NULL;   // points at nothing, deliberately

if (p != NULL) {
    printf("%d\n", *p);
} else {
    printf("p doesn't point at anything valid\n");
}
```

`NULL` is a special value meaning "this pointer isn't pointing at any
valid memory." Dereferencing a `NULL` pointer (`*p` when `p` is `NULL`) is
undefined behavior — on most systems it crashes immediately with a
segmentation fault, which is actually the *good* outcome, since it fails
loudly instead of silently corrupting something. Always initialize
pointers to `NULL` when you don't yet have a valid address for them, and
check for `NULL` before dereferencing a pointer that *might* not point
anywhere valid (a function that might fail to find something, for
example).

## Pointer arithmetic — a first look

```c
int numbers[3] = {10, 20, 30};
int *p = numbers;   // arrays decay to a pointer to their first element

printf("%d\n", *p);         // 10
printf("%d\n", *(p + 1));   // 20
printf("%d\n", *(p + 2));   // 30
```

`p + 1` doesn't mean "add 1 byte" — it means "advance by 1 *element*,"
where the element size is determined by the pointer's type. Since `p` is
`int *`, `p + 1` moves forward by `sizeof(int)` bytes (4, typically), not
1 byte. This is exactly why declaring the correct pointer type matters —
the compiler needs to know how big a "step" means for that type. You'll
go much deeper on this in chapter 19.

## Common sources of confusion (and how to get past them)

- **`*` means different things in different contexts** — in a declaration
  (`int *p`), it says "p is a pointer." In an expression (`*p = 5`), it
  means "dereference p." Same symbol, different job, depending on where
  it appears.
- **A pointer and the thing it points to are two separate variables** — `p`
  itself has an address too (`&p`), distinct from the address it *holds*
  (which is `p`'s value) and the value at that address (`*p`). Three
  distinct things, easy to conflate at first.
- **An uninitialized pointer points somewhere random**, not nowhere. This
  is why declaring `int *p;` and immediately dereferencing it without
  assigning it a real address (or `NULL`) is a serious, common bug.

## Try it yourself

- Declare an `int`, a pointer to it, and print all three of: the int's
  value, the int's address, and the pointer's own address (`&p`). Make
  sure you can explain, out loud, what each number represents.
- Write a function-free program that swaps the values of two variables
  using only pointers and dereferencing (no separate swap function yet —
  that's chapter 18's job).
- Declare a pointer, deliberately don't initialize it, and (in a throwaway
  test file only) try dereferencing it. See what happens on your system —
  most likely a segmentation fault. That crash is your program correctly
  refusing to let you touch memory you never validated.

---
**Next:** [18. Pointer Functions →](../18-pointer-functions/README.md)
