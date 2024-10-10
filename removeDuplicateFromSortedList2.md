# Remove Duplicate from Sorted List 2

<https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/description/>

## step1　自力で解く

所要時間20分
10分ほど考えてわからなかったので動画を見た
前回も同じようなコードを書いたはずだが.valを参照しなければいけないということをずっと忘れていた

```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        dummy = ListNode(0)
        dummy.next = head
        node = dummy
        while node.next and node.next.next:
            if node.next.val == node.next.next.val:
                duplicateval = node.next.val
                while node.next and node.next.val == duplicateval:
                    node.next = node.next.next
            else:
                node = node.next
        return dummy.next
```

## step2 読みやすくする&他の人の回答を見る

下の方のを見てコードを書いた
<https://github.com/rinost081/LeetCode/pull/6/commits/23bf0657770cc81fad3b207a4733cc771397579b>

自分でやった時はnode.next.val == node.next.next.valを = で書いてしまいエラーになることがあった。この間違いはよくやってしまうので気をつけたい。

```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        dummy = ListNode(-1,head)
        node = dummy

        while node:
            is_duplicate = False
            while node.next and node.next.next and node.next.val == node.next.next.val:
                is_duplicate = True
                node.next = node.next.next

            if is_duplicate:
                node.next = node.next.next
            else:
                node = node.next

        return dummy.next
```

step1 では　`dummy = ListNode(0) dummy.next = head` と書いているが一行で代入できるのでそちらの方がわかりやすい

is_duplicateのTrue, Falseで判断する方が自分は読みやすかった。

`node.next = node.next.next`が二回出てきてるのが少し気持ち悪いが読みやすさ優先か。

ハッシュマップを用いる方法も見てみる
<https://github.com/Mike0121/LeetCode/commit/5e139d55ddb6685bd87cbd685538972058a61073>

defaultdictは知らなかったので以下を参考した
[Qiita defaultdictの使い方](https://qiita.com/xza/items/72a1b07fcf64d1f4bdb7)
初期値を設定しておき,dictにkeyが存在しない場合も要素を初期値として追加することができるdict
初期値には関数を用いることもできる

defaultdict(int)はdefaultdict(lambda:int())と同義で0を返す

```python
from collections import defaultdict
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None:
            return head

        current = head
        value_counter = defaultdict(int)
        while current:
            value_counter[current.val] += 1
            current = current.next

        dummy = ListNode(0, head)
        current = dummy
        while current.next:
            while current.next and value_counter[current.next.val] > 1:
                current.next = current.next.next
            if current.next:
                current = current.next
        return dummy.next
```


重複する要素を全て削除しなければいけない場合はハッシュマップだと二周しなければいけないから効率が悪い？

## step3 3回連続エラーなしで解けるまでやる

```python
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        dummy = ListNode(-1,head)
        node = dummy

        while node:
            is_duplicate = False
            while node.next and node.next.next and node.next.val == node.next.next.val:
                is_duplicate = True
                node.next = node.next.next

            if is_duplicate:
                node.next = node.next.next
            else:
                node = node.next

        return dummy.next
```

step2での回答と同じものを書いた
変数名のタイプミスで動かないことが二回くらいあった
