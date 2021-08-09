# -04- 関数

###### tags: `TypeScript`

# 関数の宣言と呼び出し

## JavaScript 関数の基礎

### 関数の宣言方法及び呼び出し

```javascript
// 基本的な関数宣言のパターン
function AAA() {
    console.log("関数");
};

const BBB = function() {
    console.log("関数式（関数リテラル）");
};

const CCC = () => {
    console.log("関数式（アロー関数）");
};

// 基本的な関数の呼び出しパターン
AAA()； // console:関数
BBB()； // console:関数式（関数リテラル）
CCC()； // console:関数式（アロー関数）
```

### 間違った関数の宣言

🐝 基本的には、関数に名前をつけてあげないと実行または宣言できない

```javascript
// 名前をつけてない関数
function(){
    console.log("名前がついてない関数"); //Error:関数には何かしらの名前をつける必要がある
};
```

ただし、即時実行関数という関数宣言の場合は名前付けは不要である

```javascript
// 即時実行関数
(function () {
	console.log("即時実行関数");
})();

(() => {
	console.log("即時実行関数のアロー関数記法");
})();
```

### JavaScript 関数の拡張性

🍀 JavaScript では関数は第一級のオブジェクト

他のオブジェクトと全く同じように扱うことができ、変数に割り当てたり、他の関数の引数に渡したり、関数から return したり、オブジェクトやプロトタイプに割り当てたりすることができる。

```javascript
const funcA = (number, functionB) => {
	const stateC = functionB(number);
	return stateC;
};

function funcB(n) {
	return n * n;
}

const stateD = funcA(10, funcB);

console.log(stateD); // 100
```

## TypeScript の関数宣言における型定義

### 基本的な TypeScript 関数での型定義

関数のパラメーターに明示的にアノテーションすることができる

下記の add()は「a, b という number 型のパラメーターを受取り、関数の返り値は number 型の値を return する」と型定義している

```typescript
function add(a: number, b: number): number {
	return a + b;
}

add(10, 20); // 30
add(30, "文字列"); // Error：引数には(a: number, b: number)が定義されている
add(40); // Error:引数には２つの値が必要
```

**引数とパラメーター**

- パラメーター
  - 関数が実行されるために必要とするデータ
  - 関数宣言の一部として宣言される
  - 仮パラメーターと呼ばれる
- 引数
  - 関数と呼び出す時に渡すデータ
  - 実パラメーターと呼ばれる

```javascript
function add(a, b) {
	// aとbはパラメータ（仮パラメーター）
	return a + b;
}

add(10, 20); // 10と20は引数（実パラメーター）
```

## オプションパラメーターとデフォルトパラメーター

オブジェクト型やタプル型と同様に「?」を使って、パラメーターを省略することができる
関数のパラメーターを宣言するときは、必須のパラメータを先に指定し、省略可能なパラメーターをその後に指定する

```typescript
const log = (message: string, userId?: string) => {
	console.log(message, userId || "Not Signed in");
};

log("デフォルトパラメーターを使う");
//　console:"デフォルトパラメーターを使う Not Signed in"
log("デフォルトパラメーターを使わない", "0018318");
//　console:"デフォルトパラメーターを使わない 0018318"
```

**上記の関数を別の記述で書いてみる**

パラメーターにデフォルト値を付与して、「?」を使わないで記述してみる

```typescript
const log = (message: string, userId = "Not Signed in") => {
	console.log(message, userId);
};

log("デフォルトパラメーターを使わない");
//　console:"デフォルトパラメーターを使わない Not Signed in"
log("デフォルトパラメーターを使う", "0018318");
//　console:"デフォルトパラメーターを使う 0018318"
```

🐝 userId にデフォルト値を与えると、オプションのアノテート「?」がなくなる

デフォルト値を持たない関数のパラメーターと同様に、型エイリアスの定義の時に明示的にデフォルトパラメーターのアノテーションを加えることができる

```typescript
type Context = {
	appId?: string;
	userId?: string;
};

const log = (message: string, context: Context = {}) => {
	console.log("__result__");
};
```

## レストパラメーター

関数の引数に配列を受け取るとき、そのリストをパラメーターとして渡すことができる

```typescript
const sum = (numbers: number[]): number => {
	return numbers.reduce((total, i) => {
		return total + i;
	}, 0);
};

sum([1, 3, 4, 6]); // 14
```

上記は配列を受け取るという関数なので、配列自体の長さが変わった場合には許容できる

