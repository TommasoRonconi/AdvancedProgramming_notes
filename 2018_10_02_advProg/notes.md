## Advanced Programming

### Naming conventions

For the source file:
```
.cc
.cpp
.CC
.cxx
```
For header files:
```
.h
.hpp
```
Compiler:
```
g++
clang++
```
Beware of IDEs! We hate those!

### How to write a program

Let's start with the shortest possible program (file `main.cc`):
```c++
int main () {}
```
I defined a function named `main` which takes no arguments (`()`), returns an integer (`int`, checked by the operative system, type `echo $?`) and does nothing (`{}`) besides returning a zero (`return 0;`, this part is implied)

REMEMBER THE 3 TYPES OF BUG?
- those that are found by the compiler (easiest);
- those found at execution time;
- those that are because you are a stupid programmer (we hate those).

Oh oh oh oh, remember to end each command with `;`

#### Compile this motherfucker!
```
g++ main.cc
```
now file `a.out` have been created, this is your executable.
Want to control the name?
```
g++ main.cc -o main.x
```
your executable's name is now `main.x`.

Run it with
```
./main.x
```
### Time for the *hello world*
```c++
#include <iostream>

int main() {
  std::cout << "Hello, World!\n";
}
```
What happens inside the compiler you should remember:
- the `src` file is **pre-processed**, it interprets all the commands beginning with an hash `#`
- then it is fed to the actual **compiler** which produces a `.o` file (an object file)
  several source file produce several object files
- the object files are combined by the **linker** which produces the binary, aka the executable

### `c++` standards

```
c++98
c++03
c++11
c++14
c++17
c++20 #work in progress
```
select the standard with the flag
```
-std=c++XX
```
with `XX` the standard you want.

### other flags (useful for debug):
```
-Wall
-Wextra
-g
```

### Built in *types* in `c++`

check file `01_variables.cc`

2 different ways to initialize a variable:
```c++
int a = 7;
int b{5};
```
the second signature it the **universal and uniform initializing** (UUI) which always works, while the first one is not usable in particular sintaxes (e.g. in constructors), it is the pre-c++11 form.
the UUI also checks that you are not initializing stuff wrongly:
```c++
int a{7.9};
```
will be recognized as an incorrect statement.

**variables are stored in multiples of bytes**
The quantity of bytes is *implementation-dependent*, use to discover `sizeof(int)`.
There are particular types that fix the number of bytes: `int16_t` is an integer stored in 16 bytes.
Note that the max integer representable with an `int` depends on the number of bytes in which an `int` is stored.
*N.B. in python integers have no-limits: there is no limit in the number of bits an integer can occupy.*

#### cast in `c++`:
```c++
int(var)
```
(while in `c` it was `(int)var`)

#### didn't know this about `bool`s:
```c++
bool test;
test = a == b;
test = a < b;
test = a > b;
test = a <= b;
test = a >= b;
test = a != b;
```

YOU CAN BOTH initialized variable at construction (use UUI)
```c++
bool test{true};
```
BUT, if you want to (re)initialize it afterwards you cannot do this: `test{false}`, you just have to use the classic:
```c++
test = false;
```

#### constants
the `const` command is an instruction to the compiler to tell you that you are trying to do something you do not want:
```c++
const int cc{7};
```
`c++11` introduces also another expression:
```c++
constexpr double ce{cc * 8.1234 / M_PI};
```
these are operation that are done at **compile-time** instead of at execution time.

(**note that**: `string`s are declared in header `<string>` while mathematical constants, like Pi, are in `<math>`)

`constexpr` must be done with `const` variables/expressions, in example up, `cc` has been defined as `const`, `M_PI` is a `constexpr` of `math` and all the numbers (here `8.1234`) are always read at compile-time, ergo, they are `const`.

This is also interesting:
```c++
// cast to void to suppress warning of unused variable
(void)ve;
```

### Input
(file `02_hello_name.cc`)

I can use
```c++
std::cin
```
to read from the `stdin` something:
```c++
std::string name;
unsigned int age;
std::cin >> name >> age;
std::cout << "Hello, " << name << " [" << age << "]\n";
```
You will have to type
```
$ tommaso 29 [ctrl+d]
```
and it will output
```
$ Hello, tommaso [29]
```
and close.
You can use
```c++
std::cerr
```
to redirect the output to the `stder` instead of to the `stdin`.


### Computers are super-fast morrons
(file `03_loops.cc`)

#### for loops

you can also do this:
```c++
for (int i{0}; i<10; ++i) {...}
```
and this:
```c++
int j{0};
for (int i{0}; i<10; ++i, ++j) {...}
```

### functions
**declaration** + **definition** together:
```c++
void new_line() {
  std::cout << std::endl;
}

int main () {
  ...
  return 0;
}
```

or **declaration** first, then **definition**:
```c++
// declaration
void new_line ();

int main () {
  ...
  return 0;
}

// definition
void new_line() {
  std::cout << std::endl;
}
```

**NOTE**
I can also declare variables by their default initialization:
```c++
int i{};
```
for `int`, `double`, `float`, the default value is `0`, for classes, you should declare a default initializer.

**SUPER-IMPORTANT**
NEVER EVER EVER compare floating point numbers with the `==`: better ask
```c++
fabs < pow(10, -16)
```
or minor than some particularly small number.

*(next time: arrays, and pointers, and references and a lot of fun!)*

