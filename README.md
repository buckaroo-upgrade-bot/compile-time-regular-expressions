# Compile time regular expressions v2

[![Build Status](https://travis-ci.org/hanickadot/compile-time-regular-expressions.svg?branch=master)](https://travis-ci.org/hanickadot/compile-time-regular-expressions)

Fast compile-time regular expression with support for matching/searching/capturing in compile-time or runtime.

## Examples

#### Extracting number from input
```c++
std::optional<std::string_view> extract_number(std::string_view s) noexcept {
    using namespace ctre::literals;
    if (auto m = "^[a-z]++([0-9]++)$"_ctre.match(s)) {
        return m.get<1>().to_view();
    } else {
        return std::nullopt;
    }
}
```

#### Extracting values from date
```c++
struct date { std::string_view year; std::string_view month; std::string_view day; };

constexpr std::optional<date> extract_date(std::string_view s) noexcept {
    using namespace ctre::literals;
    if (auto [whole, year, month, day] = "^([0-9]{4})/([0-9]{1,2}+)/([0-9]{1,2}+)$"_ctre.match(s); whole
    ) {
        return date{year.to_view(), month.to_view(), day.to_view()};
    } else {
        return std::nullopt;
    }
}
static_assert(extract_date("2018/08/27"sv).has_value());
static_assert(extract_date("2018/08/27"sv)->year == "2018"sv);
static_assert(extract_date("2018/08/27"sv)->month == "08"sv);
static_assert(extract_date("2018/08/27"sv)->day == "27"sv);
```

#### Lexer
```c++
enum class type {
    unknown, identifier, number
};

struct lex_item {
    type t;
    std::string_view c;
};

constexpr std::optional<lex_item> lexer(std::string_view v) noexcept {
    using namespace ctre::literals;
    if (auto [m,id,num] = "^([a-z]++)|([0-9]++)$"_ctre.match(v); m) {
        if (id) {
            return lex_item{type::identifier, id};
        } else if (num) {
            return lex_item{type::number, num};
        }
    }
    return std::nullopt;
}
```

## Supported compilers

* clang 5.0+
* gcc 7.2+
