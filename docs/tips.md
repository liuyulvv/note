---
comments: true
---

# Tips

- Qt save `*.ui` files in a different path(`ui`) with `src`, to make this you should update `CMakeLists.txt` with:

  ```cmake
  set(CMAKE_AUTOUIC_SEARCH_PATHS ${PROJECT_SOURCE_DIR}/ui)
  ```

- `clang-tidy` check file:

  ```bash
  clang-tidy -format-style=file -header-filter=.* -p build src/*.cpp -- -I ./include
  ```

- `clang-format` format file:

  ```bash
  clang-format -style=file -i src/*.cpp
  ```