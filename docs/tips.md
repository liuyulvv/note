# Tips

- Qt save `*.ui` files in a different path(`ui`) with `src`, to make this you should update `CMakeLists.txt` with:

```cmake
set(CMAKE_AUTOUIC_SEARCH_PATHS ${PROJECT_SOURCE_DIR}/ui)
```