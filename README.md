# TypeScript オライリージャパン
###### tags: `メモ`

# イントロダクション

TypeScriptを使うことで受ける恩恵

- ミスをチェックしたりすることでプログラムをより安全に
- リファクタリングを容易に
- 単体テストのほぼ半分を不要に
- 自分自身または今後のエンジニアのための文書として機能
- プログラマーとしての生産性が2倍
- ~~通りの向こう側の可愛いバリスタとのデートが実現できる~~

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


## コンパイラー
**Java等のコンパイル実行**
1. プログラムがASTへと解釈される
2. ASTがバイトコードにコンパイルされる
3. バイトコードがランタイムによって評価される

**TypeScriptのコンパイル実行**
1. プログラムがTypeScript ASTへと解釈される
2. TypeScript ASTがJavaScriptコードにコンパイルされる
3. バイトコードがランタイムによって評価される

## 型チェッカー（typechecker）
コードが型安全であることを検証する特別なプログラム  

コンパイル時に型チェックを行うことで以下のことが確実になる
- 作成したプログラムが期待通りに動作してくれる
- 明らかなミスが存在しない
- ~~通りの向こう側の可愛いバリスタが後で電話してくれる~~

## 型システム(type system)
プログラマーが作成したプログラムに型を割り当てるために型チェッカーが使用するルールの集まり

### アノテーション
型が何かを明示的にTypeScriptに伝えるためにはアノテーションを使用する  

> アノテーションとは「値：型」という形式を取り、型チェッカーに「ここに値が見えるだろ？その横にあるのがその値の型だよ」と伝える

```typescript=
const a: number = 1; //　aはnumberです
const b: string = "foo"; //　bはstringです
const c: boolean[] = [true, false]; //　cはbooleanの配列です
```

明示的に型を宣言しなくてもTypeScriptは型を推論してくれる（これをTypeScriptの魔法と呼ぶらしい）
```typescript=
const a = 1; //　aはnumberです
const b = "foo"; //　bはstringです
const c = [true, false]; //　cはbooleanの配列です
```

### TypeScript⚔JavaScript

| 型システムの特徴 | JavaScript | TypeScript |
| -------- | -------- | -------- |
| 型はどのようにバインドされるか？| 動的 | 静的 |
| 型は自動的に変換されるか？   | はい | いいえ（多くの場合） |
| 型はいつチェックされるか？    | 実行時 | コンパイル時 |
| エラーはいつ表面化するか？   | 実行時（多くの場合） | コンパイル時（多くの場合） |

### 1. 型はどのようにバインドされるか?
JavaScriptの「型が動的にバインドされる」とは、型を知るためにはまず実行しないと型について解釈するまでに辿り着けないということ

TypeScriptは型付けしていないプログラムでさえ、実行前に（エディタ上などで）型推論を行いミスを見つけることができる

**TypeScriptは漸進的型付き言語**
- プログラム内の全てのものの型がわかっている場合にTypeScriptは最も力を発揮するが、**プログラムをコンパイルするためには必ずしも全ての型がわかっている必要はない**

### 2. 型は自動的に変換されるか?
JavaScriptは弱く型付けされた言語なので、最善の結果を返せるように一連のルールを適用してプログラマーの意図を理解しようとしてくれる
```javascript=
const foo1 = 3 + [1];
consle.log(foo1); // "31"
```
JavaScriptはこのプログラムを、
1. 3が数値、[1]が配列であることを理解する
2. 演算子＋を使っているので、その２つを連結したいのだろうと理解する
3. ３は数値だが暗黙的に文字列に変換し、”３”とする
4. [1]は配列だが暗黙的に文字列に変換し、”１”とする
5. それらの文字を連結し、”３１”として値を返してくれる

また上記のフローを明示的にプログラミングすることができる
```javascript=
const foo2 = (3).toString() + [1].toString();
consle.log(foo2); // "31"
```

**しかし、TypeScriptはこの暗黙的に変換する作業をしないのである**
```typescript=
const hoge1 = 3 + [1];
consle.log(hoge1); // error:数値型と配列型だから違うモノ同士だよ

const hoge2 = (3).toString() + [1].toString();
consle.log(hoge2); // "31"
```
正しそうに見えないことをするとエラーを吐き、型を明示的にしてあげることでTypeScriptは手を引いてくれる

JavaScriptが行う暗黙的な型の変換は、突き止めの難しいエラーやバグを生む温床になりかねない  

要するに、**型を変換する必要がある場合は明示的に書きましょうというお話でした**

### 3. 型はいつチェックされるか?
**JavaScript** - プログラム実行時にチェック  
**TypeScript** - コードを書いた時に型推論を行いチェック

### 4. エラーはいつ表面化するか?
**JavaScript** - プログラム実行時にエラーをthrow  
**TypeScript** - コードをコンパイルした際にエラーをthrow

## TypeScriptを書くために
### tsconfig.json
すべてのTypeScriptプロジェクトは、そのルートディレクトリにtsconfig.jsonと呼ばれるプロジェクトで扱うTypeScriptファイルについて設定を書かなければいけない

```json=
{
    "compilerOptions": {
        "lib": ["es2015"],
        "module": "commonjs",
        "outDir": "dist",
        "sourceMap": true,
        "strict": true,
        "target": "es2015"
    },
    "include": [
        "src"
    ]
}
```

tsconfig.jsonのオプション

| オプション | 説明 |　記述例 |
| ------- | -------- | -------- |
| include | TSCがTypeScriptファイルを見つけるために、どのフォルダーを探すべきか | src |
| lib     | コードを実行する環境にどのAPIが存在しているとTSCが想定すべきか | es2015 |
| module | TSCがコードをどのモジュールシステムにコンパイルするべきか | commonjs | 
| outDir  | 生成するJavaScriptコードをTSCがどのフォルダに格納するべきか | dist, out |
| strict  | 厳密にコードをチェックするか |　true |
| target  | TSCがコードをどのJavaScriptバージョンにコンパイルするか | es3, es5, es2015, es2016 |


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


