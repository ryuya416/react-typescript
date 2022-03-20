# りあクト！

1. 言語・環境編

## 環境、歴史、ツールなど

### yarn とは

npm は Node.js 公式。<br>
npm 公式リポジトリで提供されているパッケージの追加・更新・削除に加えて<br>
各パッケージ感のバージョン整合なども自動的に行ってくれる<br>
**yarn は Facebook 製の改良版 npm コマンドのようなもの**<br><br>

create-react-app 時、Yarn がグローバルインストールされていた場合は<br>
生成したプロジェクトがデフォルトで yarn を使う様になる。<br>
(npm を使いたい場合は`--use-npm`オプションを使う)<br>

#### 代表的な yarn コマンド

- yarn(install)<br>
  package.json の記述にしたがって依存関係のあるパッケージを全てインストールする
- yadn add <packagename><br>
  指定したパッケージをインストール
- yarn remove <packagename><br>
  指定したパッケージをアンインストール
- yarn upgrade <packagename><br>
  指定したパッケージを最新バージョンに更新
- yarn info <packagename><br>
  指定したパッケージについての情報を表示

### npm-scripts とは

コマンドを省略して実行するための記述。<br>
npm よりも短くて済む。<br>

```json
"scripts": {
  "start": "react-scripts start",
  "build": "react-scripts build",
  "test": "react-scripts test",
  "eject": "react-scripts eject",
  "lint": "eslint src"
}
```

### node コマンド

```
% node
```

を入力すると REPL が起動される。

```
.help
```

で REPL コマンド一覧が表示される。<br>
.load コマンドは指定したファイルの内容を現在のセッションに読み込んでくれるコマンド。

### CreateReactApp について

React はフレームワークではなく、UI 構築のための必要最小限のライブラリである。<br>
Javascript や JSX のコンパイラや、JS と CSS を minify するためのバンドラなどを<br>
導入した上で連携させて動作させる必要があった。<br><br>

Facebook 社では React を使うための環境が最初から整備されていて、<br>
公開から 4 年後の**2016 年 7 月**にそれ(CreateReactApp)がようやくリリースされた<br>

**CreateReactApp が何をしてくれるか**

- react インストール
- react-dom インストール
- react-scripts インストール
- Babel(レガシーブラウザ対応),Webpack(ソースをまとめ最適化する)の構築
- webpack-dev-server の構築
- Jest の構築

### create react app をする

```
npx create-react-app hello-world --templete typescript
```

--templete typescript で TypeScript テンプレートを指定する。

### ESLint について

create-react-app には最初から入っているが、<br>
以下を実行して.eslintrc.json を作成する必要がある

```
eslint --init or
./node_modules/.bin/eslint --init
```

そして追加で以下のパッケージをインストールする

```
yarn add -D eslint-plugin-react @typescript-eslint/parser @typescript-eslint/eslint-plugin eslint-config-airbnb eslint-plugin-jsx-a11y eslint-plugin-import
```

### prettier について

vscode の拡張で prettier をインストールした後、設定の<br>
workspace で formatOnSave をオンにする。<br>
（そうすると.vscode フォルダができ、その中に setting.json が生成される）<br>

## JavaScript

### public/index.html と src/index.tsx の結びつき

最初のソースコードを動かして、ブラウザで見た時のソースコード

```html
<body>
  <noscript>You need to enable JavaScript to run this app.</noscript>
  <div id="root"></div>
  <script src="/static/js/bundle.js"></script>
  <script src="/static/js/0.chunk.js"></script>
  <script src="/static/js/main.chunk.js"></script>
</body>
```

main.chunk.js がレガシーな JS にコンパイルされたアプリの中身。<br>
この中にコンポーネントと同じ名前の関数が定義されている。

### JavaScript における「オブジェクト」

「JavaScript での『オブジェクト』は、暗黙の内に<br>
広義のオブジェクトと狭義のオブジェクトの 2 つがある。<br>
狭義のオブジェクトとはキーとそれに対応する値を持ったプロパティの集まりであり、<br>
一般的には『連想配列』と呼ばれる、Ruby のハッシュや Java の HashMap に相当するもの。<br>
それに対して広義のオブジェクトとは、プリミティブ値以外のすべてのものを指す」<br>

### プリミティブ型とは

Boolean,Number,BigInt,String,Symbol,Null,Undefined<br>
の７種類。**「インスタンスメソッドを持たない」**<br>
→ プリミティブ型の値を定義するのにはリテラルを使う<br>

