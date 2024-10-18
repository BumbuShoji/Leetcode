# Add Two Numbers

## step1

所要時間 20m

valを置き換えた後にcarryを計算してしまった
l1とl2が同じ長さでない場合
一番最後に繰り上げがある場合

```python
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        l_sum = ListNode()

        node1 = l1
        node2 = l2
        node_sum = l_sum

        carry = 0
        val = 0

        while node1 is not None or node2 is not None:
            if node1 is not None and node2 is not None:
                val = node1.val + node2.val + carry
            elif node1 is None:
                val = node2.val + carry
            elif node2 is None:
                val = node1.val + carry
    
            carry = val//10
            val = val%10
            node_sum.next = ListNode(val)

            if node1 is not None:
                node1 = node1.next
            if node2 is not None: 
                node2 = node2.next

            node_sum = node_sum.next
        
        if carry != 0:
            node_sum.next = ListNode(carry)
        
        return l_sum.next

```

- is not None を何回も使わなければいけないのが気になる。他の方法はないのだろうか？

## step2 読みやすくする&他の人の解法を見る

### [一つ目](https://leetcode.com/problems/add-two-numbers/solutions/3675747/beats-100-c-java-python-beginner-friendly/)

```python
class Solution:
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        dummyHead = ListNode(0)
        tail = dummyHead
        carry = 0

        while l1 is not None or l2 is not None or carry != 0:
            digit1 = l1.val if l1 is not None else 0
            digit2 = l2.val if l2 is not None else 0

            sum = digit1 + digit2 + carry
            digit = sum % 10
            carry = sum // 10

            newNode = ListNode(digit)
            tail.next = newNode
            tail = tail.next

            l1 = l1.next if l1 is not None else None
            l2 = l2.next if l2 is not None else None

        result = dummyHead.next
        dummyHead.next = None
        return result

```

- carry != 0をwhileに追加しているので最後の繰り上がりを別で考える必要がない
- 直接sumを求めるのではなくdigit1,digit2などを経由している。こちらの方が良い気がする。
	- 自分の書き方だとなぜvalを求める式が変わるのかがわからない。
- sumとdigitが別の変数になっている。
	- 自分は両方valで同じ名前としていた。わかりにくいのでやめた方が良いと思う。
- if節を一行で書いている
	- 簡単なif文なのでこちらで書いてもいいかも
	- [三項演算子について](https://www.geeksforgeeks.org/ternary-operator-in-python/)よく知らないので調べてみた
		- If else
			- a = "true value" if 条件 else "false value"
		- ネストされたif else
			- a = "true value 1" if 条件1 else "true value 2" if 条件2 else "false value2"
			- ネストするなら普通に書いたほうがわかりやすそう
		- タプル
			- a = ((false value, true value)\[条件])
				- falseとtrueの順番が直感に反する
				- 条件が0を返したら0番目の要素、1を返したら1番目の要素ということらしい
		- 辞書
			- a = ({True: "true value", False: "false value"}\[条件])
			- タプルは知らなかったらわからないからこっちの方が親切だと思った。
		- Lambda
			- a = ((lambda: "false value", lambda:"true value")\[条件]())
- dummyHead.next  = Noneと最後にしている。最後のLinkedList.nextがNoneであることを明示している。
	- ListNode classは デフォルトでnext = Noneとしているので必要ない気がする。
	- これをやらない場合どのようなエラーが考えられるのだろうか。
```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
```


### [二つ目]()
- 再帰で解く方法

```python
class Solution:
def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode],carry = 0) -> Optional[ListNode]:
	if not l1 and not l2 and not carry:
		return None

	l1_val = l1.val if l1 else 0
	l2_val = l2.val if l2 else 0
	total = carry + l1_val + l2_val
	carry = total // 10
	current_node = ListNode(total % 10)

	l1_next = l1.next if l1 else None
	l2_next = l2.next if l2 else None
	current_node.next = self.addTwoNumbers(l1_next, l2_next, carry)

	return current_node
```
- not ではなく is Noneと書くべきではないのだろうか？
	- not l1, not l2はis None だがnot carry はis 0である。そこを示した方がわかりやすい気がする
- whileループを再帰で代用している。この方法は知らなかったので勉強になる。

### [三つ目](https://github.com/Mike0121/LeetCode/pull/41/files)
- 再帰で解いている

```python
class Solution:
	def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
		def get_value(node: Optional[ListNode]) -> int:
			if node is None:
				return 0
			return node.val

		def next_node(node: Optional[ListNode]) -> Optional[ListNode]:
			if node is None:
				return None
			return node.next

		def add_two_numbers_helper(node1: Optional[ListNode] , node2: Optional[ListNode], carry: int) -> Optional[ListNode]:                
			if not (node1 or node2 or carry):
				return None

			value1 = get_value(node1)
			value2 = get_value(node2)
			total = value1 + value2 + carry
			node = ListNode(total % 10)
			carry = total // 10
			node.next = _add_two_numbers_helper(next_node(node1), next_node(node2), carry)
			return node

		return _add_two_numbers_helper(l1, l2, 0)
```
- 同じことをするなら関数化すればいい
	- 簡単すぎるので今回はどちらでもいいがこの発想は持っておきたい
- `node = ListNode(total % 10)`のような書き方をするよりはtotal % 10に変数を割り当てたほうが読みやすいのではないか

### [四つめ](https://github.com/h1rosaka/arai60/pull/7/files/d94c4e1281bc3fd371bdddb2d0d2b95a010aaa4f#diff-c655c7f146b306e01c4b8ca0d6739d1fcc21f08b78456fb0dbf415fca6864c5a)
nodeの数を数字にして、和を計算してから改めてListNodeにする

```python
def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
    dummy_head = ListNode(-1)
    tail = dummy_head

    def sum_all_node_vals(base: int, head: Optional[ListNode])->int:
        total = base
        node = head
        digit_position = 0
        while node:
            total += node.val * (10**digit_position)
            node = node.next
            digit_position += 1
        return total

    total = sum_all_node_vals(0, l1)
    total = sum_all_node_vals(total, l2)

    for digit in reversed(str(total)):
        tail.next = ListNode(int(digit))
        tail = tail.next

    return dummy_head.next

```
- 思いつかなかったが確かにこの方法もできる

### 読みやすくする

```python
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        l_sum = ListNode()

        node1 = l1
        node2 = l2
        node_sum = l_sum

        carry = 0
        val = 0

        while node1 is not None or node2 is not None or carry != 0:

			node1_val = node1.val if node1 is not None else 0
			node2_val = node2.val if node2 is not None else 0

			total = node1_val + node2_val + carry
    
            carry = total//10
            val = total%10
            node_sum.next = ListNode(val)

            node1 = node1.next if node1 is not None else None
            node2 = node2.next if node2 is not None else None

            node_sum = node_sum.next

        return l_sum.next

```
- 基本的に一つ目のコードを参考にして書き換えた。
### step3
最終的なコードはstep2と同じで以下のようになった。

```python
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        l_sum = ListNode()

        node1 = l1
        node2 = l2
        node_sum = l_sum

        carry = 0
        val = 0
        while node1 is not None or node2 is not None or carry != 0:
            node1_val = node1.val if node1 is not None else 0
            node2_val = node2.val if node2 is not None else 0
            total = node1_val + node2_val + carry

            carry = total//10
            val = total%10
            node_sum.next = ListNode(val)

            node1 = node1.next if node1 is not None else None
            node2 = node2.next if node2 is not None else None
            node_sum = node_sum.next

        return l_sum.next

```
