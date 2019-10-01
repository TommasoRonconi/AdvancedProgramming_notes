# Advanced Programming - 2018/11/27

To count how many headers we have used in some folder:
```bash
find . \( -name '*.cc' -o -name '*.h' \) -exec grep 'include' | gawk '!x[$2]++'
```

- **`<chrono>` contains the highest resolution clock on the market**: use this when possible
- `<cstdlib>` contains the functions for backward compatibility with `C`

**DO NOT re-invent the wheel**

note that containers are a pain in the ass, they're the reason why in vectors definition
this
```c++
vector<int> a{3};
```
is different from this
```c++
vector<int> a(3);
```

Multi-threading is supported **natively** in c++, supported if compiling with `-pthread`, functions are included in the headers of `stdlib` named **concurrency**:
- `<atomic>`
- `<condition_variable>`
- `<future>`
- `<thread>`