- undefined はリテラルではなくプリミティブ型 undefined が格納されている「undifined」というグローバル変数
- リテラルを用いて定義した値はプリミティブ型であってオブジェクトではない
- null と undifined 以外のプリミティブ型には、それらを包容するラッパーオブジェクトがある<br>
  string→String、number→Number のように。
- これらは JavaScript の言語処理系の標準組み込みオブジェクトである

```javascript
const hoge1 = "hoge"; // ①
const hoge2 = new String("hoge"); // ②
hoge1 === hoge2; // => false
hoge1 === hoge2.valueOf(); // => true
//　リテラルでの記述①は、内部でラッパーオブジェクト②のように自動変換される。
// 値からメソッドが呼べるのは、この為である。(ラッパーオブジェクトのインスタンスメソッドを実行)
```

### 関数\_第一級オブジェクトとは

JavaScript では関数は組み込みオブジェクト Function のインスタンスである。<br>
そして変数に代入できることもあり、「第一級オブジェクト」と呼ばれる。<br>
他のオブジェクト型の値と同様に変数に代入したり、配列の要素やオブジェクトのプロパティ値にしたりできる<br>
関数を第一級オブジェクトとして扱うことができる言語の性質を**第一級関数**という<br>

### クラスの正体

```javascript
class Bird {
  constructor(name) {
    this.name = name;
  }
  chirp = () => {
    console.log(`${this.name}が鳴きました`);
  };
}
console.log(typeof Bird); // => 'function'
```

class はコンストラクタ関数のシンタックスシュガーである。<br>
コンストラクタ関数とはクラスの中のコンストラクタではない。<br>
プロトタイプオブジェクトを継承してオブジェクトインスタンスを生成するための<br>
独立した関数のことである。<br>

### プロトタイプベース

JavaScript はクラスベースではなく**プロトタイプベース**の言語。<br>
オブジェクトの抽象としてクラスの存在がない。<br>
オブジェクトは直接他のオブジェクトを継承する。<br>
その繰り返しによって構造的な表現をする。<br>
この繰り返しを**プロトタイプチェーン**と呼ぶ<br><br>

- クラス → 実態を持たない抽象概念
- プロトタイプ → 実態のあるオブジェクト

```
> new Array(1, 2, 3);
[ 1, 2, 3 ] > typeof Array // Array はコンストラクタ関数
'function'
> Array.prototype // Array に設定されているプロトタイプは []
```

### スプレッド構文はシャローコピー

スプレッド構文を使ってオブジェクトをコピーしようとした時、<br>
コピーされるオブジェクトの深さが１段階までしか有効でないことに注意する。<br>

```javascript
const patty = {
  name: 'Patty Rabit',
  email: 'patty@maple.town',
  address: { town: 'Maple Town' } // １階層深いオブジェクト指定
}
const rolley = { ...patty, name: 'Rolley Cocker' };
rolley.email = 'rolley@palm.town'
rolley.address.town = 'Palm Town'
console.log(patty)
{
  name: 'Patty Rabit',
  email: 'patty@maple.town',
  address: { town: 'Palm Town' } // バグ！ addressの参照先はコピーされず、元のままになる
}
```

いったん文字列に展開してから JSON にパースし直すか、Lodash の cloneDeep()を使う
などで対応する必要がある。

### ショートサーキット評価(短絡評価)

&&, ||, !, ? とかのやつ。<br>
右辺の評価を左辺の評価に委ねる記法。<br>
React 開発の文脈では文によって手続きを書き連ねるスタイルよりも<br>
こちらの方が好まれる。<br>
(return の中に書くときなどは if や else が使えなかったりする)<br>

### JavaScript における this

**その関数が実行されるコンテキストであるオブジェクトへの参照が格納されている「暗黙の引数」**

### モジュールシステム(require と import)

1995 年の誕生以来、JavaScript にはモジュールシステムがなかった。<br>
ファイル分割をするには HTML の script タグによる方法しかなく、<br>
ロードしたものは即座にグローバル空間へと紛れてしまう。<br><br>

2009 年に Node.js が誕生し、初めてモジュールシステムとしての「commonJS」が開発された。<br>
commonJS は`module.exports = hoge`や`const hode = require('./hoge')`のような<br>
構文、API を提供する。<br><br>

require と exports の問題を解決するべく<br>
**AMD**という仕様が出来上がった。<br>
これを実装した RequireJS というモジュールローダもこのとき発案される。<br><br>

