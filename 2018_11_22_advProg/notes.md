# Advanced Programming - 2018/11/22

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
just choosing base class and cloning into it a derived classe, it will clone the whole derived class.