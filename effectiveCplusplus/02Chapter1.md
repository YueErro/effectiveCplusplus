# Chapter 1. Accustoming Yourself to C++

## Table of contents
* [Item 1: View C++ as a federation of languages](#item-1-view-c-as-a-federation-of-languages)
* [Item 2: Prefer `const`s, `enum`s, and `inline`s to `define`s](#item-2-prefer-consts-enums-and-inlines-to-defines)
* [Item 3: Use `const` whenever possible](#item-3-use-const-whenever-possible)
* [Item 4: Make sure that objects are initialized before they're used](#item-4-make-sure-that-objects-are-initialized-before-theyre-used)

### Item 1: View C++ as a federation of languages

Primary sublanguages: **C**, **Object-Oriented C++**, **Template C++** and **The STL** (Standard Template Library).

### Item 2: Prefer `const`s, `enum`s, and `inline`s to `define`s

`#define` a macro result in multiple copies. Use constant instead to have only one copy.

To define a constant `char*`-base string you have to write `const` twice: `const char * const authorName = "Scott Meyers";`.

`string` objects are generally preferable to their `char*`-based.

To limit the scope of a constant to a class, you must make it a member, and to ensure there's at most one copy of the constant, you must make it a `static` member: `static const int NUM_TURNS = 5;`

The enum hack behaves in some ways more like a `#define` than a `const` does. If you don't want to let people get a pointer or reference to one of your integral constants, an enum is a good way to enforce that constraint.

#### Things to Remember
* For simple constants, prefer `const` objects or enums to `#define`s.
* For function-like macros, prefer inline functions to `#define`s.

### Item 3: Use `const` whenever possible

For pointers, you can specify whether the pointer itself is `const`, the data it points to is `const`, both, or neither:
```cpp
const char *p = greeting;         // non-const pointer, const data
char * const p = greeting;        // const pointer, non-const data
const char * const p = greeting;  // const pointer and data
```

If the word `const` appears to the left of the asterisk, what's *pointed to* is constant; if the word `const` appears to the right of the asterisk, the *pointer itself* is constant; if `const` appears on both sides, both are constant.

```cpp
// Both are equivalent
const Widget *pw;
Widget const * pw;
```

Declaring an `iterator` `const` is like declaring a pointer `const`.
If you want an iterator that points to something that can't be modified, you want a `const_iterator`.

```cpp
const std::vector<int>::iterator iter;    // iter acts like a T* const
std::vector<int>::const_iterator cIter;   // cIter acts like a const T*
```

Within a function declaration, `const` can refer to the function's return value, to individual parameters, and, for member functions, to the function as a whole.


Unless you need to be able to modify a parameter or local object, be sure to declare it `const`.

`const` on member functions is importat to knowing which functions may modify an object and which may not, and to making possible to work with `const` objects.
* *bitwise constness* (*physical constness*):
* *logical constness*


The bitwise `const` camp believes that a member function is `const` if and only if it doesn't modify any of the object's data members (excluding those that are static).

`mutable` is a nice solution to the bitwise-constness-is-not-what-I-had-in-mind problem.

#### Things to Remember
* Declaring something `const` helps compilers detect usage errors. `const` can be applied to objects at any scope, to function parameters and return types, and to member functions as a whole.
* Compilers enforce bitwise constness, but you should program using conceptual constness.
* When `const` and non-`const` member functions have essentially identical implementations, code duplication can be avoided by having the non-`const` version call the `const` version.

### Item 4: Make sure that objects are initialized before they're used

*Always* initialize your objects before you use
them.

#### Things to Remember
* Manually initialize objects of built-in type, because C++ only sometimes initializes them itself.
* In a constructor, prefer use of the member initialization list to assignment inside the body of the constructor. List data members in the initialization list in the same order they're declared in the class.
* Avoid initialization order problems across translation units by replacing non-local static objects with local
static objects.
