# 4d-utility-jis-checker
Shift_JISに変換できない文字の有無をチェック

### Examples

<img width="950" alt="スクリーンショット 2019-03-29 12 30 43" src="https://user-images.githubusercontent.com/1725068/55207731-c63e9f00-521e-11e9-9511-dbe1f0b0a077.png">

もっとも字数が少ない``sjis``は最初の環境依存文字で躓いている。

Windows系の``cp932``と``cp50220``は，Macの機種依存が処理できない。

一方，MacJapaneseは，オーバーラインが処理できない。

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

パターン

### sjis

* JIS X 0208（JIS漢字）の``6879``文字については，[http://www.unicode.org/](http://www.unicode.org/Public/MAPPINGS/OBSOLETE/EASTASIA/JIS/JIS0208.TXT)のリストから生成したパターンを使用しています。

* JIS X 0201（半角カナを含む日本版のASCIIコード）については，[http://www.unicode.org/](http://www.unicode.org/Public/MAPPINGS/OBSOLETE/EASTASIA/JIS/JIS0201.TXT)のリストから生成したバターン``\u0020-\u005B\u005D-\u007D\u00A5\u203E\uFF61-\uFF9F``より，ASCIIと重複しない``\u203E\uFF61-\uFF9F``の部分を使用しています。したがって，あいまい文字（``0xA5`` 円マーク，``0x203E`` オーバーライン）は，別の文字に変換されるとしても「OK」扱いになります。

http://www.w3.org/Submission/japanese-xml/#ambiguity_of_yen

* ASCII（7ビット）については，単純に``\u0000-\u007F``を使用しています。したがって，あいまい文字（``0x5C`` 円マーク/バックスラッシュ，``0x7E`` オーバーライン/チルダ）は，別の文字に変換されるとしても「OK」扱いになります。

* 標準のJIS漢字でチェックしているので，cp932のMicrosoft機種依存文字や，x-Mac-JapaneseのApple機種依存文字はダメ扱いになります。

* そのままISO-2022-JPのチェックになります。つまり，全角文字はWindows-31Jではなく，Shift_JISの範囲でサポート，半角カタカナは全角に変換（``ESC ( I``をサポートしない），円マーク・オーバーライン・バックスラッシュ・チルダは区別する（ASCIIとJIS X 0201を使い分ける）ような基本のISO-2022-JPです。

### cp932

* Windows-31Jの``7915``文字については，[http://www.unicode.org/](http://unicode.org/Public/MAPPINGS/VENDORS/MICSFT/WINDOWS/CP932.TXT)のリストから生成したパターンを使用しています。Windowsのいわゆる環境依存文字は「OK」となりますが，``0xA5``円マークと``0x203E``オーバーラインはダメ扱いです（バックスラッシュとチルダは『OK』）。

### cp50220

* Windows-31JがShift_JISの亜種かつデファクトスタンダードであるように，ISO-2022-JPの亜種であり，デファクトスタンダードとなっているがCP50220です。デファクトスタンダード，と述べるのは，代表的なブラウザがISO-2022-JPを処理する場合，標準のISO-2022-JPではなく，CP50220として扱うためです。CP50220は，標準のISO-2022-JPとは違って半角カナをそのままエンコードすることができ（``ESC ( I``をサポートする）Windowsのいわゆる環境依存文字も扱うことができます。また，cp932とは違い，``0xA5``円マークと``0x203E``オーバーラインも「OK」です。内容的には，cp932のリストにこれらの２文字を追加しただけです。ただし，オーバーラインは``0xAF``（マクロン）ではなく，``0x203E``のほうでなければなりません。

### MacJapanese

* X-Mac-Japaneseの``7355``文字については，[http://www.unicode.org/](http://ftp.unicode.org/Public/MAPPINGS/VENDORS/APPLE/JAPANESE.TXT)のリストから生成したパターンを使用しています。

