---
layout: post
title:  "信息奥赛一本通函数题库解析-2: 素数个数"
date:   2024-07-03 00:36:24 +0800
categories: 奥赛 信息奥赛 函数题库
---

原题链接: [素数个数](http://ybt.ssoier.cn:8088/problem_show.php?pid=1151)

![](https://raw.githubusercontent.com/jamiesun/images/master/default/qzMvZi.png)

## 解题思路

这道题目要求我们计算从 2 到 n（其中 n 的最大值为 50000）之间的素数个数。素数是指除了 1 和自身以外不再有其他因数的自然数。解决这个问题我们可以使用 **埃拉托色尼筛法（Sieve of Eratosthenes）**，这是一种高效的计算素数的算法。

### 埃拉托色尼筛法解释

埃拉托色尼筛法是一种古老的算法，用于寻找一个范围内的所有素数。其基本思想是从 2 开始，将每个素数的倍数标记为合数（即非素数）。具体步骤如下：

1. **初始化**：创建一个布尔数组 `is_prime`，大小为 n+1，并将所有元素初始化为 `true`。`is_prime[i]` 表示数 i 是否为素数。
2. **标记合数**：从 2 开始，对于每个数 i，如果 `is_prime[i]` 为 `true`，则将 i 的所有倍数标记为 `false`。
3. **计数素数**：遍历数组 `is_prime`，统计值为 `true` 的元素个数，这些元素对应的索引即为素数。

### 算法的时间复杂度

埃拉托色尼筛法的时间复杂度为 \(O(n \log \log n)\)，对于 n 最大值为 50000 的情况，效率是非常高的。

### 代码实现

下面是使用埃拉托色尼筛法计算从 2 到 n 范围内素数个数的代码：

```cpp
#include <iostream>
#include <algorithm> // 引入算法库以使用 fill_n
using namespace std;

// 函数：使用埃拉托色尼筛法计算素数的个数
int count_primes(int n) {
    if (n < 2) return 0; // 如果 n 小于 2，则没有素数

    // 创建布尔数组并初始化
    bool is_prime[n + 1];
    fill_n(is_prime, n + 1, true); // 初始化数组为 true

    is_prime[0] = is_prime[1] = false; // 0 和 1 不是素数

    // 使用埃拉托色尼筛法
    for (int i = 2; i * i <= n; ++i) {
        if (is_prime[i]) {
            for (int j = i * i; j <= n; j += i) {
                is_prime[j] = false; // 标记 i 的倍数为非素数
            }
        }
    }

    // 统计素数的个数
    int count = 0;
    for (int i = 2; i <= n; ++i) {
        if (is_prime[i]) {
            count++; // 如果是素数，计数加一
        }
    }

    return count; // 返回素数的总个数
}

int main() {
    int n;
    cin >> n; // 输入 n

    int prime_count = count_primes(n); // 计算素数的个数

    cout << prime_count << endl; // 输出素数的个数

    return 0;
}
```

### 代码解析

1. **初始化布尔数组**：`bool is_prime[n + 1];` 创建一个大小为 n+1 的布尔数组，并使用 `fill_n(is_prime, n + 1, true);` 初始化所有元素为 `true`。
2. **特殊处理 0 和 1**：`is_prime[0] = is_prime[1] = false;` 将 0 和 1 标记为非素数。
3. **筛选素数**：使用两层循环，外层循环从 2 到 \(\sqrt{n}\)，内层循环从 i\(^2\) 开始，将每个素数 i 的所有倍数标记为 `false`。
4. **统计素数**：最后遍历数组 `is_prime`，统计所有为 `true` 的元素个数。

这样就能高效地计算从 2 到 n 之间的素数个数并输出结果。
