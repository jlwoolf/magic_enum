```text
 __  __             _        ______                          _____
|  \/  |           (_)      |  ____|                        / ____|_     _
| \  / | __ _  __ _ _  ___  | |__   _ __  _   _ _ __ ___   | |   _| |_ _| |_
| |\/| |/ _` |/ _` | |/ __| |  __| | '_ \| | | | '_ ` _ \  | |  |_   _|_   _|
| |  | | (_| | (_| | | (__  | |____| | | | |_| | | | | | | | |____|_|   |_|
|_|  |_|\__,_|\__, |_|\___| |______|_| |_|\__,_|_| |_| |_|  \_____|
               __/ |
              |___/
```

[![Github releases](https://img.shields.io/github/release/Neargye/magic_enum.svg)](https://github.com/Neargye/magic_enum/releases)
[![Conan package](https://img.shields.io/badge/Conan-package-blueviolet)](https://conan.io/center/magic_enum)
[![Vcpkg package](https://img.shields.io/badge/Vcpkg-package-blueviolet)](https://github.com/microsoft/vcpkg/tree/master/ports/magic-enum)
[![Build2 package](https://img.shields.io/badge/Build2-package-blueviolet)](https://www.cppget.org/magic_enum?q=magic_enum)
[![License](https://img.shields.io/github/license/Neargye/magic_enum.svg)](LICENSE)
[![Try online](https://img.shields.io/badge/try-online-blue.svg)](https://wandbox.org/permlink/JPMZqT9mgaUdooyC)
[![Compiler explorer](https://img.shields.io/badge/compiler_explorer-online-blue.svg)](https://godbolt.org/z/BxfmsH)

If you like this project, please consider donating to one of the funds that help victims of the war in Ukraine: https://www.stopputin.net/.

# Magic Enum C++

Header-only C++17 library provides static reflection for enums, work with any enum type without any macro or boilerplate code.

* `enum_cast` obtains enum value from string or integer.
* `enum_value` returns enum value at specified index.
* `enum_values` obtains enum value sequence.
* `enum_count` returns number of enum values.
* `enum_integer` obtains integer value from enum value.
* `enum_name` returns name from enum value.
* `enum_names` obtains string enum name sequence.
* `enum_entries` obtains pair (value enum, string enum name) sequence.
* `enum_index` obtains index in enum value sequence from enum value.
* `enum_contains` checks whether enum contains enumerator with such value.
* `enum_type_name` returns name of enum type.
* `enum_fuse` allows multidimensional switch/cases.
* `enum_switch` allows runtime enum value transformation to constexpr context.
* `enum_for_each` calls a function with all enum constexpr value.
* `is_unscoped_enum` checks whether type is an [Unscoped enumeration](https://en.cppreference.com/w/cpp/language/enum#Unscoped_enumeration).
* `is_scoped_enum` checks whether type is an [Scoped enumeration](https://en.cppreference.com/w/cpp/language/enum#Scoped_enumerations).
* `underlying_type` improved UB-free "SFINAE-friendly" [underlying_type](https://en.cppreference.com/w/cpp/types/underlying_type).
* `ostream_operators` ostream operators for enums.
* `bitwise_operators` bitwise operators for enums.

## Documentation

* [Reference](doc/reference.md)
* [Limitations](doc/limitations.md)
* [Integration](#Integration)

## Features

* C++17
* Header-only
* Dependency-free
* Compile-time
* Enum to string
* String to enum
* Iterating over enum

## [Examples](example/example.cpp)

```cpp
// For example color enum.
enum class Color { RED = 2, BLUE = 4, GREEN = 8 };
```

* Enum value to string

  ```cpp
  Color color = Color::RED;
  auto color_name = magic_enum::enum_name(color);
  // color_name -> "RED"
  ```

* String to enum value

  ```cpp
  std::string color_name{"GREEN"};
  auto color = magic_enum::enum_cast<Color>(color_name);
  if (color.has_value()) {
    // color.value() -> Color::GREEN
  }
  ```

* Integer to enum value

  ```cpp
  int color_integer = 2;
  auto color = magic_enum::enum_cast<Color>(color_integer);
  if (color.has_value()) {
    // color.value() -> Color::RED
  }
  ```

* Indexed access to enum value

  ```cpp
  std::size_t i = 1;
  Color color = magic_enum::enum_value<Color>(i);
  // color -> Color::BLUE
  ```

* Enum value sequence

  ```cpp
  constexpr auto colors = magic_enum::enum_values<Color>();
  // colors -> {Color::RED, Color::BLUE, Color::GREEN}
  // colors[0] -> Color::RED
  ```

* Number of enum elements

  ```cpp
  constexpr std::size_t color_count = magic_enum::enum_count<Color>();
  // color_count -> 3
  ```

* Enum value to integer

  ```cpp
  Color color = Color::RED;
  auto color_integer = magic_enum::enum_integer(color);
  // color -> 2
  ```

* Enum names sequence

  ```cpp
  constexpr auto color_names = magic_enum::enum_names<Color>();
  // color_names -> {"RED", "BLUE", "GREEN"}
  // color_names[0] -> "RED"
  ```

* Enum entries sequence

  ```cpp
  constexpr auto color_entries = magic_enum::enum_entries<Color>();
  // color_entries -> {{Color::RED, "RED"}, {Color::BLUE, "BLUE"}, {Color::GREEN, "GREEN"}}
  // color_entries[0].first -> Color::RED
  // color_entries[0].second -> "RED"
  ```

* Enum fusion for multi-level switch/case statements

  ```cpp
  switch (magic_enum::enum_fuse(color, direction).value()) {
    case magic_enum::enum_fuse(Color::RED, Directions::Up).value(): // ...
    case magic_enum::enum_fuse(Color::BLUE, Directions::Down).value(): // ...
  // ...
  }
  ```
  
* Enum switch runtime value as constexpr constant
  ```cpp
  Color color = Color::RED;
  
  magic_enum::enum_switch([] (auto val) {
    constexpr Color c_color = val;
    // ...
  }, color);
  ```

* Enum iterate for each enum as constexpr constant
  ```cpp
  magic_enum::enum_for_each<Color>([] (auto val) {
    constexpr Color c_color = val;
    // ...
  });
  ```

* Ostream operator for enum

  ```cpp
  using namespace magic_enum::ostream_operators; // out-of-the-box ostream operators for enums.
  Color color = Color::BLUE;
  std::cout << color << std::endl; // "BLUE"
  ```

* Bitwise operator for enum

  ```cpp
  enum class Flags { A = 1 << 0, B = 1 << 1, C = 1 << 2, D = 1 << 3 };
  using namespace magic_enum::bitwise_operators; // out-of-the-box bitwise operators for enums.
  // Support operators: ~, |, &, ^, |=, &=, ^=.
  Flags flags = Flags::A | Flags::B & ~Flags::C;
  ```

* Checks whether type is an [Unscoped enumeration](https://en.cppreference.com/w/cpp/language/enum#Unscoped_enumeration).

  ```cpp
  enum color { red, green, blue };
  enum class direction { left, right };

  magic_enum::is_unscoped_enum<color>::value -> true
  magic_enum::is_unscoped_enum<direction>::value -> false
  magic_enum::is_unscoped_enum<int>::value -> false

  // Helper variable template.
  magic_enum::is_unscoped_enum_v<color> -> true
  ```

* Checks whether type is an [Scoped enumeration](https://en.cppreference.com/w/cpp/language/enum#Scoped_enumerations).

  ```cpp
  enum color { red, green, blue };
  enum class direction { left, right };

  magic_enum::is_scoped_enum<color>::value -> false
  magic_enum::is_scoped_enum<direction>::value -> true
  magic_enum::is_scoped_enum<int>::value -> false

  // Helper variable template.
  magic_enum::is_scoped_enum_v<direction> -> true
  ```

* Static storage enum variable to string
  This version is much lighter on the compile times and is not restricted to the enum_range [limitation](doc/limitations.md).

  ```cpp
  constexpr Color color = Color::BLUE;
  constexpr auto color_name = magic_enum::enum_name<color>();
  // color_name -> "BLUE"
  ```

## [Examples (enum_map)](example/example_enum_map.cpp)

* Create an enum_map and specif a values.

  ```cpp
  magic_enum::enum_map<Color, int, std::map> map;
  map[Color::RED] = 1;
  // map[Color::RED] -> 1, map[Color::BLUE] -> 0, map[Color::GREEN] -> 0
  ```

* Create an enum_map and pass initial values.
  
  ```cpp
  magic_enum::enum_map<Color, int, std::map> map(1, 2, 3);
  // map[Color::RED] -> 1, map[Color::BLUE] -> 2, map[Color::GREEN] -> 3
  ```

* Create an enum_map using an iterable.

  ```cpp
  std::vector<int> values = {1, 2, 3};
  magic_enum::enum_map<Color, int, std::map> map(values);
  // map[Color::RED] -> 1, map[Color::BLUE] -> 2, map[Color::GREEN] -> 3

  std::vector<std::pair<Color, int>> v_values = {
      {Color::RED, 1}, {Color::GREEN, 3}, {Color::BLUE, 2}};
  magic_enum::enum_map<Color, int, std::map> v_map(v_values);
  // v_map[Color::RED] -> 1, v_map[Color::BLUE] -> 2, v_map[Color::GREEN] -> 3
  ```

* Create an enum_map using an initializer list.
  
  ```cpp
  magic_enum::enum_map<Color, int, std::map> map = {
      {Color::RED, 1}, {Color::BLUE, 2}, {Color::GREEN, 3}};
  // map[Color::RED] -> 1, map[Color::BLUE] -> 2, map[Color::GREEN] -> 3
  ```

* enum_map's are created using all mapped_types defaults, so
  constructors do not require all values for instantiation, and will still
  work if values exceed total number of enums.

  ```cpp
  magic_enum::enum_map<Color, int, std::map> map(1, 2);
  // map[Color::RED] -> 1, map[Color::BLUE] -> 2, map[Color::GREEN] -> 0
  ```

  ```cpp
  std::vector<int> values = {2, 4, 8, 16};
  magic_enum::enum_map<Color, int, std::map> map(values);
  // map[Color::RED] -> 2, map[Color::BLUE] -> 4, map[Color::GREEN] -> 8

  std::vector<std::pair<Color, int>> v_values = {{Color::BLUE, 2}};
  magic_enum::enum_map<Color, int, std::map> v_map(v_values);
  // v_map[Color::RED] -> 0, v_map[Color::BLUE] -> 2, v_map[Color::GREEN] -> 0
  ```

  ```cpp
  magic_enum::enum_map<Color, int, std::map> map = {
      {Color::RED, 1}, {Color::GREEN, 3}};
  // map[Color::RED] -> 1, map[Color::BLUE] -> 0, map[Color::GREEN] -> 3
  ```

* Maps can be iterated.
  
  ```cpp
  magic_enum::enum_map<Color, int, std::map> map = {
      {Color::RED, 1}, {Color::BLUE, 2}, {Color::GREEN, 3}};

  for (auto [color, value] : map)
  {
      std::cout << magic_enum::enum_name(color) << " " << value << " ";
  }
  std::cout << std::endl;

  // prints:
  // RED 1 BLUE 2 GREEN 3
  ```
  
* Values can be inserted into maps the same ways as constructors.
  Iterators are necessary when value position is unknown. 

  ```cpp
  magic_enum::enum_map<Color, int, std::map> map;
  map.insert(map.begin(), 1);
  // map[Color::RED] -> 1, map[Color::BLUE] -> 0, map[Color::GREEN] -> 0

  map.insert(std::pair<Color, int>{Color::BLUE, 2});
  // map[Color::RED] -> 1, map[Color::BLUE] -> 2, map[Color::GREEN] -> 0
  ```

  ```cpp
  magic_enum::enum_map<Color, int, std::map> map;
  map.insert(map.begin(), 1, 2, 4, 8);
  // map[Color::RED] -> 1, map[Color::BLUE] -> 2, map[Color::GREEN] -> 4

  map.insert(std::pair<Color, int>{Color::BLUE, 1}, std::pair<Color, int>{Color::GREEN, 1});
  // map[Color::RED] -> 1, map[Color::BLUE] -> 1, map[Color::GREEN] -> 1
  ```

  ```cpp
  std::vector<int> values = {2, 4, 8, 16};
  magic_enum::enum_map<Color, int, std::map> map;
  map.insert(map.begin(), values);
  // map[Color::RED] -> 2, map[Color::BLUE] -> 4, map[Color::GREEN] -> 8

  std::vector<std::pair<Color, int>> v_values = {{Color::BLUE, 2}, {Color::GREEN, 2}};
  map.insert(v_values);
  // map[Color::RED] -> 2, map[Color::BLUE] -> 2, map[Color::GREEN] -> 2
  ```

  ```cpp
  magic_enum::enum_map<Color, int, std::map> map;
  map.insert(map.begin(), {1, 2, 3, 4});
  // map[Color::RED] -> 1, map[Color::BLUE] -> 2, map[Color::GREEN] -> 3

  map.insert({{Color::RED, 3}, {Color::GREEN, 1}});
  // map[Color::RED] -> 1, map[Color::BLUE] -> 2, map[Color::GREEN] -> 3
  ```

* Here is a final more detailed implementation with a custom data structure.
  
  ```cpp
  struct RGB
  {
      int r, g, b;
  };

  magic_enum::enum_map<Color, RGB, std::map> color_map = {
      {Color::RED, {255, 0, 0}},
      {Color::GREEN, {0, 255, 0}},
      {Color::BLUE, {0, 0, 255}}};

  color_map[Color::RED].r = 200;
  color_map.insert({Color::BLUE, {0, 0, 225}});

  // map[Color::RED] -> RGB(200, 0, 0),
  // map[Color::GREEN] -> RGB(0, 255, 0),
  // map[Color::BLUE] -> RGB(0, 0, 225)
  ```

* Below is an example with `std::unordered_map` and a general `map_type`. In order to use  a custom map type it needs to:
  
  1.  have a template of form `map_type<key_type, mapped_type, Args...>`
  2.  be iterable (have an `iterator` and `const_iterator` type)
  3.  have a `value_type` container that stores both `key_type` & `mapped_type` and works with `std::get<T>` (like `std::pair` & `std::tuple`)
  
   <br>

  ```cpp
  typedef u_enum_map magic_enum::enum_map<Color, int, std::unordered_map>;
  /* u_enum_map::enum_type -> enum Color
   * u_enum_map::mapped_type -> int
   * u_enum_map::value_type -> struct std::pair<enum Color, int>
   *
   * u_enum_map::map_type ->
   *      class std::unordered_map<enum Color, int>
   */    
  ```

  ```cpp
  typedef custom_map magic_enum::enum_map<Color, int, map_type, arg1, arg2>;
  /* custom_map::enum_type -> enum Color
   * custom_map::mapped_type -> int
   * custom_map::value_type -> map_type::value_type
   *
   * magic_enum::enum_map<Color, int, map_type, arg1, arg2>::map_type ->
   *      class map_type<enum Color, int, arg1, arg2> 
   */    
  ```

## Remarks

* `magic_enum` does not pretend to be a silver bullet for reflection for enums, it was originally designed for small enum.

* Before use, read the [limitations](doc/limitations.md) of functionality.

## Integration

* You should add the required file [magic_enum.hpp](include/magic_enum.hpp).

* If you are using [vcpkg](https://github.com/Microsoft/vcpkg/) on your project for external dependencies, then you can use the [magic-enum package](https://github.com/microsoft/vcpkg/tree/master/ports/magic-enum).

* If you are using [Conan](https://www.conan.io/) to manage your dependencies, merely add `magic_enum/x.y.z` to your conan's requires, where `x.y.z` is the release version you want to use.

* If you are using [Build2](https://build2.org/) to build and manage your dependencies, add `depends: magic_enum ^x.y.z` to the manifest file where `x.y.z` is the release version you want to use. You can then import the target using `magic_enum%lib{magic_enum}`.

* Alternatively, you can use something like [CPM](https://github.com/TheLartians/CPM) which is based on CMake's `Fetch_Content` module.

  ```cmake
  CPMAddPackage(
      NAME magic_enum
      GITHUB_REPOSITORY Neargye/magic_enum
      GIT_TAG x.y.z # Where `x.y.z` is the release version you want to use.
  )
  ```

* Bazel is also supported, simply add to your WORKSPACE file:

  ```
  http_archive(
      name = "magic_enum",
      strip_prefix = "magic_enum-<commit>",
      urls = ["https://github.com/Neargye/magic_enum/archive/<commit>.zip"],
  )
  ```

  To use bazel inside the repository it's possible to do:

  ```
  bazel build //...
  bazel test //...
  bazel run //:example
  ```

  (Note that you must use a supported compiler or specify it with `export CC= <compiler>`.)

* If you are using [Ros](https://www.ros.org/), you can include this package by adding `<depend>magic_enum</depend>` to your package.xml and include this package in your workspace. In your CMakeLists.txt add the following:
  ```cmake
  find_package(magic_enum CONFIG REQUIRED)
  ...
  target_link_libraries(your_executable magic_enum::magic_enum)
  ```

## Compiler compatibility

* Clang/LLVM >= 6
* MSVC++ >= 14.11 / Visual Studio >= 2017
* Xcode >= 10
* GCC >= 9
* MinGW >= 9

## Licensed under the [MIT License](LICENSE)
