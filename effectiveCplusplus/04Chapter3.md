# Chapter 3. Resource Management

Common resources include file descriptors, mutex locks, fonts and brushes in graphical user interfaces (GUIs), database connections, and network sockets.

If you allocate memory and never deallocate it, you've got memory leak.

## Table of contents
* [Item 13: Use objects to manage resources](#item-13-use-objects-to-manage-resources)
* [Item 14: Think carefully about copying behavior in resource-managing classes](#item-14-think-carefully-about-copying-behavior-in-resource-managing-classes)
* [Item 15: Provide access to raw resources in resource-managing classes](#item-15-provide-access-to-raw-resources-in-resource-managing-classes)
* [Item 16: Use the same form in corresponding uses of new and delete](#item-16-use-the-same-form-in-corresponding-uses-of-new-and-delete)
* [Item 17: Store `new`ed objects in smart pointers in standalone statements](#item-17-store-newed-objects-in-smart-pointers-in-standalone-statements)

### Item 13: Use objects to manage resources

`auto_ptr` is a pointer-like object (a smart pointer) whose destructor automatically calls `delete` on what it points to: `std::auto_ptr<Investment> pInv( createInvestment() );`. It's important that there never be more than one `auto_ptr` pointing to an object. Copying them sets them to null, and the copying pointer assumes sole ownership of the resource!

Critical aspects of using objects to manage resources:
* **Resources are acquired and immediately turned over to resource-managing objects**: the idea of using objects to manage resources is often called *Resource Acquisition Is Initialization* (RAII).
* **Resource-managing objects use their destructors to ensure that resources are released**: destructors are called automatically when an object is destroyed.

An alternative to `auto_ptr` is a *reference-counting smart pointer* (RCSP), a smart pointer that keeps track of how many objects point to a particular resource and automatically deletes the resource when nobody is pointing to it any longer. RCSPs can't break cycles of references.

`auto_ptr` and `tr1::shared_ptr` use `delete` in their destructors, not `delete []`. That means that using `auto_ptr` or `TR1::shared_ptr` with dynamically allocated arrays is a bad idea.

#### Things to Remember
* To prevent resource leaks, use RAII objects that acquire resources in their constructors and release them in their destructors.
* Two commonly useful RAII classes are `TR1::shared_ptr` and `auto_ptr`. `tr1::shared_ptr` is usually the better choice, because its behavior when copied is intuitive. Copying an `auto_ptr` sets it to null.

### Item 14: Think carefully about copying behavior in resource-managing classes

What should happen when an RAII object is copied?
* **Prohibit copying**
* **Reference-count the underlying resource** (`tr1::shared_ptr`)
* **Copy the underlying resource** ("deep copy")
* **Transfer ownership of the underlying resource** (`auto_ptr`)

#### Things to Remember
* Copying an RAII object entails copying the resource it manages, so the copying behavior of the resource determines the copying behavior of the RAII object.
* Common RAII class copying behaviors are disallowing copying and performing reference counting, but other behaviors are possible.

### Item 15: Provide access to raw resources in resource-managing classes

#### Things to Remember
* APIs often require access to raw resources, so each RAII class should offer a way to get at the resource it manages.
* Access may be via explicit conversion or implicit conversion. In general, explicit conversion is safer, but implicit conversion is more convenient for clients.

### Item 16: Use the same form in corresponding uses of new and delete

When you emply a `new` *expression* memory is allocated and one or more constructors are called for that memory. When you employ a `delete` *expression* one or more destructors are called for the memory, then the memory is deallocated.

If you use brackets in your use of `delete`, `delete` assumes an array is pointed to. Otherwise, it assumes that a single object is pointed to:
```cpp
std::string *string_ptr1 = new std::string;
std::string * string_ptr2 = new std::string[100];

delete string_ptr1;     // delete and object
delete [] string_ptr2;  // delete an array of objects
```

If you use `[]` in a `new` expression, you must use `[]` in the corresponding `delete` expression. If you don't use `[]` in a `new` expression, don't use `[]` in the matching `delete` expression.

This rule is also noteworthy for the `typedef`-inclined.

### Things to Remember
* If you use `[]` in a `new` expression, you must use `[]` in the corresponding `delete` expression. If you don't use `[]` in a `new` expression, you mustn't use `[]` in the corresponding `delete` expression.

### Item 17: Store `new`ed objects in smart pointers in standalone statements

#### Things to Remember
* Store `new`ed objects in smart pointers in standalone statements. Failure to do this can lead to subtle resource leaks when exceptions are thrown.
