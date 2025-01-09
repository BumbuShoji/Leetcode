# Two Sum

## Step1

### 考えたこと

全部の数字の和を要素として持っといてそれと一致するものを出力すれば良いのでは
j は i < j <= len(nums) にしないと同じ和を2回計算することになる
i は len(nums) - 1 にしないと　i = len(nums)で j が定義できなくなる

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        sum_to_indices = {}

        for i in range(len(nums) - 1):
            for j in range(i + 1,len(nums)):
                sum_nums = nums[i] + nums[j]
                sum_to_indices[sum_nums] = [i,j]

        return sum_to_indices[target]
```

## Step2

### Hash Mapとは

ハッシュ関数を用いてキーを一意に管理し、そのキーに対応するvalueを保持する。pythonではdictがそれ
- 方法
	- キーからハッシュ値を計算する
	- ハッシュ値を元にバケット（Hash Map内の配列）のどこに格納するかを決める
	- 値を格納・取得する
		- 格納：バケットにキーとバリューのペアを保存
		- 取得：キーからハッシュ値を計算し、対応するバケットからバリューを取り出す
- 特徴
	- 速い：検索・追加・削除がO(1)
- 衝突
	- 違うキーで同じハッシュ値が計算されてしまうこと
	- 対処法
		- チェイニング：同じハッシュ値の要素をまとめて保存する（連結リストなどを使用）
		- オープンアドレッシング：空いてるバケットを探して再確認する
			- pythonのdictはこれを用いている（二次探査）
- リハッシュ：バケットが要素で満たされてくると自動的にバケットのサイズを増やして再配置を行う
	- 衝突を軽減する
	- リハッシュの際は一時的にO(n)かかる

### 参考にしたもの

https://github.com/katsukii/leetcode/pull/2/files/7023788fe5ed525606712b0c5f4fd025d5f03f21#diff-e834754f59f018414ec6917d9f04e6be9d730c536df3f52d9181d7aa3539b651

java読んだことなくて難しい。
書かれているものをpythonで書くと以下のようになる
```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        num_to_index = {}
        for i in range(len(nums)):
            complement = target - nums[i]
            if complement in num_to_index:
                return [num_to_index[complement], i]
            num_to_index[nums[i]] = i

        raise ValueError("No two sum pair found.")
```

targetから引いた余りがnum_to_indexの中にないか探す
numsの中から探せばいいじゃんと思ったが、numsは配列なのでもう一周しないと見つからない
- 計算量
	- 時間計算量
		- O(n)
	- 空間計算量
		- 最悪O(n)

https://github.com/Hurukawa2121/leetcode/pull/11

C++も読んだことない
解法は同じ
赤黒木 std::mapというものも使えるらしい

### 赤黒木

自己平衡二分探索木の一種
-  性質
	- 各ノードが赤もしくは黒である
	- ルートノードは常に黒
	- 全ての葉ノードは黒
	- 赤ノードの子は黒
	- 任意のノードからその子孫の葉ノードに至るまでの黒ノードの数は同じ
- 利点
	- 挿入、削除、検索がO(logn)

https://github.com/t0hsumi/leetcode/pull/11

入力をソートして両端から調べていく方法もある
left、rightが行きすぎて戻ることがあるからHashmapのものより少しだけ遅そうに見える
- 計算量 n = len(nums)
	- 時間計算量
		- ソート　O(nlogn)
		- 左右からの探索　O(n)
	- 空間計算量
		- num_index_pairs O(n)

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        num_index_pairs = []
        for i, num in enumerate(nums):
            num_index_pairs.append((num, i))
        num_index_pairs.sort()

        left = 0
        right = len(nums) - 1
        while left < right:
            two_sum = num_index_pairs[left][0] + num_index_pairs[right][0]
            if two_sum == target:
                return [num_index_pairs[left][1], num_index_pairs[right][1]]

            if two_sum > target:
                right -= 1
            else:
                left += 1

        raise RuntimeError(
            f"No pair found in twoSum(): nums = {nums}, target = {target}"
        )
```

for i in range(len(nums))と for i, _ in enumerate(nums) はどっちが早いんだろう
https://stackoverflow.com/questions/11990105/rangelenlist-or-enumeratelist
rangeの方がちょい早いらしい？enumerateはtupleを出力してるからか。
rangeの方が絶対にわかりやすくはある。

## Step3

ハッシュマップで書いた

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        num_to_index = {}
        for i in range(len(nums)):
            complement = target - nums[i]
            if complement in num_to_index:
                return [num_to_index[complement], i]
            num_to_index[nums[i]] = i
```
