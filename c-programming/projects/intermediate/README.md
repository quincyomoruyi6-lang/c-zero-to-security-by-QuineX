# Intermediate Projects

**Home:** [README](../../README.md)

These lean on **chapters 16 through 30** — pointers, dynamic memory,
structs, and file handling. This is where C starts to feel like the real
thing.

## 1. Custom Dynamic Array ("Vector")
Build a resizable array from scratch: a struct holding a `malloc`'d
buffer, a current size, and a current capacity, with functions to push a
new value (growing/`realloc`ing when full) and get a value by index with
bounds checking.

**Practices:** dynamic memory, structs, pointers to structs.

## 2. Singly Linked List
Implement `insert`, `delete`, `search`, and `print` for a basic linked
list of integers. Once it works, add a `free_list` function that
correctly frees every node — and verify there are zero leaks with
`valgrind`.

**Practices:** dynamic memory, structs, pointers, self-referencing
structs (chapter 25).

## 3. Simple Command-Line Note-Taking App
Append timestamped notes to a file, list all notes, and delete a note by
number. Persist everything between runs using real file I/O.

**Practices:** file handling, command-line arguments, string parsing.

## 4. A Tiny Shell
Read a command from the user, split it into arguments, and use `fork` +
`exec` to actually run it (research `execvp` for this — it's the natural
next step past what chapter 30 introduced). Support at least `cd` as a
built-in, since real shells can't just `fork`/`exec` a directory change.

**Practices:** command-line parsing, processes, string manipulation.

## 5. Custom String Library
Reimplement a handful of `string.h` functions from scratch — `strlen`,
`strcpy`, `strcmp`, `strcat` — matching their real behavior exactly,
including edge cases (empty strings, equal-length comparisons).

**Practices:** pointers, strings, careful attention to edge cases.

## 6. Student Record Manager
An array (or dynamic array) of `struct Student` records, loaded from and
saved to a file, with add/search/delete/sort operations through a simple
menu.

**Practices:** structs, file I/O, dynamic memory, sorting.

---
Once these feel comfortable, move on to [`security/`](../security/README.md)
or [`embedded/`](../embedded/README.md), depending on which direction
you're headed.
