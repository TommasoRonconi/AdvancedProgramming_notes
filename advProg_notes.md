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

# Advanced Programming (2018.10.04)

# Advanced Programming (2018/10/09)

Work directory: `P1.3_seed/lectures/c++/02_functions_and_arrays/`

## Functions

this function does not accept any argument:
```c++
ret_type function ();
```

while this accepts arguments:
```c++
ret_type function_with_args (const var1_type var1, const var2_type var2)
```

*please use `const` as much as possible*, because
- it tells the compiler to double check that I am not trying to change it
- it also allows the compiler to optimize the code (since it knows it is constant) 

while C does not, in C++ **overloading of functions is allowed** (see file `01_functions.cc`).

The problem with function overloading is that there are a lot of code repetitions, thus we prefer..

### Templates (`02_templates.cc`)

the syntax:
```c++
template < typename T > //here we introduce type T
	 T function_name (const T var1, const T var2);	
```
with type `T` we have an alias for whatever type we want to use.
This `T` survives until the end of the declaration (aka, in this case) until the semi-column.

When I want to implement that function (e.g. after the `main` or in another file) I have to repeat the definition of `T`:

```c++
template < typename T >
	 T function_name (const T var1, const T var2) {

	 // do stuff with var1 & var2

	 return some_var_of_type_T;
}
```
To call the function for a certain type (e.g. integer) use `function_name<int>(intvar1, intvar2)`.
If the return-type `T` can be deduced from the input variables you don't need to call it with `<type>`, you can still use that expression anyway, for casting the input variables on the fly into another type and thus call the corresponding version of the template (if you want to cast only the result, do it the classical way: `int(function_name<double>(double_var1, double_var2))`).

The compiler, when finds templates, it authomatically generates only the functions needed (if in the code you use the template for an `int` and a `double`, there will be only the 2 corresponding overloaded functions in the binary).

### Changing values of argument variables

(file: `03_wrong_args.cc`)
In this case it does not work because even though within function `swap` the position of the variables has been changed, it is not in its caller, because when I call a function I am creating a copy of that variable.

**Remember now: STACK vs HEAP**:
Variables are authomatically created in the stack.
When I enter the main a level of stack is reserved and the variables I define are created in that levelo of stack. Any called function leaves in its own level of stack, thus it can see only variables that leave in that level of stack (and global variables, that leave in another level of stack either, of course).
Therefore, when calling a function that accepts arguments, a copy of the arguments is created in the new level of stack.
THIS MEANS passing variables **BY VALUE** as opposed to passing variables **BY REFERENCE**
Now remember this stuff:
- **objects** are a portion of memory
- **variables** are named objects
- each variable as a **value**
- how to interpret that value is told by the **type**

Now open file `04_vars_pointers_refs.cc`.
In `c++` there are also
- pointers (unary operator: `var_type*`)
- references (unary operator: `var_type&`), differently to C, here this is a new type.
Still, we can use the ampersand symbol as in C to access the name of the position in memory of some variable:
`d` is generated in the stack, its position in memory can be identified by some number, this number is returned by the ampersand symbol: `&`.
Output of `04_vars_pointers_refs.x`:
```
content of	   d: 9.9
content of	 ref: 9.9
content of	 ptr: 0x7fff9a772b08

address of	   d: 0x7fff9a772b08
address of	 ref: 0x7fff9a772b08
address of	 ptr: 0x7fff9a772b10

ref = 7.7;
content of	   d: 7.7

*ptr = 5.5;
content of	   d: 5.5

content of	copy: 5.5

copy = 0;
content of	copy: 0
content of	   d: 5.5
```
A reference is an **alias**, it does not require another slot of memory.
Instead a pointer, which is a variable by itself, occupies a place in memory.
To access the content of the position in memory saved in a variable of type pointer, we have to **DE-REFERENCE** it:
```c++
*ptr = new_value;
```
with this I am changing the value inside position `ptr`.
```c++
std::cout << *ptr;
```
with this I am seeing the value inside position `ptr`.

**Now** open `05_swaps.cc`.
To comprehend better how pointers & references work.

- C-style swap uses pointers.
- C++ style swap uses references (because it can).

*you can think at references as to pointers that authomatically de-reference themselves*
Remember that if I am passing an integer that does not need to be changed, pass by value (either with references or pointers), since it occupies 4B instead of the 8B of pointers and references.

## Arrays

An array is a sequence of objects of the same type.

### Static

(file `06_static_arrays.cc`)
In `c++` the size of a static array (aka, an array defined in the stack) is something that has to be declared at compile time.
```c++
int ai[4];
float af[9];
```
To have an array with size defined at run time... we'll see in another lecture.
We start counting from 0!