しかし、次の関数などで引数の合計を求めたい場合、パラメーターが 3 つに固定されているのでそれ以上それ以下の引数を受け取りたい場合にとても許容し辛い

```typescript
const sum = (a: number, b: number, c: number) => {};

sum(1, 2, 4);
```

場合によっては決まった引数をとる固定アリティ関数の代わりに、可変長引数関数を使いたい場合が出てくる

そこで便利になるのが**レストパラメーター**である

パラメーターに「...」をつけることで、そのパラメーターはレストパラメーターとして扱うことができる

```typescript
const sum = (...number: number[]): number => {
    return numbres.reduce((total, i) => total + n, 0);
};

sum(1, 2, 4, ...); // いくつ引数を受け取ってもsum()は許容できる
```

🐝 関数は最大で１つのパタメーターを持つことができ、そのパラメーターは関数のパラメータリストの中で最後に記述しないといけない

## apply, call, bind

関数の呼び出し方法には複数の方法がある

```typescript
const add = (a: number, b: number) => {
	return a + b;
};

// すべて同じ結果を返す
add(10, 20);
add.apply(null, [10, 20]);
add.call(null, 10, 20);
add.bid(null, 10, 20)();
```

- apply
  - 値を関数内の this にバインドし、２番目の引数のパラメーターとして展開する
  - 上記の例では、null を this にバインド
- call
  - apply と同様のことをするが、引数を展開する代わりに順番に適用する
- bind
  - this 引数と、引数のリストを関数にバインドするという点は同じだが、bind は関数を呼び出さない

上記の呼び出し方法で、もし this を使うとしたらこのようになる

```typescript
function functionCall(a: string, b: stirng) {
	console.log(a + this.name + b);
}

const myName = {
	name: "miyasan",
};

// apply
functionCall.apply(myName, ["はじめまして！ ", "です！"]);

// call
functionCall.call(myName, "はじめまして！ ", "です！");

// bind
const newGreeting = functionCall.bind(myName);
newGreeting("はじめまして！ ", "です！");
```

JavaScript はすべての関数に対して this 変数が定義される（他の言語ではあまりないらしい）

🐝 関数をどのように呼び出したかによって、this は異なる値を持つ

これにより、コードが脆弱になり、理解しづらくなる場合がある
このため多くのチームではクラスとメソッドの場合を除いて、関数を呼び出す全ての場所で this を禁止してる場合がある

this が脆弱である理由は、メソッドの呼び出し時にドット「.」の左側にあるものの値を取るから

```javascript
const XXX = {
    a() {
        return this;　// ②よってこのthisはXXXを指す
    };
};

// ①XXXがthisとなる
const a = XXX.a();
// ③aはa()となり、a()の本体内のthisはXXXとなるためundefined
console.log(a);　// Error
```

もうちょっと深ぼり

```javascript
function fancyDate() {
	// ③thisはDateオブジェクトなのでgetMonth()などが呼び出せる
	return `${this.getMonth() + 1}/${this.getDate()}/${this.getFullYear()} `;
}

// ①call()で呼び出したので、callの第一引数がthisに割り当てられる
// ②この呼び出しをした時、fancyDateがthisとなる
fancyDate.call(new Date());

// thisに割り当ててあげればとにかく呼び出し可能！！
fancyDate(); // NG!!
fancyDate(new Date()); // NG!!
fancyDate.apply(new Date()); // OK!!
fancyDate.bind(new Date())(); // OK!!
```

### this の型付け

基本的に this は関数に隠れているので、明示的に型付けしてあげるとわかりやすくなる

```typescript
function fancyDate(this: Date, a: number, b: string) {
	// thisはDate型だとアノテーションする
	console.log(
		`${this.getMonth() + 1}/${this.getDate()}/${this.getFullYear()}` + a + b
	);
}

// call()の第一引数はthisに割り当てられる
fancyDate.call(new Date(), 10, "引数"); // OK!!
```

### アロー関数の this の扱い方

**アロー関数における this の特徴**

- アロー関数は自身で this を持たない
- アロー関数はレキシカルスコープの this を参照する
- スコープに this がない場合、その一つ外側のスコープで this を探す
- アロー関数を呼び出す時、call, apply, bind を使った呼び出しは不適切

> レキシカルスコープとダイナミックスコープの違い
> https://wemo.tech/904

🐝 レキスカルスコープとは関数を定義した時点でその関数のスコープが決まる

🚨 大前提として JavaScript のグローバルオブジェクトは window オブジェクトのことを指し、関数の外側で使われる this はグローバルオブジェクト（window オブジェクト）を参照する

