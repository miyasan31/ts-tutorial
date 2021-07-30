# TypeScript イントロダクション
###### tags: `TypeScript`

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
// JavaScript
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
// TypeScript
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
// TypeScript
const a: number = 1; //　aはnumberです
const b: string = "foo"; //　bはstringです
const c: boolean[] = [true, false]; //　cはbooleanの配列です
```

明示的に型を宣言しなくてもTypeScriptは型を推論してくれる（これをTypeScriptの魔法と呼ぶらしい）
```typescript=
// TypeScript
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
// JavaScript
const foo2 = (3).toString() + [1].toString();
consle.log(foo2); // "31"
```

**しかし、TypeScriptはこの暗黙的に変換する作業をしないのである**
```typescript=
// TypeScript
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

# TypeScript 型について
###### tags: `TypeScript`


# 型について

## 型（type）
値と、それを使ってできる事柄の集まり

- boolean型
    - true, falseの２つだけ
    - 演算や操作を行えるのは、||, &&, ? の集まり
- number型
    - 全ての数値
    - 演算や操作を行えるのは、+, -, *, /, %, ||, &&, % の集まり
    - toFiexd(), toPrecision(), toString()などのnumber型に使えるメソッドも含まれる
- string型
    - 全ての文字列
    - 演算や操作を行えるのは、||, &&, ?, + の集まり
    - concat(), toUpperCase*()などのstrinｇ型に使えるメソッドも含まれる

ある変数の型がTであることに対し、型がTであることはもちろん分かるが、それ以上に**そのTを使って「具体的に何ができるか」** が重要となる


## アノテート
```javascript=
// JavaScript
function squareOf(n) {
    return n * n;
};
squareOf(2); // 4
squareOf('z'); // NaN
```
上記のsquareOf関数は引数にnumber型が渡った時のみ機能する
そこで明示的に引数の型を表すために、パラメーターの型をアノテートする

```typescript=
// TypeScript
function squareOf(n: number) {
    return n * n;
};
squareOf(2); // 4
squareOf('z'); // error:引数にnumber型をください！
```

## 基本的な型

### any型
    使用可能演算子：全ての演算
    期待する値：全ての値
    

- any型はゴッドファーザー
- あるモノの型が何であるか、プロイグラマーまたはTypeScriptがわからない場合のデフォルトの型
- **最後の手段であり、できるだけ避けるべき**
- any型を使えばなんでもできる
- JavaScriptで書いてるのと同じ

**例外** プログラマーが意図して文字列型と数値型を加算するのをTypeScriptに伝える方法

