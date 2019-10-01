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

*Try to understand why it does not compile*