# Top K Frequent Elements
## Step 1
最初問題文をきちんと読んでなくk番目の文字だけを出力してしまった。
下のコードでクリアできた

- 考えたこと
	- numをカウントするの前もやった気がする。もっと良い方法があったはずだが覚えていない
	- 出力はanyorderだからソートせずに出力する良い方法があるのでは
		- 前回のheap queをvalueに対して使えばもっとはやい？
			- k個のheap queに全部を入れてって最小値より大きいかどうかで決めるとか
	- このコードの計算量は？
		- 時間計算量
			- O(NlogN)
			-  N個のソートが一番時間かかる
				- 参考：https://qiita.com/drken/items/44c60118ab3703f7727f
		- 空間計算量
			- O(N)

```python
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        count = {}

        for num in nums:
            if num in count:
                count[num] += 1
            else:
                count[num] = 1

        count_sorted = sorted(count.items(), reverse=True, key=lambda x:x[1])
        k_elements = [count_sorted[i][0] for i in range(k)]

        return k_elements
```

## Step 2
## 参考にしたもの
https://github.com/ichika0615/arai60/pull/7/files/08902bdb62491c1a4bbb02577c7065ff2c39be7c#diff-e3f37462765cfdb7f4c96fe7709ec227852982f43da62904f61487b896733300


### 頻度のまとめ方
https://github.com/katataku/leetcode/pull/9#discussion_r1860320969
```python
if num not in count:
	count[num] = 0
count[num] +=1
```
<https://docs.python.org/3/library/stdtypes.html#dict.setdefault>
setdefault(key, default)
keyがあればkeyの値,なければdefaultを持つkeyを追加しkeyを返す
```python
count.setdefault(num,0)
count[num] += 1
```

<https://docs.python.org/3/library/stdtypes.html#dict.get>
get(key, default)
- keyがある場合はkeyの値を返しそうでなければdefaultを返す
```
count[num] = count.get(num, 0) + 1
```
default dict
<https://arc.net/l/quote/ovudyfku>
 - <https://qiita.com/xza/items/72a1b07fcf64d1f4bdb7>
	 - defaultdictでlambdaを使っているとそのままpickleにできない
```
count = defaultdict(int)
count[num] += 1
```
collection.Counter
<https://docs.python.org/ja/3/library/collections.html#collections.Counter>
- dictのサブクラス
- 負のカウントも取れる
- 独自メソッド
	- elements
	- most_common
	- subtract
	- total　　
### listへの追加
<https://note.nkmk.me/python-list-append-extend-insert/>
<https://docs.python.org/ja/3/library/stdtypes.html#sequence-types-list-tuple-range>
- append
- insert
- extend
### ソート手法
#### heap que
<https://docs.python.org/3/library/heapq.html>
- 課題
	- (key:value)をどうやってheap queにすればいいのかわからない。
		- (value, key)のtupleにしてheap queに追加する
```python
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        values_to_frequency = {}
        for num in nums:
            values_to_frequency[num] = 1 + values_to_frequency.get(num, 0)

        frequent_values = []
        for value, frequency in values_to_frequency.items():
            heapq.heappush(frequent_values, (frequency, value))
            while k < len(frequent_values):
                heapq.heappop(frequent_values)

        return [value for _, value in frequent_values]
```

#### quick sort
<https://github.com/ichika0615/arai60/pull/7/files/08902bdb62491c1a4bbb02577c7065ff2c39be7c#r1874546511>
- クイックソートとは
	- ランダムに値を選んでそれよりも大きいものを左、小さいものを右にする
- 計算量
	- 時間計算量
		- 平均 O(NlogN) データがランダムに分布している時
		- 最悪 O(N^2) データがすでにソートされてる or pivotで常に最大もしくは最小を選ぶとき
	- 空間計算量
		- 平均 O(logN)
		- 最悪 O(N)
	- 最悪を避ける方法
		- 再帰が深くなった時に別のソートを用いる
			- 例：深さがlog Nを超えたらheap sortを用いる
		- 事前にシャッフルする
			- O(N)かかるがデータがすでにソートされている場合をなくせる
		- 中央値近似
			- 先頭、中央、末尾から三つの要素を選んでその中央値をpivotとして用いる
				- ランダムで三つ選んでもよいが、選ぶ場所固定しても十分であるため、一般的に用いられる。
			- 中央値をpivotにしてクイックソートすると計算量はO(NlogN)になる。ただし毎回中央値を求めるとそれにO(N)かかる。そのため三つの要素の中央値を用いることでO(1)で擬似的に中央値を求める

```python
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        def quick_select(left, right):
            pivot_index = random.randint(left, right)
            pivot_frequency = values_to_frequency[values[pivot_index]]
            values[right], values[pivot_index] = values[pivot_index], values[right]

            partition_index = left
            for i in range(left, right):
                if values_to_frequency[values[i]] > pivot_frequency:
                    values[i], values[partition_index] = values[partition_index],values[i]
                    partition_index += 1
            values[right], values[partition_index] = values[partition_index], values[right]
            if partition_index == k - 1:
                return
            elif partition_index < k - 1:
                return quick_select(partition_index + 1, right)
            elif partition_index > k - 1:
                return quick_select(left, partition_index)

        values_to_frequency = collections.Counter(nums)
        values = list(values_to_frequency.keys())
        quick_select(0, len(values) - 1)
        return values[:k]
```
https://github.com/tarinaihitori/leetcode/pull/9/files/52ab57de8403d162adde7a044a97297aa8a61c41#diff-013dde8937de27960afd2d09c6325ebf53eb00c65d9d2772bc598e798bcdb877
### バケットソート
同じkeyのものをまとめてソートする。今回は頻度をkeyにした。
- 計算量
	- 時間計算量　平均O(N + k) 
		- 最悪O(N^2) 全ての要素が一つのバケットに入る場合
	- 空間計算量　O(N)
- データが均等に分布しバケットの数が適切であるときに早く、データが偏ってバケットの数が適切でない時に遅い

```python
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        num_to_count = {}
        for num in nums:
            if num not in num_to_count:
                num_to_count[num] = 0
            num_to_count[num] += 1

        bucket = [[] for _ in range(len(nums) + 1)]

        for num, freq in num_to_count.items():
            bucket[freq].append(num)

        result = []
        for i in range(len(bucket) - 1, 0, -1):
            if bucket[i]:
                result.extend(bucket[i])
            if len(result) >= k:
                return result[:k]
```

## Step 3

heapで書いた
- 変数名について
	- num_to_countは頻度(frequency)ではなく出現回数(count)を数えているのでcountを用いた
	- top_k_heapはループの中でk個の要素を維持していることをわかりやすくするため
		- frequency_heap, count_heapも考えたがこちらの方がわかりやすいと思う
- 出現回数の数え方
	- if num not in ~がkeyの存在しない時だけ例外処理をしている感じで好み
- 出力の返し方
	- `[count_to_num[1] for count_to_num in top_k_heap]`も考えた。こちらでも可能だがnumを明示的に返す方が良いか

```python
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        num_to_count = {}
        for num in nums:
            if num not in num_to_count:
                num_to_count[num] = 0
            num_to_count[num] += 1

        top_k_heap = []
        for num, count in num_to_count.items():
            heapq.heappush(top_k_heap, (count, num))
            while len(top_k_heap) > k:
                heapq.heappop(top_k_heap)

        return [num for _, num in top_k_heap]
```
