# Valid Parentheses

## step1

- 最初の十分で下のコードを書いた
- ([)]のような事例に対応できていない。

```python
class Solution:
    def isValid(self, s: str) -> bool:
        brackets = {"round":0,"curly":0,"square":0}
        for character in s:
            if character == "(":
                brackets["round"] += 1
            elif character == ")":
                brackets["round"] += -1
                if brackets["round"] < 0:
                    return False
            elif character == "{":
                brackets["curly"] += 1
            elif character == "}":
                brackets["curly"] += -1
                if brackets["curly"] < 0:
                    return False
            elif character == "[":
                brackets["square"] += 1
            elif character == "]":
                brackets["square"] += -1
                if brackets["square"] < 0:
                    return False
            
        if all(val == 0 for val in brackets.values()):
            return True
        else:
            return False
```

- わからなかったため[回答](https://leetcode.com/problems/valid-parentheses/solutions/3399077/easy-solutions-in-java-python-and-c-look-at-once-with-exaplanation/)を見た

```python
class Solution:
    def isValid(self, s: str) -> bool:
        stack = []
        for c in s:
            if c in '({[':
                stack.append(c)
            else:
                if not stack or (c == ')' and stack[-1] != '(') or (c == '}' and stack[-1] != '{') or (c == ']' and stack[-1] != '['):
                    return False
                stack.pop()
        return not stack 

```

- stackに入れて後ろから確かめていくのは思いつかなかった
- `return not stack`で返すとbool値になるのは知らなかった
- [[引用符]]は"と'で違いがあるのだろうか？
	- [pep8](https://peps.python.org/pep-0008/#string-quotes)によるとどちらでもいいらしい
	- 文字列内にどちらかが含まれる場合はバックスラッシュを使うためもう一つを使うべき
- ペアのdictを定義して下のようにやった方が簡単になるのではないかと思った

```python
class Solution:
    def isValid(self, s: str) -> bool:
        stack = []
        pair = {')':'(','}':'{',']':'['}
        for c in s:
            if c in '({[':
                stack.append(c)
            else:
                if not stack or stack[-1] != pair[c]:
                    return False
                stack.pop()
        return not stack 
```

- `if len(stack) == 0 or stack.pop() != brackets[s[i]]:`としてもできるらしい
- list.pop(i)について<https://docs.python.org/ja/3/tutorial/datastructures.html>
	- リストのi番目の要素を削除しそれを返す。何も指定されない場合は最後の要素を返す。

## step2 他の人のコードを見る・コードの改善

[valid parentheses は、チョムスキー階層、タイプ-2、文脈自由文法だから、プッシュダウンオートマトンで書ける](https://discord.com/channels/1084280443945353267/1201211204547383386/1202541275115425822)
 - [[チョムスキー階層]]
	 - チョムスキーによって提唱された形式文法の分類のための理論的枠組み
	 - タイプ0~3がある
		 - タイプ2:文脈自由文法
			- 非終端記号が単独で生成規則に従う
			- プッシュダウンオートマトンで書ける
- プッシュダウンオートマトン
	- 要素
		- 状態集合
			- 有限の内部状態を保つ
		- 入力
		- スタック
		- 初期状態
		- スタック初期記号
			- スタックが空であることを示すもの
		- 状態遷移関数
			- 現在の状態・入力・スタックのトップ記号に基づいて次の状態とスタック操作を決定する
	- 自分が書いているコードのやり方に名前がついてるということ
	- 今後調べてみる
## [再帰下降構文解析](https://www.youtube.com/watch?v=b83HIvQ8bqY)でも解ける
- 難しそうだったので今後の課題とする
## [moriさん](https://github.com/hroc135/leetcode/pull/6/files/a54d34fc4e5ed6ed09a40bf695d55924b8a9b42f#diff-bfc7c633525ec3f5b431366ade3df4c7bbc2592dca465c6b4fc39882d99dec66)
- やり方はほぼ同じ
- Lifo Queueを用いている
	- [[スタックとキュー]]のうちスタックを実装するもの
	- 主にスレッド通信に用いられる
	- スタック操作に効率化されている
- 今回はListの順番の要素が必要ないためLifoQueueを用いるのも有用と考えられる。
- dictをopen_to_closeで実装した方が直感的なので良いと考える
```python
from queue import LifoQueue


class Solution:
    def isValid(self, s: str) -> bool:
        open_to_close = {
            "(": ")",
            "{": "}",
            "[": "]"
        }
        open_brackets = LifoQueue()

        for c in s:
            if c in open_to_close:
                open_brackets.put(c)
                continue
            if open_brackets.empty():
                return False
            top = open_brackets.get()
            if c != open_to_close[top]:
                return False

        return open_brackets.empty()
```

### 自分の改善

```python
class Solution:
    def isValid(self, s: str) -> bool:
        open_to_close = {
            "(":")",
            "{":"}",
            "[":"]"
        }
        stack = []

        for c in s:
            if c in open_to_close:
                stack.append(c)
                continue
            if not stack or c != open_to_close[stack.pop()]:
                return False
        
        return not stack
```

collections.dequeを用いてみる

- [Listとdequeの計算量の違い](https://wiki.python.org/moin/TimeComplexity)
	- append,popどちらもO(1)なので今回は差がない

```python
from collections import deque


class Solution:
    def isValid(self, s: str) -> bool:
        open_to_close = {
            "(":")",
            "{":"}",
            "[":"]"
            }
        stack = deque()
    
        for c in s:
            if c in open_to_close:
                stack.append(c)
                continue
            if not stack or c != open_to_close[stack.pop()]:
                return False
        
        return not stack
```

いろいろ調べていたら4時間くらいかけてしまった。これで良いのだろうか？

## step3

step2と同じ

```python
class Solution:
    def isValid(self, s: str) -> bool:
        open_to_close = {
            "(":")",
            "{":"}",
            "[":"]"
        }
        stack = []

        for c in s:
            if c in open_to_close:
                stack.append(c)
                continue
            if not stack or c != open_to_close[stack.pop()]:
                return False
        
        return not stack
```
