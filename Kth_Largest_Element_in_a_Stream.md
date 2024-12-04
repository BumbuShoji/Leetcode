# Kth Largest Element in a Stream

## step1
- 手でやる場合を考える
	- リストを用意する
	- 新しいものをリストに入れて大きい順に並び替える
	- K番目を取り出す
- 下のコードを書いた
	- 10分くらい
```python
class KthLargest:

    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.nums = nums

    def add(self, val: int) -> int:
        self.nums.append(val)
        self.nums.sort(reverse = True)
        return self.nums[self.k - 1]
```

## step2 　他の人のコードを見る・自分の改善

### 読んだコード

https://github.com/tarinaihitori/leetcode/pull/8/files/b93a4ee64137e37666b03b99b6c0602ce8cf2a10#diff-6e7e84a8c0c13aabe8bad58b32c7215762116a974e2143b20ee931bf24b51a2f
https://github.com/katataku/leetcode/pull/8/files/7c8c51d9b82aa240bd4a4b5ed8bb657ba137baf5#diff-6e7e84a8c0c13aabe8bad58b32c7215762116a974e2143b20ee931bf24b51a2f
https://github.com/haniwachann/leetcode/pull/1/files/991655bbb5a07f7c508494233b6b22494d5517df#diff-6f3d970863dc500b1778101efd01e2a76334accabb99be3850492a4e4c30240d

- この方はC++でやってる。いずれは自分もpython以外を読めるようになった方がいい気がしている
	- 割と読めた。C++とpythonは似てるから割と読めるんですね


- [heapというものがあるらしい](https://github.com/tarinaihitori/leetcode/pull/8/files/b93a4ee64137e37666b03b99b6c0602ce8cf2a10#diff-6e7e84a8c0c13aabe8bad58b32c7215762116a974e2143b20ee931bf24b51a2f)
- heap queとは
	- [heapqのドキュメント](https://docs.python.org/3/library/heapq.html)
	- 全ての親ノードの値が子ノードの値以下であるバイナリツリー
	- log(n)で追加や削除が行えるので速い
	- k番目の要素のみを取得できればいいので他の要素が大きい順に並んでいる必要などはない
heapqを使った実装<https://github.com/katataku/leetcode/pull/8/files/7c8c51d9b82aa240bd4a4b5ed8bb657ba137baf5#diff-6e7e84a8c0c13aabe8bad58b32c7215762116a974e2143b20ee931bf24b51a2f>

- initでheapqを作成するときも個別の操作を書くのではなくaddを使用することでコードがシンプルになっているのが良いと感じた
```python
class KthLagest:

	def __init__(self, k: int, nums: List[int]):
		self.k = k
		self.kth_largest_nums = []
		for num in nums:
			self.add(num)

	def add(self, val: int) -> int:
		heapq.heappush(self.kth_largest_nums, val)
		if len(self._kth_largest_nums) > self.k:
			heapq.heappop(self.kth_largest_nums)
		return self.kth_largest_nums[0]

```

heapifyを使う実装も考えられる

```python
class KthLargest:

	def __init_(self, k: int, nums: List[int])
		self.k = k
		self.nums = nums

		heapq.heapify(self.nums)

		while k < len(self.nums):
			heapq.heappop(self.nums)

	def add(self, val: int) -> int:
		heapq.heappush(self.nums, val)
		
		if len(self.nums) > self.k:
			heapq.heappop(self.nums)
		return self.nums[0]
```

[C++であればmultisetが使える](https://github.com/haniwachann/leetcode/pull/1/files/991655bbb5a07f7c508494233b6b22494d5517df#diff-6f3d970863dc500b1778101efd01e2a76334accabb99be3850492a4e4c30240d)
- pythonに同じようなライブラリーはないのだろうか
	- 外部ライブラリーに[SortedList](https://grantjenks.com/docs/sortedcontainers/sortedlist.html)がある
	- log(n)で挿入、削除が可能
	- [SortedListの仕組み](https://zenn.dev/pokeshun/articles/f5362825e512cf)
		- Listの要素を分割して保持している
		- Listの分割サイズが1000であるためkが小さい問題では普通のListとあまり変わらないのではないか
SortedListの場合

```python
from sortedcontainer import SortedList


class KthLargest:

	def __init__(self,k: int,nums: List[int]):
		self.k = k
		self.nums = SortedList()

		for num in nums:
			self.add(num)

	def add(self, val: int) -> int:
		self.nums.add(val)

		if len(self.nums) > self.k:
			self.nums.pop(0)

		return self.nums[0]
```

## Step3

heapqを使った実装を書いてみる
```python
class KthLargest:
    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.kth_largest = []

        for num in nums:
            self.add(num)

    def add(self, val: int):
        heapq.heappush(self.kth_largest, val)

        if len(self.kth_largest) > self.k:
            heapq.heappop(self.kth_largest)

        return self.kth_largest[0]
    
```

タイピングのミスがめっちゃ多い。タイピングを鍛えるにはたくさん書くしかないのだろうか。
