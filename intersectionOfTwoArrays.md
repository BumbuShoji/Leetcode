# Intersection of Two Arrays

## Step1

### 考えたこと

二つの配列から共通する要素を取り出す。
片方をsetにして、もう片方をループで回して一致しているやつを保存する
両方setにして共通要素を取得するだけで十分では？

```python
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        intersection_nums = set(nums1) & set(nums2)
        return list(intersection_nums)
```

- 計算量 n = len(nums1), m = len(nums2)
	- 時間計算量　0(n+m)
		- setの作成がそれぞれO(n), O(m)だから
	- 空間計算量 0(n+m)
		- 最大の大きさn,mのsetを作るから
## Step2

- set
<https://docs.python.org/3/library/stdtypes.html#set>

変更不可能でハッシュ可能なfrozensetもある
要素の削除の方法が三つある
- remove
	- 指定した要素
	- 存在しない場合エラーを返す
- discard
	- 指定した要素
	- 存在しなくてもエラーを返さない
- pop
	- 任意の要素
記号でないものならset以外とも比較できる
可`set('abc').intersection('cbs')`
不可`set('abc') & 'cbs'`

なので下のようにしても動く

```python
# 例１
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        intersection_nums = set(nums1).intersection(nums2)
        return list(intersection_nums)
```

これと下はどのくらい異なるのだろう

```python
# 例2
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        intersection_nums = []
        set1 = set(nums1)
        for num in nums2:
            if num in set1 and num not in intersection_nums
                intersection_nums.append(num)
        return intersection_nums
```

- intersectionの実装
<https://github.com/python/cpython/blob/39e69a7cd54d44c9061db89bb15c460d30fba7a6/Objects/setobject.c#L1354-L1355>

set().intersection(iterable)はiterableの要素を一つずつ取り出してsetに含まれているか確認している。
つまりループに関しては同じ。PythonとCPythonの違いはあるかもしれない。
一方 例２はnumがlistに存在しているからそこで最大O(k) (k = len(list))かかる。

内部で小さい方と大きい方を入れ替えて小さい方を走査している

CPythonの実装怖くて手出してなかったけどChatGPTに解説してもらえば全然理解できた

<https://github.com/aoshi2025s/leetcode-review/pull/2>

- 片方をsetにして一致するか一つずつ確かめる
	- どちらかが非常に大きい際に有効
- ソートした後に一つずつ確かめる
	- 要素が莫大でメモリに乗らない際に有効

<https://github.com/t0hsumi/leetcode/pull/13>

>こういう簡単な問題だと特に、問題を見た時に想定する範囲や取り出す知識の幅がまだまだ小さいように感じた

私も同意です。

<https://github.com/nittoco/leetcode/pull/15>

>>setにしたりlistにしたりするのには抵抗がある
>変換には全てを走査するコストがかかることを意識するべき

intersection_numsじゃなくてcommonで十分かも
というか変数に代入しないでも良い

#### &とintersection()
- &の実装
<https://github.com/python/cpython/blob/39e69a7cd54d44c9061db89bb15c460d30fba7a6/Objects/setobject.c#L1510>

どういう意図があって&は両方setじゃないと使えないようにしたのだろうか
set & iterable で使えたら iterable & set でも使えないと気持ち悪いみたいなところだろうか

```python
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        return list(set(nums1).intersection(nums2))

class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        return list(set(nums1) & set(nums2))
```

- 両方をsetに変換するのとしないのはどちらが良いのか
	- 変換する
		- メリット
			- 内部でどちらが大きいか判定して小さい方をiterしてくれる
		- デメリット
			- setに変換する計算がある
	- 変換しないのメリデメはその逆
外でどっちが大きいか判定して大きい方setにすれば良いか？

```python
class Solution():
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        if len(nums1) > len(nums2):
            return list(set(nums1).intersection(nums2))
        else:
            return list(set(nums2).intersection(nums1))
```

## Step3

```python
class Solution():
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        if len(nums1) > len(nums2):
            return list(set(nums1).intersection(nums2))
        else:
            return list(set(nums2).intersection(nums1))
```