そして 2015 年、ECMAScript2015 の大変革の中で<br>
**ESModules**というモジュールシステムが発案された。<br>
これが`import`と`export`による構文。<br>

## 関数型プログラミング

与えられた引数が同じなら返る値も常に一定であること : **参照透過性**<br>

- *命令型プログラミング*　
  ステップ順にしたがって連続した文章を記述していく<br>
  → 手続き型プログラミング、オブジェクト思考プログラミング<br><br>

- _宣言型プログラミング_
  出力を得る方法ではなく、出力の性質・あるべき状態を宣言する<br>
  SQL などが宣言型プログラミング言語にあたる。<br>
  「どのようにデータ取得するか」でなく「どんなデータが欲しいか」記述<br>
  「どんなデータが欲しいか」の記述を複雑化するのは困難。<br>
  **そこで生まれたのが関数型プログラミングである。**

  ```javascript
  // 関数型プログラミング例
  {
    const range = (start, end) =>
      [...new Array(end - start).keys()].map((n) => n + start);
    console.log(range(1, 101).filter((n) => n % 8 === 0));
  }
  ```

  可変性（Mutability）と不変性（Immutability）

---

関数型プログラミングのパラダイム

```
ⅰ. 名前を持たないその場限りの関数（無名関数）を定義できる
ⅱ. 変数に関数を代入できる
ⅲ. 関数の引数として関数を渡したり、戻り値として関数を返すことができる（高階関数）
ⅳ. 関数に特定の引数を固定した新しい関数を作ることができる（部分適用）
ⅴ. 複数の高階関数を合成してひとつの関数にできる（関数合成）
```

---

### 関数のカリー化

複数の引数をとる関数をより少ない引数をとる<br>
関数に分割して入れ子にする構造に変えること。<br>
→ 上位の関数で一部の引数をとり、その中でもう一部の引数をとる<br>
関数を return することで `calc(2)(3)`のような記述ができる<br>

### カリー化された関数の部分適用

```javascript
const withMultiple = (n) => (m) => n * m;
console.log(withMultiple(3)(5)); // 15
const triple = withMultiple(3);
console.log(triple(5)); // 15
```

カリー化された関数の一部を引数を固定して新しい関数をつくることを「関数の部分適用」と言う

## TypeScript

### unknown は any の型安全版

任意の型の値を代入可能だが、それ自体は何のプロパティもプロトタイプメソッドも持たない<br>

```typescript
const str = '{"id": 1, "username": "jhon_doe"}';
const user: unknown = JSON.parse(str);
console.log(user.id, user.address.zipcode);
// error TS2339: Property 'id' 'address' does not exist on type 'unknown'.
```

この例では user.id ですらもアクセスできずコンパイルに通らない。

### never は「何も代入できない型」

```ts
const greet = (friend: "Serval" | "Carol" | "cheeth") => {
  switch (friend) {
    case "Serval":
      return `Hello, ${friend}!`;
      break;
    case "Serval":
      return `Yah, ${friend}!`;
      break;
    case "Serval":
      return `Hi, ${friend}!`;
      break;
    default: {
      const check: never = friend;
    }
  }
};
```

このように case の漏れをチェックするのに使える。<br>
常に例外を投げたり、無限ループで処理が永遠に終わらない関数の戻り値などにも never 型を適用できる。<br>

### クラスの２つの顔

クラスはそれ自体が実装でありながら、型定義としての側面を併せ持つ。<br>
そのクラスの型を抽象化して定義する方法は２つ。<br><br>

- abstract 修飾子を用いて抽象クラスを作成する
- インターフェースを使う

前者は今日では TypeScript で推奨されていない。<br>
抽象クラスはその定義に実装を含むことができてしまい、実装を伴う継承は避けるべき。<br>
本来は実装を伴わない型だけを適用したいからインターフェースを使う。<br>

**クラスから型を抽出し、それを適用する書き方**

```ts
interface Shape {
  readonly name: string;
  getArea: () => number;
}
interface Quadrangle {
  sideA: number;
  sideB?: number;
  sideC?: number;
  sideD?: number;
}
class Rectangle implements Shape, Quadrangle {
  readonly name = 'rectangle';
  sideA: number;
  sideB; number;
  constructor(sideA: number, sideB: number) {
    this.sideA = sideA;
    this.sideB = sideB;
  }
  getArea = (): number => this.sideA : this.sideB;
}
```

### 関数のオーバーロード可否

アロー関数 → オーバーロード不可<br>
普通の関数(function 付き) → オーバーロード可能<br><br>

