---
title: "LC - Floyds slow fast pointer" 
date: 2026-01-13
tags: ["graph"]
weight: -3805
---

## Detect Loop 
Recently I practice the topic for floyd's fast slow pointer algorithm to detect loop.
The basic problem is that there is a link list with cycle (that is each node only points to at most one node), we can detect it by walking a fast pointer (do two `->next` per loop), and slow pointer (one `->next` at a time). In this case, there is a loop if two pointers can meet each other.

A basic version for this is LC-141, code as follows:

```c++
class Solution {
public:
    bool hasCycle(ListNode *head) {
        if(head == nullptr) return false;
        ListNode* fast = head;
        ListNode* slow = head;
        while(fast != nullptr && fast->next != nullptr) {
            slow = slow->next;
            fast = fast->next->next;
            if(slow == fast) return true;
        }
        return false;
    }
};
```

## Detect Loop entrance
Another enhance part is the entrance of the loop, and this can be done by walking the meet ppotinr and the original point simultaneously when it collides.

`Original -> entrance (x next) -> meet (x+y next)`

`Original -> entrance -> … -> entrance -> … -> entrance -> meet (2*x+2*y next)`

That means that `(x+y)` is a multiplicatoin of the loop length. Then when we walk from meet point with `x` steps, in got back to entrance, and this is where it meets with the origianl point.

```c++
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        ListNode* fast = head;
        ListNode* slow = head;
        while(fast && fast->next) {
            slow = slow->next;
            fast = fast->next->next;
            if (slow == fast) break;
        }
        if(fast == nullptr || fast->next == nullptr) return nullptr;
        while(head != slow) {
            head = head->next;
            slow = slow->next;
        }
        return head;
    }
};
```

## General loop detection problem (Find duplicate number)
An intertesitng problem is LC-287, where we can conert the problme into a linked list waling problem. We can try to solve this type of problem when seeing each node at most walk to one node.
```c++
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        int slow = 0;
        int fast = 0;
        do {
            slow = nums[slow];
            fast = nums[nums[fast]];
            if(slow == fast) break;
        } while(fast && nums[fast]);
        slow = 0;
        while(fast != slow) {
            fast = nums[fast];
            slow = nums[slow];
        }
        return fast;
    }
};
```


## More complex loop detection problem
LC-457 is also a more general loop detection problem wiht more constriant on it, but we can also easily solved this with floyd alogirthm.

```c++
class Solution {
public:
    bool circularArrayLoop(vector<int>& nums) {
        int n = nums.size();
        for(const auto& dir: {1, -1}) {
            for(int i = 0 ; i < n ; ++i) {
                if(dir*nums[i] < 0) continue;
                int slow = i;
                int fast = i;
                while(true) {
                    if(dir*nums[slow] < 0 || nums[slow] % n == 0) break;
                    slow = ((slow + nums[slow]) % n + n) % n;
                    if(dir*nums[fast] < 0 || nums[fast] % n == 0) break;
                    fast = ((fast + nums[fast]) % n + n) % n;
                    if(dir*nums[fast] < 0 || nums[fast] % n == 0) break;
                    fast = ((fast + nums[fast]) % n + n) % n;
                    if(slow == fast) return true;
                }
                // remark;
                slow = i;
                while(true) {
                    if(dir*nums[slow] < 0 || nums[slow] % n == 0) break;
                    slow = ((slow + nums[slow]) % n + n) % n;
                    nums[slow] = 0;
                }
                nums[i] = 0;
            }
        }
        return false;
    }
};
```
