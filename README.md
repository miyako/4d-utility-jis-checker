# 4d-utility-jis-checker
Shift_JISに変換できない文字の有無をチェック

### Examples

<img width="660" alt="スクリーンショット 2019-03-29 10 06 51" src="https://user-images.githubusercontent.com/1725068/55202443-ca60c180-520a-11e9-92f7-d0cb60ce0bc8.png">

## About

正規表現``Match regex``でチェックします。 

```
$t:="日本語ｱｲｳｴｵあいうえおabcde!#$é"
$m:=cp50221 ($t)
```

上記の例では，下記のようなオブジェクトが返されます。

```
{
	"match": false,
	"pos": 22,
	"char": "é",
	"len": 1
}
```

変換できない文字が無ければ，プロパティは``match``だけです。

```
{
	"match": true
}
```
