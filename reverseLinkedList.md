# Reverse Linked List

## step1

- 問題を整理する
- リンクドリストが与えられるので逆にして返せ
- 手でやるならどうやるか
	- 前から読んでってvalをstackに書いておく
	- 最後まで読み終わったらstackの後ろからvalを取り出してLinked Listを繋げていく
- コードで書いてみる
- 5分ほどで解けた
- 最初に書いたとき`while node.next is not None:`としてしまった
	- nodeの次がなくなったら次に進めないなという感覚があったから
	- 実際はnode.next is Noneでストップしてしまうと最後の要素をカウントできない

```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None:
            return None
         
        stack = list()
        node = head
        
        while node is not None:
            stack.append(node.val)
            node = node.next

        dummy = ListNode(-1)
        reverse_node = dummy

        while stack:
            val = stack.pop()
            next_node = ListNode(val)
            reverse_node.next = next_node
            reverse_node = reverse_node.next

        return dummy.next
```

## step2 他の人の解法を見る・自分の修正


### [tarinaiさん](https://github.com/tarinaihitori/leetcode/pull/6/files/7ae6c941f485e4fd52fab64cf17584150ea87d03#diff-f1530fc1072ee1f0b7de99a2e5236992c72355da69982c8ca516fcfba7c57927)
- リストを読んでるときに逆向きにつけ直している
	- こっちの方が一周で終わるから早い
	- 自分のやり方と考え方から違って面白い
- currentとpreviousという名前の付け方について議論している
	- [ここ](https://github.com/colorbox/leetcode/pull/22#discussion_r1694239660)でも
	- 今のと前のは確かに違和感がある
	- reversed と not reversedのように操作を終えたもの、操作前のもののような名付けは直感に合う
```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        current = head
        previous = None
        while current:
            next_temp = current.next
            current.next = previous
            previous = current
            current = next_temp
        return previous
```

### 再帰

- 再帰でもできるらしいので自分で考えてみる
- 再帰で考えたことがないのでどのようなところから取り組めばいいのかわからない
- 手でやる場合
        - 一つのリンクドリストに一人が担当しているとする
	- 前の人から信号を後ろの人に渡す
	- 後ろの人がいなかったらリンクを逆向きにして前に返す
	- 後ろの人から信号が来たらリンクを逆にして前に返す
- よくわからないので他の人の回答を見てそれを解釈する

- 解釈

```python
class Solution:
# leetcodeはデフォルトの再帰の深さの最大数が55,000のため、今回の制約(最大5,000)だとRecursionErrorは考慮しなくて良い
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        def _reverse_linked_list(head, previous):
		    # head = 次に処理するnode
		    # previous = すでに処理したnode
            if head is None:
                return previous
            # 次に処理するnodeがなくなっていればすでに処理したnodeを返す
            temp = head.next
            # 次に処理するnodeの次のnodeを取得する
            head.next = previous
            # head.nextをすでに処理したnodeに繋げる
            return _reverse_linked_list(temp, head)
            # tempを次に処理するnode、headをすでに処理したnodeとして次の操作に移行する
        return _reverse_linked_list(head, None)
        # 一番最初は次に処理するのはheadですでに処理したnodeは存在しない
```
- 仕組みはわかったがどうやったらこの再帰を思いつけるだろう
- 手でやるなら
	- 何人かで鎖のようなものを繋ぎかえる
	- 前の人からは繋ぎかえたもの最後と繋ぎかえてないものの先頭を渡される
	- 繋ぎかえてないものの先頭を外して、繋ぎかえたものの最後に繋げる
	- 次の人に渡す
	- 繋ぎかえてないものがなくなったら繋ぎかえたものを返す
- RecursionErrorとは
	- 再帰が深すぎる時に起きるエラー
	- [sys.setrecursionlimit()](https://docs.python.org/3/library/sys.html#sys.setrecursionlimit)で変更できる

```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None or head.next is None:
            return head
        new_head = self.reverseList(head.next)
        # 最後の要素をとってくる
        head.next.next = head
        head.next = None
        # 繋いでる向きを反対にする
        return new_head
```
- 手でやるなら
- 何人かで鎖のようなものを繋ぎかえる
	- 最初に鎖の担当する箇所を決める
	- 担当箇所が鎖の最後ならそれを前の人に戻す
	- 戻されたら自分の箇所を逆向きにつなぐ
	- 鎖の最後の部分を前の人に戻す
	- この時自分の操作した部分は最後の部分に連なっている
	- 最初の人まで戻ってきたら最初の向きを繋ぎかえて鎖の最後を返す
- わかりにくい説明になってしまった。
- 鎖全体のheadと自分の担当箇所のheadが存在するが同じ単語で表されているところがコードの難しさに繋がっている気がする
- わかりやすいように変えると次のようになるか？
```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        def reverseListHelper(previous):
            if previous is None or previous.next is None:
                return previous
            
            last_node = reverseListHelper(previous.next)
            
            previous.next.next = previous
            previous.next = None 
            return last_node

        return reverseListHelper(head)
```

[Odaさんの再帰の考え方](https://discord.com/channels/1084280443945353267/1231966485610758196/1239417493211320382)
>再帰というのは、リストの頭を担当する部下が、何かを下流にお願いして、仕事が返ってきて、自分も何かをすると、全体のリストが逆順になるというものです。
>下流に、自分よりも下流の部分を逆順にしろ、といって、したよ。といって、ぽいっと渡されても困りますね。その時に、自分が効率的に仕事をするために何を教えてほしいですか。

- 効率的に仕事をするには何を教えて欲しいかという視点を持っておきたい

### 自分の修正

- stackを使うより一周しながらリンクを繋ぎ直す方がわかりやすいと思ったのでそちらでやる
- 自分で書いてみるとnext_tempが思いつきづらいと思った

```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        reversed = None
        not_reversed = head

        while not_reversed:
            next_temp = not_reversed.next
            not_reversed.next = reversed

            reversed = not_reversed
            not_reversed = next_temp           
        
        return reversed
```

`while not_reversed` ではなく`while not_reversed is not None`と書くべきなのでは？

- is not None について
- Googleは[Always use "is None"(or "is not None")](https://google.github.io/styleguide/pyguide.html#2144-decision)と言っている
- pep8は[beware of writing](https://peps.python.org/pep-0008/#programming-recommendations:~:text=Also%2C%20beware%20of%20writing%20if%20x%20when%20you%20really%20mean%20if%20x%20is%20not%20None)
- [型ヒントからわかる場合は省略しても良いかもしれない](https://github.com/h1rosaka/arai60/pull/5/files/bc2027b5d4f57992bbe8afb6924f3cf95e591cc3#r1716372910)
- if a: だと 0や何らかの空集合にもFalseと反応するから注意しろというのが本質
- Google は大規模に開発しているからここらへん細かいのかも？
- 変わらない悩みなの面白い
	- [stack overflow](https://stackoverflow.com/questions/7816363/if-a-vs-if-a-is-not-none)
	- [Reddit](https://www.reddit.com/r/learnpython/comments/of5s8i/whats_the_most_pythonic_way_of_checking_if/)
- 今回は 読みやすさ優先で while not_reversedを使います。
## step3
三回連続で書いた
step3最後のものと同じ。

```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        reversed = None
        not_reversed = head

        while not_reversed:
            next_temp = not_reversed.next
            not_reversed.next = reversed

            reversed = not_reversed
            not_reversed = next_temp

        return reversed