オーバーロードする予定がないメソッドの型定義はアロー関数で書いた方が<br>
意図がわかりやすい。<br>

### 型エイリアスとインターフェースの違い

```ts
type Unit = "USD" | "EUR" | "JPY" | "GBP";
type TCurrency = {
  unit: Unit;
  amount: number;
};
interface ICurrency {
  unit: Unit;
  amount: number;
}
```

型エイリアスは、新しい型を作るのではなく<br>
「無名の文字列リテラル型にそれを参照するための別名を与える」<br>
コンパイラはこれを区別している。<br>
また、MappedType や ConditionalType が使える<br><br>

インターフェース文は型の宣言なので、その型には本来の名前が与えられる<br>
型エイリアスはすでに無名でつくられた型に別名を与える。<br>
これは本来の名前が無いままになる。<br>
またインターフェースでは、再定義のような書き方は<br>
新規に上書きするのではなく拡張されていく。<br>

### in 演算子

for...in 文ではオブジェクトからインクリメンタルにキーを抽出する。<br>
型のコンテキストでは、列挙された型の中から、各要素の型の値を抽出して**MappedTypes**を作れる。<br>

```ts
const obj = { a: 1, b: 2, c: 3 }
console.log('a' in obj); // true
type Fig = 'one' | 'two' | 'three';
type FigMap = { [k in Fig]?: number };
const figMap: FigMap = {
  one: 1,
  two: 2,
  three; 3,
};
figMap.four = 4; // compile error!!
```

### keyof 演算子

オブジェクトの型からキーを抜き出して列挙する。

```ts
const permissions = {
  r: 0b100,
  w: 0b010,
  x: 0b001,
}
// permissionsからオブジェクト型を判定、そこからさらにkeyを抜き出してunion型にする
type PermsChar = keyob typeof permissions // 'r' | 'w' | 'x'
// Utility型をつくり、そこに型変換したpermissionsを渡して値を列挙する
type ValueOf<T> = T[keyof T];
type PermsNum = ValueOf<tyoeof permissions>; // 1 | 2 | 4
```

### 配列の要素から型を作りたいとき

```ts
const species = ["rabbit", "bear", "fox", "dog"] as const;
type Species = typeof species[number]; // それぞれの名前のunion型ができる;
```

### テンプレートリテラル型

JavaScript のテンプレートリテラルによる文字列を型として扱うことができる<br>
タイポなどの防止に使えそう<br>
→ DB へのクエリの生成とかに使う<br>

```ts
type DateFormat = `${number}-${number}-${number}`;
const date1: DateFormat = "2020-12-05";
const date2: DateFormat = "Dec. 5, 2020"; // compile error!
```

### 組み込みユーティリティ型

#### 各プロパティの属性を変更する

- Partial<T><br>
  T のプロパティを全て省略可能にする
- Required<T>
  T のプロパティを全て必須にする
- Readonly<T>
  T のプロパティを全て読み取り専用にする

#### オブジェクトの型からプロパティを取捨選択する

- Pick<T,K><br>
  T から K が指定するキーのプロパティだけ抽出する
- Omit<T,K><br>
  T から K が指定するキーのプロパティを省く

```ts
type Todo = {
  title: string;
  description: string;
  isDone: boolean;
};
// 以下２つはどちらも同じ型になる
type PickedTodo = Pick<Todo, "title" | "isDone">;
type Omittedtodo = Omit<Todo, "description">;
```

#### 列挙的な型を加工する

※Picked と Omit との違い → こっちは単なる union 型などの**要素**に限る

- Extract<T,U><br>
  T から U の要素だけを抽出する
- Exclude<T,U><br>
  T から U の要素を省く
  <br>
- NonNullable<T><br>
  T から null と undefined を省く
- Record<K,T><br>
  K の要素をキーとしてプロパティの値の型を T としてオブジェクトの型を作成する
  K は union 型？

#### 関数を扱う

- Parameters<T><br>
  T の引数の型を抽出してタプル型で返す
- ReturnType<T><br>
  T の戻り値の型を返す

#### 文字列リテラル型と組み合わせて使う

- Uppercase<T><br>
  T の各要素の文字列をすべて大文字にする
- Lowercase<T><br>
  T の各要素の文字列をすべて小文字にする
- Capitalize<T><br>
  T の各要素の文字列の頭を大文字にする
- Uncapitalize<T><br>
  T の各要素の文字列の頭を小文字にする
