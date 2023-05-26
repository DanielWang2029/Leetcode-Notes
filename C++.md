
# C++ coding tips

## Vector

### Sort a vector

```cpp
vector<int> vec = {2, 4, 3, 1, 5};
std::sort(vec.begin(), vec.end());  // {1, 2, 3, 4, 5}
```

### Find max of a vector

```cpp
vector<int> vec = {2, 4, 3, 1, 5};
int max = *std::max_element(vec.begin(), vec.end());  // 5
```
