# Find K Pairs with Smallest Sums

## step1

- 考えたこと
- 前回と同様全てをdictに入れた後heapを回す
-  max heapにする方法がわからない
	- マイナスをつける
- 和が同じnumsがある可能性がある
	- dictに入れるときはappendを使う
	- numでループしてheapに追加する

### 1回目
- 失敗した
- Memory Limit
	- for i: for jで回しているところ、もしくはdictに追加する要素が多すぎるからと考える
	- 直接heapに入れたらいいのでは
```python
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        sum_to_nums = {}
        for i in range(len(nums1)):
            for j in range(len(nums2)):
                num1, num2 = nums1[i], nums2[j]
                sum_nums = num1 + num2
                if sum_nums not in sum_to_nums:
                    sum_to_nums[sum_nums] = []
                sum_to_nums[sum_nums].append([num1, num2])

        top_k_heap = []
        for sum_nums, nums_list in sum_to_nums.items():
            for nums in nums_list:
                heapq.heappush(top_k_heap, (- sum_nums, nums))
                while len(top_k_heap) > k:
                    heapq.heappop(top_k_heap)

        top_k_nums = [nums for _, nums in top_k_heap]
        
        return top_k_nums
```
### 2回目
- 失敗　Time Limit Error
- 直接 heapに入れた
- non-decreasing orderを活用しなければいけないのではないか
```python
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        top_k_heap = []

        for i in range(len(nums1)):
            for j in range(len(nums2)):
                num1, num2 = nums1[i], nums2[j]
                sum_nums = num1 + num2
                heapq.heappush(top_k_heap, (- sum_nums, [num1, num2]))
                while len(top_k_heap) > k:
                    heapq.heappop(top_k_heap)

        top_k_nums = [num for _, num in top_k_heap]
        return top_k_nums
```
### 3回目
editorial を見て解法を確認した
<https://leetcode.com/problems/find-k-pairs-with-smallest-sums/editorial/>

```python
class Solution:
    def kSmallestPairs(self, nums1, nums2, k):
        if not nums1 or not nums2 or k == 0:
            return []

        heap = []
        result = []
        
        for i in range(min(len(nums1), k)):
            heapq.heappush(heap, (nums1[i] + nums2[0], i, 0))

        while heap and len(result) < k:
            curr_sum, i, j = heapq.heappop(heap)
            result.append((nums1[i], nums2[j]))

            if j + 1 < len(nums2):
                heapq.heappush(heap, (nums1[i] + nums2[j + 1], i, j + 1))
        
        return result
```
## Step2
### 参照したコード
https://github.com/t0hsumi/leetcode/pull/10/files/d703a89f2de1a430e07a0ed814985f0ede4d10da#diff-c26614d26c37c5ff5b5b65c6bd9c8794ce69288118e1e2c8a184fa9e335b28bf

editorialの回答はnums1全部とnums2[0]の和を入れてから小さいものを一つ入れて次に小さいと思われるものを一つ入れる
このコードはnums1[0],nums2[0]のみを入れてから次に小さいと思われるものを二つずつ入れている
こちらのコードの方は二つずつ追加されるのでheapがどんどん大きくなっていくがeditorialはheapの大きさが固定されるのでメモリ効率が良さそう
len(num1)が非常に大きいときはeditorialでは不向き

最初に(0,0)だけ入れる方が私には直感的

