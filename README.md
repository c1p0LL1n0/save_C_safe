Ihar Vauchok

# safe-c-library

Полностью безопасная замена стандартной библиотеки C — без `malloc`, без переполнений буфера, без падений на `NULL`.

**99 програмных тестов** — каждый краевой случай проверен.

## Структура

| Файл | Назначение | Строк |
|------|-----------|-------|
| `safe_c.c` | Безопасные строки, память, itoa, классификация | 676 |
| `safe_c_demo.c` | Визуальная демонстрация (40+ тестов) | ~280 |
| `safe_c_verify.c` | Программный авто-верификатор (64 теста) | 200 |
| `safe_stdlib.c` | Врапперы stdlib: арифметика, atoi, вектор, файлы, snprintf, qsort, malloc | 259 |
| `safe_stdlib_demo.c` | Визуальная демонстрация stdlib (20+ тестов) | ~115 |
| `safe_stdlib_verify.c` | Программный авто-верификатор stdlib (35 тестов) | 90 |

## Быстрый старт

```bash
git clone https://github.com/c1p0LL1n0/safe-c-library.git
cd save_C_safe

# Авто-тесты (главное!)
gcc -Wall -Wextra -std=c99 safe_c_verify.c -o verify && ./verify
gcc -Wall -Wextra -std=c99 safe_stdlib_verify.c -o verify2 && ./verify2

# Визуальные демо
gcc -Wall -Wextra -std=c99 safe_c_demo.c -o demo && ./demo
gcc -Wall -Wextra -std=c99 safe_stdlib_demo.c -o demo2 && ./demo2
```

## Структура `t_safe_str`

```c
typedef struct s_safe_str {
    char        *ptr;           // pointer to buffer
    const int   max_capacity;   // limit (readonly)
    int         len;            // current stringlength
} t_safe_str;

// Constructer without malloc — compaund litteral on stack
#define MAKE_SAFE_STR(size) \
    (t_safe_str){ .ptr = (char[size]){0}, .max_capacity = size, .len = 0 }
```
## API: `safe_c.c` — 31 function

### String operations

| Function | Analog | Return |
|---------|--------|------------|
| `ft_strlen_safe(s)` | `strlen` | `int` — O(1) |
| `ft_strcpy_safe(dest, src)` | `strcpy` | `t_safe_str*` |
| `ft_strncpy_safe(dest, src, n)` | `strncpy` | `t_safe_str*` |
| `ft_strcat_safe(dest, src)` | `strcat` | `t_safe_str*` |
| `ft_strlcat_safe(dest, src, n)` | `strlcat` | `t_safe_str*` |
| `ft_strjoin_safe(dest, s1, s2)` | — | `t_safe_str*` |
| `ft_strdup_safe(dest, src)` | `strdup` | `t_safe_str*` |
| `ft_strclear_safe(s)` | — | `t_safe_str*` |

### Compare

| Function | Return |
|---------|-----------|
| `ft_strcmp_safe(a, b)` | `<0`, `0`, `>0` |
| `ft_strncmp_safe(a, b, n)` | `<0`, `0`, `>0` |
| `ft_strequ_safe(a, b)` | `1`/`0` |

### Search

| Function | Analog |
|---------|--------|
| `ft_strchr_safe(s, c)` | `strchr` |
| `ft_strrchr_safe(s, c)` | `strrchr` |
| `ft_strstr_safe(hay, needle)` | `strstr` |

### Substrings and transformation

| Function | Notes |
|---------|-----------|
| `ft_substr_safe(dest, src, start, len)` | |
| `ft_strtrim_safe(dest, src, set)` | `set=NULL` → spaces |
| `ft_toupper_safe(s)` | in-place |
| `ft_tolower_safe(s)` | in-place |
| `ft_striteri_safe(s, callback)` | in-place |

### Memory

| Function | Analog |
|---------|--------|
| `ft_memset_safe(d, c, n)` | `memset` |
| `ft_memcpy_safe(d, s, n)` | `memcpy` |
| `ft_memmove_safe(d, s, n)` | `memmove` |
| `ft_bzero_safe(d)` | `bzero` |

### Numbers

`ft_itoa_safe(n, dest)` → `t_safe_str*`

### Symbols

`ft_isalpha` · `ft_isdigit` · `ft_isalnum` · `ft_isascii` · `ft_isprint` · `ft_isspace` · `ft_toupper` · `ft_tolower`

### Strings
## API: `safe_stdlib.c` — 18 функций

