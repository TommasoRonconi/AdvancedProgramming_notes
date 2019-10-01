# Advanced Programming - 2018/10/16

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
