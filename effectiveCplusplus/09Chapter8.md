# Chapter 8. Customizing new and delete

## Table of contents
* [Item 49: Understand the behavior of the new-handler](#item-49-understand-the-behavior-of-the-new-handler)
* [Item 50: Understand when it makes sense to replace `new` and `delete`](#item-50-understand-when-it-makes-sense-to-replace-new-and-delete)
* [Item 51: Adhere to convention when writing `new` and `delete`](#item-51-adhere-to-convention-when-writing-new-and-delete)
* [Item 52: Write placement `delete` if you write placement `new`](#item-52-write-placement-delete-if-you-write-placement-new)

### Item 49: Understand the behavior of the new-handler

When `operator new` can't satisfy a memory allocation request, it throws an exception. Before `operator new` throws an exception in response to an unsatisfiable request for memory, it calls a client- specifiable error-handling function called a *new-handler*. To specify the out-of-memory-handling function, clients call `set_new_handler`, a standard library function declared in <new>:

```cpp
typdef void(*new_handler) ();
new_handler std::set_new_handler(new_handler p) throw();
```

A well-designed new-handler function must do one of the following:
* **Make more memory available**: For example, allocate a large block of memory at program start-up, then release it for use in the program the first time the new-handler is invoked.
* **Install a different new-handler**: `set_new_handler`.
* **Deinstall the new-handler**: pass the null pointer to `set_new_handler`. With no new-handler installed, `operator new` will throw an exception when memory allocation is unsuccessful.
* **Throw an exception**: of type `bad_alloc` or some type derived from `bad_alloc`. Such exceptions will not be caught by `operator new`, so they will propagate to the site originating the request for memory.
* **Not return**: typically by calling `abort` or `exit`.

*Curiously recurring template pattern* (CRTP) consists in inheriting from a template class and use the derived class itself as a template parameter of the base class.

#### Things to Remember
* `set_new_handler` allows you to specify a function to be called when memory allocation requests cannot be satisfied.
* Nothrow `new` is of limited utility, because it applies only to memory allocation; subsequent constructor calls may still throw exceptions.

### Item 50: Understand when it makes sense to replace `new` and `delete`

Most common reasons to replace the compiler-provided versions of `operator new` or `operator delete`:
* **To detect usage errors**: memory leaks.
* **To improve efficiency**.
* **To collect usage statistics**

#### Things to Remember
* There are many valid reasons for writing custom versions of `new` and `delete`, including improving performance, debugging heap usage errors, and collecting heap usage information.

### Item 51: Adhere to convention when writing `new` and `delete`

#### Things to Remember
* `operator new` should contain an infinite loop trying to allocate memory, should call the new- handler if it can't satisfy a memory request, and should handle requests for zero bytes. Class-specific versions should handle requests for larger blocks than expected.
* `operator delete` should do nothing if passed a pointer that is null. Class-specific versions should handle blocks that are larger than expected.

### Item 52: Write placement `delete` if you write placement `new`

When an `operator new` function takes extra parameters, that function is known as a *placement* version of `new`. Placement `delete` is called *only* if an exception arises from a constructor call that's coupled to a call to a placement `new`. Applying `delete` to a pointer never yields a call to a placement version of `delete`. *Never*.

#### Things to Remember
* When you write a placement version of `operator new`, be sure to write the corresponding placement version of `operator delete`. If you don't, your program may experience subtle, intermittent memory leaks.
* When you declare placement versions of `new` and `delete`, be sure not to unintentionally hide the normal versions of those functions.
