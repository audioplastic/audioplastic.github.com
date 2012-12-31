---
layout: post
title: "Matlab-like profiling in C++ using RAII"
date: 2012-12-31 15:59
comments: true
categories: 
---

I've always liked the ability to rapidly profile code snippets in Matlab using the 'tic' and 'toc' commands. I wanted to develop something equally as simple to use in C++ for rapid tests. It turns out that a particularly elegant way to do this is to use RAII techniques, allowing easy profiling of any commands within a scope. A time marker is made on construction, and another time marker is made on destruction, and then compared to the original time marker.

The new 'chrono' classes bundles in the C++11 standard library make this task particularly simple. Below is the full class for simple tic toc type profiling ...

```c++
// TicToc RAII profiler
class TicToc
{
private:
    typedef std::chrono::high_resolution_clock clock;
    typedef std::chrono::microseconds res;
    clock::time_point t1, t2;
    
public:
    TicToc() {
        t1 = std::chrono::high_resolution_clock::now();
    }
    
    ~TicToc()
    {
        t2 = clock::now();
        std::cout << "Elapsed time is "
        << std::chrono::duration_cast<res>(t2-t1).count()/1e6
        << " seconds.\n"; 
    }
};
```

Putting this to use is very simple. Just make a new scope using the scope resolution operator and create an instance of TicToc. When it goes out of scope, it will be deleted from the stack and will display the elapsed time. 


```c++
{
    TicToc t;
    someFunctionToProfile();
    perhapsSomethingElse();
}
```

Enjoy and HAPPY NEW YEAR!