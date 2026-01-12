---
title: "LC - Digit DP problems"
date: 2026-01-06
tags: ["dp", "digit-dp"]
weight: -3800
---

## Approach
Usually whne a digit-DP problems occurs it is classify as hard. I've read someones answer before, but did not quite understand the strategy. In order to be familiar with digit DP, I try to figure out the systematic method for this, and try to write all of the related problems, including LC-233, 902, 2719, 2827, and I find that actually this type of problem is not that hard.

The approach for digit DP is baesd on DP, but with a `tight` constraint. For instance when traverse to a problem `12xx` from `1234`, and traverse from `11xx` from `1234`, the subproblem may be different. And the only difference is the `tight` part, that is when that digit is approaching the upper limit.

After knowing this idea, we can easily implement the `tight` strategy, and tight would restrict the `limit` that can be used in next level searching. If the problem itself is not tight, then its subproblem would not be tight.

Another interesting part is the propagation of current solutions metric. For instance, you want to count how many 1's for each number, then you need to pass the number of ones that us present currently (ex. pass `count=1` to all the subproblems of `12xx`), and in the end you will return that cnt (ex. `1211` will return 3). Another example is the leading zeros, you can propagate to the subproblem to shrink the digits (ex. `leadZero = true` for subproblems of `02xx`). And lc-2827 is probably the most complicated question among all that you need to maintain the `evenCnt`, `oddCnt`, and `modular_by_k`, but actually there idea are all the same.

## Code - LC 902
Following code is for lc-902, where I maintain a leadZero with digit-DP/ 
```c++
class Solution {
public:
    vector<int> dig;
    unordered_set<int> number;
    int dp[10][2][2];
    
    int atMostNGivenDigitSet(vector<string>& digits, int n) {
        string s = to_string(n);
        for(const auto& c: s) {
            dig.push_back(c-'0');
        }
        for(const auto& st: digits) {
            number.insert(stoi(st));
        }
        memset(dp, -1, sizeof(dp));
        return traverse(0, true, true) - 1;
    }

    int traverse(int pos, bool tight, bool leadZero) {
        if(pos == dig.size()) return 1;
        int limit = tight ? dig[pos] : 9;
        if(dp[pos][tight][leadZero] != -1) return dp[pos][tight][leadZero];

        int sum = 0;
        for(int i = 0 ; i <= limit ; ++i) {
            if(!(i == 0 && leadZero) && !number.count(i)) continue;
            sum += traverse(pos+1, tight && (i == limit), leadZero && (i == 0));
        }
        // cout << pos << " " << tight << " -> " << sum << endl;
        dp[pos][tight][leadZero] = sum;
        return sum;
    }
};
```

## Code - LC 2827
Following code is for lc-2827, where I maintain a evenCnt, oddCnt, and modular.
```c++
class Solution {
public:

    int memo[10][2][10][10][21];

    int numberOfBeautifulIntegers(int low, int high, int k) {
        memset(memo, -1, sizeof(memo));
        int highCnt = traverse(to_string(high), 0, true, 0, 0, 0, k, true);
        memset(memo, -1, sizeof(memo));
        int lowCnt = traverse(to_string(low-1), 0, true, 0, 0, 0, k, true);
        return highCnt - lowCnt;
    }

    int traverse(const string& s, int pos, bool tight, int oddCnt, int evenCnt, int mod, const int& k, bool leadZero) {
        if(pos == s.size()) {
            return (oddCnt == evenCnt && mod == 0) ? 1 : 0;
        }
        if(memo[pos][tight][oddCnt][evenCnt][mod] != -1) return memo[pos][tight][oddCnt][evenCnt][mod];
        
        int limit = tight ? s[pos]-'0' : 9;

        int cnt = 0;
        for(int i = 0 ; i <= limit ; ++i) {
            int nextMod = mod * 10 + i;
            nextMod %= k;
            if(i == 0) cnt += traverse(s, pos+1, tight && limit == i, oddCnt, leadZero ? evenCnt : evenCnt+1, nextMod, k, leadZero);
            else if(i % 2 == 0) cnt += traverse(s, pos+1, tight && limit == i, oddCnt, evenCnt+1, nextMod, k, false);
            else cnt += traverse(s, pos+1, tight && limit == i, oddCnt+1, evenCnt, nextMod, k, false);
        }

        memo[pos][tight][oddCnt][evenCnt][mod] = cnt;
        return cnt;
    }
};
``` 
