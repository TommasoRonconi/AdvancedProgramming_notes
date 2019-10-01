# Advanced Programming - 2018/11/29

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
`static` variables are local, but survive till the end of the program.