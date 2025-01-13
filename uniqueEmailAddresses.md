# Unique Email Address

## Step1

listのメールに指示された変換を加えてdictに追加して最後にdictの要素の個数を調べる。
メールの文字列を前から舐めていって"."がきたら削除"+"が来たらそっから@までを削除、@が来たらその後をそのまま返す
len(emails),len(emails[i])はどちらも100以下なので両方ループさせても大丈夫

flagで判定する方法しか思いつかなかった。

```python
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        processed_to_emails = {}
        for email in emails:
            processed = ""
            at_flag = False
            plus_flag = False
            for character in email:
                if character == "@":
                    at_flag = True
                if character == "+" and at_flag == False:
                    plus_flag = True

                if at_flag == False:
                    if plus_flag == False:
                        if character != ".":
                            processed += character
                else:
                    processed += character
            
            processed_to_emails[processed] = email
        return len(processed_to_emails.keys())
```

## Step2

<https://github.com/Hurukawa2121/leetcode/pull/14>

- @の位置を探してそれより前をlocalそれより後をdomainとする
- localから+の位置を探してそれより前だけを取得する
- +より前から.を削除する
- dictじゃなくてset
	- 確かにdictのvalueは使ってないのでdictである意味はなかった

- processedじゃなくてcanonicalizeとしてる。normalizeとかでもいいかも
normalizeとcanonicalizeの違い
normalizeは冗長性削減でcanonicalizeは比較可能化らしい
><https://it.sifr.me/study/the-difference-between-normalize-and-canonicalize/>

- pythonで書くと下のような感じ
	- これだとplus_index = -1の時にうまくいかない

```python
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        processed_emails = set()
        for email in emails:
            at_index = email.find("@")
            local = email[:at_index]
            domain = email[at_index:]

            plus_index = local.find("+")
            local_without_plus = local[:plus_index]
            local_without_plus_and_dot = local_without_plus.replace(".","")

            processed = local_without_plus_and_dot + domain
            processed_emails.add(processed)
        return len(processed_emails)
```

下のようにしたら動くが、無理やり感が強い。良い方法はないか。
```python
if plus_index >= 0:
	local_without_plus = local[:plus_index]
else:
	local_without_plus = local
```

>実務だとemailを処理する部分に絞ってテストを書きたくなりそう

テストコードとはなんだろう。名前は聞くが実態がわからない。
unittestやdoctestというテスト用のモジュールもあるらしい
<https://docs.python.org/ja/3/library/development.html>

<https://github.com/katataku/leetcode/pull/13>

- @をfindしてから分けるのではなく `email.split("@")`で十分
>https://github.com/fhiyo/leetcode/pull/17/files#r1629640510
>>`local_name = local_name.split("+")[0]`の方が読みやすい

`split()[0]`ってなんだ？
<https://docs.python.org/3/library/stdtypes.html#str.split>
split()はリストを返すからそれの1番目の要素ってことか
引数maxsplit：splitする回数を指定する。maxsplit=1なら２つ以下で分けられる
splitする要素を指定しないと空白でsplitする

```python
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        canonicalized_emails = set()
        for email in emails:
            local, domain = email.split("@", maxsplit = 1)
        
            local_without_plus = local.split("+")[0]
            local_without_plus_and_dot = local_without_plus.replace(".", "")

            canonicalized = local_without_plus_and_dot + "@" +domain
            canonicalized_emails.add(canonicalized)
        return len(canonicalized_emails)
```

- 変数名
canonicalized_emailsよりunique_emailsの方が良さそう
local, domainはlocal_name, domain_name or partでも良い
local_without _plus : local_no_plusとか
- エラーハンドリング
	- エラーが起こる例にはどのようなものがあるか
		- @がないor複数
		- 空文字列、@の前が空
		- 文字列ではない

<https://github.com/tarinaihitori/leetcode/pull/14/files/ee89bb0fd3407a8226d3bc0b05271830fedfb013#diff-d65d43698547a0f3cfcdb7f005de30ed4cd0c45ae015fd01094d6647cfa0a84a>

>pythonは比較的再代入を厭わない傾向がある

>他の解き方としてステートマシーンと正規表現がある

