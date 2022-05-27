# #️⃣ hashmap.h

[![Actions Status](https://github.com/sheredom/hashmap.h/workflows/CMake/badge.svg)](https://github.com/sheredom/hashmap.h/actions)
[![Build status](https://ci.appveyor.com/api/projects/status/1crw9uccf869aiss?svg=true)](https://ci.appveyor.com/project/sheredom/hashmap-h)
[![Sponsor](https://img.shields.io/badge/💜-sponsor-blueviolet)](https://github.com/sponsors/sheredom)

A simple one header hashmap implementation for C/C++.

## Usage

Just `#include "hashmap.h"` in your code!

The current supported compilers are gcc, clang and msvc.

The current supported platforms are Linux, macOS and Windows.

### Fundamental Design

The hashmap is made to work with UTF-8 string slices - sections of strings that
are passed with a pointer and an explicit length. The reason for this design
choice was that the hashmap is being used, by the author, to map symbols that
are already resident in memory from a source file of a programming language. To
save from causing millions of additional allocations to move these UTF-8 string
slices into null-terminated strings, an explicit length is always passed.

Note also that while the API passes char* pointers as the key - these keys are
never used with the C API string functions. Instead `memcmp` is used to compare
keys. This allows us to use UTF-8 strings in place of regular ASCII strings with
no additional code.

### Create a Hashmap

To create a hashmap call the `hashmap_create` function:

```c
const unsigned initial_size = 2;
const unsigned key_type = STRING_KEY;
struct hashmap_s hashmap;
if (0 != hashmap_create(initial_size, key_type, &hashmap)) {
  // error!
}
```

The `initial_size` parameter only sets the initial size of the hashmap - which
can grow if multiple keys hit the same hash entry. The initial size must be a
power of two, and creation will fail if it is not.

### Put Something in a Hashmap

To put an item in the hashmap use the `hashmap_put` function:

```c
int meaning_of_life = 42;
char question = 6 * 8;

if (0 != hashmap_put(&hashmap, "life", strlen("life"), 0, &meaning_of_life)) {
  // error!
}

if (0 != hashmap_put(&hashmap, "?", strlen("?"), 0, &question)) {
  // error!
}
```

Notice that multiple entries of _differing_ types can exist in the same hashmap.
The hashmap is not typed - it can store any `void*` data as the value for a key.

### Get Something from a Hashmap

To get an entry from a hashmap use the `hashmap_get` function:

```c
void* const element = hashmap_get(&hashmap, "x", strlen("x"), 0);
if (NULL == element) {
  // error!
}
```

The function will return `NULL` if the element is not found. Note that the key
used to get an element does not have to be the same pointer used to put an
element in the hashmap - but the string slice must match for a hit to occur.

### Remove Something from a Hashmap

To remove an entry from a hashmap use the `hashmap_remove` function:

```c
if (0 != hashmap_remove(&hashmap, "x", strlen("x"), 0)) {
  // error!
}
```

