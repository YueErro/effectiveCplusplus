# Chapter 4. Designs and Declarations

## Table of contents
* [Item 18: Make interfaces easy to use correctly and hard to use incorrectly](#item-18-make-interfaces-easy-to-use-correctly-and-hard-to-use-incorrectly)
* [Item 19: Treat class design as type design](#item-19-treat-class-design-as-type-design)
* [Item 20: Prefer pass-by-reference-to-const to pass-by-value](#item-20-prefer-pass-by-reference-to-const-to-pass-by-value)
* [Item 21: Don't try to return a reference when you must return an object](#item-21-dont-try-to-return-a-reference-when-you-must-return-an-object)
* [Item 22: Declare data members `private`](#item-22-declare-data-members-private)
* [Item 23: Prefer non-member non-friend functions to member functions](#item-23-prefer-non-member-non-friend-functions-to-member-functions)
* [Item 24: Declare non-member functions when type conversions should apply to all parameters](#item-24-declare-non-member-functions-when-type-conversions-should-apply-to-all-parameters)
* [Item 25: Consider support for a non-throwing `swap`](#item-25-consider-support-for-a-non-throwing-swap)

### Item 18: Make interfaces easy to use correctly and hard to use incorrectly

The "cross-DLL problem" crops up when an object is created using `new` in one dynamically linked library (DLL) but is `delete`d  in a different DLL.

#### Things to Remember
* Good interfaces are easy to use correctly and hard to use incorrectly. Your should strive for these characteristics in all your interfaces.
* Ways to facilitate correct use include consistency in interfaces and behavioral compatibility with built-in types.
* Ways to prevent errors include creating new types, restricting operations on types, constraining object values, and eliminating client resource management responsibilities.
* `TR1::shared_ptr` supports custom deleters. This prevents the cross-DLL problem,

### Item 19: Treat class design as type design

Defining a new class defines a new type. Every class requires to answer:
* **How should objects of your new type be created and destroyed?**
* **How should object initialization differ from object assignment?**
* **What does it mean for objects of your new type to be passed by value?**
* **What are the restrictions on legal values for your new type?**
* **Does your new type fit into an inheritance graph?**
* **What kind of type conversions are allowed for your new type?**
* **What operators and functions make sense for the new type?**
* **What standard functions should be disallowed?**
* **Who should have access to the members of your new type?**
* **What is the "undeclared interface" of your new type?**
* **How general is your new type?** (If it is a whole *family* of type define a new class *template*)
* **Is a new type really what you need?**

### Item 20: Prefer pass-by-reference-to-const to pass-by-value

C++ passes objects to and from functions by value. Function parameters are initialized with *copies* of the actual arguments, and function callers get back a *copy* of the value returned by the function produced by the objects' copy constructors.

If you *want* all your objects to be reliably initialized and destroyed use pass by reference-to-`const` because no new objects are being created: `bool validateStudent(const Student& s);`.

Passing by reference also avoids the *slicing problem*. When a derived class object is passed (by value) as a base class object, the base class copy constructor is called, and the specialized features that make the object behave like a derived class object are "sliced" off.

If you have an object of a built-in type (e.g., an `int`), it's often more efficient to pass it by value. The same applies to iterators and function objects in the STL. For everything else, follow the advice of this Item and prefer pass-by-reference-to-`const` over pass-by-value.

#### Things to Remember
* Prefer pass-by-reference-to-`const` over pass-by-value. It's typically more efficient and it avoids the slicing problem.
* The rule doesn't apply to built-in types and STL iterator and function object types. For them, pass- by-value is usually appropriate.

### Item 21: Don't try to return a reference when you must return an object

The right way to write a function that must return a new object is to have that function return a new object.

#### Things to Remember
* Never return a pointer or reference to a local stack object, a reference to a heap-allocated object, or a pointer or reference to a local static object if there is a chance that more than one such object will be needed.

### Item 22: Declare data members `private`

#### Things to Remember
* Declare data members `private`. It gives clients syntactically uniform access to data, affords fine-grained access control, allows invariants to be enforced, and offers class authors implementation flexibility.
* `protected` is no more encapsulated than `public`.

### Item 23: Prefer non-member non-friend functions to member functions

This reasoning applies only to non-member *non-friend* functions. Friends have the same access to a class's private members that member functions have, hence the same impact on encapsulation. From an encapsulation point of view, the choice isn't between member and non-member functions, it's between member functions and non-member non-friend functions.

Thing to note is that just because concerns about encapsulation dictate that a function be a non-member of one class doesn't mean it can't be a member of another class.

#### Things to Remember
* Prefer non-member non-friend functions to member functions. Doing so increases encapsulation, packaging flexibility, and functional extensibility.

### Item 24: Declare non-member functions when type conversions should apply to all parameters

Parameters are eligible for implicit type conversion *only if they are listed in the parameter list*. What `this` points to is *never* eligible for implicit conversions.

#### Things to Remember
* If you need type conversions on all parameters to a function (including the one pointed to by the `this` pointer), the function must be a non-member.

### Item 25: Consider support for a non-throwing `swap`

`swap` was originally introduced as part of the STL, it's since become a mainstay of exception-safe programming and a common mechanism for coping with the possibility of assignment to self.

To `swap` the values of two objects is to give each the other's value. By default, swapping is accomplished via the standard `swap` algorithm.

As long as your types support copying the default `swap` implementation will let objects of your types be swapped without your having to do the following:

1. Offer a public `swap` member function that efficiently swaps the value of two objects of your type.
2. Offer a non-member `swap` in the same namespace as your class or template. Have it call your `swap` member function.
3. If you're writing a class (not a class template), specialize `std::swap` for your class. Have it also call your `swap` member function.

If you're calling `swap`, be sure to include a using declaration to make `std::swap` visible in your function, then call `swap` without any namespace qualification.

It's okay to totally specialize templates in `std`, but it's not okay to add `new` templates to `std`. So declare a non-member `swap` that calls the member `swap`.

#### Things to Remember
* Provide a `swap` member function when `std::swap` would be inefficient for your type. Make sure your `swap` doesn't throw exceptions.
* If you offer a member `swap`, also offer a non-member `swap` that calls the member. For classes (not templates), specialize `std::swap`, too.
* When calling `swap`, employ a `using` declaration for `std::swap`, then call `swap` without namespace qualification.
* It's fine to totally specialize `std` templates for user-defined types, but never try to add something completely new to `std`.
