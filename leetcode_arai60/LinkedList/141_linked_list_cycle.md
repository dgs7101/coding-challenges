## 取り組み方
（garunituleさんのmdファイルの書き方が個人的に良かったので、かなり参考にさせていただきました。）
step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを見た場合はコードを消して再度挑戦。これを正解するまで。
step2: コードを読みやすく整える。動くコードになったら終了。
step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

## step1
### 思考メモ
慣れていない単語や英語で問題文を理解するだけ時間がかなり経ってしまい、解法についてしっかり考えられなかった...
解法についてパッと思いついたのは、setを使ってポインタを記録していき、重複があればtrueとする方法だが、
1 -> 2 -> 4
     ↑
     3 
のパターンはポイント重複してるがサイクルがないからダメそう？ このパターンは連結リストではないため考慮しない？ などと考えていたら5分経ったため、一旦解法を見ることに。
（問題文をよく見たら、posが連結リストの末尾のポインタを内部的に決定するとのことなので、上記パターンにはなりえない設定のようでした。）

解法によると、fast, slowでポインタを先頭から+2, +1ずつ動かしていき、ループがあればfastが何周か回ってslowと重なるタイミングがあるため、
逆に、slowと重ならずにfastがlistの最後までたどり着いたらfalseを返せばよい、とのこと。
解説が理解できた気がしたのでコードを書こうとするも、ポインタの理解があいまいなため実装できず再度答えを見る。
（個人的な話だが、ListNode *headよりListNode* headのほうが理解しやすいんだなということを思うなど。型定義(ListNode*) 変数名(head)で分かれていると役割が明確な気がして嬉しい。）
解説見てから一から書いて提出したC++コードは以下に記載。

また、setでも実装できそうなのでstep2で書いてみるのと、なぜ模範解答がsetを使っていなかったのかを調べることにする。

### 自分の回答

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
    bool hasCycle(ListNode* head) {
        ListNode* fast = head;
        ListNode* slow = head;

        while(fast != nullptr && fast->next != nullptr && slow != nullptr){
            fast = fast->next->next;
            slow = slow->next;
            if(fast == slow) return true;
        }
        return false;
    }
};

## step2
### 思考
step1の時点で読みやすいコードになったので、疑問点の調査と他の人のコードリーディングをやってみる

### step1の疑問点の調査
別解として、setを使った場合で実装してみた。

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
    bool hasCycle(ListNode *head) {
        std::set<ListNode*> visited;
        ListNode* current = head;
        while(current != nullptr){
            if(visited.contains(current)) return true;
            visited.insert(current);
            current = current->next;
        }
        return false;
    }
};


Q. setではなくunordered_setでもよいか？ 
A. 
https://chromium.googlesource.com/chromium/src/+/master/base/containers/README.md
に書かれている通り、unordered_setはよほど必要な理由がない場合以外は使わない方が良さそう。
> Note that this advice never suggests the use of std::unordered_map and std::unordered_set. These containers provides similar features to the Abseil flat hash containers but with worse performance. They should only be used if absolutely required for compatibility with third-party code.
また、レファレンスを見ているとsetよりもabsl::flat_hash_setの方が今回の実装では良さそうだが、leetcode上だとabsl-cppライブラリがincludeできなさそうなのでスルーする。


Q. fast,slowを使った実装が模範解答になっている理由（setでの実装よりも優れている点）は？
A. 
空間計算量も抑えつつ、破壊的変更がない利点があるため。
fast,slowを使った実装は、Hare-Tortoise algorithm(Floyd’s Cycle Finding Algorithm)と呼ばれるアルゴリズムであり、
時間計算量：O(N),
空間計算量：O(1)
となる。

一方で、setを使用した実装では、
時間計算量：O(NlogN), 
空間計算量：O(N)
となる。
（なお、absl::flat_hash_setにしても時間計算量は平均O(N)になる。空間計算量はO(N)のまま）

つまり、Hare-Tortoise algorithmは空間計算量をO(1)に抑え、挿入などの破壊的変更がない。

## step3
setでの実装の方が基礎的な問題なので、こちらの実装で進めた。