ステートマシーンは自分が最初にやったやつ。他の人のをみてみる

<https://github.com/hayashi-ay/leetcode/pull/25>

- ステートマシーン部分を関数化している
- 変数名
	- at_flag -> is_domain
	- plus_flag -> ignore_alias
		- メールの+以降ってエイリアスって呼ぶんだ
	- こっちの方が変数名から意味がわかって良い
- ifを多段階にすると読みにくくなるのでcontinueを使った方が良い
- なんでlistへappendして最後にstrにしてるんだろう
	- strはimmutableなのでs += t は sを複製して新しい文字列を作成している。そのためlistで操作した方がメモリ効率が良い。
	- <https://docs.python.org/3/library/stdtypes.html#str>

```python
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        def canonicalize(email):
            canonicalized = []
            is_domain = False
            ignore_alias = False
            for c in email:
                if is_domain:
                    canonicalized.append(c)
                    continue
                if c == "@":
                    canonicalized.append(c)
                    is_domain = True
                    continue
                if ignore_alias:
                    continue
                if c == "+":
                    ignore_alias = True
                    continue
                if c == ".":
                    continue
                canonicalized.append(c)
            return "".join(canonicalized)

        unique_emails = set()
        for email in emails:
            unique_emails.add(canonicalize(email))
        return len(unique_emails)
```


正規表現はわからないのでやってみる

- reの使い方
<https://docs.python.org/ja/3/howto/regex.html#>
- reのドキュメント
<https://docs.python.org/ja/3/library/re.html#module-re>
- 分割と置換
https://docs.python.org/ja/3/howto/regex.html#splitting-strings

```python
import re


class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        unique_emails = set()
        at = re.compile(r"@")
        plus = re.compile(r"\+")
	    dot = re.compile(r"\.")
        for email in emails:
             local, domain = at.split(email, 1)
             local_no_plus = plus.split(local)[0]
             local_no_plus_and_dots = dot.sub("", local_no_plus)
             canonical_email = local_no_plus_and_dots + "@" + domain
             unique_emails.add(canonical_email)
        return len(unique_emails)


         
```

正規表現を用いてるコード
<https://github.com/colorbox/leetcode/pull/28>

 - 正規表現のみを用いてやったコードをGPTに書いてもらった。

```python
import re


class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        unique_emails = set()
        pattern = re.compile(r"(^[^+@]+)(?:\+.*)?@(.+)")
        for email in emails:
            # 正規表現でローカル部分を処理し、ドメインを保持
            match = pattern.match(email)
            if match:
                local, domain = match.groups()
                # ローカル部分からピリオドを削除
                local = local.replace(".", "")
                canonical_email = f"{local}@{domain}"
                unique_emails.add(canonical_email)
        return len(unique_emails)
```

local + "@" + domainじゃなくて f"{local}@{domain}"の方がわかりやすいな
f-stringについて
<https://docs.python.org/3/reference/lexical_analysis.html#formatted-string-literals>


正規表現`(^[^+@]+)(?:\+.*)?@(.+)`
`()` : グループを示す
`^` : 行の先頭を示す
`[]` : 文字クラスを示す
`^+@` : +と@以外にマッチすることを示す
`(^[^+@]+)` : 行の先頭から+と@以外の１文字以上の連続する文字列を探してグループにする

`(?:` : キャプチャしない（グループとして保存しない）ことを示す
`\+` : +にマッチする
`.*` : ０文字以上の文字列にマッチする
`(?:\+.*)?` : +から始まる0文字以上の連続する文字列を探す。保存しない。

`@(.+)`: ＠から始まる1文字以上の連続する文字列を探してグループに保存する。

- Noneになる例
@がない、ローカルがない、ドメインがない
エイリアスは空白でもマッチする



## Step3

local = username+alias なので+より前の変数名をusernameにした
local_no_plus_and_no_dotsが長すぎて気に入らなかったため

```python
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        unique_emails = set()
        for email in emails:
            local, domain = email.split("@")
            username = local.split("+")[0]
            username_no_dots = username.replace(".", "")
            unique_emails.add(f"{username_no_dots}@{domain}")
        return len(unique_emails)
```
