# Chapter 6. Inheritance and Object-Oriented Design

Inheritance can be single or multiple, and each inheritance link can be public, protected, or private. Each link can also be virtual or non-virtual.

## Table of contents
* [Item 32: Make sure public inheritance models "is-a"](#item-32-make-sure-public-inheritance-models-is-a)
* [Item 33: Avoid hiding inherited names](#item-33-avoid-hiding-inherited-names)
* [Item 34: Differentiate between inheritance of interface and inheritance of implementation](#item-34-differentiate-between-inheritance-of-interface-and-inheritance-of-implementation)
* [Item 35: Consider alternatives to virtual functions](#item-35-consider-alternatives-to-virtual-fuctions)
* [Item 36: Never redefine an inherited non-virtual function](#item-36-never-redefine-an-inherited-non-virtual-function)
* [Item 37: Never redefine a function's inherited default parameter value](#item-37-never-redefine-a-functions-inherited-default-parameter-value)
* [Item 38: Model "has-a" or "is-implemented-in-terms-of" through composition](#item-38-model-has-a-or-is-implemented-in-terms-of-through-composition)
* [Item 39: Use private inheritance judiciously](#item-39-use-private-inheritance-judiciously)
* [Item 40: Use multiple inheritance judiciously](#item-40-use-multiple-inheritance-judiciously)

### Item 32: Make sure public inheritance models "is-a"

#### Things to Remember
* Public inheritance means "is-a." Everything that applies to base classes must also apply to derived classes, because every derived class object is a base class object.

### Item 33: Avoid hiding inherited names

#### things to Remember
* Names in derived classes hide names in base classes. Under public inheritance, this is never desirable.
* To make hidden names visible again, employ `using` declarations or forwarding functions.

### Item 34: Differentiate between inheritance of interface and inheritance of implementation

### Things to Remember
* Inheritance of interface is different from inheritance of implementation. Under public inheritance, derived classes always inherit base class interfaces.
* Pure virtual functions specify inheritance of interface only.
* Simple (impure) virtual functions specify inheritance of interface plus inheritance of a default implementation.
* Non-virtual functions specify inheritance of interface plus inheritance of a mandatory implementation.

### Item 35: Consider alternatives to virtual functions

Virtual functions should almost always be private.

*non-virtual interface (NVI) idiom* is having clients call private virtual functions indirectly through public non-virtual member functions. I call the non-virtual function the virtual function's *wrapper*. The wrapper ensures that before a virtual function is called, the proper context is set up, and after the call is over, the context is cleaned up.

#### Summary
* Use the **non-virtual interface idiom** (NVI idiom), a form of the Template Method design pattern that wraps public non-virtual member functions around less accessible virtual functions.
* Replace virtual functions with **function pointer data members**, a stripped-down manifestation of the Strategy design pattern.
* Replace virtual functions with **tr1::function data members**, thus allowing use of any callable entity with a signature compatible with what you need. This, too, is a form of the Strategy design pattern.
* Replace virtual functions in one hierarchy with **virtual functions in another hierarchy**. This is the conventional implementation of the Strategy design pattern.

#### Things to Remember
*  Alternatives to virtual functions include the NVI idiom and various forms of the Strategy design pattern. The NVI idiom is itself an example of the Template Method design pattern.
* A disadvantage of moving functionality from a member function to a function outside the class is that the non-member function lacks access to the class's non-public members.
* `tr1::function` objects act like generalized function pointers. Such objects support all callable entities compatible with a given target signature.

### Item 36: Never redefine an inherited non-virtual function

That's all.

### Item 37: Never redefine a function's inherited default parameter value

There are only two kinds of functions you can inherit: virtual and non-virtual. It's always a mistake to redefine an inherited non-virtual function. Virtual functions are `dynamically bound`, meaning that the particular function called is determined by the dynamic type of the object through which it's invoked. But default parameter values are statically bound, that means you may end up invoking a virtual function defined in a `derived class` but using a default parameter value from a `base class`:

```cpp
pc->draw(Shape::Red);   // Virtual function
pr->draw();             // Default parameter value
```

An object's dynamic type is determined by the type of the object to which it currently refers. That is, its dynamic type indicates how it will behave.

#### Things to Remember
* Never redefine an inherited default parameter value, because default parameter values are statically bound, while virtual functions — the only functions you should be overriding — are dynamically bound.

### Item 38: Model "has-a" or "is-implemented-in-terms-of" through composition

*Composition* is the relationship between types that arises when objects of one type contain objects of another type. It's also known as *layering*, *containment*, *aggregation*, and *embedding*.

Composition means either "has-a" or "is-implemented-in-terms-of".

#### Things to Remember
* Composition has meanings completely different from that of public inheritance.
* In the application domain, composition means has-a. In the implementation domain, it means is- implemented-in-terms-of.

### Item 39: Use private inheritance judiciously

#### Things to Remember
* Private inheritance means is-implemented-in-terms of. It's usually inferior to composition, but it makes sense when a derived class needs access to protected base class members or needs to redefine inherited virtual functions.
* Unlike composition, private inheritance can enable the empty base optimization. This can be important for library developers who strive to minimize object sizes.

### Item 40: Use multiple inheritance judiciously

Multiple inheritance (MI) and single inheritance(SI).

Anytime you use public inheritance, use *virtual* public inheritance.

Don't use virtual bases unless you need to. By default, use non-virtual inheritance. If you must use virtual base classes, try to avoid putting data in them.

SI design is almost certainly preferable.

#### Things to Remember
* Multiple inheritance is more complex than single inheritance. It can lead to new ambiguity issues and to the need for virtual inheritance.
* Virtual inheritance imposes costs in size, speed, and complexity of initialization and assignment. It's most practical when virtual base classes have no data.
* Multiple inheritance does have legitimate uses. One scenario involves combining public inheritance from an Interface class with private inheritance from a class that helps with implementation.
