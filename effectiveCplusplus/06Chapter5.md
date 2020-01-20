# Chapter 5. Implementations

## Table of contents
* [Item 26: Postpone variable definitions as long as possible](#item-26-postpone-variable-definitions-as-long-as-possible)
* [Item 27: Minimize casting](#item-27-minimize-casting)
* [Item 28: Avoid returning "handles" to object internals](#item-28-avoid-returning-handles-to-object-internals)
* [Item 29: Strive for exception-safe code](#item-29-strive-for-exception-safe-code)
* [Item 30: Understand the ins and outs of inlining](#item-30-understand-the-ins-and-outs-of-inlining)
* [Item 31: Minimize compilation dependencies between files](#item-31-minimize-compilation-dependencies-between-files)

### Item 26: Postpone variable definitions as long as possible

You should also try to postpone the definition until you have initialization arguments for it.

Imagine:
* Approach A: 1 constructor + 1 destructor + `n` assignments
* Approach B: `n` constructors + `n` Destructors

For classes where an assignment costs less than a constructor-destructor pair, Approach A is generally more efficient. Otherwise, Approach B is probably better.

#### Things to Remember
*  Postpone variable definitions as long as possible. It increases program clarity and improves program efficiency.

### Item 27: Minimize casting

* `const_cast<T>(expression)`: is typically used to cast away the constness of objects.
* `dynamic_cast<T>(expression)`: is primarily used to perform "safe downcasting". It is also the only cast that may have a significant runtime cost.
* `reinterpret_cast<T>(expression)`: is intended for low-level casts that yield implementation-dependent results, e.g., casting a pointer to an `int`.
* `static_cast<T>(expression)`: can be used to force implicit conversions (e.g., non-`const` object to `const` object, `int` to `double`, etc.). It can also be used to perform the reverse of many such conversions (e.g., pointer-to-base to pointer-to-derived), though it cannot cast from `const` to non-`const` objects.

Type conversions of any kind often lead to code that is executed at runtime.

You definitely want to avoid is designs that involve cascading `dynamic_cast`s.

#### Things to Remember
* Avoid casts whenever practical, especially `dynamic_casts` in performance-sensitive code. If a design requires casting, try to develop a cast-free alternative.
* When casting is necessary, try to hide it inside a function. Clients can then call the function instead of putting casts in their own code.
* Prefer C++-style casts to old-style casts. They are easier to see, and they are more specific about what they do.

### Item 28: Avoid returning "handles" to object internals.

References, pointers, and iterators are all *handles*, and returning a handle to an object's internals always runs the risk of compromising an object's encapsulation.

"internals" mainly protected and private data members.

#### Things to Remember
* Avoid returning handles (references, pointers, or iterators) to object internals. It increases encapsulation, helps `const` member functions act `const`, and minimizes the creation of dangling handles.

### Item 29: Strive for exception-safe code

When an exception is thrown, exception-safe functions:
* **Leak no resources**
* **Don't allow data structures to become corrupted**

Exception-safe functions offer one of three guarantees:
* **Basic guarantee**: if an exception is thrown, everything in the program remains in a valid state. No objects or data structures become corrupted, and all objects are in an internally consistent state. However, the program could be in *any* valid state.
* **Strong guarantee**: if an exception is thrown, the state of the program is unchanged. Calls to such functions are *atomic* in the sense that if they succeed, they succeed completely, and if they fail, the program state is as if they'd never been called.
* **Nothrow guarantee**: never to throw exceptions, because they always do what they promise to do. All operations on built-in types are nothrow, This is a critical building block of exception-safe code.

Exception-safe code must offer one of the three guarantees above. If it doesn't, it's not exception-safe. Offering no exception safety guarantee should be an option only if your crack team of requirements analysts has identified a need for your application to leak resources and run with corrupt data structures.

As a general rule, you want to offer the strongest guarantee that's practical. As long as functions operate only on local state, it's relatively easy to offer the strong guarantee.

#### Things to Remember
* Exception-safe functions leak no resources and allow no data structures to become corrupted, even when exceptions are thrown. Such functions offer the basic, strong, or nothrow guarantees.
* The strong guarantee can often be implemented via copy-and-`swap`, but the strong guarantee is not practical for all functions.
* A function can usually offer a guarantee no stronger than the weakest guarantee of the functions it calls.

### Item 30: Understand the ins and outs of inlining

An inline function replaces each call of that function with its code body. It is a *request* to compilers even if it may be ignored, not a command. The request can be given implicitly or explicitly:

```cpp
int age() const { return the_age; }                 // implicit
inline const T& std::max(const T& a, const T& b)    // explicit
{ return a < b ? b : a; }
```

Inline functions must typically be in header files, because most build environments do inlining during compilation. Templates are typically in header files as well, because compilers need to know what a template looks like in order to instantiate it when it's used. Template instantiation is independent of inlining. If you're writing a template and you believe that all the functions instantiated from the template should be inlined, declare the template `inline`.

#### Things to Remember
* Limit most inlining to small, frequently called functions. This facilitates debugging and binary upgradability, minimizes potential code bloat, and maximizes the chances of greater program speed.
* Don't declare function templates `inline` just because they appear in header files.

### Item 31: Minimize compilation dependencies between files

If parsing standard headers really is a problem, you may need to change your interface design to avoid using the parts of the standard library that give rise to the undesirable `#includes`.

Make your header files self-sufficient whenever it's practical, and when it's not, depend on declarations in other files, not definitions. Everything else flows from this simple design strategy. Hence:
* **Avoid using objects when object references and pointers will do**: you may define references and pointers to a type with only a declaration for the type. Defining *objects* of a type necessitates the presence of the type's definition.
* **Depend on class declarations instead of class definitions whenever you can**: note that you never need a class definition to declare a function using that class, not even if the function passes or returns the class type by value.
* **Provide separate header files for declarations and definitions**

### Things to Remember
* The general idea behind minimizing compilation dependencies is to depend on declarations instead of definitions. Two approaches based on this idea are Handle classes and Interface classes.
* Library header files should exist in full and declaration-only forms. This applies regardless of whether templates are involved.
