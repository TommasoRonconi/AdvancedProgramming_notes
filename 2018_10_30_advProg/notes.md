# Advanced programming - 2018/10/30

Today **ERROR HANDLING**

[Base directory](/home/tomi/MHPC/P1.3_seed/lectures/c++/05_error_handling)

Tests before the begin of function: **PRECONDITIONS**
Tests after the function: **POSTCONDITION**

How can a function tell if its return argument is correct?

Let's start here [00_errno.cc](/home/tomi/MHPC/P1.3_seed/lectures/c++/05_error_handling/00_errno.cc)

There is a variable called `errno` which outputs the error code if some error happened.
It returns zero if everything went fine.

## Exceptions

[01_exceptions.cc](/home/tomi/MHPC/P1.3_seed/lectures/c++/05_error_handling/01_exceptions.cc)
This file is a more sofisticated way to implement a square root, it uses the following construct:
```c++
try {}
catch () {}    // within {} the catch-close:
               // what will happen when the exception within () is caught?
catch (...) {} // within () the ... mean 'every other possible case'
```
When I detect an exception I throw an `exception`
```c++
  // test the pre-conditions
  if (d < 0)
    throw Negative_number{};
  if (d > 50)
    throw Bigger_than_expected{};
```
which tells me which exception depending on which condition I set.
Looking in the file should clarify things a bit.
The two structures
```c++
struct Negative_number {};

struct Bigger_than_expected {};
```
are just used to define two types, the types of the exceptions that could happen.

When the exception thrown is catched by the program, it will execute what's inside the specific `catch`-close.

[02_exceptions.cc](/home/tomi/MHPC/P1.3_seed/lectures/c++/05_error_handling/02_exceptions.cc)

Here we have a custom type which contains a `struct`, in which the custom type used to throw the exception contains *a message*:
```c++
struct Square_root_invalid {
  std::string message;
  Square_root_invalid(const std::string& s) : message{s} {}
};
```
And it is usefull because you can use this:
```c++
catch (const Square_root_invalid& s) {
    std::cerr << s.message << std::endl;
    return 1;
  }
```
where the exception has been thrown as
```c++
throw Square_root_invalid{"Cannot handle negative numbers. You gave me " + std::to_string(d)};
```
So now I have a general custom type to throw exceptions, changing just the custom message.

[03_error.cc](/home/tomi/MHPC/P1.3_seed/lectures/c++/05_error_handling/03_error.cc) to see a different approach, in which there is the evample of an implementation using the header [ap_error.h](/home/tomi/MHPC/P1.3_seed/lectures/c++/05_error_handling/ap_error.h).

Running the executable `03_error.x`:

```bash
please insert a number
$ -9

---------------------------------------------------------------------
A runtime exception has been thrown

       file: 03_error.cc
   function: double square_root(double)
       line: 39

In our library the argument must be positive and less or equal than 50.
```

**BUT**
*Does error checking affect the performance??* **YES**

What's the compromise?

We have of course to distinguish
- checks on user input
- checks on programmer's implementation

The first is of course unavoidable and should be checked with `if` statements.
The second is dealt with `assert()` which is not considered by the compiler if we use the flag `-DNDEBUG` at compilation time.

## Asserts

[04_assert.cc](/home/tomi/MHPC/P1.3_seed/lectures/c++/05_error_handling/04_assert.cc)
This comes from `C`. Included with the header `assert.h`, they do not throw exceptions because `C` has no exceptions.
*Assertions are never enough*
The advantage is that with asserts conditions are checked at compile time, while exceptions are checked at run time.

Can I combine assertions and exceptions? Yes, but it is a little bit messy (should check in [ap_error.h](/home/tomi/MHPC/P1.3_seed/lectures/c++/05_error_handling/ap_error.h)).

What if I am using RAII (resource acquisition is initialization)? How can I check if the acquisition has gone correctly?

## Stack unwinding

[05_stack_unwinding.cc](/home/tomi/MHPC/P1.3_seed/lectures/c++/05_error_handling/05_stack_unwinding.cc)

Class `Vector` implements the RAII approach, `elem` is allocated in the constructor and deleted in the denstructor.

**a constructor is called only by functions who's construction went fine**

If I `throw` the same exception I caught, I don't need to specify the name:
```c++
} catch (...) {
  delete[] ptr;  // <----                                         
  throw;
}
```
- Constructors **can** throw exceptions
- Denstructors **cannot** but the compiler won't complain, it will be *undefined behaviour*

## Smart pointers

[06_smart_pointers.cc](/home/tomi/MHPC/P1.3_seed/lectures/c++/05_error_handling/06_smart_pointers.cc)

### `unique_ptr`

this pointer constructor knows its owner and knows it has to be released when the owner does not need it anymore.

It is important not to construct smart pointers in ... IN WHAT??? I MISSED IT!!

Anyways, even in an **high performance computing** environment, it is **ALWAYS BETTER TO USE SMART POINTERS**, it is super safer!