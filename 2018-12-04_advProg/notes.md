# Advanced Programming - 2018/12/03

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

which are also viable ways of commenting on more than one line in python.