**Warning!** static arrays do not have a range check => we can search for element `ai[111]`: **Undefined behaviour** (it could be `segmentation fault` but also some value, not necessarily something strange, it's accessing a position in memory 111 integers away from the beginning of the array).

**Warning!** `size_t` is aliased to `long long unsigned int`, remember when using a lot of them, if they are not needed to be that long, they would just occupy memory for no reason.

Other way of defining arrays:
```c++
double ad1{1., 2., 3.}; // an array of size 3 with defined content
double ad2[9]{1., 2., 3.}; // an array of size 9 with the first 3 elements defined, and the last 6
                           // elements equal to 0
double ad3[9]{}; // an array of size 9 with all the elements set to 0
```

An array decays to a pointer to its first element:
```c++
*ad1; //accesses value '1.'
```

### Dynamic

(file `07_dynamic_arrays.cc`)

To define the size of an array at run-time you have to use the keyword `new` (is kinda like `malloc` in C):
```c++
std::size_t n;
std::cin >> n;
int* da{new int[n]};
```
The only difference between `new` and `malloc` is that `new`, when calling it for non built-in types or classes, **it calls the constructor** of said non built-in type or class (we're gonna see it later).
The array `da` is defined in the *heap*, this implies we need to release its memory when we do not need it anymore:
```c++
delete[] da;
```
Either: **MEMORY LEAK!!**# Advanced Programming - 2018/10/16

Lecture root: `P1.3_seed/lectures/c++/`

Today we'll dealing with
- types, special types
- keywords
- matrices
- arguments
- `std::array`s and `std::vector`s

**note**: every compiler has a built-in variable `__cplusplus`, if it is greater than `201103L` it is possibly `c++14` or `c++17`

**note** this initialization here
```c++
new T[l]{};
```
returns an object allocated on the heap of type `T`, of size `l`, with all the element initialized to zero `{}`, it is the equivalent of `C`s `calloc`.
(While simply `new T[l]` is equivalent to `malloc`.

## auto

Remember templates?
```c++
template < class T >
 T * init (const std::size_t L) {
     return new T[L]
 }	 
```
that can be called from `main` as
```c++
double * ad { init<double> (10)};
```
why to repeat `double`?
The compiler already knows it, `auto` command is introduced from `c++11` (no curly braces in this case, we'll see later why)
```c++
auto aa = init<double>(10);
```
variable `aa` is of the type returned by `init` template function (see file `01_auto.cc`).
The type of variables defined `auto`matically, is interpreted by the compiler at compile type.
**note**: we can understand what type an `auto` variable is by forcing an error of the compiler, let's declare this:
```c++
template <class T>
void debug (T var);
```
without defining it.
So if I call
```c++
auto pb = something;
debug(pb);
```
the compiler will tell me that the function `debug`, accepting a variable of type of `pb`, is not defined.

**From `c++11`, when a variable type can be understood by it's initialization, we can use `auto`.**

## const & pointers

(refer to `02_const_and_pointers.cc`)
- `const int *pc = &a;` I cannot change the value contained in the pointer (pointer to const);
- `int* const cp = &a;` I cannot change the address of the pointer (const pointer), but I can dereference it and change its containt;
- `const int* const cpc = &a;` I cannot change the content I cannot change the address.

It is useful for example when I try to access the elements (e.g. for printing it).
Either, look at the reference file, function `print_sentinel`.

**remember** that the sentil is the element past the last one. I don't want neither to modify the memory location (or I won't find the end of the array anymore) nor the content (because I do not own its content) of this element.

Also **remember** that this things are equivalent:
```c++
a[i] == *(&a[0]+i) == *(a + i) == *(i + a) == i[a];
```

## Special pointers

(`03_special_pointers.cc`)

- `char** ppc;` pointer of pointer;
- `int* ap[7]` an array of pointers, and since an array decays to a pointer to its first element, `ap` is an `int**`;
- `void* pv{pi}` (is similar to `C`s way of defining templates), a void pointer cannot be dereferenced (e.g. `*pv`), nor I can operate on it (e.g. `++pv`), **BUT** I can *cast* it to whatever I want. There are several types of casts in `C++`, a `static_cast<int>` is a cast to an integer resolved at compile time.

An important **keyword**: `nullptr`, when I don't want to define it. PLEASE **do not use `NULL`**, its implementation depends on the compiler.

It is e.g. a good idea to use it to remove dangling pointers:
```c++
delete[] da; // release memory loc in heap -> dangling pointer
da = nullptr; // point to the first memory location available. (its address is zero)
```
Actually, `nullptr` is a reference, thus, it cannot be dereferenced.
If a variable always HAS a value -> use references (references are pointers that authomatically dereference themselves, so they HAVE to have a value).
For a pointer this is not necessary.
Therefore, since `nullptr = 0` thus
```c++
auto pt = nullptr;
if (pt) // this is false
```

- `int (*fp)(const char *);` is a pointer to a function `*fp` that returns an integer and accepts a `const char*` as an argument;

**NOTE** Ehy, there is a function in `C++` that returns the type of a variable, so I can define a variable as
```c++
decltype(some_var) some_other_var = func_that_returns_the_type_of_some_var();
```
what's the use of `decltype()`? we'll see this in a long while but trust alberto always (TAA).

## Arguments
(`04_command_line_arguments.cc`)
```c++
#include <iostream>

int main(int argc, char* argv[]) {
  for (auto i = 0; i < argc; ++i)
    std::cout << "argv[" << i << "] = " << argv[i] << std::endl;
  return 0;
}
```
while `argc` counts the number of arguments, `argv` is an array of `char*` which contains the arguments.

## Matrices

(`05_matrices.cc`)

```c++
int ma[6][5]; // matrix: 6 rows, 5 columns each
```
**REMEMBER** cache trashes: `C++` lists matrices row-wise.

### How do I pass a matrix to a function?

I can pass a pointer to the first element of the matrix, the number of lines and the number of columns.
```c++
void func (int* p, int row, int col);
< ... >
int* p = &ma[0][0];
func(p, 6, 5);
```
Ugly.

In HPC we prefer to allocating matrices, allocating an array of size N_row*N_columns:
```c++
int * d_ma = new int[6 * 5]{};
func(d_ma, 6, 5);
```

## Containers

### `std:array`

Need to include `<array>

they leave in the stack, they need to be allocated with known size.
The size has to be known at compile time.
```c++
std::array<int, 4> a{1, 2, 3, 4};
```

You can copy arrays
```c++
std::array<int, 4> b{a}; //compile-time
b = a; // run-time
```
- To access in a loop: `for (auto x : b)`
- to modify in a loop: `for (auto & x : a)`
- to control if some element exist (at run-time): function `at`: `b.at(90);` if element 90 does not exist the program crashes throwing an exception.

**Remember** that stack is faster than heap but way smaller:
- small things -> stack
- big things -> heap

## `std::vector`

if you do not want to specify the size at compile time use `std::vector`s:
Initialization:
```c++
std:vector<int> aa {1,2,4};
```
is a std initialized list with 3 elements: 1,2,4
```c++
std:vector<int> aa (4,4);
```
is a vector of 4 elements, each of that is equal to 4.

- **VECTORS ARE ALWAYS CONTIGUOUS IN MEMORY**
- **PUSH_BACK RE-ALLOCATES MEMORY, CONTIGUOUSLY**
- **EMPLACE_BACK ACCEPTS A LOT OF ARGUMENTS AND BUILDS OBJECTS THAN PUSHES THEM BACK.**
# Advanced Programming - 2018/10/23

Reference folder: `P1.3_seed/lectures/c++/04_custom_types`

*Language shapes the way we think, and determines what we can think about*
(B.L. Whorf)

A type in programming is just the implementation of a concept.


## Custom types

### `enum`: it is used for options/actions

(file `01_enum.cc`)
It adds readability to the choice-makeing, more easy to maintain and debug.
`enum` introduces a type:
```c++
enum color { red, yellow, green };
```
the type is named `color` and can assume 3 possible values.

We can use the `swithc-case` statement along with some defined `enum`.
This statement cannot use strings, vectors, floats **BUT ONLY** `int`, `bool` and `enum` variables.

Remember to put `break` or it will execute all the following actions even though it found a match.

**NB** you cannot assign an integer to an `enum`, moreover, why would you want to do that? avoid it, you could also assign a number which is not in the list.

(file `02_scoped_enum.cc`)
Since `c++11` we also have **scoped** `enum`s:
```c++
enum class color { red, yellow, green };
```
they can be referred to with `::` (is called **scope resolution operator**)
```c++
color::red
```
this allows you to give the same name to variables with an equivalent meaning but used in different cases: the color green could be referring to both a traffic light or a sign: we will refer to the namespace of the variable when calling it: `traffic_light::green` VS `sign::green`.

A difference with normal `enum`s is that they are not authomatically converted into integers.

But, `03_why_scoped.cc` enums?
try to run it with & without the commented line!
```bash
error: reference to 'dec' is ambiguous
```

### Namespaces

(file `04_namespace.cc`)

```c++
namespace choose_a_name {

  int variable;

  namespace nested {

    void hello1();
    void hello2();
    void hello3();

  }  // namespace nested
}  // namespace choose_a_name
```
The fully qualified name of `variable` is `choose_a_name::variable`, the full qualified name of the function `hello1` is instead `choose_a_name::nested::hello1()`.

If I want to define the function not where they are declared I have two choices:
- either I call them with their fully qualified name
```c++
void choose_a_name::nested::hello1() {
  std::cout << "hello1 from nested\n"
            << "variable is " << choose_a_name::variable << std::endl;
}
```
- or I go inside the namespace:
```c++
namespace choose_a_name {
  namespace nested {
    void hello2() {
      std::cout << "hello2 from nested\n"
                << "variable is " << variable << std::endl;
    }
  }  // namespace nested
}  // namespace choose_a_name
```
- **BONUS**: call `using namespace`
```c++
using namespace choose_a_name;

void nested::hello3() {
  std::cout << "hello3 from nested\n"
            << "variable is " << variable << std::endl;
}
```

Other cool thing is that I can populate my scope with just some functions of some namespace:
```c++
using namespace std::cout;
using namespace std::endl;
```
so now from wherever in the program I can call `cout` and `endl` without having to use everytime `std::` qualifier AND I avoid name-clashes with other functions omonimus to `std` function that I might have defined.

### Classes!

A `class` is in general a `struct` with a simple difference:
- members of a `struct` are by default public
- member variables of a `class` are by default private (i.e. cannot be accessed by outside the class, but functions can (if they are defined as `public`))
Eccept for this, in `C++`, they are **the same** thing.

Look at file `05_struct_class.cc`!!!!
Look at the differences between `class Point_c` and `struct Point_s` (which implement basically the same object).

Look at the `main`: when I define `Point_s ps;` `ps` **is an object, an object is an instantiation of a class**, in this case `Point_s`.

**Note** a difference between defining a function inside or outside a class definition are by-syntax inlined and not inlined, respsectively in c++.

You call a function of a `class` with a dot `.`:
```c++
ps.print();
```
**public functions of a class can access the private members of a class**

The possibility of defining `private` members of a class is given so that the *consistency* of the program is maintained: I am avoiding that the user does messes with the class members definition!
So I can use a function to define private variables that before setting it checks that the value I'm giving it is consistent.

Now let's have a look at

#### Constructos/destructors

(file `06_constructos_destructor.cc`)

are special functions, they do not have return type and have the same name of the class.
They can accept, as function, as many arguments as they want or none.
The Constructo with no arguments is called the default constructor, the destructor wants a `~` before the name and no arguments.

```c++
Foo f2();
```
might return an error because the compiler thinks you are trying to define a function that returns type `Foo` and wants no arguments, in general, it gives **unexpected behaviour**.
Note that the correct way of doing it is
```c++
Foo f1{};
```
Instead, both the following signatures are correct:
```c++
Foo f3 {arg1, arg2, arg3};
Foo f4 (arg1, arg2, arg3);
```
but is preferable to use the first one.

**The descructor is authomatically invoked when the object goes out of scope**

I can define the constructor and the destructor outside the class declaration:
```c++
Foo::Foo (arg_type1 arg1, arg_type2 arg2, arg_type3 arg3) :
	 _arg1{arg1},
	 _arg2{arg2}, //case1
	 _arg3{arg3} // all variables defined here are 'for constructor':
	 	     // I must use the same order
	 	     // I used to declare them in the class definition.

{

// variables defined here are for this scope, unless they are declared in the class (private variables of the class)
// the difference here is that private variables of the class have already been constructed, at most, I can change their value

   _arg2{arg2}; // case2

}
```
look at the file `06_constructor_destructor.cc` for further infos.
The difference between `case1` and `case2` is that in case 1 I construct variable `arg2` with the correct value, in case 2 (supposing I didn't call first case1) I first construct arg2 with the default constructor and then I change its value.
The case1 is **more performant**, we prefer this one.

#### Operator overloading

I can overload not only functions BUT ALSO operators:
```c++
std::ostream& operator<< (std::ostream& os, const Foo& f) {
	      os << f._i << " " << f._d << std::endl;
	      return os;
}
```
So that when from a program I call
```c++
Foo f;
std::cout << f;
```
it will authomatically print its components.
Nonetheless **operators are functions** that can be accessed more easily.
E.g. I can define a class `Point` and overload the operator `-` (minus) that authomatically computes the difference between two points.

Why the ampersand `&`? because
- in the first argument I need it because I need to modify the value I am passing it (so also a pointer would have done the job)
- in the type because I want to return the same object type (that I have changed)
- in the second argument I use it because class `Foo` could be huge so I am sure that the variable I'm passing is not *heavier* than it's address (in 64x machines, 8 bytes)

#### Template classes

(file `07_template_class.cc`)

*RAII = resource acquisition is initialization*
look at it for an implementation of something like `std::vector`, which is a good example.
Note that what I acquire in the constructor (memory allocation) is released in the destructor!

Note that this is one of the cases in which using the qualifier `const` is mandatory. (try commenting and uncommenting the lines suggested in the source file to see what happens forgetting the `const`.

*Try to understand why it does not compile*# Advanced programming - 2018/10/30

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

Anyways, even in an **high performance computing** environment, it is **ALWAYS BETTER TO USE SMART POINTERS**, it is super safer!# Advanced Programming - 2018/11/06

still on constructors and **built-in** types

## copycat

[01_surprise.cc](/home/tomi/MHPC/P1.3_seed/lectures/c++/06_copy_move_semantics/01_surprise.cc)

try to compile and run the code.
If the curly braces are not left empty, the default constructor simply does not define the content of the built-in type.
I can explicitly ask the compiler to generate the default-constructor:

## Default

[02_default.cc](/home/tomi/MHPC/P1.3_seed/lectures/c++/06_copy_move_semantics/02_default.cc)

[03_default.cc](/home/tomi/MHPC/P1.3_seed/lectures/c++/06_copy_move_semantics/03_default.cc)
a default copy constructor is copied by value, but since pointers are particular types of variables, their value is a pointer => **MUCH FASTER**

is a **shallow copy** done with the `move` command





[](/home/tomi/MHPC/P1.3_seed/lectures/c++/06_copy_move_semantics/)
[](/home/tomi/MHPC/P1.3_seed/lectures/c++/06_copy_move_semantics/)
[](/home/tomi/MHPC/P1.3_seed/lectures/c++/06_copy_move_semantics/)
[](/home/tomi/MHPC/P1.3_seed/lectures/c++/06_copy_move_semantics/)
[](/home/tomi/MHPC/P1.3_seed/lectures/c++/06_copy_move_semantics/)
[](/home/tomi/MHPC/P1.3_seed/lectures/c++/06_copy_move_semantics/)# Advanced Programming - 2018/11/13

[base dir](/home/tomi/MHPC/P1.3_seed/lectures/c++/07_inheritance)

## Inheritance

We want to build a class in the most general way possible, so that future expansions are possible.

[00_first_trial.cc](/home/tomi/MHPC/P1.3_seed/lectures/c++/07_inheritance/00_first_trial.cc) is the wrong way.

But why is this wrong?
it compiles and kind of works.

It is wrong from the conceptual view point.
As it is the dog has an animal in its private variables.
```c++
struct Dog {
  Animal _animal;
  void speak() const noexcept { std::cout << "Bau\n"; }
  void info() const noexcept { _animal.info(); }
  Dog() noexcept : _animal{} {}
  Dog(const unsigned int a, const double d) noexcept : _animal{a, d} {}
};
```
Actually we would like to implement this so that it clearly tells you that a dog **IS** an animal.
Furthermore, if many of the supposedly derived objects need a set of common functions, it is pointless to re-write the same functions again and again.

Now file [01_inheritance.cc](/home/tomi/MHPC/P1.3_seed/lectures/c++/07_inheritance/01_inheritance.cc).
Even in this case it is not the correct way.
```c++
struct Dog : public Animal {
  void speak() const noexcept { std::cout << "Bau\n"; }
  Dog() noexcept = default;
  Dog(const unsigned int a, const double d) : Animal{a, d} {}
};
```
(even inheritance can be `public` `private` and `protected`)
In this case `struct Dog` is inheriting from `animal`.
In this case `Animal` is the **Parent** (or **Base**) class and `Dog` is the **child** (or **derived**).
Dog inherits all the interface of animal, meaning all the public members and functions.

**a child class is always greater (or equal) in memory than its parent**
e.g. `Snake` has the public member `dangerous` added to all those inherited from `Animal`.
Note that the private members of animal are still owned also by snake and dog **BUT** they are not visible.

How do you construct a derived class?
```c++
  Snake(const unsigned int a, const double w, const bool b)
      : Animal{a, w}, dangerous{b} {}
```
first you have to call the constructor of the base class. **THEN** you can call the particular members of the derived.

A dog **IS** an animal, thus I should be able to call a function of animal thruough class dog.
This is called **POLIMORPHISM** (e.g. like templates, the functions implementation depends on what you are applying them, this is **COMPILED TIME (or STATIC) POLYMORPHISM**)
we instead want to implement **RUNTIME (Or DYNAMIC) POLYMORPHISM**. The nature of the functions can be only understood at run time.
In the last file opened we did not implement the class so that it has dynamic polymorphism.

Also in this case, the function compiles BUT the runtime polymorphism does not work
```c++
Animal* p = new Snake{1, 2.3, false};
std::cout << "through pointer\n";
p->info();
p->speak();
```
it called function `speak` of `Animal` not of `Snake`.


File [02_virtual.cc](/home/tomi/MHPC/P1.3_seed/lectures/c++/07_inheritance/02_virtual.cc)

**virtual** is the keyword to trigger the runtime polymorphism: functions that will be inherited must be declared as `virtual`.
```c++
  virtual void speak() const noexcept { std::cout << "Unknown\n"; }
```
**ALSO** the destructor should be declared `virtual`.
```c++
  virtual ~Animal() {}
```

This virtual thingy has been introduced since `c++11`, what happened before?
To declare that you were overwriting a function, the keyword virtual should be repeated in the derived class' function. But once a function is declared virtual it remains so.
What when you have multiple inheritances?
maybe a class rotweiler would like to inherit from dog, which inherits from animal.

Since `c++11` we introduce the **positional keyword `override`** which is the **LAST KEYWORD ALWAYS** when using keywords.
```c++
  void speak() const noexcept override { std::cout << "Bau\n"; }
```
This tells the compiler to
- check you are overriding an inherited function
- this is not overloading! its the same function but with a different implementation particular to this case.

To use a particular parent implementation of a derived function I should call it from its fully blablabla name: calling `Animal::speak` from class `Snake`

Often a base class implements just an INTERFACE, public members and functions that should be inherited one day, but the library can work even without derived classes!
```c++
struct Animal {
  unsigned int age;
  double weight;

  Animal(const unsigned int a, const double w) : age{a}, weight{w} {
    AP_ERROR(!(weight < 0)) << "invalid weight!\n";
  }

  Animal() : Animal{0, 0} {}  // delegating constructor

  virtual void speak() const noexcept { std::cout << "Unknown\n"; }
  virtual void info() const noexcept {
    std::cout << "age:\t" << age << '\n' << "weight:\t" << weight << '\n';
  }

  virtual ~Animal() {}
};
```

Let's open file [03_virtual_destructor.cc](/home/tomi/MHPC/P1.3_seed/lectures/c++/07_inheritance/03_virtual_destructor.cc):

This call here:
```c++
  { Derived d; }
```
is expected to call in the same line both the constructor AND the destructor, thus it should appear on stdin
```
$ Base
$ Derived
$ ~Derived
$ ~Base
```
and this happens for that call, but calling a pointer it only denstructs tha base class, not the derived, happens this:
```
$ Base
$ Derived
$ ~Base
```
If I instead declare the destructor of `base` as virtual, everything goes fine.
**TAKE HOME MESSAGE**: you have a virtual function? you must declare your destructor as virtual too.

Parents hide informations from their children .

Note also that, class `Animal` should be **abstract**
```c++
  Animal() : Animal{0, 0} {}  // delegating constructor
```
this tells you that you cannot instantiate the class without overriding (cannot declare `Animal a;`)

To make mark the class as abstract it is sufficient to declare one function of tha class as **poor virtual**:
```c++
  virtual void speak() const = 0;
```

### Aliases

sometimes it is usefull (since classes and their functions have really long and difficult names) you can use the keyword `using`:
```c++
using Anaconda = DangerousSnake;
```
or also
```c++
class Matrix { ... }
using vector = Matrix<1, i, T>;
```

so that calling `vector` will always be equivalent to call for `Matrix<1, i, T>`.

Also, if a child has the very same constructor than its parent, I can simply declare the new class with using:
```c++
class Dog : public Animal {
 public:
  void speak() const noexcept override { std::cout << "Bau\n"; }
  
  // instead of these 2 lines:
  // Dog() = default;
  // Dog(const unsigned int a, const double d) : Animal{a, d} {}
  
  // use only this:
  using Animal::Animal;	 
};
```

(**there are 4 chapters in our reference book about inheritance**)

### Dynamic casts

file [05_dynamic_cast.cc](/home/tomi/MHPC/P1.3_seed/lectures/c++/07_inheritance/05_dynamic_cast.cc)

It's a cast that is performed at **runtime**!
- `static_cast` is done at compile time
- `dynamic_cast` is done at run time

It is used to determine the point in hierarchy.

**A smart combination of object composition and inheritance drives you to an optimal design pattern.**
Some helpful design patterns exists (like iterators).

NOW look at the image: [iostream_hierarchy.png](/home/tomi/MHPC/P1.3_seed/lectures/c++/07_inheritance/iostream_hierarchy.png)(arrows point to the parent class).
`std::cout` and `std::cin` are objects of type `ostream` and `istream` respectively.

Also interesting: [exceptions_hierarchy.png](/home/tomi/MHPC/P1.3_seed/lectures/c++/07_inheritance/exceptions_hierarchy.png).

## How to organize files

[example directory](/home/tomi/MHPC/P1.3_seed/lectures/c++/07_inheritance/organized)

type tree:
```
.
├── Doxygen
│   └── doxy.in
├── include
│   ├── animal.h
│   ├── dog.h
│   ├── helper_functions.h
│   └── snake.h
├── main.cc
├── Makefile
└── src
    ├── animal.cc
    ├── dog.cc
    ├── helper_functions.cc
    └── snake.cc

3 directories, 11 files
```
also an example of documentation with **Doxygen** which will be super useful in your future life dude.


[](/home/tomi/MHPC/P1.3_seed/lectures/c++/07_inheritance)
[](/home/tomi/MHPC/P1.3_seed/lectures/c++/07_inheritance)
[](/home/tomi/MHPC/P1.3_seed/lectures/c++/07_inheritance)
[](/home/tomi/MHPC/P1.3_seed/lectures/c++/07_inheritance)
# Advanced Programming 2018-11-20

*Keep it simple, as simple as you can. But not simpler.* (Einstein?)

## Iterators

we're gonna implement a linked list, for fun and for profit (to understand how iterators work).

Each node contains:
- a value
- pointer to next node
- a pointer to previous node (if **doubly** l.l.)

We will do only the simpler case.
We're gonna also implement the `auto` iterator.
We want **data hiding**, particular structure of the list kept hiden to the user.

- Data hiding is a programming approach, easier to maintain and expand your programs.

So here we go:

### Linked list
the code implemented by alberto is in `lectures/08_iterators/`

```c++
template <class T>
class List{

};
```

first design choice to face:
- list **is** a block
- list **has** a block
do we define the list itself as a block or do we want it to be a container of blocks?

First of all, the user needs the values, not the blocks, thus we don't need him/her to be able to access the object **block**.

**I want this software to be as reliable as possible, to ease re-use**

How do we write the class block?
It should again be templated on the value:
```c++
template <class T>
	 class Block{

};
```
since `T` will be the same for both block and list, it is maybe good to define one in within of the other:
```c++
template <class T>
class List{
      struct Node{ // since Node is now part of List, it is not templated anymore
      	     T val; // ok we need this
	     std::unique_ptr<Node> next;
	     Node(const T& v, Node* n): val{v}, next{n} {}
	     ~Node() = default;
      };
};
```
we need a pointer to next node, and the node will have to leave on the heap => we will have to deal with memory allocation/deallocation => it might not be a bad idea to use `unique` pointers, since only the previous node will be insigned in releasing the memory occupied by that Node.

Furthermore, why are we using `struct` for Node and `class` for List.
All members of list are visible to node but only public members of node are visible to node.
- user of class List doesn't know the existence of class Node
- in structs, all members are public by default, if not specified conversely.

How do we want to use this class List?

We need the two functions:
- push_front
- push_back

First pointer of the list is **HEAD**, it is again a `unique_ptr` and it should be private.
What about the `public` interface?

```c++
private:
	void push_front(const T& t){
	     
	}
public:
	List() = default;
	void push( const T& t, method m);
```
I might want one day to add other type of push. So I might want to define a general function `push` with possibly an `scoped enum` to select which `push` to use.
```c++
enum class method{push_back, push_front};
```

The advantage here is that for the particular case of the first element of the list, I can avoid duplicating `if (head == nullptr)` in both `push_back` and `push_front`, but just check it once in function `push`:
```c++
void push (const T& t, method m=method::push_front) {
    if (head == nullptr) {
       // I could do this:
       // Node * tmp = new Node{t, nullptr};
       // head.reset(tmp);
       // or collpse it into one line:
       head.reset(new Node{t, nullptr});
       return; // because I don't need whatever comes later
    }
    switch (m){
    case method::push_back:
	   	push_back(t);
	   	break;
    case method::push_front:
	   	push_front(t);
		break;
    default:
		throw std::runtime_error{"unknown method\n"};
    }
}
```
functions of `unique prt`
- `T* get()` takes the raw pointer value
- `void reset(T* t = nullptr);` changes the value of pointer
- `T* release()` releases the ownership and gets the raw pointer

So now we have to implement the `push_front`
```c++
void push_front(const T& t) {
     /*
     auto old = head.release();
     auto tmp = new Node{ new Node{t, old} };
     head.reset(tmp);
     */
     head.reset(new Node{t, head.release()});
}
```
aaaaand the function `push_back` (which now here I am defining it as if I was defining it outside the class definition just to distinguish it)
```c++
template <typename T>
void List<T>::push_back(const T& t) {
     Node * tmp = head.get();
     while ( tmp->next != nullptr ) { // equivalent to: while(tmp->next)
     	   tmp = tmp->next.get();
     }
     tmp->next.reset(new Note{t, nullptr});
}
```
Also usefull is the overload of operator `put to`. (look at alby's code)

Now, ok that we want data hiding, but we want to print them.

#### Friendship

For now let's just use *friendship*:
Anywhere (either in private and whathever else) within our class we can define a friendship with some other class

```c++
friend std::ostream& operator<<(std::ostream&, const List&);
```
just simply put the word `friend` in front of the regular class declaration of your friend.

Back to our printing, see in alby's implementation.

**Now we can enter the main topic**

How can we implement our functions so that we don't need to know how our objects are implemented.


**an iterator is a GENERALIXED POINTER**

there are different kind of pointers.

An iterator is a class that allows you to move by overloading
- the unary operator `T& operator*()` the *de-reference* operator
- the operator `T& operator->()`
- also the pre-increment `iterator operator++() //++i`
- and the post-increment `iterator operator++(int) //i++`
it uses the same syntax of pointers.

Why do I also need **Const iterators**??
There are situations in which I really do need to be sure that I am not changing the values within my nodes..
Const iterators are exactly like iterators but, when dereferenced, instead returning a reference to something they return a **const** reference to something.

SO I can define a const iterator (which is an iterator) as derived class of iterator that inherits everything from iterator but the constructor.

**NOTE: another difference between struct and class: when it inherits, inherited functions from struct are by default defined as `public`, viceversa for `class`# Advanced Programming - 2018/11/22

## Buffering Lecture

## Copying variables:

2 ways to copy a variable

- **shallow** 

- **deep**

this difference comes out when copying pointers, they're the same for Plain Oblabla Data (POD).

When our class has pointers, there is instead a difference between the 2.
Always remember the three concepts:
- **type**: the way to interpret the content (in terms of bits) of an object
- **object**: a portion of memory (both in stack or heap)
- **content**: the sequence of bits composing the object
- **variable**: a named object.

In the case of pointers if I implement a shallow copy of a pointer:
```c++
T* a1;
[...] // now a1 is pointing to, say, 0x11
auto a2 = a1; // also a2 is pointing to 0x11
```
now a2 contains the same address than a1, it's a cheap copy.

This might be what we want but we could also want to copy the content of `*a1` into another position.
This is what **deep copying** means.
I have to implement my copy constructor, e.g. for class vector, containing a pointer to first element.:
```c++
template < class T >
class vector {
T * elem;
vector (const vector & other); // this is the copy constructor declaration
}
```

and this is the copy-constructor definition:
```c++
vector::vector (const vector & other) {

elem = new T[other.size()];
for (auto i = 0; i<other.size(); ++i)
    elem[i]= other[i];

}
```
If I want to use the operator 'equal' I have to overload it:
```c++
vector & operator= (const vector & other);
```

NOTE THAT:
```c++
vector< double > v2;
v2 = v1;
```
works as **deep copy** without copy-constructor, while this
```c++
auto v2 = v1;
```
does not.

What about the `move` semantics? it is a way of *stealing* information from another pointer..

## What is a Polymorphic copy?

base <- child <- nephew

this inheritance is useful because I can use runtime polymorphism:

I can have a base pointer (which can point to all the spring of base):
```c++
base * p = new child;
```
But can I copy the new object child created?
```c++
auto n = *p; // slicing
```
I got **SLICING**, what does it mean? it means that, since I am copying a pointer to base, also `n` will be of class `base`, not `child` as we want:

I am copying only the part of memory reserved to base: I am loosing information!
In this case I knew it was the class `child` that I wanted, but sometimes this is not the case, e.g.:

```c++
base * factoring (std::string s) {
     if (s=="child")
     	return new child;
     if (s=="nephew")
     	return new nephew;
     else
        [...]
}
```
I have to implement a **polymorphic copy**!
Worldwide typically called **clone**:
```c++
class base {
      virtual base * clone () const {
      	      return new base{*this};
      };
}
```
Now I can override it in all the spring:
```c++
child * clone () const override {
      return new child{* this};
}
```
now I can avoid slicing:
```c++
base * n = p->clone();
```
just choosing base class and cloning into it a derived classe, it will clone the whole derived class.# Advanced Programming - 2018/11/27

To count how many headers we have used in some folder:
```bash
find . \( -name '*.cc' -o -name '*.h' \) -exec grep 'include' | gawk '!x[$2]++'
```

- **`<chrono>` contains the highest resolution clock on the market**: use this when possible
- `<cstdlib>` contains the functions for backward compatibility with `C`

**DO NOT re-invent the wheel**

note that containers are a pain in the ass, they're the reason why in vectors definition
this
```c++
vector<int> a{3};
```
is different from this
```c++
vector<int> a(3);
```

Multi-threading is supported **natively** in c++, supported if compiling with `-pthread`, functions are included in the headers of `stdlib` named **concurrency**:
- `<atomic>`
- `<condition_variable>`
- `<future>`
- `<thread>`# Advanced Programming - 2018/11/29

**NOTE** the `end` iterator is always implemented as the position past the last one of our data-structure.
e.g.: in a linked list it points to `nullptr` aka the last element pointed to by the list.

## Lambda functions

Super-dooper useful exspecially in `c++14`.
A `lambda_function` has a *capture* `[]`, the *arguments* `()`, the return type (most of the times is automathically deduced by the compiler, and its *definition* `{}`
- **capture** : it allows the lambda to see all the authomatic and the local variables defined outside:
```c++
for (auto i = 0; i<N; ++i) {

    auto lambda_func = [&] ( double a, double b ) -> return_type {};

}
```
two possible types of capture:
- (everything) by reference: `[&]` the variables passed are modifiable within the lambda
- (everything) by value: `[=]` the variables are copied and possible mods made in the lambda are not transfered outside
of course we can campture something by ref and soumething by val:
`[&N, i]`

from `c++14` we can also give arguments as `auto`:
```c++
auto my_f = [&N, i] (auto a, auto b) { ++i; N += i; };
```

**init capture** from `c++14` also another feature, you can *custumly* initialize a variable:
```c++
[i = i] ( auto a, auto b ) {}
```

**NOTE THAT static variables are not captured when I capture by value in lambdas**
so the init capture:
```c++
static int a;
[ a = a ] () {};
```
in this way I am telling the lambda to capture by value a variable `a` and call it `a`.

static variables remember the last value they assumed: if I have a function like this:
```c++
void func () {

     static int a = 0; 
     a++;

}
```

first time I call `func` `a` is initialized to `0` then increased by `1`. Next time I will call `func` it will remember that the value of `a` has put to be equal to `1` and not re-initialized.
`static` variables are local, but survive till the end of the program.# Advanced Programming - 2018/12/03

# Python - eddaje.

Dafuq is `ABC`? Guess what? is a programming language.
**Python** is developed from same guy (Guido van Rossum, esticazzi), still main features of python come from ABC, thus we care.
`BDFL` is this Guido guy, as much as Linus Torblabla is the `DFL` of the Linux kernel.

How old is python? first version dates back to 1991.

**Python is an interpreted language**

To run a python program, at least, 3 ways:
- write `$ python` in **shell**, than write command-per-command (use it to test synthax or for quick checks)
- write a **script** and run it with `$ python script_name.py`
- **interactive** (jupyther) notebooks (**IPython**)
Two main branches:
- python `2.7`
- python > `3` (we're gonna use this)

There are different implementations of python, the most used is `CPython` (is not `Cython`, chyton is a package that allows you to compile a code written in python), which, guess what? is written in `C`.

**Python is a DINAMICALLY typed language**
aka, variables are not bounded to an object, everything in python is an object (also types of variables). Try this:
```python
> a = 5
> a
5
> type(a)
<class 'int'>
> a = "hello world"
> type(a)
<class 'str'>
```
cool.

just be carefull: since it is an interpreted language, there is no compiler helping you to find stupid bugs in the code -> you'll find them at run time.

**NOTE** also that, in py3, all the division are performed assuming you want a floating point number as return value.

**Python allocates and free-s memory for you (and it is pretty efficient, you have to be a pro-bug-creator to make it crash).

## Ways of defining a string in python:

- `' '`
- `" "`

also these:

- `''' '''`
- `""" """`

which are also viable ways of commenting on more than one line in python.# Advanced Programming - 2018/12/11

