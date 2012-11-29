---
layout: post
title: "C++11 is poetic"
date: 2012-11-29 16:13
comments: true
categories: 
---

I will start posting cool little blocks of code, like short poems when I see neat little things to do ..

```c++ Find multiples in a vector the C++11 way
#include <iostream>
#include <vector>
using namespace std;

int main ()
{
	vector<int> v{12,324,45,65,787,9}; //Uniform initialization
	cout << "In vector: "; 
	for (auto val : v) {cout << val << ", ";} //Range based for
	int m = 5;
	cout<< "there are " 
		<< count_if(v.begin(), v.end(), [m](int i){return i%m == 0;}) //lambdas
		<< " multiples of " << m << endl;
}

```

{% blockquote %}
In vector: 12, 324, 45, 65, 787, 9, there are 2 multiples of 5
{% endblockquote %}