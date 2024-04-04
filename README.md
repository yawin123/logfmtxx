# logfmtxx

Header-only C++23 structured logging library using the
[logfmt](https://brandur.org/logfmt) format.

## Installation

Just copy the `include/logfmtxx.hpp` in your project.

Or with [Shipp](https://github.com/linkdd/shipp), add it to your dependencies:

```json
{
  "name": "myproject",
  "version": "0.1.0",
  "dependencies": [
    {
      "name": "logfmtxx",
      "url": "https://github.com/linkdd/logfmtxx.git",
      "version": "v0.3.0"
    }
  ]
}
```

## Usage

First, include the relevant headers:

```cpp
#include <iostream> // if you wish to print logs with std::cout or std::cerr
#include <logfmtxx.hpp>
```

Then create a logger:

```cpp
auto logger = logfmtxx::logger{
  [](const std::string& message) {
    std::cout << message << std::endl;
  }
};
```

Or with a functor to avoid allocations:

```cpp
struct printer {
  void operator()(const std::string& message) {
    std::cout << message << std::endl;
  }
};
auto logger = logfmtxx::logger{printer{}};
```

Or with global fields:

```cpp
auto logger = logfmtxx::logger{
  [](const std::string& message) {
    std::cout << message << std::endl;
  },
  logfmtxx::field{"foo", 42},
  logfmtxx::field{"bar", 3.14}
};
```

Then, call the `log()` method:

```cpp
logger.log(logfmtxx::level::info, "hello world");
// or
logger.info("hello world");
```

The result should be:

```
time="2001-01-01T00:00:00Z" level=info message="hello world" foo=42 bar=3.14000
```

You can add extra fields as well:

```cpp
logger.log(
  logfmtxx::level::error,
  "internal server error",
  logfmtxx::field{"http.method", "GET"},
  logfmtxx::field{"http.path", "/"},
  logfmtxx::field{"http.status", 500}
);
```

The result should be:

```
time="2001-01-01T00:00:00Z" level=error message="internal server error" foo=42 bar=3.14000 http.method="GET" http.path="/" http.status=500
```

If your type implements `as_string()`, it can be used with a field:

```cpp
struct foo {
  std::string as_string() const {
    return "i am foo";
  }
};

logger.log(logfmtxx::level::info, "example", logfmtxx::field{"foo", foo{}});
```

## License

This software is released under the terms of the
[BSD 0-clause License](./LICENSE.txt).