```javascript
// thisはグローバルオブジェクト
console.log(this === window); // true

// 関数（名前付き関数）
function functionA() {
	// thisはfunctionA()の呼び出し元のオブジェクトを参照
	return console.log(this.data);
}

// 関数式（関数リテラル）
const functionB = function () {
	// thisはfunctionB()の呼び出し元のオブジェクトを参照
	return console.log(this.data);
};

// 関数式（アロー関数）
const functionC = () => {
	// アロー関数で定義したこの時点で、functionC()の呼び出し元はwindowオブジェクトになる
	// functionC()内では、window === thisとなる
	return console.log(this.data);
};

// this === window
this.data = "グローバルObjがthisだよ";

const Obj = {
	data: "Objがthisだよ",
	funcA: functionA,
	funcB: functionB,
	funcC: functionC,
};

Obj.funcA(); // console:Objがthisだよ
Obj.funcB(); // console:Objがthisだよ
Obj.funcC(); // console:グローバルObjがthisだよ

const Obj2 = {
	data: "Obj2がthisだよ",
	funcAll: Obj,
	funcA: functionA,
	funcD: function () {
		const functionD = () => {
			// アロー関数で定義したこの時点で、functionD()の呼び出し元はObj2になる
			// functionD()内では、Obj2　=== thisとなる
			return console.log(this.data);
		};
		return functionD();
	},
};

Obj2.funcAll.funcA(); // console:Objがthisだよ
Obj2.funcAll.funcB(); // console:Objがthisだよ
Obj2.funcAll.funcC(); // console:グローバルObjがthisだよ
Obj2.funcA(); // console:Obj2がthisだよ
Obj2.funcD(); // console:Obj2がthisだよ
```

## 呼び出しシグネチャ

次の関数の型を表す場合、呼び出しシグネチャという記法を使う

```typescript
const add = (a: number, b: number): number => {
	return a + b;
};

// 上記のadd（）を型で表現する

// 呼び出しシグネチャ
add: (a: number, b: number) => number;

// やってしまいがちなFunction型
add: Function;
```

🐝 Function 型は、それが関数ということだけしか表現できず、どのようなパラメーターを受け取り、どんな戻り値を返しているのか表現できていない

### 呼び出しシグネチャが表現できるパラメーター

- プリミティブ型
- this の型
- 戻り値の型
- レストパラメーター
- オプションパラメーター

🚨 デフォルトパラメーターは指定不可（デフォルト値は型ではない）

```typescript
type Log = (message: string, userId?: string) => void;

// log()にのLog型をアノテーション
const log: Log = (message, userId = "Not Signed In") => {
	console.log(message, userId);
};

log("みや", "01003");
```

## 文脈的型付け

log 関数は、宣言したと同時に Log 型をアノテーションしていたので、log()のパラメーターをアノテーションする必要がなかったことに注目

これを**文脈的型付け**と呼ばれる、TypeScript の強力的な機能

### パラメーターだけでなく戻り値の型も文脈的型付けは行われる

Log 型はコールバック関数の戻り値が無いので void 型としてされており、log()は値をコールバックしないので（処理が console 出力）、型定義として成立する

もし仮に number 型を引数に受け取り、number 型を返す関数を定義すると

```typescript
// NumberCallback型は２つのnumber型を受け取り、number型をコールバックする
type NumberCallback = (a: number, b: number) => number;

const numberCallback: NumberCallback = (a, b) => {
	return a + b; // 戻り値がnumber型
};

const result = numberCallback(10, 20);
console.log(result); // 30(typeof number)
```

### 呼び出しシグネチャの完全記法と省略記法

```typescript
// 完全記法
type Log = {
	(message: stirng, userid?: string): void;
};

// ----- or -----

// 省略記法
type Log = (message: stirng, userid?: string) => void;

const log: Log = (message, userId = "Not Signed In") => {
	console.log(message, userId);
};
```

今回の log()のようなシンプルな関数であれば、省略記法の方が簡潔で見やすくなるが、もし型の中に複数の関数を定義する場合は完全記法が適するケースが多い

## オーバーロードされた関数の型

オーバーロードされた関数とは、**複数のシグネチャを持つ関数**

🍀 引数の値を判断し、それに応じた戻り値を返すことのできる関数の型

ここでは DOM API を例にオーバーロードされた関数を宣言してみる

```typescript
type CreateElement = {
  (tag: "a"): HTMLAnchorElement
  (tag: "div"): HTMLDivElement
  (tag: "table"): HTMLTableElement
  (tag: string): HTMElement
}

const createElement: CreateElement = (tag: string): HTMLElement => {
    return // .............
    }
}
```

