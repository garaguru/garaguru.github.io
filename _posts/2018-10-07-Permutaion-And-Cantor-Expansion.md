---
layout: post
title: 康托展开与全排列问题详解
excerpt: 本文讲解康托展开和全排列问题，包括一个简单的 next_permutation 实现
date: 2018-10-07 14:56:43
categories: 算法
tags: 算法
author: Garaguru
mathjax: true
---

* content
{:toc}

## 康托展开 Cantor Expansion
$X=a_n*(n-1)!+a_{n-1}*(n-2)!+...+a_i*(i-1)!+...+a_2*1!+a_1*0!$

含义：
> X 表示这是第几个排列，$a_i$ 表示这个位置的数在当前未出现的的元素中排第几个，并且有 $0 \le a_i \le i, 1 \le i \le n$

## next_permutation 实现
```c++
  void nextPermutation(vector<int>& nums) {
    int i;
    for (i = nums.size() - 2; i >= 0; i--) {
      if (nums[i] < nums[i + 1]) break;
    }
    if (i < 0) {
      reverse(nums.begin(), nums.end());
      return;
    } else {
      int j;
      for (j = nums.size() - 1; j >= 0; j--) {
        if (nums[j] > nums[i]) break;
      }
      swap(nums[i], nums[j]);
      reverse(nums.begin() + i + 1, nums.end());
    }
  }
```
## 应用

### 题目：找出第 16 个 n = 5 的序列（12345）

1. 首先第十六个也就是要前面有15个数，要调用15次next_permutation函数。

2. 根据第一行的那个全排列公式，15 / 4! = 0 …15  =>  有0个数比他小的数是1，所以第一位是1

3. 拿走刚才的余数15，用15 / 3! = 2 …3   =>  剩下的数里有两个数比他小的是4（1已经没了），所以第二位是4

4. 拿走余数3， 用 3 / 2! = 1 …1   =>  剩下的数里有一个数比他小的是3，所以第三位是3

5. 拿走余数1， 用 1/  1! = 1 …0    =>  剩下的数里有一个数比他小的是 5（只剩2和5了），所以第四位是5

得出结果：1 4 3 5 2



### 第二类题：已知是 n = 5，求 14352 是它的第几个序列？

用刚才的那道题的反向思维：

1. 第一位是1，有0个数小于1，即0* 4！

2. 第二位是4，有2个数小于4，即2* 3！

3. 第三位是3，有1个数小于3，即1* 2！

4. 第四位是5，有1个数小于5，即1* 1！

5. 第五位是2，不过不用算，因为肯定是0

所以14352是 n = 5的第 0 + 12 + 2 + 1 + 0 = 15 + 1（求的是第几个，所以要加一） = 16
