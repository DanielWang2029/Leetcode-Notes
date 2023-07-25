
# C++ coding tips

## Core

### Templete T

T serve as a templete for type, and the compiler will internally generate and add the corresponding function with the correct type according to input type. This reduces the need to write the same code for different data types.

```cpp
template <class T> T myMax(T x, T y)
{
    return (x > y) ? x : y;
}

myMax<int>(3, 7);  // call myMax for int
myMax<double>(3.0, 7.0);  // call myMax for double
myMax<char>('g', 'e');  // call myMax for char
```

Note that keyword ***class*** and ***typename*** are interchangeable (in general).

## Class

### Initialize a class

```cpp
class cls {
    public:
        int left, right, mid;
        // constructors, other public vars and fns goes here
    private:
        // private vars and fns goes here
};
```

### Constructor of a class

Use ***this*** keyword to refer the to-be-contructed object.

```cpp
public:
    cls(int l, int r, int m) {
        this->left = l;
        this->right = r;
        this->mid = m;
        // additional things
    }
```

which is equivalent to

```cpp
public:
    cls(int l, int r, int m) : left(l), right(r), mid(m) {
        // additional things
    }
```

### Named Constructor Idiom

We know that if the input data types are different, we should have different constructors. However, there might be cases where we want to create two different constructors that have the same input data type. However, we cannot do that because all constructor are functions named after the class and there cannot exist two functions with the same name and identical input types. In this case, we want to declear all constructor (named after the class) in ***private*** or ***protected*** sections, and provide ***public static*** methods (not named after the class) that return a object of the class type.

```cpp
class Point {
public:
    static Point rectangular(float x, float y) {return Point(x, y);};  // Rectangular
    static Point polar(float radius, float angle) {return Point(radius * std::cos(angle), radius * std::sin(angle));};  // Polar
private:
    Point(float x, float y) : x_(x), y_(y) {};     // Rectangular coordinates
    float x_, y_;
};

Point p1 = Point::rectangular(5.7, 1.2);
Point p2 = Point::polar(5.7, 1.2);
```

## Vector

### Sort a vector

```cpp
vector<int> vec = {2, 4, 3, 1, 5};
std::sort(vec.begin(), vec.end());  // {1, 2, 3, 4, 5}
```

### Find max of a vector

```cpp
vector<int> vec = {4, 5, 2, 3, 1};
int max = *std::max_element(vec.begin(), vec.end());  // 5
```

### Form a subvector from vector

```cpp
vector<int> vec = {1, 2, 3, 4, 5};
vector<int> subvec1(vec.begin() + 2, vec.begin() + 4);  // {3, 4}
vector<int> subvec1(&vec[0], &vec[3]);  // {1, 2, 3}
```

### Circular shift a vector

```cpp
vector<int> vec = {1, 2, 3, 4, 5, 6, 7};
int shift = 3;
std::reverse(vec.begin(), vec.end() - shift);  // {1, 2, 3, 4, 7, 6, 5}
std::reverse(vec.end() - shift, vec.end());  // {4, 3, 2, 1, 7, 6, 5}
std::reverse(vec.begin(), vec.end());  // {5, 6, 7, 1, 2, 3, 4}
```
