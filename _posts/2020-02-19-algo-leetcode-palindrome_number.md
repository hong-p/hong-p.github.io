---
title: "[알고리즘-LeetCode] Palindrome Number"
excerpt: "알고리즘 문제풀이 c++"
toc: true
toc_sticky: true

categories:
 - Algorithm
tags:
 - Algorithm
---
# Palindrome Number

## [Problem](https://leetcode.com/problems/palindrome-number/)

> Determine whether an integer is a palindrome. An integer is a palindrome when it reads the same backward as forward.


Example1 :
```cpp
Input: 121
Output: true
```

Example2 :
```cpp
Input: -121
Output: false
Explanation: From left to right, it reads -121. From right to left, it becomes 121-. Therefore it is not a palindrome.
```

Example3 : 
```cpp
Input: 10
Output: false
Explanation: Reads 01 from right to left. Therefore it is not a palindrome.
```
Follow up :   
Coud you solve it without converting the integer to a string?

---

## 1.Solution 1
원래 문제는 문자열을 사용하지 않고 해결해 보라고 했으나..  
저는 Follow up을 보지 못하고 문자열로 풀어보았습니다.  
`stringstream` 을 이용해서 숫자를 문자열로 변환하고 문자열을 vector 로 변환해서 맨앞과 맨뒤가 동일한지 확인하며   
중간으로 점점 좁혀오는 방식으로 풀었습니다.
int -> string(stringstream활용) -> vector(char)  
for문으로 vector 사이즈의 반절만 돌면서 i 번째 와 size-i-1 번째를 비교하여 풀었습니다.  
다른게 있으면 false로 리턴해서 끝내버리고  
계속 같으면 palindrome 으로 true를 리턴하는 방식입니다.
```cpp
    bool isPalindrome1(int x){
        if(x<0) return false;
        string str = "";
        stringstream sstr;
        sstr << x;
        str = sstr.str();
        vector<char> v(str.begin(), str.end());
        int vSize = v.size();
        int halfSize = (vSize/2)+1;
        for(int i=0; i<halfSize; i++){
            if(v[i] != v[vSize-i-1]) return false;
        }
        return true;
    }
```

---
## Solution 2
두번째 풀이는 leetcode에 올려져 있는 풀이를 보고 참고해서 풀었습니다.  
문자열을 이용하지 않고 숫자로만 계산해서 푸는 풀이입니다.  
while문을 돌면서 10으로 나눈 나머지를 계산해서  
뒤집힌(reverse) 숫자를 계산해 냅니다.  
while 문의 조건은 (x>reverse)로 x가 reverse 보다 클때까지만 반복 합니다.   
x를 10으로 나눈 나머지를 reverse 한 숫자에 10을 곱한값에 더해주는 것을 반복  

x = 1234 숫자가 있으면  
reverse = x(1234)%10 = 4 가 되고  
x = x/10 = 123 으로 바꿔준다.  
x(123) > reverse(4) 로 true 이므로  다음 loop  
reverse = reverse(4)*10 + x(123)%10 = 40 + 3 = 43  
x = x(123)/10 = 12  
x(12) > reverse(43) 로 false 이므로  loop 탈출  

반절만 돌려보면 palindrome인지 알수 있으므로   
반만 돌리고 x와 reverse 값을 비교해보고  
두수가 같은지 보거나   
혹은 숫자가 홀수 자릿수일 경우 reverse 를 10으로 나눈 값을 비교해 보아서 같으면 true를 리턴합니다.

```cpp
    bool isPalindrome(int x){
        // 0일 경우 true 리턴
        if(x==0) {return true;}
        int reverse=0;
        // 0보다 작거나 x를 10으로 나눈 나머지가 0인경우 palindrome이 될 수 없어 false 리턴
        if(x<0 || x%10==0) return false;
        while(x>reverse){
            reverse = reverse*10 + x%10;
            x = x/10;
        }
        return x==reverse || x==reverse/10;
    }
```

---
## 전체 Code
참고 하시라고 전체 cpp 코드도 공유드립니다.

```cpp
#include <iostream>
#include <sstream>
#include <vector>
#include <limits.h>  

using namespace std;

class Solution {
public:
    bool isPalindrome(int x){
        // 0일 경우 true 리턴
        if(x==0) {return true;}
        int reverse=0;
        // 0보다 작거나 x를 10으로 나눈 나머지가 0인경우 palindrome이 될 수 없어 false 리턴
        if(x<0 || x%10==0) return false;
        while(x>reverse){
            reverse = reverse*10 + x%10;
            x = x/10;
        }
        return x==reverse || x==reverse/10;
    }

    bool isPalindrome1(int x){
        if(x<0) return false;
        string str = "";
        stringstream sstr;
        sstr << x;
        str = sstr.str();
        vector<char> v(str.begin(), str.end());
        int vSize = v.size();
        int halfSize = (vSize/2)+1;
        for(int i=0; i<halfSize; i++){
            if(v[i] != v[vSize-i-1]) return false;
        }
        return true;
    }
};

int main(){
    Solution s;
    int num;
    // cin >> num;
    num = 100;
    bool ret = s.isPalindrome(num);
    cout << ret << endl;

    return 0;
}
```
