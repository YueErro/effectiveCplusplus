# Chapter 7. Templates and Generic Programming

## Table of contents
* [Item 41: Understand implicit interfaces and compile-time polymorphism](#item-41-understand-implicit-interfaces-and-compile-time-polymorphism)
* [Item 42: Understand the two meanings of `typename`](#item-42-understand-the-two-meanings-of-typename)
* [Item 43: Know how to access names in templatized base classes](#item-43-know-how-to-access-names-in-templatized-base-classes)
* [Item 44: Factor parameter-independent code out of templates](#item-44-factor-parameter-independent-code-out-of-templates)
* [Item 45: Use member function templates to accept "all compatible types"](#item-45-use-member-function-templates-to-accept-all-compatible-types)
* [Item 46: Define non-member functions inside templates when type conversions are desired](#item-46-define-non-member-functions-inside-templates-when-type-conversions-are-desired)
* [Item 47: Use traits classes for information about types](#item-47-use-traits-classes-for-information-about-types)
* [Item 48: Be aware of template metaprogramming](#item-48-be-aware-of-template-metaprogramming)

### Item 41: Understand implicit interfaces and compile-time polymorphism

We talk about poliymorphism in inheritance, when an object can be "parent" or any "child" type with different functions with the same name. Object-oriented programming revolves around *explicit* interfaces and *runtime* polymorphism.

Instantiating function templates with different template parameters leads to different functions being called, this is known as *compile-time polymorphism*.

#### Things to Remember
* Both classes and templates support interfaces and polymorphism.
* For classes, interfaces are explicit and centered on function signatures. Polymorphism occurs at runtime through virtual functions.
* For template parameters, interfaces are implicit and based on valid expressions. Polymorphism occurs during compilation through template instantiation and function overloading resolution.

### Item 42: Understand the two meanings of `typename`

```cpp
template<class T> class Widget;     // uses "class"
template<typename T> class Widget;  // uses ""typename"
```

There is no different between them. The use of `typename` is mandatory anytime you refer to a nested dependent type name in a template.

#### Things to Remember
* When declaring template parameters, `class` and `typename` are interchangeable.
* Use `typename` to identify nested dependent type names, except in base class lists or as a base class identifier in a member initialization list.

### Item 43: Know how to access names in templatized base classes

`using` declarations bring hidden base class names into a derived class's scope.

#### Things to Remember
* In derived class templates, refer to names in base class templates via a "`this->`" prefix, via `using` declarations, or via an explicit base class qualification.

### Item 44: Factor parameter-independent code out of templates

If you're not careful, using templates can lead to *code bloat*: binaries with replicated code, data, or both. The result can be source code that looks fit and trim, yet object code that's fat and flabby, do *commonality and variability analysis*.

#### Things to Remember
* Templates generate multiple classes and multiple functions, so any template code not dependent on a template parameter causes bloat.
* Bloat due to non-type template parameters can often be eliminated by replacing template parameters with function parameters or class data members.
* Bloat due to type parameters can be reduced by sharing implementations for instantiation types with identical binary representations.

### Item 45: Use member function templates to accept "all compatible types"
*Smart pointers* can be used to automatically delete heap-based resources at the right time.

If the number of constructors we need is unlimited, we need a constructor *template* and not a constructor *function*, a template can be instantiated to generate an unlimited number of functions.

*Implicit* conversion from a built-in pointer or other smart pointer type is not permitted.

#### Things to Remember
* Use member function templates to generate functions that accept all compatible types.
* If you declare member templates for generalized copy construction or generalized assignment, you'll still need to declare the normal copy constructor and copy assignment operator, too.

### Item 46: Define non-member functions inside templates when type conversions are desired

#### Things to Remember
* When writing a class template that offers functions related to the template that support implicit type conversions on all parameters, define those functions as friends inside the class template.

### Item 47: Use traits classes for information about types

*Input iterators* can move only forward, can move only one step at a time, can only read what they point to, and can read what they're pointing to only once. They're modeled on the read pointer into an input file; the C++ library's `istream_iterators` are representative of this category. *Output iterators* are analogous, but for output: they move only forward, move only one step at a time, can only write what they point to, and can write it only once. They're modeled on the write pointer into an output file; `ostream_iterators` epitomize this category.*Forward iterators* can do everything input and output iterators can do, plus they can read or write what they point to more than once. *Bidirectional iterators* add to forward iterators the ability to move backward as well as forward.*Random access iterators* add to bidirectional iterators the ability to perform "iterator arithmetic".

```cpp
struct input_iterator_tag{};
struct output_iterator_tag{};
struct forward_iterator_tag: public input_iterator_tag{};
struct bidirectional_iterator_tag: public forward_iterator_tag{};
struct random_iterator_tag: public bidirectional_iterator_tag{};
```

Traits are a technique and a convention followed by C++ programmers. For iterators, the template in the standard library is named `iterator_traits`: `struct iterator_traits;`.
`iterator_traits` offers a *partial template specialization* for pointer types: `struct iterator_traits<IterT*>{...};`.

To Design and implement a traits class:
* Identify some information about types you'd like to make available (e.g., for iterators, their iterator category).
* Choose a name to identify that information (e.g., `iterator_category`).
* Provide a template and set of specializations (e.g., `iterator_traits`) that contain the information for the types you want to support.

`ìterator_traits` is actually `std::iterator_traits`. How to use a traits class:
* Create a set of overloaded "worker" functions or function templates (e.g., `doAdvance`) that differ in a traits parameter. Implement each function in accord with the traits information passed.
* Create a "master" function or function template (e.g., `advance`) that calls the workers, passing information provided by a traits class.

#### Things to Remember
* Traits classes make information about types available during compilation. They're implemented using templates and template specializations.
* In conjunction with overloading, traits classes make it possible to perform compile-time `if...else` tests on types.

### Item 48: Be aware of template metaprogramming

Template metaprogramming (TMP) is the process of writing template-based C++ programs that execute during compilation. It was discovered, not invented. TMP loops don't involve recursive function calls, they involve recursive *template instantiations*.

To know if TMP is worthy:
* **Ensuring dimensional unit correctness**: fractional dimensional exponents can be supported.
* **Optimizing matrix operations**: using an advanced template technology related to TMP called *expression templates*, it's possible to eliminate the temporaries and merge the loops, all without changing the syntax of the client code above. The resulting software uses less memory and runs dramatically faster.
* **Generating custom design pattern implementations**: using a TMP-based technology called *policy-based design*, it's possible to create templates representing independent design choices ("policies") that can be combined in arbitrary ways to yield pattern implementations with custom behavior.

#### Things to Remember
* Template metaprogramming can shift work from runtime to compile-time, thus enabling earlier error detection and higher runtime performance.
* TMP can be used to generate custom code based on combinations of policy choices, and it can also be used to avoid generating code inappropriate for particular types.
