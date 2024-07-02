---
layout: post
title: "信息奥赛一本通函数题库解析-4: 回文三位数"
date: 2024-07-03 01:24:37 +0800
categories: algorithms olympiad functions
---


原题链接: [](http://ybt.ssoier.cn:8088/problem_show.php?pid=1155)

![](https://raw.githubusercontent.com/jamiesun/images/master/default/F9bxon.png)

## 解题思路

题目要求找出所有既是回文数又是素数的三位数。一个回文数是指从左到右读和从右到左读都相同的数。一个素数是指除了 1 和自身外没有其他因数的自然数。

### 步骤

1. **判断一个数是否为素数**：编写一个函数 `is_prime` 来判断一个数是否为素数。
2. **判断一个数是否为回文数**：编写一个函数 `is_palindrome` 来判断一个数是否为回文数。
3. **遍历所有三位数**：从 100 到 999 遍历每个数，检查它们是否既是回文数又是素数，并输出结果。

### 代码实现

以下是实现上述步骤的 C++ 代码：

```cpp
#include <iostream>
#include <cmath>
using namespace std;

// 函数：判断一个数是否为素数
bool is_prime(int num) {
    if (num <= 1) return false; // 1 及以下的数不是素数
    for (int i = 2; i <= sqrt(num); ++i) {
        if (num % i == 0) return false; // 如果找到因子，则不是素数
    }
    return true; // 如果没有找到因子，则是素数
}

// 函数：判断一个数是否为回文数
bool is_palindrome(int num) {
    int original = num, reversed = 0;
    while (num > 0) {
        reversed = reversed * 10 + num % 10;
        num /= 10;
    }
    return original == reversed;
}

int main() {
    // 遍历所有三位数
    for (int i = 100; i <= 999; ++i) {
        if (is_prime(i) && is_palindrome(i)) {
            cout << i << endl; // 输出既是回文数又是素数的三位数
        }
    }

    return 0;
}
```

### 代码讲解

1. **`is_prime` 函数**：用于判断一个数是否为素数。若数小于或等于1，则返回 `false`。否则，通过遍历从 2 到 \(\sqrt{num}\) 的所有整数，检查 `num` 是否能被整除。如果能被整除，则返回 `false`；否则返回 `true`。

    ```cpp
    bool is_prime(int num) {
        if (num <= 1) return false;
        for (int i = 2; i <= sqrt(num); ++i) {
            if (num % i == 0) return false;
        }
        return true;
    }
    ```

2. **`is_palindrome` 函数**：用于判断一个数是否为回文数。通过将数 `num` 反转并与原数 `original` 比较，若相同则为回文数。

    ```cpp
    bool is_palindrome(int num) {
        int original = num, reversed = 0;
        while (num > 0) {
            reversed = reversed * 10 + num % 10;
            num /= 10;
        }
        return original == reversed;
    }
    ```

3. **主程序**：遍历所有三位数（100 到 999），对于每个数，使用 `is_prime` 和 `is_palindrome` 函数检查其是否既是回文数又是素数。如果是，则输出该数。

    ```cpp
    int main() {
        for (int i = 100; i <= 999; ++i) {
            if (is_prime(i) && is_palindrome(i)) {
                cout << i << endl;
            }
        }
        return 0;
    }
    ```

### 知识点总结

1. **素数判断**：通过判断一个数是否能被 2 到 \(\sqrt{num}\) 之间的数整除来判断其是否为素数。
2. **回文数判断**：通过反转数字并与原数字比较来判断其是否为回文数。
3. **遍历和条件判断**：遍历所有三位数并使用条件判断来筛选既是回文数又是素数的数。

以上代码和讲解展示了如何有效地找出所有既是回文数又是素数的三位数并按要求输出结果。