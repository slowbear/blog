---
title: "Slowy的弟弟模板(待完成)"
author: Slowy
date: 2021-04-19
update: 2021-04-29
#description:
#image: 
categories: algorithm
#tags:
---

# 基础

## Header

```cpp
#include <bits/stdc++.h>
using namespace std;
using LL = long long;
using ULL = unsigned long long;
#define rep(i, a, b) for (auto i = (a); i < (b); ++i)
#define rev(i, a, b) for (auto i = (b - 1); i >= (a); --i)
#define grep(i, u) for (auto i = gh[u]; i != -1; i = gn[i])
#define SZ(V) static_cast<int>(V.size())
#define mem(x, v) memset(x, v, sizeof(x))
#define cpy(x, y) memcpy(x, y, sizeof(x))
```

## 快速I/O

```cpp
namespace IO {
// 关闭流同步
void unsync() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  cout.tie(nullptr);
}
template <typename T>
void write_real(T x) {
  if (x > 9) write_real(x / 10);
  putchar(x % 10 + 48);
}
template <typename T>
inline void write(T x) {
  if (x < 0) putchar('-'), x = -x;
  write_real(x);
}
template <typename T>
inline void read(T &x) {
  x = 0;
  T sgn = 1;
  char c = getchar();
  for (; !isdigit(c); c = getchar())
    if (c == '-') sgn = -1;
  for (; isdigit(c); c = getchar()) x = (x << 1) + (x << 3) + (c ^ 48);
  x *= sgn;
}
};  // namespace IO
```

# 搜索

## DLX

```cpp
namespace DLX {
// 结点个数
constexpr int MAX_NUM = 10100;

int U[MAX_NUM], D[MAX_NUM], L[MAX_NUM], R[MAX_NUM];

int first[MAX_NUM], cnt[MAX_NUM], row[MAX_NUM], col[MAX_NUM], total;

void remove(int c) {
  L[R[c]] = L[c];
  R[L[c]] = R[c];
  for (int i = D[c]; i != c; i = D[i]) {
    for (int j = R[i]; j != i; j = R[j]) {
      U[D[j]] = U[j];
      D[U[j]] = D[j];
      --cnt[col[j]];
    }
  }
}

void recover(int c) {
  for (int i = U[c]; i != c; i = U[i]) {
    for (int j = L[i]; j != i; j = L[j]) {
      U[D[j]] = D[U[j]] = j;
      ++cnt[col[j]];
    }
  }
  R[L[c]] = L[R[c]] = c;
}

void build(int /*r */, int c) {
  rep(i, 0, c + 1) {
    L[i] = i - 1;
    R[i] = i + 1;
    U[i] = D[i] = i;
  }
  L[0] = c;
  R[c] = 0;
  mem(first, 0);
  mem(cnt, 0);
  total = c + 1;
}

void insert(int r, int c) {
  row[total] = r;
  col[total] = c;
  ++cnt[c];
  D[total] = D[c];
  U[D[c]] = total;
  U[total] = c;
  D[c] = total;
  if (first[r]) {
    L[total] = first[r];
    R[total] = R[first[r]];
    R[first[r]] = L[R[total]] = total;
  } else {
    first[r] = L[total] = R[total] = total;
  }
  ++total;
}

int ans[MAX_NUM];

void choose(int /*r */) {
  // 处理选择第r行
}

bool dance(int dep = 0) {
  if (!R[0]) {
    rep(i, 0, dep) choose(ans[i]);
    return true;
  }
  int x = R[0];
  for (int i = R[x]; i; i = R[i]) {
    if (cnt[i] < cnt[x]) x = i;
  }
  remove(x);
  for (int i = D[x]; i != x; i = D[i]) {
    ans[dep] = row[i];
    for (int j = R[i]; j != i; j = R[j]) remove(col[j]);
    if (dance(dep + 1)) return true;
    for (int j = L[i]; j != i; j = L[j]) recover(col[j]);
  }
  recover(x);
  return false;
}
}  // namespace DLX
```

# 计算几何

## 基本运算

```cpp
namespace Geometry {
constexpr double eps = 1e-7;

struct Point {
  double x, y;
  Point operator+(const Point &other) const {
    return {x + other.x, y + other.y};
  }
  Point operator-(const Point &other) const {
    return {x - other.x, y - other.y};
  }
  Point operator*(double factor) const { return {x * factor, y * factor}; }
  Point operator/(double factor) const { return {x / factor, y / factor}; }
  Point rotate() const { return {-y, x}; }
  // 点积
  double operator*(const Point &other) const {
    return x * other.x + y * other.y;
  }
  // 叉积
  double operator^(const Point &other) const {
    return x * other.y - y * other.x;
  }
  double length() const { return sqrt(*this * *this); }
};

// 夹角sin
double sin_theta(const Point &p1, const Point &p2) {
  return (p1 ^ p2) / p1.length() / p2.length();
}

// 夹角cos
double cos_theta(const Point &p1, const Point &p2) {
  return (p1 * p2) / p1.length() / p2.length();
}

// 线段交点
Point cross(const Point &u1, const Point &v1, const Point &u2,
            const Point &v2) {
  Point a = v1 - u1, b = v2 - u2, u = v1 - v2;
  return v1 - a * (u ^ b) / (a ^ b);
}

struct Circle {
  Point o;
  double r;
  bool include(const Point &p) const { return (p - o).length() <= r + eps; }
};

// 三点圆
Circle generate_circle(const Point &x, const Point &y, const Point &z) {
  Point a = y - x, b = z - y, m1 = (x + y) / 2, m2 = (y + z) / 2;
  Point o = cross(m1, m1 + a.rotate(), m2, m2 + b.rotate());
  double r = (z - x).length() / fabs(sin_theta(a, b)) / 2;
  return {o, r};
}
}  // namespace Geometry
```

# 数学

## 快速幂

```cpp
namespace Math {
inline int quick_pow(int a, int b, int p) {
  int result = 1;
  for (; b; b >>= 1, a = static_cast<LL>(a) * a % p) {
    if (b & 1) result = static_cast<LL>(result) * a % p;
  }
  return result;
}
}  // namespace Math
```

# DP

## 单调栈

## 单调队列

## 四边形不等式

## 插头DP

## 动态DP

# 数据结构

## 线段树

## 函数式Treap

# 图论

## 网络流

### Dinic

### ISAP
