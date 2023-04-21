# inline

`inline` is usually used in `class member function` like this:

```cpp
// InlineDemo.h
class InlineDemo {
public:
    inline void Test() {
        // do something
    }
};
```

The `inline` before `void Test` can be omitted when the definition is in the `class`.

If you try to definite an `inline` function outside of a class, you may write the definition in the `.h` or `.cpp` file.

You can put the definition in a `header` file outside of the `class`, but cannot in a `source` file.

```cpp
// InlineDemo.h
class InlineDemo {
public:
    inline void Test();
};

inline void InlineDemo::Test() {
    // do something
}
```

If in a `source` file like this:

!!! bug "You will get a link error if you invoke inline in another source file."

```cpp
// InlineDemo.h
class InlineDemo {
public:
    inline void Test();
};

// InlineDemo.cpp
inline void InlineDemo::Test() {
    // do something
}
```

If the definition of an `inline` function was in a `source` file, other `source` cannot invoke this function, you will got a `unresolved external` error from the linker.

According to the [Where to put inline keyword](https://isocpp.org/wiki/faq/inline-functions#where-to-put-inline-keyword), the best practice is putting the `inline` in `definition`, don't put in the declaration within class body:

```cpp
// InlineDemo.h
class InlineDemo {
public:
    void Test();  // Don't put inline here
};

inline void InlineDemo::Test() {  // Put inline here
    // do something
}
```

> Reference:
>
> - [Must the definition of a C++ inline functions be in the same file?](https://stackoverflow.com/questions/9338152/must-the-definition-of-a-c-inline-functions-be-in-the-same-file)
> - [Where to put inline keyword](https://isocpp.org/wiki/faq/inline-functions#where-to-put-inline-keyword)