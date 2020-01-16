# Chapter 2. Constructors, Destructors, and Assignment Operators

## Table of contents
* [Item 5: Know what functions C++ silently writes and calls](#item-5-know-what-functions-c-silently-writes-and-calls)
* [Item 6: Explicitly disallow the use of compiler-generated functions you do not want](#item-6-explicitly-disallow-the-use-of-compiler-generated-functions-you-do-not-want)
* [Item 7: Declare destructors virtual in polymorphic base classes](#item-7-declare-destructors-virtual-in-polymorphic-base-classes)
* [Item 8: Prevent exceptions from leaving destructors](#item-8-prevent-exceptions-from-leaving-destructors)
* [Item 9: Never call virtual functions during construction or destruction](#item-9-never-call-virtual-functions-during-construction-or-destruction)
* [Item 10: Have assignment operators return a reference to *this](#item-10-have-assignment-operators-return-a-reference-to-this)
* [Item 11: Handle assignment to self in operator=](#item-11-handle-assignment-to-self-in-operator)
* [Item 12: Copy all parts of an object](#item-12-copy-all-parts-of-an-object)

### Item 5: Know what functions C++ silently writes and calls

If you don't declare them yourself, compilers will declare their own versions of a copy constructor, a copy assignment operator, and a destructor. Furthermore, if you declare no constructors at all, compilers will also declare a default constructor for you. All these functions will be both `public` and `inline`.

```cpp
class Empty{
public:
  Empty(){...}                            // Empty e1; --> default constructor
  Empty(const Empty& rhs){...}            // Empty e2(e1); --> copy constructor
  ~Empty(){...}                           // Empty e1; --> destructor
  Empty& operator=(const Empty& rhs){...} // e2 = 21; --> copy assignment operator
};
```

The generated destructor is non-virtual unless it's for a class inheriting from a base class that itself declares a virtual destructor.

If you want to support assignment in a class containing a reference member, you must define the copy assignment operator yourself.

#### Things to Remember
* Compilers may implicitly generate a class's default constructor, copy constructor, copy assignment operator, and destructor.

### Item 6: Explicitly disallow the use of compiler-generated functions you do not want

To *prevent* copying declare the copy constructor and the copy assignment operator *private* and do not *define* member and friend functions.

To keep a class objects from being copied inherit from `Uncopyable`:

```cpp
class CanNotBeCopied: private Uncopyable{
  // Class no longer declares copy ctor or copy assign. operator
};
```

The implementation and use of `Uncopyable` include some subtleties, such asthe fact that inheritance form `Uncopyable` needn't be public and that `Uncopyable`'s destructor need not be virtual.

#### Things to Remember
* To disallow functionality automatically provided by compilers, declare the corresponding member functions `private` and give no implementations. Using a base class like `Uncopyable` is one way to do this.

### Item 7: Declare destructors virtual in polymorphic base classes

A *factory function* returns a base class pointer to a newly-created derived class object: `TimerKeeper *ptk =  getTimeKeeper();`. Avoid leaking memory: `delete ptk;`.

Any class with virtual functions, often indicates it is not meant to be used as a base class, should almost certainly have a virtual destructor. If a class does *not* contain virtual functions, making the destructor virtual is usually a bad idea. _Declare a virtual destructor in a class if and only if that class contains at least one virtual function_.


Occasionally it can be convenient to give a class a pure virtual destructor, which result in *abstract classes*, classes that can't be instantiated:

```cpp
class AWOV{
public:
  virtual AWOV::~AWOV() = 0;  // you must provide a definition for the pure virtual destructor
};
```

The rule for giving base classes virtual destructors applies only to *polymorphic* base classes. To base classes designed to allow the manipulation of derived class types through base class interfaces.

#### Thing to Remember
* Polymorphic base classes should declare virtual destructors. If a class has any virtual functions, it should have a virtual destructor.
* Classes not designed to be base classes or not designed to be used polymorphically should not declare virtual destructors.

### Item 8: Prevent exceptions from leaving destructors

C++ doesn't prohibit destructors from emitting exceptions, but it certainly discourages the practice. In general, swallowing exceptions is a bad idea, because it suppresses important information

#### Things to Remember
* Destructors should never emit exceptions. If functions called in a destructor may throw, the destructor should catch any exceptions, then swallow them or terminate the program.
* If class clients need to be able to react to exceptions thrown during an operation, the class should provide a regular (i.e., non-destructor) function that performs the operation.

### Item 9: Never call virtual functions during construction or destruction

You shouldn't call virtual functions during construction or destruction, because the calls won't do what you think, and if they did, you'd still be unhappy.

#### Things to Remember
* Don't call virtual functions during construction or destruction, because such calls will never go to a more derived class than that of the currently executing constructor or destructor.

### Item 10: Have assignment operators return a reference to *this

Assignments can be chained together and they are right-associative: `x = y = z = 15;` equals to `x = (y = (z = 15));`.

#### Things to Remember
* Have assignment operators return a reference to *this.

### Item 11: Handle assignment to self in operator=

Assignment to self: `w = w;`. When this happends *aliasing* occur, having more than one way to refer to an object.

Passing something by value makes a *copy* of it.

#### Things to Remember
* Make sure `operator=` is well-behaved when an object is assigned to itself. Techniques include comparing addresses of source and target objects, careful statement ordering, and copy-and-`swap`.
* Make sure that any function operating on more than one object behaves correctly if two or more of the objects are the same.

### Item 12: Copy all parts of an object

To copy objects use *copying functions*: copy constructor and copy assignment operator.

If you add a data member to a class, you need to make sure that you update the copying functions, too.

When you're writing a copying function, be sure to (1) copy all local data members and (2) invoke the appropriate copying function in all base classes, too.

It makes no sense to have the copy assignment operator call the copy constructor, because you'd be trying to construct an object that already exists. Neither the other way around. If you want to have similar code bodies, create a third member function that both call. Such a function is typically private and is often named `init`.

#### Things to Remember
* Copying functions should be sure to copy all of an object's data members and all of its base class parts.
* Don't try to implement one of the copying functions in terms of the other. Instead, put common functionality in a third function that both call.
