---
layout: post
title:  "信息奥赛一本通函数题库解析-4: 绝对素数"
date:   2024-07-03 01:16:39 +0800
categories: olympiad-prime-numbers
---

原题链接: [](http://ybt.ssoier.cn:8088/problem_show.php?pid=1153)

![](https://raw.githubusercontent.com/jamiesun/images/master/default/p8ZvfS.png)

## 解题思路

题目要求找出所有二位绝对素数。一个绝对素数是指它和它的数字对换后的数都是素数。比如 13 和 31 都是素数，所以 13 是绝对素数。

### 步骤

1. **判断一个数是否为素数**：编写一个函数 `is_prime` 来判断一个数是否为素数。
2. **判断一个数是否为绝对素数**：编写一个函数 `is_absolute_prime` 来判断一个数和它的数字对换后的数是否都是素数。
3. **遍历所有两位数**：从 10 到 99 遍历每个数，检查它们是否为绝对素数，并输出结果。

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

// 函数：检查两个数是否为绝对素数
bool is_absolute_prime(int num) {
    int reversed_num = (num % 10) * 10 + (num / 10); // 对换数字位置
    return is_prime(num) && is_prime(reversed_num); // 判断原数和对换后的数是否都是素数
}

int main() {
    // 遍历所有两位数
    for (int i = 10; i <= 99; ++i) {
        if (is_absolute_prime(i)) {
            cout << i << endl; // 输出绝对素数
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

2. **`is_absolute_prime` 函数**：用于判断一个数及其数字对换后的数是否都是素数。首先将数字对换位置，然后使用 `is_prime` 函数检查两个数是否为素数。

    ```cpp
    bool is_absolute_prime(int num) {
        int reversed_num = (num % 10) * 10 + (num / 10);
        return is_prime(num) && is_prime(reversed_num);
    }
    ```

3. **主程序**：遍历所有两位数（10 到 99），对于每个数，使用 `is_absolute_prime` 函数检查其是否为绝对素数。如果是，则输出该数。

    ```cpp
    int main() {
        for (int i = 10; i <= 99; ++i) {
            if (is_absolute_prime(i)) {
                cout << i << endl;
            }
        }
        return 0;
    }
    ```

### 知识点总结

1. **素数判断**：通过判断一个数是否能被 2 到 \(\sqrt{num}\) 之间的数整除来判断其是否为素数。
2. **数字对换**：通过取余和除法运算对换两位数的数字。
3. **遍历和条件判断**：遍历所有两位数并使用条件判断来筛选绝对素数。

以上代码和讲解展示了如何有效地找出所有二位绝对素数并按要求输出结果。