極端な例

```typescript
type StringOrNumber = {
	(param: string): string;
	(param: number): number;
};

const StringOrNumberFunc: StringOrNumber = (params: string | number): any => {
	return params;
};

StringOrNumberFunc("みや");
StringOrNumberFunc(100);
```

# ポリモーフィズム

ポリモーフィズムは**多様性**を取り入れた型定義の方法

> ポリモーフィズムについて
> https://qiita.com/tatsumin0206/items/bbe05ccc765275382e1b

前の節で、オーバーライドされた関数の極端な例を書いたが、その関数はパラメーターを(string | number)と書いて、戻り値が any と書いたりしてスマートと言えるコードではない

これを便利にするのが**ジェネリック型パラメーター**になる

## ジェネリック型パラメーター

複数の場所で、型レベルの制約を制限するために使われるプレースホルダーの型
**多相型パラメーター**とも呼ばれる

```typescript
type StringOrNumber<T> = {
	(param: T): T;
};

const StringOrNumberFunc: StringOrNumber<string | number> = (params) => {
	return params;
};

const resultString = StringOrNumberFunc("みや"); // OK!!
const resultNumber = StringOrNumberFunc(100); // OK!!
```

呼び出しが行われるまでどんな型の引数を受け取るのかわからない場合、StringOrNumber 型というジェネリック型パラメーターを型定義することで、あらゆる型の引数を受け取ることができる

## ジェネリックはいつバインドされるか？

**AAA 型にジェネリック型をバインドする場合**

```typescript
type AAA<T> = {
	(param: T): T;
};

// 関数の宣言時点でstring型がバインドされる
const funcA: AAA<string> = (params) => {
	return params;
};
```

AAA 型を funcA()にアノテーションする時、AAA 型は何型を受け取るのかバインドしないといけない

**BBB 型のシグネチャにジェネリック型をバインドする場合**

```typescript
type BBB = {
  <T>(param: T): T;
};

const funcB: BBB = (params) => {
  return params;
};

funcB（"aaaa"）; // 関数の呼び出し時点で、string型がバインドされる
```

funcB()が実行される時、引数の型を推論して具体的な型を T にバインドする

## ジェネリックはどこで宣言できるか？

**複数の宣言パターン**

1. 完全な呼び出しシグネチャ & 呼び出し時バインド

   ```typescript
   type Filter = {
   	<T>(array: T[], f: (item: T) => boolean): T[];
   };

   const filter: Filter = (array, f) => {
   	// ......
   };

   filter([1, 2, 3], (item) => item > 0);
   ```

1. 完全な呼び出しシグネチャ & 宣言時バインド

   ```typescript
   type Filter<T> = {
   	(array: T[], f: (item: T) => boolean): T[];
   };

   const filter: Filter<string> = (array, f) => {
   	// ......
   };
   ```

1. 省略記法の呼び出しシグネチャ & 呼び出し時バインド

   ```typescript
   type Filter = <T>(array: T[], f: (item: T) => boolean) => T[];

   const filter: Filter = (array, f) => {
   	// ......
   };

   filter([1, 2, 3], (item) => item > 0);
   ```

1. 省略記法の呼び出しシグネチャ & 宣言時バインド

   ```typescript
   type Filter<T> = (array: T[], f: (item: T) => boolean) => T[];

   const filter: Filter<string> = (array, f) => {
   	// ......
   };
   ```

1. 呼び出しシグネチャを直接アノテーション & 宣言時バインド

   ```typescript
   // 関数
   function filter<T>(array: T[], f: (item: T) => boolean): T[] {
   	// ......
   }

   // 関数式
   const filter = <T>(array: T[], f: (item: T) => boolean): T[] => {
   	// ......
   };
   ```

## ジェネリックの型推論

JavaScript の標準関数の map()を例に説明する

```typescript
const map = <T, U>(array: T[], f: (item: T) => U): U[] => {
	let result = [];
	for (let i = 0; i < array.length; i++) {
		result[i] = f(array[i]);
	}
	return result;
};

const array = [1, 53, 64];
const fnA = (item) => item > 20; // booleanを返す
const fnB = (item) => item; // // itemをそのまま返す

// すべて自動的に推論してくれる
map(array, fnA); // OK!!

// もちろんアノテーションすることも可能
map<number, boolean>(array, fnA); // OK!!

// アノテーションするならすべて定義しないといけない
map<number>(array, fnA); // Error:２つの型引数が必要だよ！！
// 間違った型をアノテーションできない
map<number, string>(array, fnA); // Error:２つ目の引数はbooleanである必要があります
// これもいけちゃう
map<number, boolean | string>(array, fnA); // OK!!
map<number, boolean | string>(array, fnB); // OK!!
// これも
map<number, string>(array, fnB); // OK!!
// けどこれは無理
map<number, string>(array, fnA); // Error:fnA()はbooleanを返します
```

