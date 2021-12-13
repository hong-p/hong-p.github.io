---
title: "[알고리즘-LeetCode] Add Two Numbers"
excerpt: "알고리즘 문제풀이 java"
toc: true
toc_sticky: true

categories:
 - Algorithm
tags:
 - Algorithm
---

# Add Two Numbers

## [Problem](https://leetcode.com/problems/add-two-numbers/)  
 > You are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.
 
 > You may assume the two numbers do not contain any leading zero, except the number 0 itself.

 Example:
 ```java
Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 0 -> 8
Explanation: 342 + 465 = 807.
 ```

## 1.Solution
일단 저의 첫 풀이는 while 문을 이용해서 문자열에 각 node 들을 방문하면서 val 들을 꺼냈고  
문자열로 만들어서 뒤집어서 숫자로 변환해서 합을 구했습니다.  
단순하게 문제에서 하라는데로 했죠..
답은 맞았지만 속도가 매우느렸네요 **11ms**  
소스코드에 주석을 달아놓았으니 주석을 참고하시면 이해하기 편할거에요 
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        StringBuffer sb = new StringBuffer();
        boolean biggerthan10 = false; // 10이상인지 확인해서 10이상이면 뒤에 1을 더해주기 위해 사용 
        while(true){
            int sum = l1.val + l2.val;
            if(biggerthan10) {
                sum+=1; // 전에 숫자합이 10이상이면 1을 더해줌
                biggerthan10 = false;
            }
            sb.append(sum%10); // StringBuilder에 추가 합이 10이상일수도 있으니 10으로나눈 나머지만 넣음 
            if(sum>=10) biggerthan10 = true;
            if(l1.next ==null && l2.next==null) {
                if(biggerthan10) sb.append(1);
                break; // 둘다 next 가 null이면 while loop를 종료 
            }
            // next 가 null이면 val 값을 0으로 넣어주고 null이 아니면 next로 바꿔줌 
            if(l1.next==null) l1.val=0;
            else l1=l1.next;
            if(l2.next==null) l2.val=0;
            else l2=l2.next;
        }
        // 문자열을 배열로 만들고 다시한번 for문을 돌면서 ListNode를 생성
        String[] resultStr = sb.toString().split("");
        int len = resultStr.length;
        ListNode result = new ListNode(Integer.parseInt(resultStr[0]));
        ListNode temp = result;
        for(int i=1; i<len ; i++){
            temp.next = new ListNode(Integer.parseInt(resultStr[i]));
            temp = temp.next;
        }
        return result;
    }
}
```
소스를 보면 매우 비효율적인 것을 알게될거에요.  
그래서 개선을 해보기로 했습니다. 뭔가 반복문 한번이면 해결될것 같았거든요..
그래서 생각한건 재귀호출 **(recursive)**  
근데 재귀로 짜는게 너무 오랜만이라 소스가 굉장히 이상하고.. 지저분하네요  

## 2.Solution
이전과는 다르게 문자열을 만들어 두 수의 합을 따로 계산하지 않고
바로 result ListNode에 만들기때문에 마지막에 for문을 도는 부분을 제거할 수 있었구요  
코드를 좀 더 다듬으면 깔끔한 코드가 나올것 같은데 저렇게 짜는 것도 시행착오중에 하나라고 생각하고 올려봅니다.  
근데 다음에 올릴 LeetCode에 있는 답과 비교하면 속도는 아래 코드가 더 좋았어요. **1 ms** 
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    boolean biggerthan10 = false;

    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        int sum = l1.val + l2.val; // 합계를 구하고 
        if(biggerthan10) {
            sum+=1;
            biggerthan10 = false;
        }
        if(sum>=10) biggerthan10 = true;

        // 바로 결과를 담을 ListNode에 넣어줍니다.
        ListNode result = new ListNode(sum%10);

        // 첫번째 만든 코드와 마찬가지로 둘다 next가 null이고 합이 10이상이면 1을 가진 Node 를 추가하고 종료합니다.
        if(l1.next ==null && l2.next==null) {
            if(biggerthan10) result.next = new ListNode(1);
            return result;
        }else{
            // 둘중 하나의 next만 null이라면 null인 ListNode에 0이 들어있는 Node를 만들어 넣어주고 recursive메서드를 호출합니다.
            if(l1.next==null) l1.next=new ListNode(0);
            if(l2.next==null) l2.next=new ListNode(0);
            result.next = new ListNode(0);
            recursive(l1.next,l2.next,result.next);
        }

        return result;
    }

    public void recursive(ListNode l1, ListNode l2, ListNode result){

        int sum = l1.val + l2.val;
        if(biggerthan10) {
            sum+=1;
            biggerthan10 = false;
        }
        if(sum>=10) biggerthan10 = true;

        result.val = sum%10;
        if(l1.next ==null && l2.next==null) {
            if(biggerthan10) result.next = new ListNode(1);
            return;
        }else{
            if(l1.next==null) l1.next=new ListNode(0);
            if(l2.next==null) l2.next=new ListNode(0);
            result.next = new ListNode(0);
            recursive(l1.next,l2.next,result.next);
        }
    }
}
```
## 3.Solution
이제 마지막으로 LeetCode에 올려져있는 답을 보았습니다.  
역시 소스가 매우 깔끔하고 군더더기 없네요.. 근데 속도는 **2 ms** 로 조금 느리네요.  
아무래도 반복문 보다는 recursive 가 더 속도는 빠른것 같습니다.
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode ln1, ListNode ln2) {
        ListNode head = new ListNode(0); // headNode 를 하나 만들고
        ListNode l1 = ln1, l2 = ln2, curr = head; // 받은 객체들을 복사해서  다른변수에 넣어줍니다.
        int carry = 0; // 숫자 합이 10이 넘으면 뒤 숫자에 넘겨주기 위한 변수
        // 두 Node가 모두 null 일때 까지 반복
        while(l1 != null || l2 != null){
            int x = (l1 != null) ? l1.val : 0; // 객체가 null이면 0 아니면 val값을 x에 저장
            int y = (l2 != null) ? l2.val : 0;
            int sum = carry + x + y; // x와 y를 더하는데 carry 값도 함께 더함 
            curr.next = new ListNode(sum%10); // 10으로 나눈 나머지를 next에 담고
            carry = sum/10; // 10으로 나눈 몫은 다음 숫자에 더해주기위해 carry에 넣어줌 예를들면 0+5+6=11 일경우 1을 뒷자리로 넘겨줌
            curr =  curr.next; // 현재 Node를 next로 치환시켜준다. 다음 node를 만들어주기 위함
            if (l1 != null) l1 = l1.next; // curr와 마찬가지로 next로 바꿔주는데 현재 Node가 null이 아닌경우만 next로 바꿔준다. null인경우 null point exception이 발생됨. 
            if (l2 != null) l2 = l2.next;
        }
        // 반복문을 빠져나왔는데 합계가 10을 넘었을 경우 마지막에 carry 값도 Node로 만들어 줘야 한다.
        if(carry>0) curr.next = new ListNode(carry);
        return head.next; // 처음에 head로 만든 Node는 쓸모없는 것이므로 head.next를 리턴한다.
    }
}
```
