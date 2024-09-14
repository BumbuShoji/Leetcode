# 83.Remove Duplicate from Sorted List

## step1

所要時間　約10分

最初出力型を何にすれば良いかわからなかったので迷った。

[リンク](https://leetcode.com/problems/remove-duplicates-from-sorted-list/solutions/5380806/beats-100-java-python-c-fully-explained)を参考にしてコードを書いた。

```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None:
            return None
        node = head
        node2 = head.next
        visited = set()
        visited.add(head.val)
        while node2:
            if node2.val in visited:
                node.next = node2.next
                node = node
                node2 = node.next
            else:
                visited.add(node2.val)
                node = node.next
                node2 = node2.next
        return head
```

## step2

>The list is guaranteed to be sorted in ascending order.
上のように書かれているので同じ数字は連続でしか出現しない。そのためsetに入れる必要はなく直前の値のみを参照すれば良い。

```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None:
            return None
        node = head
        node2 = head.next
        visited = node.val
        while node2:
            if node2.val == visited:
                node.next = node2.next
                node2 = node.next
            else:
                node = node.next
                node2 = node2.next
                visited = node.val
        return head
```

```python
if node2.val = visited:
    node.next = node2.next
    node2 = node.next
```

この部分は下のようにも書ける

```python
if node2.val = visited:
    node2 = node2.next
    node.next = node2
```

node.nextをつなげてからnode2を移動させるか、その逆かが異なる。
下の方が見やすい気もする。

次のような記法もあった。
<https://leetcode.com/problems/remove-duplicates-from-sorted-list/solutions/5665020/27ms-99-11-easy-solution-pointer>

```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        cur = head
        while cur:
            while cur.next and cur.next.val == cur.val:
                cur.next = cur.next.next
            cur = cur.next
        return head
```

こちらの方が短く書けてるが、実際に動かしてみると平均的に5msほど遅かった。
しかし、27msで解ける時もあったので問題に左右されやすいコードなのだろう。原因はわからない。

## step3

一番最後の方法でミスなく三回連続で成功できるまで行った。
コードは同じなので示さない。
＝＝を＝にしてしまうミスが多くあった。
