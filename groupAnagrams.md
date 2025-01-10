# Group Anagrams

## Step1

### 考えたこと

strを文字のリストにして要素が同じものを一緒とみなす
setみたいな順番を考慮しないやつが良いか。setは重複が消えてしまうのでダメ。
ではdictか。[a, b, c] をkeyにして全ての要素を過不足なく含むものをvalueに入れる
文字列の長さが全て同じであるという仮定はないので注意。

#### 誤回答

リストはkeyにできないらしい。可変であるため。tupleならできる
sorted()の出力はリスト。tupleをリストに変えてから順序を出していた。
dict.values()の出力はdict.valuesでありlistではない。

#### dict.values()

https://docs.python.org/3/library/stdtypes.html#dict.values
https://docs.python.org/3/library/stdtypes.html#dictionary-view-objects

dict.keys(), dict.values(), dict.items() の出力は Dictionary view objectというもの。
dictの変更を動的に反映して表示する

```python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        character_to_word = {}
        for word in strs:
            characters_tuple = sorted(tuple(word))
            if characters_tuple not in character_to_word:
                character_to_word[characters_tuple] = []
            characrer_to_word[characters_tuple].append(word)
        return character_to_word.values()
```

#### 正答

wordをlistにしてからsortしてtupleにするところが無理やり感がある。良いやり方はないのか
- 計算量　
	- 時間計算量
		- sorted：O(wlogw)
			- この時w = len(word)は高々100なのであまり気にせずとも大丈夫
		- ループ：O(n)
			- n = len(strs) <= 10^4
	- 空間計算量
		- dictに保存するので O(n)

```python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        characters_to_word = {}
        for word in strs:
            characters = tuple(sorted(list(word)))
            if characters not in characters_to_word:
                characters_to_word[characters] = []
            characters_to_word[characters].append(word)
        return list(characters_to_word.values())
```

sortedは文字列に使った場合、個々が分けられたlistを返す。上のコードは `tuple(sorted(word))` で十分

#### sorted()

<https://docs.python.org/3/library/functions.html#sorted>

key: 要素に対してどのような関数をかませてから比較をするか
reverse: 昇順か降順か
## Step2

### 参考にしたもの

https://github.com/Hurukawa2121/leetcode/pull/12/files/03cabb8f64fb0ff8da925922063bddfae954593b#diff-4fa0cdca65a411c66c6c945917a26313ab139d2114a0dd45f24fd2f86a4d14b3

やり方は大体同じ
C++は dict.values()みたいなのが存在しないのかループを回してvalueを取得している

https://github.com/t0hsumi/leetcode/pull/12

wordごとにどのcharacterが出現しているかを文字コードを使って数える方法がある
- string.ascii_lowercase
<https://docs.python.org/3/library/string.html#string.ascii_lowercase>

全てのlowercaseを含む文字列
upper, digit, hexdigit(数字とA-Fの小文字および大文字), octdigits(0-7), punctuation, whitespace(空白と見做されるもの：スペース、タブ、改行、改ページ、縦のタブ), printable

```python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        frequency_to_anagrams = defaultdict(list)
        for word in strs:
            frequency = Counters(word)
            lowercase_frequency = tuple(
                frequency[lowercase] for lowercase in string.ascii_lowercase    
            )
            frequency_to_anagrams[lowercase_frequency].append(word)
        return list(frequency_to_anagrams.values())
```

出力の返し方
```python
# t0hsumiさん
return [values for values in characters_to_word.values()]
# 自分
return list(characters_to_word.values())
```
characters_to_wordじゃなくてcharacters_to_anagramsの方がわかりやすいか？

tuple(sorted(string))じゃなくてstr(sorted(string))でもいいのか。

str(sorted(string))と.join(sorted(string))は異なる
<https://docs.python.org/3/library/stdtypes.html#str>
str(object)は内部でtype(object).str(object)を呼ぶ。
https://docs.python.org/3/library/stdtypes.html#str.join
iterableを受け取りstrを返す。iterableの中にnon-strがあるとエラーになる

- 内包表記とジェネレーター式
https://docs.python.org/ja/3/tutorial/datastructures.html#list-comprehensions
https://docs.python.org/3/reference/expressions.html#generator-expressions
- リスト内包表記
	- [...]
	- リストを返す
	- 全ての要素を一旦メモリに格納する
- ジェネレーター式
	- (...)
	- generator objectを返す
	- 要素が必要になるタイミングで一つずつ生成しメモリ効率が良い
	- 一度使うと要素が消費される

## Step3

defaultdictを使ってみた
変数名をcharacters_to_anagramsに
sortした後strにするのは違和感があるのでtupleに変換

``` python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        characters_to_anagrams = defaultdict(list)
        for word in strs:
            characters = tuple(sorted(word))
            characters_to_anagrams[characters].append(word)
        return list(characters_to_anagrams.values())
```