参考コードからの修正点
visitedではなくprocessedの方が良いという指摘があったのでprocessed
next_pair_candidates はわかりにくいので、candidates_heapとした。変数名にheapと入れる方がheap queue だとわかるので好み
processedにaddするのがheap queue に追加する前だったので後に配置した。こちらの方が意味的に正確であると思う。
```python
class Solution:
    def kSmallestPirs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        processed = set()
        candidates_heap = []
        k_smallest_pair = []

        heapq.heappush(candidates_heap, (nums1[0] + nums2[0], 0, 0))
        processed.add((0, 0))
        while len(k_smallest_pairs) < k:
            _, index1, index2 = heapq.heappop(candidates_heap)
            k_smallest_pairs.append([nums1[index1], nums2[index2]])
            
            if index1 + 1 < len(nums1 and (index1 + 1, index2)) not in processed:
                heapq.heappush(
                   candidates_heap,
                   (nums1[index1 + 1] + nums2[index2], index1 + 1, index2)
                )
                processed.add((index1 + 1, index2))
                
            if index2 + 1 < len(nums2) and (index1, index2 + 1) not in processed:
                heapq.heappush(
                    candidates_heap,
                    (nums1[index1] + nums2[index2 + 1], index1, index2 + 1)
                )
                processed.add(index1, index2 + 1)
        
        return k_smallest_pairs        
```
https://github.com/TORUS0818/leetcode/commit/09636a11c0c084c245f82bb029f198c2d0966455#diff-12341a70069caa7ec44aba610c42a0123605c2680c11a88728158764788bead7
setを用いない方法
next_index1,next_index2でどの数字が既に入れられているかを管理する
next_index1, next_index2は固定長だからメモリ効率がsetより良いのか？
 - set : O(min(k, len(nums1)*len(nums2)))
 - next_index: O(len(nums1) + len(nums2))
  kが大きい時やlen(nums1), len(nums2)が大きいときはnext_indexの方が良い


(0,0)から追加していく。また左上から一つずつheapには追加される
例えばnext_index1[1] == 2 とするとそれは (1,0)と(1,1)が追加されていることを意味する。
```python
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        k_smallest_pairs = []
        candidates_heap = [(nums1[0] + nums2[0], 0, 0)]
        next_index1 = [0] * len(nums2)
        next_index2 = [0] * len(nums1)

        while len(k_smallest_pairs) < k:
            _, index1, index2 = heapq.heappop(candidates_heap)
            k_smallest_pairs.append([nums1[index1], nums2[index2]])
            next_index1[index2] += 1
            next_index2[index1] += 1

            if index1 + 1 < len(nums1) and index2 == next_index2[index1 + 1]:
                heapq.heappush(
                    candidates_heap,
                    (nums1[index1 + 1] + nums2[index2], index1 + 1, index2)
                    )
            if index2 + 1 < len(nums2) and index1 == next_index1[index2 + 1]:
                heapq.heappush(
                    candidates_heap,
                    (nums1[index1] + nums2[index2 + 1], index1, index2 + 1)
                    )
        return k_smallest_pairs
```

#### heapが空になる場合
k > len(nums1)*len(nums2) の時は len(k_smallest_pairs) < k を満たしていてもheapが空になる
while candidates_heap and len(k_smallest_pairs) < k: とすると良い。Leetcodeでは k <= len(nums1)*len(nums2)とされているので考慮しなくていい
#### heapへのpush,popの回数
popは共通でk回
push
- (0,0)から：2k回
- nums1全体とnums2[0]のペアを最初に入れる場合：len(nums1) + k

## Step3
難しいやり方に慣れたかったのでnext_index方式で書いた

```python
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        k_smallest_pairs = []
        candidates_heap =[(nums1[0] + nums2[0], 0, 0)]
        next_index1 = [0] * len(nums2)
        next_index2 = [0] * len(nums1)

        while len(k_smallest_pairs) < k:
            _, index1, index2 = heapq.heappop(candidates_heap)
            k_smallest_pairs.append([nums1[index1], nums2[index2]])
            next_index1[index2] += 1
            next_index2[index1] += 1

            if index1 + 1 < len(nums1) and index2 == next_index2[index1 + 1]:
                heapq.heappush(
                    candidates_heap,
                    (nums1[index1 + 1] + nums2[index2], index1 + 1, index2)
                )
            if index2 + 1 < len(nums2) and index1 == next_index1[index2 + 1]:
                heapq.heappush(
                    candidates_heap,
                    (nums1[index1] + nums2[index2 + 1], index1, index2 + 1)
                )
        return k_smallest_pairs
```