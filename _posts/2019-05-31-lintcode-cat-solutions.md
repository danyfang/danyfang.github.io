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

1. Snow Boots
n bricks, the snow on the ith brick is f[i] feet thick. b pairs of boots, numbered from 0 to b-1, the ith pair of boots can walk in snow up to s[i] feet deep, with a maximum step length of d[i]. Each time you can choose to keep dressed in current boots, or discard current boots and wear the smallest numbered boots, or discard the smallest numbered boots. If We want to get to the last brick, please find the minimum number of boots should be discarded.

2. Example
the example given by the problem is :
  n = 10; b = 4; f = {0,2,8,3,6,7,5,1,4,0}; st = {2,4,3,7}; d = {3,2,4,1};
and the answer is 2.


Solution:
At first glance, this problem seemed really complicated, three vectors to deal with and it is not very straightforward. The natural idea is to imitate the walking process and try updating the index of the bricks that can walk to, and this suggests a greedy solution. But it turned out greedy solution did not pass the challenge because of this condition: Keep current boots. That means as long as there is a brick that current boot can walk to, the person can keep walking. For the second and third condition, it suggests that the walking can be picked up from any point that any previous boot has reached. So it should be clear to solve it now, we can use one dimensional array to denote the bricks that can be reached, and we start walking by dressing from the first boot to the last boot, during which if we have reached the last brick, we return the index of current boot, which equals the number of boots that are discarded, and it is guaranteed minimum.

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
This problem is called Skip Stones

1. Skip Stones
There are n stones between the starting point and end point. The distance between the starting point and the ith (i starts form 0) stone is d[i]. And the distance between the starting point and end point is target. From the starting point, we can only jumpy to the adjacent stone until the end point.
Now you can remove at most m stones. Return the maximum value of the shortest jump distance in your jumping from starting point to end point.
0<=m<=n<=50000, target < 10^9

2. Example
n = 5, m =2, target = 25, d = [2,11,14,17,21]
output = 4, remove the first and third one, then the shortest jumpy distance is 4



Solution:
There exists a naive solution for this problem. First tranform the distance array into a gap array, then iterate over the array and find the minimum gap, then merge this gap to the gap fllows (if the minimum gap is the last one, merge the gap precedes). After m merges we iterate over the gap array and return the minim gap. But obviously this solution is O(n^2) and since n could be up to 50000 so this solution is not acceptable.

My idea is to use a map to store the gap as key and its indexes as values and we use a set to store all available gap indexes. Each time we search for the smallest gap index (logn time) and we search for the gap to be merged (logn time), we then update the map and the index. And this algorithm can have runtime of O(n(logn)^2) (The following solution used a vector but can be replaced with a set)

```cpp
class Solution{
public:
  int getDistance(int n, int m, int t, vector<int>& d){
    if(n == 0 || m == n)
      return t;
    vector<int> dis(n+1, 0);
    dis[0] = d[0];
    int ans = d[0];
    for(int i=1; i<n; ++i){
      dis[i] = d[i] - d[i-1];
      ans = max(ans, dis[i]);
    }
    dis[n] = t-d[n-1];
    map<int, vector<int>> m_;
    set<int> s_;
    for(int i=0; i<=n; ++i){
      m_[dis[i]].push_back(i);
      s_.insert(i);
    }
    while(m-- > 0){
      auto k = m_.begin()->first;
      auto id = m_[k][0];
      auto id_add = s_.upper_bound(id);
      if(id_add == s_.end()){
        id_add = s_.lower_bound(id);
        id_add--;
      }
      //found the index of the element in dis to add
      auto addition = dis[*id_add];
      dis[*id_add] += dis[id];
      dis[id] = -1;
      s_.erase(id);
      auto new_add = dis[*id_add];
      //now update the m_, remove some key value pairs
      //the affected are the found one and the add one
      //and we need to add the new_add one
      if(m_[k].size() == 1){
        m_.erase(k);
      }
      else{
        vector<int> v;
        for(int x=0; x<m_[k].size(); ++x){
          v.push_back(m_[k][x]);
        }
        m_[k] = v;
      }
      if(m_[addition].size() == 1){
        m_.erase(addition);
      }
      else{
        vector<int> v;
        for(int x=0; x<m_[addition].size(); ++x){
          if(m_[addition][x] == *id_add){
            continue;
          }
          v.push_back(m_[addition][x]);
        }
        m_[addition] = v;
      }
      m_[new_add].push_back(*id_add);

    }
    for(auto& k : dis){
      if(k != -1){
        ans = min(ans, k);
      }
    }
    return ans;
  }

```




























