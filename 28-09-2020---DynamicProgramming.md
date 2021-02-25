---
title: Dynamic Programming Problems Collection. 
date: "2020-09-28T14:19:44.012Z"
template: "post"
draft: false
slug: "Dp Problems"
category: "CP"
tags:
  - "Guide"
  - "Cp"
  - "Competative Programming"
description: "My Collection of standered Dp problems."
socialImage: "/media/dp_thumb.png"
---

## Index
<style >
    ul li p {
        margin: 0!important;
        font-size: 1rem!important;
    }
    ul li a {
        font-size: 1rem!important;
    }
    ul {
        list-style: decimal;
    }
</style>

- [Wild Card Matching](#wild-card-matching)
  - [Hint](#hint)
  - [Code](#code)
- [Kardane's Alogrithm - Maximum Subarray sum](#kardanes-alogrithm---maximum-subarray-sum)
  - [Problem](#problem)
  - [Solution](#solution)
- [Edit Distance](#edit-distance)
  - [Problem](#problem-1)
  - [Solution](#solution-1)
- [Maximum Rectangle](#maximum-rectangle)
  - [Problem](#problem-2)
  - [Hint](#hint-1)
  - [Solution](#solution-2)
- [Decode Ways](#decode-ways)
  - [Problem](#problem-3)
  - [Hint](#hint-2)
  - [Solution](#solution-3)
- [NOTE](#note)
- [Continuous Subarray Sum](#continuous-subarray-sum)
  - [Problem](#problem-4)
  - [Hint](#hint-3)
- [Remove boxes](#remove-boxes)
  - [Problem](#problem-5)
  - [Note](#note-1)
- [Alien Dictionary](#alien-dictionary)
  - [Problem](#problem-6)
  - [Hint](#hint-4)
  - [Warn](#warn)


# Wild Card Matching

Problem : Give a string and a pattern containg wildcards ?, *; Check weather the patter matches the string.

## Hint
1. 2D dp problem - (pattern + 1) x (string + 1). 
2. Initialize the dp 0th row and column.
3. Check conditions baised on pattern
   *, ?, and character
4. Derive the formula.

## Code

```cpp
bool match(string s, string p){
    int n = s.length();
    int m = p.length();
    int dp[n+1][m+1];
    // initialize 0,0 as true because empty pattern and text are always matching.
    dp[0][0] = 1;
    // initialize i,0 {i ∈ [1, n]} as false because when the pattern is empty and there is some text then the vlaues should always be false
    for(int i = 1; i <= n; i++) dp[i][0] = 0;
    // initalize 0,j {j ∈ [1,m]} as false if p[x] ≠ * otherwise dp[0][j-1] because of the following reasons
    // consider a pattern * it matches anything of any length
    // if the text is empty, pattern * should match the text.
    
    for(int j = 1; j <= m; j++) dp[0][j] = (p[j-1] == '*') ? dp[0][j-1] : 0;

    for(int i = 1; i <= n; i++){
        for(int j = 1; j <= m; j++){
            // p[x] = s[x] or p[x] matches anything then true
            if(p[j-1] == s[i-1] || p[j-1] == '?'){
                dp[i][j] = dp[i-1][j-1];
            }else if(p[j-1] == '*'){
                dp[i][j] = dp[i-1][j] | dp[i][j-1];
            }else{
                dp[i][j] = 0;
            }
        }
    }
    return dp[n][m] == 1;
}
```

# Kardane's Alogrithm - Maximum Subarray sum

## Problem 
   + Given an array find it's maximum contagious sum.

## Solution
   + two variable dp.


```cpp
long long find(vector<int> &arr){
    long long sum_so_far = -1e18, sum_here = 0;
    for(int i = 0; i < n; i++){
        sum_here = sum_here + arr[i];
        if(sum_here > sum_so_far){
            sum_so_far = sum_here;
        }
        if(sum_here < 0){
            sum_here = 0;
        }
    }
    return sum_so_far;
}
```

# Edit Distance

## Problem 
   + Given two strings a, b find the minimum number of operations (insert, replace, delete) on characters to make them equal.

## Solution

```cpp
class Solution {
public:
    int minDistance(string w2, string w1) {
        int n = w1.length();
        int m = w2.length();
        
        if(!n & !m) return 0;
        else if(!n) return 1;
        else if(!m) return 1;
        
        int dp[n][m];
        dp[0][0] = (w1[0] == w2[0]) ? 0 : 1;
        
        for(int i = 1, al = 1; i < n; i++){
            if(w1[i] == w2[0] && al){
                dp[i][0] = dp[i-1][0];   
                al = 0;
            }else{
                dp[i][0] = dp[i-1][0] + 1;       
            }
        }
        
        for(int i = 1, al = 1; i < m; i++){
            if(w1[0] == w2[i] && al){
                dp[0][i] = dp[0][i-1]; 
                al = 0;
            }else{
                dp[0][i] = dp[0][i-1] + 1;
            }
        }
        
        for(int i = 1; i < n; i++){
            for(int j = 1; j < m; j++){
                if(w1[i] == w2[j]){
                    dp[i][j] = dp[i-1][j-1];
                }else{
                    dp[i][j] = min(dp[i-1][j-1], min(dp[i][j-1], dp[i-1][j])) + 1;
                }
            }
        }
        
        return dp[n-1][m-1];
    }
};
```

# Maximum Rectangle

## Problem 
   + Given a grid of 0's and 1's find the rectangle containing the maximum no of 1's.
   + ![example](https://assets.leetcode.com/uploads/2020/09/14/maximal.jpg) <br/> answer : 6

## Hint
   + Use maximum area under histogram with dp

## Solution

```cpp
class Solution {
private:
    int maxArea(int *heights, int n){
        if(n == 0)
        {
            return 0;
        }
        // for each index i we will store the indices till where the right < heights[i] > left 
        int left[n];
        int right[n];
        left[0]=-1;
        right[n-1]=n;
        for(int i=1;i<n;i++)
        {
            int p=i-1;
            while(p>=0 && heights[p]>=heights[i])
            {
                p=left[p];
            }
            left[i]=p;
        }
        for(int i=n-2;i>=0;i--)
        {
            int p=i+1;
            while(p<n && heights[p]>=heights[i])
            {
            p=right[p];    
            }
            right[i]=p;
        }
       int area=0;
        for(int i=0;i<n;i++)
        {
            area=max(area, heights[i]*(right[i]-left[i]-1));
        }
        return area;
    }
public:
    int maximalRectangle(vector<vector<char>>& g) {
        int n = g.size();
        if(n == 0 || g[0].empty()) return 0;
        int m = g[0].size();
        
        int dp[m+10];
        for(int i = 0; i < m; i++){
            dp[i] = 0;
        }
        
        int ans = g[0][0] == '1';
        // for each n rows make a histogram 
        for(int i = 0; i < n; i++){
            for(int j = 0; j < m; j++){
                if(g[i][j] == '1'){
                    dp[j] += 1;
                }else{
                    dp[j] = 0;
                }
            }
            ans = max(ans, maxArea(dp, m));
        }
        
        return ans;
    }
};
```

# Decode Ways

## Problem
    A message containing letters from A-Z is being encoded to numbers using the following mapping:

    'A' -> 1
    'B' -> 2
    ...
    'Z' -> 26
    Given a non-empty string containing only digits, determine the total number of ways to decode it.

    The answer is guaranteed to fit in a 32-bit integer.

## Hint
   + Isomorphic tree problem.
   + Fibonacci Sequence.

## Solution

```cpp
class Solution {
public:
    int numDecodings(string s) {
        int n = s.length();
        for(auto &x : s){
            x-='0';
        }
        int dp[n+1];
        for(int i = 2; i <= n; i++){
            dp[i] = 0;
        }
        dp[0] = 1;
        dp[1] = (s[0] >= 1) ? 1 : 0;

        for(int i = 2; i <= n; i++){
            int d = s[i-1];
            int dd = s[i-2] * 10 + d;

            if(d >= 1) dp[i] += dp[i - 1];
            
            if(dd >= 10 && dd <= 26) dp[i] += dp[i - 2];
        }

        return dp[n];
    }
}
```

### NOTE
    Number of unique btree of n nodes is catalan number. 
        c(n) = 1/(n+1) * (2n)! / n!

# Continuous Subarray Sum

## Problem
  + Given a list of non-negative numbers and a target integer k, write a function to check if the array has a continuous subarray of size at least 2 that sums up to a multiple of k, that is, sums up to n*k where n is also an integer.

## Hint
   + Math
   + prefix sum : p0, p1, p2, .... pn
       + suppose  i to j be the indices which has a sum that is divisible by k.
       + then, sum(i, j) = p(j) - p(i) and sum(i, j) % k = 0
       + (p(j)-p(i))%k = 0
       + p(j) % k = p(i) % k;
       + so return true if p(x) are equal for any two values

# Remove boxes

## Problem
Given several boxes with different colors represented by different positive numbers.
You may experience several rounds to remove boxes until there is no box left. Each time you can choose some continuous boxes with the same color (composed of k boxes, k >= 1), remove them and get k*k points.
Find the maximum points you can get.

### Note
    solve https://leetcode.com/problems/decode-ways-ii/submissions/

# Alien Dictionary 
   + link - https://codeforces.com/contest/1424/problem/M

## Problem
 Given a list of words in a dictionary. Construct the sequence of character of language 

## Hint
    Use tropological sort.

### Warn
    faster io.
    for n == 1 k == 1 print all the same a to z if char is present in the word.


### NOTE - COOKOFF OCT
    1) To make the sum of array not divisible by it's length put 2, 1 alternatively.
       1) It can be proved as follows
          size array      rem
          1    2          1
          2    2, 1       1
          3    2, 1, 2    2
    2) GCD of two consecutive no is 1; if an array or sub-array contain 1 then gcd is 1;