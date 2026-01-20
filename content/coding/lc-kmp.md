---
title: "LC - KMP alogirthms" 
date: 2026-01-15
tags: ["kmp"]
weight: -3805
---

Recently practicing hte KMP algorithm, and I think this is really interesting.

This alogirthm is a linear time method to find if a string `s` (say size m) is a substring of `t` (say size n).

A naive approach is to do comparison for each characters using two loops, and the complexity is `O(m*n)`, while using the KMP algorithm, we can reuse the property of string `s` by pre-building a LSP (longest suffix prefix) with linear time `O(m)`. In this case the comparison can be done in `O(n)`.

Here is how the LSP is build and used (LC 28):
# LC-28: Find substring
```c++
class Solution {
public:
    int strStr(string haystack, string needle) {
        int n = haystack.size();
        int m = needle.size();

        vector<int> lsp(m, 0);
        int len = 0;
        int i = 1;
        while(i < m) {
            // [0 .. len-1] == [i-len .. i-1]
            if(needle[len] == needle[i]) {
                ++len;
                lsp[i] = len;
                ++i;
            } else if(len == 0) {
                ++i;
            } else {
                len = lsp[len-1];
            }
        }

        i = 0;
        int j = 0;
        while(i < n) {
            if(needle[j] == haystack[i]) {
                ++i;
                ++j;
                if(j == m) return i-m;
            } else if(j == 0) {
                ++i;
            } else {
                j = lsp[j-1];
            }
        }
        return -1;


    }

    
};
```

In this code, `lsp[i]` means the longest suffix prefix in that range.

Take `AABAAC` for instance,
* when `A`, len = 0 (first one is always 0)
* whne `AA`, len = 1 (since idx1 == idx0)
* when `AAB`, len = lsp[0] = 0 (since idx2 != idx1)
* when `AABA`, len = 1 (since idx3 == idx0)
* when `AABAA`, len = 2 (since idx4 == idx1)
* when `AABAAC`, len = lsp[1] = 1 (since idx5 != idx2), len = 0 (since idx5 != idx0)

The interseting part is the time complexity, we may think that when we move forward, we might need to get backward for the lsp part and this is `O(m)`, so the time complexity is `O(m*m)`, but actually it is amortized `O(m)`, since you can think of moving forward as putting coins into a bucket, and when we move backward, we at most take all the coins out of the bucket, so overvall the moving for forward and backward would not be more than `m`.

## LC-1392: Find substring
This problem is the same as the KMP above, and we can simply implement it.
```c++
class Solution {
public:
    string longestPrefix(string s) {
        int n = s.size();
        vector<int> lsp(n, 0);
        int i = 1;
        int len = 0;
        while(i < n) {
            if(s[i] == s[len]) {
                ++len;
                lsp[i] = len;
                ++i;
            } else if(len == 0) {
                ++i;
            } else {
                len = lsp[len-1];
            }
        }
        
        len = lsp[n-1];
        return s.substr(0, len);
    }
};
```

## LC-459: Find repeated patterns
This is a interesting topic that can be solved also by KMP, we can think that the word is somthing like `<pattern1><pttern2> ... <patternM>`, then using KMP we may find the LSP as `<pattern1> .. <pattern_M-1>`, and we only need to check if the length is satisfied, that is the lsp should be positive and the remaining length can divide the totla length.

```c++
class Solution {
public:
    bool repeatedSubstringPattern(string s) {
        int n = s.size();
        vector<int> lsp(n, 0);
        int i = 1;
        int len = 0;
        while(i < n) {
            if(s[i] == s[len]) {
                ++len;
                lsp[i] = len;
                ++i;
            } else if(len == 0) {
                ++i;
            } else {
                len = lsp[len-1];
            }
        }

        len = n - lsp[n-1];
        return (n % len == 0 && lsp[n-1] != 0);
    }
};
```

## LC-214: Shortest palindrome
Also really interesting problem to solve with KMP. For a string `__aacecaaab` (`_` denotes the missing character to make it palindrome), we can do reverse of it and concat in the back to become `aacecaaab#baaacecaa`, when we do matching, we always match something like `...#__aacecaa`, missing in the reversed part since the original string is missing front, and what we need to do is find out the `__` part (`ba` in this case), and put in front of the original string (so getting `baaacecaaab` in this case).


```c++
class Solution {
public:
    string shortestPalindrome(string s) {
        string rev = s;
        reverse(rev.begin(), rev.end());
        rev = s + '#' + rev;

        int n = rev.size();
        vector<int> lsp(n, 0);
        for(int i = 1, len = 0; i < n;) {
            if(rev[i] == rev[len]) {
                ++len;
                lsp[i] = len;
                ++i;
            } else if(len == 0) {
                ++i;
            } else {
                len = lsp[len-1];
            }
        }

        rev = rev.substr(lsp[n-1], s.size()-lsp[n-1]);
        reverse(rev.begin(), rev.end());
        return rev + s;
    }
};
```
