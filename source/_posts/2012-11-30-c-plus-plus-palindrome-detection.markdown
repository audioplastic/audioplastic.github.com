---
layout: post
title: "C++ palindrome detection"
date: 2012-11-30 16:11
comments: true
categories: 
---

An interesting question popped up on stackoverflow about palindrome detection in c++. A palindrome is a word whose characters can be reversed, and no change is detectable, e.g. "racecar". My initial thought was to make a copy and use the std::reverse algorithm . . . 

```c++
tupni = input;
std::reverse(tupni.begin(), tupni.end());

if(tupni==input){ ...
```

However, this copy can be avoided using reverse itterators . . 

```c++
bool isPalindrome(const std::string& str)
{
    // comparison based on 2 string iterators - a "normal" one
    // and a reversed one
    return std::equal(str.rbegin(), str.rend(), str.begin());
}
```

Seeing as this function only contains a single command, it might be nice to use a lambda as an alternative. You can specify a return type from a lambda with this syntax []()->return_type{} ...

```c++
auto isPalindrome = [](const std::string& str)->bool{return std::equal(str.rbegin(), str.rend(), str.begin());};
std::cout << isPalindrome("racecar") << std::endl; // prints 1
std::cout << isPalindrome("truck") << std::endl; // prints 0 
```


