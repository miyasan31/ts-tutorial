# TypeScript オライリージャパン
###### tags: `メモ`

# イントロダクション

- ミスをチェックしたりすることでプログラムをより安全に
- リファクタリングを容易に
- 単体テストのほぼ半分を不要に
- 自分自身または今後のエンジニアのための文書として機能

TypeScriptを使うとプログラマーとしての生産性が2倍になり、~~通りの向こう側の可愛いバリスタとのデートが実現できるかもしれない~~

## 型安全性（type safety）
型を使って、プログラムが不正な事をしないように防ぐこと　

**JavaScript** - 不正なことをしようとしてもエラーを出さずに、（親切に、静かに）プログラマーの意図を解釈して最善な値を返してくれる親切な言語
```javascript=
3 + [] // 文字列の3と評価

const object = {};
object.foo; // undefined（未定義）と評価

function hoge(number) {
    return number/2;
};
hoge("foo"); // NaNと評価

```
**TypeScript** - 不正なことをしようとすると即座にエラーを起こし、型警察としての職務を全うすることに執着した頑固な言語

```typescript=
3 + [] // error:数字と文字列の演算はできないよ！

const object = {};
object.foo; // error:objectはfooなんて持ってないよ！

function hoge(number) {
    return number/2;
};
hoge("foo"); // error:hoge()の引数は数値型が欲しい！
```


### エラーを出すタイミング
**JavaScript** - プログラムを実行したとき  
**TypeScript** - エディターで記述したとき  



# TypeScript：全体像
# 型について
# 関数
# クラスとインターフェース
# 高度な型
# エラー処理
# 非同期プログラミングと並行、並列処理
# フロントエンドとバックエンドのフレームワーク
# 名前空間とモジュール
# JavaScirptとの相互運用
# TypeScriptのビルドと実行
# 終わりに