通常 TypeScript は関数に渡される引数をもとに、ジェネリック型に対する具体的な型を推論するので次のようなケースに出くわすことがある

次の例は、**resolve が何型なのか TypeScript が推論してくれない一例**
そのために promise オブジェクトの then メソッドの引数にどんな型が帰ってくるのか不明な場合に起きる挙動である

```typescript
const promise = new Promise((resolve) => {
	resolve(49);
});

promise.then((result) => {
	result * 2; // Error:resultはunknown型です
});
```

これを修正するには Promise のジェネリックパラメーターを明示的にアノテートする必要がある

```typescript
const promise = new Promise<number>((resolve) => {
	resolve(49);
});

promise.then((result) => {
	result * 2; // OK!!
});
```

## ジェネリック型エイリアス

型エイリアスにジェネリック型パラメーターを割り当てる

そして関数の引数に応じて、TypeScript に推論させることができる

```typescript
type MyInput<T> = {
	value: T;
	type: "text" | "number";
	fn: (item: T) => T;
};

const func = (item) => item;

// ①string型を受け取るジェネリック型エイリアス
function inputText<T>(params: MyInput<T>): T {
	const result = params.fn(params.value);
	return result;
}

// valueの型を推論した後、すべてのTにstring型をバインドする
inputText({ value: "みや", type: "text", fn: func });

// ②number型を受け取るジェネリック型エイリアス
const inputNumber = <T>(params: MyInput<T>): T => {
	const result = params.fn(params.value);
	return result;
};

// valueの型を推論した後、すべてのTにnumber型をバインドする
inputNumber({ value: 1, type: "number", fn: func });
```

## 制限付きポリモーフィズム

ジェネリック型に**少なくともこの型（またはそれ以上に拡張した型）でないといけない**という制限を設けることができる

```typescript
// 人間型
type Human = {
	name: string;
	body: boolean;
};

// 非人間型∏
type NoHuman = Human & {
	tail: boolean;
};

// ケンタウロス型
type Centaur = NoHuman & {
	horse: boolean;
};

// 狼人間型
type WolfMan = NoHuman & {
	wolf: boolean;
};

// 変態可能
type Transformation = {
	transformation: true;
};

const human: Human = { name: "人間", body: true };
const centaur: Centaur = {
	name: "ケンタウロス",
	body: true,
	tail: true,
	horse: true,
};
const wolfman: WolfMan = { name: "狼人間", body: true, tail: true, wolf: true };
const transformationWolfman: WolfMan & Transformation = {
	name: "変態可能な狼人間",
	body: true,
	tail: true,
	wolf: true,
	transformation: true,
};

// ①<T>は人間型以上に拡張した型でないといけない
const HumanCheck = <T extends Human>(body: T): void => {
	console.log(body.name);
};

HumanCheck(human); // OK!!
HumanCheck(centaur); // OK!!
HumanCheck(wolfman); // OK!!

// ②<T>は非人間型以上に拡張した型でないといけない
const NoHumanCheck = <T extends NoHuman>(body: T): void => {
	console.log(body.name);
};

NoHumanCheck(human); // Error:humanは非人間型以上に拡張してないよ
NoHumanCheck(centaur); // OK!!
NoHumanCheck(wolfman); // OK!!

// ③<T>は変態可能な狼人間型でないといけない
const WolfManCheck = <T extends WolfMan & Transformation>(body: T): void => {
	console.log(body.name);
};

WolfManCheck(wolfman); // Error:wolfmanは変態不可の狼人間
WolfManCheck(transformationWolfman); // OK!!
```

## ジェネリック型のデフォルト値

ジェネリック型にデフォルトパラメーターは付ける意味ない気がする

# 型駆動開発

- TypeScript でコードを書くときに、気が付くとコードが「型によって先導されている」ことがよくある
- これを型駆動開発という

🍀 シグネチャで関数の概略を記述し、その後で値を埋め込むプログラミングのやり方

そのシグネチャを見ただけで、その関数がどんな処理をするかについてある程度は直観的に理解できるようになる
