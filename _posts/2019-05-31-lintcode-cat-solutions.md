---
layout: post
title:  "Lintcode CAT challenge solutions"
categories: lintcode dynamic-programming algorithms 
tags: c++ algorithms dynamic-programming
author: xui
---

* content
{:toc}

# Lintcode CAT challenge solutions
## Introduction
As of this moment, I reached lintcode CAT tier Gold IV and i am only three questions away to Platinum, below are some difficult problems that got me stuck during the challenge. I failed these questions during the challenge so I want to write down the solutions that i came up after the challenge. If there is any opportunity to submit it in the future, i will know if my solutions are correct or not.

## Problem 1
This problem is called Snow Boots.

1686. Snow Boots
n bricks, the snow on the ith brick is f[i] feet thick. b pairs of boots, numbered from 0 to b-1, the ith pair of boots can walk in snow up to s[i] feet deep, with a maximum step length of d[i]. Each time you can choose to keep dressed in current boots, or discard current boots and wear the smallest numbered boots, or discard the smallest numbered boots. If We want to get to the last brick, please find the minimum number of boots should be discarded.

Solution:
At first glance, this problem seemed really complicated, three vectors to deal with and it is not very straightforward. The natural idea is to imitate the walking process and try updating the index of the bricks that can walk to, and this suggests a greedy solution. But it turned out greedy solution did not pass the challenge because of this condition: Keep current boots. That means as long as there is a brick that current boot can walk to, the person can keep walking. For the second and third condition, it suggests that the walking can be picked up from any point that any previous boot has reached. So it should be clear to solve it now, we can use one dimensional array to denote the bricks that can be reached, and we start from dressing the first boot to the last boot, during which if we have reached the last brick, we return the index of current boot, which equals the number of boots that are discarded, and it is guaranteed minimum.

```cpp
int getMinimumNumOfBoots(int n, int b, vector<int>& f, vector<int>& s, vector<int>&d){
    vector<int> dp(n, 0);
    dp[0] = 1;
    for(int i=0; i<b; ++i){//iterate over boots
        for(int j=0; j<n; ++j){//iterate over bricks
            if(dp[j]){//if this brick has been reached
               for(int x=1; x<d[i] && x+j<n; ++x){//we try to walk as far as possible
                if(f[x+j] <= s[i]){
                    dp[x+j] = 1;
                }
               } 
            }
        }
        if(dp[n-1])//last brick has been reached
            return i;
    }
    return -1;//can not reach the last brick
}
```


## Problem 2
This problem is called



