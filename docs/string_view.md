---
comments: true
---

# string_view

| [Original link](https://abseil.io/tips/1)

When create a function need to take a (constant) string as an argument, there are three common alternatives:

```cpp
void takeCharStr(const char* s);          // C
void takeString(const std::string& s);    // C++
void takeStringView(std::string_view s);  // C++ 17
```

`takeCharStr` and `takeString` work well if the caller has the string in the format, but when a conversion is needed(either from `const char*` to `std::string` or `std::string` to `const char*`), the caller have to do some other works.

Callers need to convert a `std::string` to `const char*` through `c_str()` function(efficient but inconvenient):

```cpp
void alreadyHasString(const std::string& s) {
    takeCharStr(s.c_str());  // explicit conversion
}
```

Callers don't need to do anything convert `const char*` to `std::string`, but will invoke the creation of a temporary `std::string`(convenient but inefficient) and copy the temporary string:

```cpp
void alreadyHasCharStr(const char* s) {
    takeString(s);  // creation and copy
}
```

`string_view` is added to C++ stand since `C++ 17`. It is a `view` of an existing character buffer. `string_view` consists of only a `pointer` and a `length`, identifying a section of character data that is not `owned` by the `string_view` and cannot be modified by the `view`. Consequently, making a copy of a `string_view` is a `shallow` operation no string data copied.

`string_view` has implicit conversion constructors from both `const char*` and `const std::string&`, and since `string_view` doesn’t copy, there is no O(n) memory penalty for making a hidden copy. In the case where a `const std::string&` is passed, the constructor runs in O(1) time. In the case where a `const char*` is passed, the constructor invokes a `strlen()` automatically (or you can use the two-parameter `string_view` constructor).

```cpp
void alreadyHasString(const std::string& s) {
    takeStringView(s);  // no explicit conversion; convenient!
}

void alreadyHasCharStr(const char* s) {
    takeStringView(s);  // no copy; efficient!
}
```

Because the `string_view` does not own its data, any strings pointed to by the `string_view` (just like a `const char*`) must have a known `lifespan`, and must outlast the `string_view` itself. This means that using `string_view` for storage is often questionable: you need some proof that the underlying data will outlive the `string_view`.

If your API only needs to reference the string data during a single call, and doesn’t need to modify the data, accepting a `string_view` is sufficient.

Changing parameters to pass by `string_view` can be inefficient if those are then passed to a function requiring a `std::string` or a `NUL-terminated` `const char*`.

Unlike other string types, you should pass `string_view` by value just like you would pass an `int` or a `double` because `string_view` is a small value.

Marking a `string_view` as `const` only affects whether the `string_view` object itself can be modified, and not whether it can be used to modify the underlying chars – it never can. This is exactly analogous to how a `const char*` can never be used to modify the chars, even if the pointer itself can be modified.

`string_view` is not necessarily `NUL-terminated`.