```typescript=
// TypeScript
const a: any = 666;
const b: any = ['danger'];
const c = a + b; // '666danger'
```
![](https://i.imgur.com/9yMg0BI.jpg)

### unknown型
    使用可能演算子：===, !==, ||, &&, ?
    期待する値：真偽判断

- 前もって型がわからない場合に、anyではなく代わりにunknown型を使う（ほぼ無い）
- unknown型の変数の型がわかるまでは、TypeScriptはunknown型の使用を許可しない（型ガード）

```typescript=
const a: unknown = 666; // unknown型
let b = a === 123; // boolean型 > false
let c = a + 10; // error:aの型はnumber型とは限りませんよ
if (typeof a === 'number') { // 型ガード
  let d = a + 10; // aの型がnumber型だったら演算できるよ
}
```

### boolean型
    使用可能演算子：===, !==, ||, &&, ?
    期待する値：true, false
    
```typescript=
let a = true; // boolean型
let b = false; // boolean型
const c = true; // true型、読み取り専用型
let d: boolean = true; // boolean型
let e: true = true; // boolean型の中のtrue型（リテラル型）
let f: true = false; // error:true型にfalseを割り当てできませんよ〜
```

## リテラル型（literal type）
ただ一つの値を現し、それ以外の値は受け入れない型
- constで変数を定義すると、TypeScriptはリテラル型を推論する
- TypeScript特有の型システムなので、~~Javaを使ってる友人に自慢できるよ~~
    

### number型
    使用可能演算子：===, !==, ||, &&, ?, +, -, *, /, %, <
    期待する値：整数、浮動小数点数、正数、負数、Infinity（無限大）, NaN（非数）

### bigint型
    使用可能演算子：===, !==, ||, &&, ?, +, -, *, /, %, <
    期待する値：全ての数字（number型は2^53までしか表現できない）

### string型
    使用可能演算子：===, !==, ||, &&, ?, +
    期待する値：全ての文字列

### symbol型
    使用可能演算子：===, !==, ||, &&, ?, +
    期待する値：
    
- 常に一意の値であること
- symbol型はどの値とも等しくならない
- constで定義した場合、unique symbolとTypeScriptは推論する
- unique symbolは、それかそれ以外かになる（ローランド型？）
    
```typescript=
let a = Symbol('a'); // symbol型
let b: symbol = Symbol('b'); // symbol型
let c = a === b; // boolean型 > false
let d = a + 'x'; // error;

const e = Symbol('e'); // e型のsymbol
const f: unique symbol = Symbol('f'); // f型のsymbol
let g: unique symbol = Symbol('f'); // unique symbol型はconstで定義してね

let h = e === e; // boolean型 > true
let i = e === f; // error:symbol型は一意でないといけないので常にfalseを返す
```

> 何に使うかわかりません  
> 使い方
>https://marsquai.com/a70497b9-805e-40a9-855d-1826345ca65f/1dc3824a-2ab9-471f-ad58-6226a37245ce/b18ffd4a-6899-439a-9e94-cd456f6c5f75/

### オブジェクト型（object, Object）
- オブジェクトの形状を指定する
- シンプルなオブジェクトと複雑なオブジェクトを区別できない
- JavaScriptは名前的型付けよりも、構造的な型付けが好み

**間違った使い方**
```typescript=
let a: object = {
    b: 'x'
};
console.log(a.b); //error:プロパティbは、object型に存在しません
// 一応、xやstring型とは表示してくれる
// アノテーションしたobject型の構造を教えろ〜！！ってエラー出る
```
object型は、それを構成するアタについては多くを語らず、その変数がJavaScriptのオブジェクトでありnullでないということだけを伝える

**TypeScriptに推論させてみると**
```typescript=
let a = {
    b: 'x'
};
console.log(a.b); //エラーは発生しない
// aオブジェクトはbというプロパティを持つこと推論してくれる
```

**これを明示的にアノテーションしてTypeScriptに教えてあげると**

```typescript=
let a: { b: string, c: number } = {
    b: 'x',
    c: 100
};
console.log(a.b); // OK
console.log(a.c); // OK
// aオブジェクトはbとcのプロパティを持つことTypeScriptに教えてあげることができる
```
**このオブジェクトの構造を明示的に記述する方法をオブジェクトリテラル表記という**


```typescript=
// オブジェクトリテラル表記
let c: {
    firestName: string,
    lastName: string
} = {
    firestName: 'みや',
    lastName: 'さん'
};


class Person {
    constructor(
        public firestName: string,
        public lastName: string
    ){}
};

c = new Person('みや', 'さん');
```

{ firstName: string, lastName: string } はオブジェクトの形状（構造）を表現しており、オブジェクトリテラルとクラスインスタンスの両方の形状（構造）に合致するので、TypeScriptはPersonクラスのインスタンスをcに代入できる


---

## 構造的型付け（structual typing）
プログラミングのスタイルの一種で、あるオブジェクトが特定のプロパティを持つことだけを重視し、その名前が何であるか（名前的型付け）は気にしない。一部の言語では、ダックタイピングとも呼ばれている。

## 名前的型付け⚔構造的型付け

**JavaやPHPは名前的型付け（公称型）**
- 継承関係に着目する言語
```java=
public class Animal {
    private String name;
    
    public Animal(String name) {
        this.name = name;
    }
}

public class Machine {
    private String name;
    
    public Machine(String name) {
        this.name = name;
    }
}

Animal Cat = new Animal();
// Animal = Cat は継承関係にあるのでOK
// Animal =　Machine は構造は同じだが継承関係を持ってないのでNG
// Machine = Cat は構造は同じだが継承関係を持ってないのでNG

Cat Kitten = new Cat();
// Kitten = Cat は継承関係にあるのでOK
// Kitten = Animal は継承関係にあるのでOK
```

**TypeScriptは構造的型付け**
- そのオブジェクトの構造に着目する言語
```typescript=
class Animal {
  public name: string = '';
}
class Machine {
  public name: string = '';
}

let machine: Machine = new Machine()
let animal: Animal = new Animal()
machine = animal // 同じnameを持つオブジェクトなのでOK
```

> https://qiita.com/suin/items/52cf80021361168f6b0e  
> https://scrapbox.io/nishio/%E3%80%8C%E5%90%8D%E5%89%8D%E7%9A%84%E5%9E%8B%E3%82%B7%E3%82%B9%E3%83%86%E3%83%A0%E3%81%A8%E6%A7%8B%E9%80%A0%E7%9A%84%E5%9E%8B%E3%82%B7%E3%82%B9%E3%83%86%E3%83%A0%E3%81%AE%E9%81%95%E3%81%84%E3%80%8D%E5%8A%A0%E7%AD%86%E6%A1%88
> 

## constを使ってオブジェクト宣言した場合の型推論
```typescript=
const a: { b: number } = {
    b: 12
};
console.log(typeof a.b); // 12ではなくnumber型
```
>　constで定義したのに、リテラルの１２じゃなくてnumberと推論するのはなぜか？

→ それはJavaScriptの場合、オブジェクトはミュータブル（変更可能）なので、プログラマーがconstでオブジェクトを定義しても、後でオブジェクトフィールドを変更する可能性があるのをTypeScriptは知ってるから（６章で詳しく学びましょう）

## 明確な割り当て

## インデックスシグネチャ
[key: T]: U という構文を用いて、オブジェクトがより多くのキーを含む可能性があることをTypeScriptに伝える方法

```typescript=
let data: {[key: number]: string} = {
    1: 'aaaaaaa',
    2: 'bbbbbbb'
} 


const data: { [key: number]: string } = {
  '1': 'aaaaaaa',
  2: 'bbbbbbb'
  // true: 'bbbbbbb'
};

type kata = { '1': 'aaaaaaa'; [key: string]: string };

const data1: kata = {
  '1': 'aaaaaaa',
  2: 'bbbbbbb',
  3: 'bbbbbbb',
  4: 'bbbbbbb',
  6: 'bbbbbbb',
  7: 'bbbbbbb',
  8: 'bbbbbbb',
  9: 'bbbbbbb',
  0: 'bbbbbbb'
};

type Index = 'a' | 'b' | 'c';
type FromIndex = { [k in Index]?: number };
```

---

### 型エイリアス

変数宣言を使って、値の別名を変数として宣言できるのと同様に、TypeScriptでは**ある型を指し示す型エイリアスを宣言することができる**

> 先程のオブジェクトリテラル表記を見やすく使いやすくすることができる

```typescript=
 type Age = number;
 
 type Person = {
     name: string
     age: Age // nubmer型
 };
```

Ageはただのnumber型で、型エイリアスTypeScriptによって推論されないので、明示的に型付けする必要がある

```typescript=
 // let age: Age = 21;
 let age = 21; // Ageはただのnumber型に過ぎないのでTypeScriptに推論させてもOK
 
 let driver: Person = {
     name: 'みやさん'
     age: age
 };
 
```

型エイリアスも変数と同様に、同じ名前の型を２つ宣言することはできない（同じ型構造のものを別の名前で複数宣言することはできる）


### 合併型と交差型
型の合併と交差を表現するためには特別な演算子が用意されており、合併を表現する「|」と、交差を表現する「&」がある

**合併型**  
狼、人間、狼人間の3パターンになることができる
```typescript=
type 人間 = { };
type 狼 = { };

type 狼人間　= 人間 | 狼; 
```

**交差型**  
ケンタウロスでしかない
```typescript=
type 人間 = { };
type 馬 = { };

type ケンタウロス　 = 人間 & 馬;
```


### 配列型
```typescript=
let a = [1, 2, 3]; // number[]型
let b = ['a', 'b']; // string[]型
let c: string[] = ['a']; // string[]型
let d = [1, 'a'] // (string | number[])型
const e = [2, 'a'] // (string | number[])型

let f = ['red'];
f.push('blue'); // OK！！
f.push(１); // error:fがstring[]型だからnumber型の値はpushできないよ！！
f.push(true); // error:fがstring[]型だかboolean型の値はpushできないよ！！
```

    Array<T>とT[]どちらで書くべきか？

    これに関してはどっちでも良い  
    パフォーマンスにおいても違いはない

(string | number[])型 など型を混合して定義することもできるが、**配列は、全ての要素が同じ型を持つように設計するべき**

→ 配列に何かしらの処理をするとき、添字ごとに型チェックを行わないといけないから
→ 全ての要素を二乗する場合、strgin型の値にも二乗してしまいエラーを起こす

特殊なパターン
```typescript=
let g = [];
g.push('1'); // OK
g.push(2); // OK
g.push(false); // OK
```

空の配列を初期化すると、その配列の要素が何の型であるべきかTypeScriptにはわからないので、それをany型と判別する
そして、TypeScriptは配列の型を組み合わせ始める


### タプル型

タプルは配列のサブタイプ
固定長の配列を型付けするために特別な方法であり、その配列の各インデックスでの値は特定の既知の型を持つ

```typescript=
let a: [string, string, number] = ['a', 'b', 1]; // これが既知の値

a = ['c', 'd', 'e', 10]; // error:配列[2]はnumber型を期待している
a = ['c', 'd', 5, 10]; // error:配列aの添字2までの固定長です
```

タプルは可変長の要素もサポートしている
これを使うと最小限の長さについて制約を持ったタプルを型付けできる

```typescript=
// 少なくとも一つの要素を持たないといけない配列
let friends: [string, ...string[]] = ['a', 'b', 'c', 'd'];

// 不均一な配列
let friends: [string, number, boolean, ...string[]] = ['a', 10, false, 'd', 'e', 'f'];
```

### null型
値が欠如している

### undefined型
あるものがまだ定義されていない

### void型
明示的に何も返さない関数の戻り値

### never型
決して戻ることのない関数の型

```typescript=
// nullを返す関数
function a(x: number) { 
    if (x < 10) {
     return x;
    }
    return null;
};

// undefiendを返す関数
function b() {
    return undefiend;
};

// voidを返す関数
function c() {
    let a = 2 + 2;
};

// neverを返す関数
function d() {
    throw TypeError('I always error!');
};

function e() {
    while (true) {
        dosomething();
    }
};
```

a - 明示的にnullを返す関数
b - 明示的にundefiendを返す関数
c - undefiendを返しているが、明示的にreturnを使って宣言していないのでvoidを返す関数
d, e - 例外をスローしたり、永久に実行される場合、呼び出し元に戻ることがないのでneverを返す関数

### 列挙型
列挙型の安全な仕様には落とし穴が伴うため、列挙型の使用は控えることをお勧めする
