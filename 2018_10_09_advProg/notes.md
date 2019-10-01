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
Either: **MEMORY LEAK!!**