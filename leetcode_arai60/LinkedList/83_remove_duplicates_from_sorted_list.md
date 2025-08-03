## 取り組み方
step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを見た場合はコードを消して再度挑戦。これを正解するまで。
step2: コードを読みやすく整える。動くコードになったら終了。
step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

## step1
### 思考メモ
実装方針として、現時点と次点のvalが一致したらポインタをその先に設定することで良いと解釈し、以下を実装

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        ListNode* node = head;
        while (node && node->next) {
            if (node->val == node->next->val) {
                node->next = node->next->next;
            } else {
                node = node->next;
            }
        }
        return head;
    }
};
```

## step2
### 思考メモ
他の人の解法を見ていると、再帰での実装でもできるよう。
練習のため実装してみる。

- 再帰でheadの指定先を更新し続ける実装をする
- まず、再帰の終了条件はheadもしくはhead->nextがnullptrの場合
- このあたりで命名が気になってきた。リストを破壊的に変更して重複のない構造にする実装なので、deleteDuplicatesではなくmakeUniqueListとか？
- 今のnodeの値と次のnodeの値が同じなら、次のnode以降から重複除去したリストの先頭を新しいheadにしたい。

```cpp
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (head == nullptr || head->next == nullptr) {
            return head;
        } else if (head->val != head->next->val) {
            head->next = deleteDuplicates(head->next);
        } else {
            head = deleteDuplicates(head->next);
        }
        return head;
    }
};
```

ループでの実装との比較
- ループではnodeポインタのみで処理するためO(1), 再帰では関数呼出しでnode数だけスタックを積むため空間計算量が O(n)
- 再帰で気を付けたいstack overflowだが、今回のLeetcodeでの制約が変わらない前提ではノード数300のため問題ない
- そのため、空間計算量の観点から再帰ではなくループを採用する

## step3
step1で行う。