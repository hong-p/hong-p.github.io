---
title: "[알고리즘-LeetCode] Two Sum"
excerpt: "알고리즘 문제풀이 c++"
toc: true
toc_sticky: true

categories:
 - Algorithm
tags:
 - Algorithm
---

# Two Sum

## [Problem](https://leetcode.com/problems/two-sum/)  
> Given an array of integers, return indices of the two numbers such that they add up to a specific target.

> You may assume that each input would have exactly one solution, and you may not use the same element twice.

Example:
```cpp
iven nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
```

## 1.Solution
원래 가장 첫번째 풀었던 방법은 O(n2)방식으로 단순하게 이중for문으로 풀었습니다.  
이 방법은 구현하기엔 간단하지만 시간복잡도가 높아져 별로 좋지못한 방법입니다.
```java
import java.util.Arrays;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class TwoSum {
    public static void main(String[] args) {
//        int[] nums = {2,11,15,7};
//        int[] nums = {-3,4,3,90};
        int[] nums = {-3,4,3,90};
        int target = 0;
        int[] result = twoSum(nums, target);
        System.out.println(result[0] +","+ result[1]);
    }

    private static int[] twoSum(int[] nums, int target) {
        int len = nums.length;
        int[] result = new int[2];
        for(int i=0; i<len-1; i++){
            for(int j=i+1; j<len ;j++){
                if(nums[i]+nums[j] == target) {
                    result[0]=i;
                    result[1]=j;
                    return result;
                }
            }
        }
        return null;
    }
}

```

## 2.Solution
두번째 솔루션은 위에 java와는 다르게 c++로 풀어보았습니다.
사실 LeetCode에 나와있는 답을 보고 풀었는데 정말 좋은 아이디어인것 같습니다.
map을 활용해서 O(n)만에 for문 한번으로 끝내는 방법입니다.
```cpp
#include <iostream>
#include <vector>
#include <map>

using namespace std;


class Solution{
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        vector<int> res;
        map<int, int> m;
        for(int i=0; i< nums.size(); i++){
            int val = target - nums[i];
            if(m.count(val)>0){ 
                res.push_back(m[val]);
                res.push_back(i);
                return res; 
            }
            m.insert(pair<int, int>(nums[i], i));
        }
        return nums;
    }

};


int main()
{
    Solution s;
    vector<int> nums, res;
    int target = 9;
    nums.push_back(2);
    nums.push_back(7);
    nums.push_back(11);
    nums.push_back(15);
    res = s.twoSum(nums, target);
    for(int i=0; i<res.size(); i++){
        cout << res[i] << endl;
    }
    return 0;
}
```
