# Advanced Programming - 2018/11/13

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
