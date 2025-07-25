# Two Pointer

## Fast Slow Pointer
* https://leetcode.com/problems/linked-list-cycle-ii/description/
``` cpp
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        if(!head || !head->next) return nullptr;
        auto slow = head, fast = head;
        while(1) {
            if(!fast || !fast->next) return nullptr;
            slow = slow->next;
            fast = fast->next->next;
            if(slow==fast) break;
        }

        fast = head;
        while(slow!=fast) {
            slow = slow->next;
            fast = fast->next;
        }
        return slow;
    }
};
```
