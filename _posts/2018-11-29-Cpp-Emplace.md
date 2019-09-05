---
layout: post
title: C++ 中 emplace 的特点
excerpt: 本文介绍 emplace emplace_back 等操作在 C++ 中的用处
date: 2018-11-29 13:24:33
categories: C/C++
tags: C/C++
author: Garaguru
mathjax: true
---

* content
{:toc}

现在许多的容器都支持了 如 emplace emplace_back 等方法，那 emplace 的优势在哪里呢？

以下面这段代码为例子：
```c++
vector<pair<int,int>> example;

// push 等非 emplace 的方法都是直接放进去一个容器的对象，必须先构造好了对象再将其移动到容器中
example.push_back(make_pair(1,2));
// emplace 将直接在对应的容器的位置构造对象
example.emplace_back(1,2);
```

以下是一段测试的代码：
```c++
#include <vector> 
#include <map> 
#include <string> 
#include <iostream> 
using namespace std;  
 
struct Complicated  
{  
         int year;  
         double country;  
         std::string name;  
 
         Complicated(int a, double b, string c):year(a),country(b),name(c)  
         {  
                  cout<<"is constucted"<<endl;  
         }  
 
         Complicated(const Complicated&other):year(other.year),county(other.  
             county),name(std::move(other.name))  
         {  
                  cout<<"is moved"<<endl;  
         }  
};  
 
int main()  
{  
         std::map<int, Complicated> m;  
         int anInt = 4;  
         double aDouble = 5.0;  
         std::string aString = "C++";  
         cout<<"—insert--"<<endl;  
         m.insert(std::make_pair(4, Complicated(anInt, aDouble, aString)));  
 
         cout<<"—emplace--"<<endl;  
         // should be easier for the optimizer  
         m.emplace(4, Complicated(anInt, aDouble, aString));  
 
         cout<<"--emplace_back--"<<endl;  
         vector<Complicated> v;  
         v.emplace_back(anInt, aDouble, aString);  
         cout<<"--push_back--"<<endl;  
         v.push_back(Complicated(anInt, aDouble, aString));  
} 
```