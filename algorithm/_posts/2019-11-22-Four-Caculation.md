---
title: "Four caculation"
date: 2019-11-22
image: /assets/img/blog/IMG_0269.JPG
description: >
  BigInteger Caculate
author: author2
comments: true
---

大数问题归类....

# 减法

```c++
#include<iostream>
#include<cstdio>
#include<string>
#include<cstring>
#define m 9000
using namespace std;
int main() 
{
  int x[m],y[m];
  char b[m],c[m];
  int i,s,r,t,z; 
  gets(b);
  gets(c);
  s=strlen(b);
  r=strlen(c);
  if(s>r) t=s;
  else t=r;
  for(int i=0;i<s;i++) x[i]=b[s-i-1]-'0';
  for(int i=0;i<r;i++) y[i]=c[r-i-1]-'0';
  for(int i=0;i<t;i++)
  {
    x[i]-=y[i];
    if(x[i]<0)
    {
      x[i+1]--;
      x[i]+=10;
    }
  }
  int z1=t;
  while(x[z1]==0&&z1!=0) z1--;
  for(int i=z1;i>=0;i--) { 
    cout<<x[i];
  }
}
```



# 乘法

```c++
class Solution {
public:
  string multiply(string num1, string num2) {
    int l1 = num1.length();
    int l2 = num2.length();
    if (num1 == "0" || num2 == "0")
      return "0";
    string res(l1 + l2, '0');
    for (int i = l1 - 1; i >= 0; i--)
    {
      int step = 0;
      for (int j = l2 - 1; j >= 0; j--)
      {
        int mul = (num1[i] - '0') * (num2[j] - '0');
        int sum = res[i+j+1] - '0' + step + mul % 10;
        res[i+j+1] = sum % 10 + '0';
        step = sum / 10 + mul / 10;
      }
      res[i] += step;
    }
    for (int i = 0; i < l1 + l2; i++)
    {
      if (res[i] != '0')
        return res.substr(i);
    }
    return "0";
  }
}
```



# 最小公倍数

```c++
int main() {
    bool prime[MAX];

    //找出1-10000的质数
    for (int i = 0; i < MAX; i++) {
        prime[i] = true;
    }
    for (int i = 2; i < MAX; i++) {
        if (!prime[i]) continue;
        for (int j = i+1; j < MAX; j++) {
            if (j%i == 0) prime[j] = false;
        }
    }
    int n;
    cin >> n;

    //分解质因数，找出最大对应最大幂
    int mi[MAX];
    for (int i = 0; i <= n; i++) {
        mi[i] = 0;
    }
    int count = 0;
    for (int i = 2; i <= n; i++) {
        int k = i;
        for (int j = 2; j <= n; j++) {
            if (!prime[j]) continue;
            count = 0;
            while (k%j == 0) {
                k = k/j;
                count++;
            }
            if (count > mi[j]) mi[j] = count;
            if (k == 0) break;
        }
    }

    //每个质数最大幂相乘，即为最小公倍数
    int ans = 1;
    for (int i = 2; i <= n; i++) {
        if (!prime[i]) continue;
        for (int j = 0; j < mi[i]; j++) {
            ans = (ans*i)%MOD;
        }
    }
    cout << ans << endl;
    return 0;
}
```



