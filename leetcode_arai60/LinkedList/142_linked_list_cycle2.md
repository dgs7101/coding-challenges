## 取り組み方
step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを見た場合はコードを消して再度挑戦。これを正解するまで。
step2: コードを読みやすく整える。動くコードになったら終了。
step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

## step1
### 思考メモ
前回の141. Linked List Cycleをベースに返す値をindexにすれば良さそう？と思ったが、
よくよくdetectCycleを見ると、indexではなくポインタを返せばよいとのことで、以下を提出。
まだ問題の理解に時間がかかる、、、（ここまで10分）

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        std::set<ListNode*> visited;
        ListNode* node = head;
        while (node != nullptr) {
            if (visited.contains(node)) {
                return node;
            }
            visited.insert(node);
            node = node->next;
        }
        return nullptr;
    }
};
```

## step2
### 思考メモ
Floyd's cycle-finding algorithm での解法を実装してみる。
https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.jfs03xpyyrfl

亀と倍速ウサギの衝突地点を求め、それぞれスタート地点・衝突地点から同速で進んだ時に合流した点が求める地点になるらしい。
やはりこの解法は初見で思いつくものではない...

class Solution {
public:
    ListNode* detectCycle(ListNode* head) {
        ListNode* meeting = findMeetingPoint(head);
        if (!meeting) return nullptr;

        ListNode* head_node = head;
        ListNode* meating_node = meeting;
        while (head_node != meating_node) {
            head_node = head_node->next;
            meating_node = meating_node->next;
        }
        return head_node;
    }

private:
    ListNode* findMeetingPoint(ListNode* head) const {
        ListNode* slow = head;
        ListNode* fast = head;
        while (fast && fast->next) {
            slow = slow->next;
            fast = fast->next->next;
            if (slow == fast) return slow;
        }
        return nullptr;
    }
};

## step3
setでの実装の方が基礎的な問題なので、こちらの実装で進めた。