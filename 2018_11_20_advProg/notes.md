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

**NOTE: another difference between struct and class: when it inherits, inherited functions from struct are by default defined as `public`, viceversa for `class`