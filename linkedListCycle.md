# LinkedListCycle1

## step1

所要時間 30分

10分ほど考えてわからなかったので解説動画を見た。
Linked Listを知らなかったので調べた。
[Linked Listとは](https://astrostory.hatenablog.com/entry/2020/02/24/070213)
[Linked ListとArray Listの違い](https://qiita.com/BumpeiShimada/items/522798a380dc26c50a50)
その後動画を参考にコードを書いた。

```python
class Solution: 
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        if not head:
            return False
        slow = head
        fast = head
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
            if fast == slow:
                return True
                break
        else:
            return False

```

## step2

所要時間30分

step1を修正する
returnを行うと関数自体が終了することを理解していなかったため必要のないbreakとelseを入れてしまった。

```python
class Solution: 
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        if not head:
            return False
        slow = head
        fast = head
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
            if fast == slow:
                return True
                #break　削除
        #else: 削除
        return False

```

<https://qiita.com/toshi_machine/items/3b2a5c04da949ac78298>
上のリンクで紹介されているコードがなぜうまくいくのかわからないので考えてみた。

```python
class Solution:
    def hasCycle(self, head: ListNode) -> bool:
        if not head:
            return False
        slow = head
        fast = head.next
        #なぜfast = head.nextとしても通るのだろうか
        #0番目のリンクリストが存在するとして考えれば良い。またここをfast = head.nextとしないと次の slow != fastに引っかかる　

        while slow != fast:
            if not fast or not fast.next:
                return False
            slow = slow.next
            fast = fast.next.next
        return True
```

0番目のリストを想定するのは無意味な仮定に思えるのでここでは採用しない。
"slowとfastが同じでない時、動かし続ける"と"fastとfast.nextが存在する時、動かし続ける"ではコードの意味が変わる気がする。
前者はループを見つけることに重きをおいていて後者はループがないものを見つけることに重きをおいていると言えるのではないか？

## step3

所要時間5分

何も見ないで三回連続で成功するまでコードを書いた
一度`while fast or fast.next`としてしまった。コードの意味をきちんと理解していないから起こすミスであると考える。

最終的なコードはstep2と同様であるため示さない。