All return `1` (OK) or `0` (error), result with pointer.

### Safe arythmetic

| Function | Check |
|---------|----------|
| `ft_add_safe(a, b, &r)` | `a + b` without overflow |
| `ft_sub_safe(a, b, &r)` | `a - b` without overflow |
| `ft_mul_safe(a, b, &r)` | `a * b` without overflow |
| `ft_div_safe(a, b, &r)` | `b != 0`, not `INT_MIN / -1` |
| `ft_mod_safe(a, b, &r)` | `b != 0`, not `INT_MIN % -1` |
| `ft_long_to_int_safe(n, &r)` | `n` enough for `int` |

### String conversion

| Function | Analog | Return |
|---------|--------|---------|
| `ft_atoi_safe(str, &r)` | `atoi` | 1/0 |
| `ft_strtoi_safe(str, &end, &r)` | `strtol` | 1/0 |
| `ft_atol_safe(str, &r)` | `atol` | 1/0 |

### Dynamical vector

```c
typedef struct s_safe_vec {
    void *data; int elem_size, capacity, len, error;
} t_safe_vec;
```

`ft_vec_new(sz, cap)` · `ft_vec_get(v, i)` · `ft_vec_push(v, e)` · `ft_vec_pop(v)` · `ft_vec_free(v)`

### Files / formatting / memory

`ft_fopen_safe` · `ft_fgets_safe` · `ft_getline_safe` · `ft_snprintf_safe` · `ft_qsort_safe` · `ft_malloc_safe` · `ft_calloc_safe` · `ft_free_safe`

## Example

```c
#define SAFE_C_STDIO
#include "safe_stdlib.c"

int main(void) {
    t_safe_str name = MAKE_SAFE_STR(64);
    ft_strcpy_safe(&name, "World");

    t_safe_str greeting = MAKE_SAFE_STR(128);
    ft_strjoin_safe(&greeting, "Hello, ", name.ptr);
    printf("%s\n", greeting.ptr);

    int val;
    if (ft_atoi_safe("12345", &val))
        printf("Parsed: %d\n", val);

    int result;
    if (ft_mul_safe(1000000, 3000, &result))
        printf("1000000*3000 = %d\n", result);
    else
        printf("Overflow prevented!\n");

    return (0);
}
```

## Safety

- **NULL-safe** — no one  return `NULL`
- **Bound-check** — no one byte writes in `max_capacity`
- **Always `\0`** — guaranted 0 terminated
- **No malloc** в `safe_c.c` — only stack letters
- **O(1) Len** — field `len` in structure
- **Compound literals** — buffers on the fly: `&(t_safe_str){.ptr=(char[32]){0}, .max_capacity=32}`
- **Error nums** — `safe_stdlib.c`: 1 = success, 0 = fault

## Compilation

```bash
gcc -Wall -Wextra -std=c99 safe_c_verify.c -o verify
```

Only `#include "safe_c.c"` or `#include "safe_stdlib.c"` — without external dependencies.
## ⚠️ Discleimer

Use with caution. This library is a fun project written in C. The author provides no guarantees against bugs, security vulnerabilities, or undefined behavior. Please conduct your own code audit before deploying this library to any production environment.
Although the library has successfully passed 99 programmatic tests (64 for safe_c, 35 for safe_stdlib) and handles all known edge cases, absolute safety cannot be guaranteed due to the inherent complexities of the C programming language and platform-specific variances.

## License (MIT)

```
MIT License

Copyright (c) 2026 c1p0LL1n0

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```
TL;DR: Do whatever you want with the code—use it, modify it, redistribute it. Just preserve the authorship notice. Offered without warranties.

How to Contribute

Pull requests are highly encouraged! Areas that could use improvement:
• 🐛 Found a bug? — Open an issue and attach a minimal reproducible example (MRE).
• 🔧 New Features — Implement safe wrappers for remaining risky functions (strtok, realpath, system, etc.).
• 🧪 Testing — Write test cases covering new edge cases or establish fuzz testing.
• 📦 Build Systems — Add comprehensive CMakeLists.txt or Makefile integration.
• 🌐 Safe Networking — Implement security-centric wrappers around socket, bind, and connect.
• 🧵 Thread Safety — Provide thread-safe _r variants for applicable routines.
Coding Style: Absolutely no heap memory allocation (malloc) inside safe_c.c. Mandatory NULL pointers and boundary checking are required for all contributions. Every return value must carry explicit meaning (no void routines).



Ihar Vauchok written with AI and love <3
