# Introduction

The purpose of this book is to show you how to use C++ *effectively*. I assume you already know C++ as a *language* and that you have some experience in its use. What I provide here is a guide to using the language so that your software is comprehensible, maintanable, portable, extensible, efficient, and likely to behave as you expect.

### Terminology

A ***declaraion*** tells compilers about the name and type of something, but it omits certain details.

Note that I refer to the integer x as an "object," even though it's of built-in type. Some people reserve the name "object" for variables of user-defined type.

Each function¡s decalartion revelas its ***signature***, parameter and return types. Using "interface" to talk about a function's signature.

A ***definition*** is where compilers set aside memory for the object. For a function or a function template, the definition provides the code body. For a class or a class template, the definition lists the members of the class or template.

***Initialization*** is the process of giving an object its first value. A ***default constructor*** is one that can be called without any arguments.

Constructors declared `explicit` are usually preferable to non-`explicit` ones, because they prevent compilers from performing unexpected (often unintended) type conversions.

The ***copy constructor*** is used to initialize an object with a different object of the same type, and the ***copy assignment operator*** is used to copy the value from one object to another of the same type: `Widget& operator=(const Widget& rhs);`. `lhs` and `rhs`, "left-hand side" and "right-hand side".

Pass-by-value *means* "calll the copy constructor". It is a bad idea to pass user-defined types by value. Pass-by-reference-to-`const` is typically a better choice.

***Function objects*** are objects that act like functions.

***Undefined behavior***: dereferencig a null pointer or referring to an invalid array index.

***ctor*** and ***dtor***, constructors and destructors.

`pt`, "pointer to T" and `rw` "reference to w". Occasionally `mf` "member functions".

TR1 ("Technical Report 1") is a specification for new functionality being added to C++'s standard library. All TR1 components are in the namespace `tr1`.

Boost is an organization and a web site (http://boost.org) offering portable, peer-reviewed, open source C++ libraries. Most TR1 functionality is based on work done at Boost.
