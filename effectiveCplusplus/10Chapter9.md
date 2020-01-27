# Chapter 9. Miscellany

## Table of contents
* [Item 53: Pay attention to compiler warnings](#item-53-pay-attention-to-compiler-warnings)
* [Item 54: Familiarize yourself with the standard library, including TR1](#item-54-familiarize-yourself-with-the-standard-library-including-tr1)
* [Item 55: Familiarize yourself with Boost](#item-55-familiarize-yourself-with-boost)

### Item 53: Pay attention to compiler warnings

It's important to make sure that before you dismiss a warning, you understand exactly what it's trying to tell you.

#### Things to Remember
* Take compiler warnings seriously, and strive to compile warning-free at the maximum warning level supported by your compilers.
* Don't become dependent on compiler warnings, because different compilers warn about different things. Porting to a new compiler may eliminate warning messages you've come to rely on.

### Item 54: Familiarize yourself with the standard library, including TR1

Major parts of the standard C++ library:
* **The Standard Template Library (STL)**
* **Iostreams**
* **Support for internationalization**: including the ability to have multiple active locales. Types like `wchar_t` and `wstring` facilitate working with Unicode.
* **Support for numeric processing**: including templates for complex numbers (`complex`) and arrays of pure values (`valarray`).
* **An exception hierarchy**: including the base class `exception`, its derived classes `logic_error` and `runtime_error` and various classes that inherit from those.
* **C89's standard library**

Examples of the following TR1 components:
* **smart pointers** `TR1::shared_ptr` and `tr1::weak_ptr`. `TR1::shared_ptrs` act like built-in pointers, but they keep track of how many `tr1::shared_ptrs` point to an object. This is known as *reference counting*. When the last such pointer is destroyed (i.e., when the reference count for an object becomes zero), the object is automatically deleted. This works well in preventing resource leaks in acyclic data structures, but if two or more objects contain `tr1::shared_ptrs` such that a cycle is formed, the cycle may keep each object's reference count above zero, even when all external pointers to the cycle have been destroyed (i.e., when the group of objects as a whole is unreachable). That's where `TR1::weak_ptrs` come in. `TR1::weak_ptrs` are designed to act as cycle-inducing pointers in otherwise acyclic `tr1::shared_ptr`-based data structures. `tr1::weak_ptrs` don't participate in reference counting. When the last `tr1::shared_ptr` to an object is destroyed, the object is deleted, even if `tr1::weak_ptrs` continue to point there. Such `tr1::weak_ptrs` are automatically marked as invalid, however.
* **tr1::function**, which makes it possible to represent any *callable entity* whose signature is consistent with a target signature:
```cpp
// Both methods are equivalent, param type is a function taking an int and returning a string
void method( std::string func(int) );
void equivalent_method( std::string (int) );
```
* **tr1::bind**: does everything the STL binders `bind1st` and `bind2nd` do, plus much more. It works with both `const` and non-`const` and with by-reference parameters. It handles function pointers without help, so there's no need to mess with `ptr_fun`, `mem_fun`, or `mem_fun_ref` before calling it.
* **Hash tables**.
* **Regular expressions**.
* **Tuples**: can hold an arbitrary number of objects.
* **tr1::array**: uses no dynamic memory.
* **tr1::mem_fn**: a syntactically uniform way of adapting member function pointers.
* **tr1::reference_wrapper**: makes possible to create containers that act as if they hold references.
* **Random number generation**.
* **Mathematical special functions**.
* **C99 compatibility extensions**.
* **Type traits**: can reveal the proper alignment for a type, a crucial piece of information for programmers writing custom memory allocation functions.
* **tr1::result_of**: writing templates, it makes referring to a function return types easy.

#### Things to Remember
* The primary standard C++ library functionality consists of the STL, iostreams, and locales. The C99 standard library is also included.
* TR1 adds support for smart pointers (e.g., `tr1::shared_ptr`), generalized function pointers (`tr1::function`), hash-based containers, regular expressions, and 10 other components.
* TR1 itself is only a specification. To take advantage of TR1, you need an implementation. One source for implementations of TR1 components is Boost.

### Item 55: Familiarize yourself with Boost

Boost is both a community of C++ developers and a collection of freely downloadable C++ libraries. Its web site is [http://boost.org](http://boost.org).

It has a uniquely close and influential relationship with the C++ standardization committee and its process for accepting libraries is a special characteristic. To do so, consider questions such as these:
* How good are the design and implementation?
* Is the code portable across compilers and operating systems?
* Is the library likely to be of use to its target audience, i.e., people working in the domain the library addresses?
* Is the documentation clear, complete, and accurate?

General categories of libraries:
* **String and text processing**.
* **Containers**.
* **Function objects and higher-order programming** (such as Lambda).
* **Generic programming**.
* **Template metaprogramming** TMP.
* **Math and numerics**.
* **Correctness and testing**.
* **Data structures**.
* **Inter-language support**.
* **Memory**: `scoped_array`, an `auto_ptr`-like smart pointer for dynamically allocated arrays.
* **Miscellaneous**.

#### Things to Remember
* Boost is a community and web site for the development of free, open source, peer-reviewed C++ libraries. Boost plays an influential role in C++ standardization.
* Boost offers implementations of many TR1 components, but it also offers many other libraries, too.
