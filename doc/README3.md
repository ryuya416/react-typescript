# りあクト！

3. React 応用編

# Redux

Redux の掲げる３原則

- Single source of truth(信頼できる唯一の情報源)
  アプリケーションの状態は、ただ一つの store オブジェクトによる
  ツリー構造で表現される。
  store が一つに集約されることで、デバッグやテストがやりやすくなる。

- State is read-only(状態は読み取り専用)
  　直接書き換えることができない。
  　書き換える為には、どんなイベントが起こったかを表現する action
  　を発行すること。

- Changes are made with pure functions(変更は純粋関数にて行われる)
  Redux では store は状態を格納する一つのステートツリーと、
  それを更新するための reducer という純粋関数で表現される装置から構成される。

## Redux を使ってみる

### App.tsx

最初に store を初期化して`Provider`コンポーネントに
props として渡す必要がある。

```tsx
const store = createStore(counterReducer, initialState);
ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root") as HTMLElement
);
```

### actions

独自 UtilityType で action の値に型をつける

```tsx
export const CounterActionType = {
  ADD: "ADD",
  DECREMENT: "DECREMENT",
  INCREMENT: "INCREMENT",
} as const;
type ValueOf<T> = T[keyof T];
```

**dispatcher に action を発行する時は直のオブジェクト形式ではなく、
action creator 関数の戻り値を使う様にする。**

### reducers

reducer とは、`(prevState,action) => newState`で表現される**純粋関数**である。

## ReduxStyleGuide

Redux を使用するとコードの記述量が多くなってしまう。
そこに対するルール決めの動きがいくつか生まれ始めた。
(例)

- FSA(Flux Standard Action)
- Ducks

2019 年 11 月に公式が出したガイドラインが**ReduxStyleGuide**

### 優先度 A：必須

1. state を直接書き換えない

2. reducer に副作用を持たせない
   外部システム通信や reducer の外の変数を書き換えるなど

3. シリアライズできない値を state や action に入れない
   Promise 関数やクラスインスタンスのような`JSON.stringfy()`したときに
   値が同一であることが保証されないものを state や action に入れてはいけない

4. store は１アプリにつき１つのみ

### 優先度 B：強く推奨

### 優先度 C：普通に推奨

- Action に関するもの 5. action を setter でなくイベントとしてモデリングする 6. action の名前は意味を的確にしたものにする 7. action タイプ名を「ドメインモデル/イベント種別」のフォーマットで 8. action を FSA に準拠させる 9. dispatch する action は直に書かず action creator を使って生成

- ツールやデザインパターンの利用に関するもの 10. Redux のロジックを書くときは ReduxToolkit を使う 11. イミュータブルな状態の更新には Immer を使う 12. デバッグには ReduxDevTools 拡張を使う 13. ファイル構造には「feature folder」または Ducks パターンを適用する

- 設計に関するもの 14. どの状態をどこに持たせるかは柔軟に考える 15. フォームの状態を Redux に入れない 16. 複雑なロジックはコンポーネントの外に追い出す 17. 非同期処理には ReduxThunk を使う

## ReduxToolkit を使う

当初は「ReduxStarterKit」という名前で、
最初のバージョンは 2018 年 2 月に公開された。

**ReduxToolkit の提供する主要な API**

- configureStore
  各種デフォルト値が設定可能な`createStore`のカスタム版

- createReducer
  reducer の作成を簡単にしてくれる

- createAction
  action creator を作成してくれる

- createSlice
  action の定義と action creator, reducer をまとめて生成できる

### 良い構成は Ducks パターンによるディレクトリ分けと createSlice 使用

**createSlice を定義したらトップレベルのファイルに追加**

```tsx
import React from "react";
import ReactDOM from "react-dom";
import { configureStore } from "@reduxjs/toolkit";
import { Provider } from "react-redux";
// eslint-disable-next-line import/no-unresolved
import { counterSlice } from "features/counter";
import reportWebVitals from "./reportWebVitals";
import App from "./App";
import "semantic-ui-css/semantic.min.css";
import "./index.css";
const store = configureStore({ reducer: counterSlice.reducer });
ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root") as HTMLElement
);
```

**RTK を使用しないトップレベルファイル**

```tsx
import ReactDOM from "react-dom";
import { createStore } from "redux";
import { Provider } from "react-redux";
import { counterReducer, initialState } from "reducer";
import reportWebVitals from "./reportWebVitals";
import App from "./App";
import "semantic-ui-css/semantic.min.css";
import "./index.css";
const store = createStore(counterReducer, initialState);
ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root") as HTMLElement
);
```

## useReducer

Redux ではアプリケーションを包括するグローバルな状態を action と
reducer で管理していたが、useReducer は同じことを個別のコンポーネントで可能にする HooksAPI

**useReducer**
`const [state, dispatch] = useReducer(reducer, initialState)`

**useState**
`const [state, setState] = useState(initialState)`

**createStore**
`const store = createStore(reducer, initialState)`

### stateHook の正体

`useState`は機能を限定した`useReducer`である。
Redux ではアプリケーションを包括するグローバルな状態を action と reducer で管理していた。
useReducer は同じことを個別のコンポーネントで可能にする Hooks API

**クラスコンポーネントが state をメンバー変数として持つのに対して
stateHook はその状態をどこでどうやって保持しているか**
React では Ver16.0 から Fiber というレンダリングのためのアーキテクチャを導入している。
React が提供する`useXXX`の HooksAPI コンポーネントの中で使用すると
仮想 DOM にマウントされた ReactElements が対応する Fiber の「メモ化された状態」
領域の中にその Hooks のオブジェクトがつくられる。

## コンポーネントの中で非同期処理を行う

コンポーネントの中で非同期処理をどこに記述するかという問題は
当初の React にとっては大きな課題であった。
class component の中で非同期処理を行い、フェッチした結果を
state に入れて render()の中で表示させる方法は
時間軸によって変わるライフサイクルメソッドの複数箇所に
同じ処理を記述する必要があった。

その後 React の成長に伴い、Redux では**ミドルウェア**という仕組みが
用意されていた。　外部から dispatcher を拡張して、
reducer の実行前後に任意の処理を追加できるようにするためのもの。

Redux では`createStore`の第３引数に dispatch()関数をラップした
処理が記述されたミドルウェアを適用できる。

**有名なミドルウェア**

- Redux Persist (store 内の state を loal strage などに永続化)
- redux-logger (action の発行と state の遷移履歴の記録)
- ReduxThunk, redux-saga, redux-observable (非同期処理を扱う)
  Redux 公式サイトの「Ecosystem」に載っている。

## Redux 不要論

React16.3.0 で ContextAPI が導入された。
これは React 本体の機能のみでグローバルステートを管理するソリューション。

```tsx
const ThemeContext = React.createContext("light");
constApp = () => {
  const theme = useState("light");
  return (
    <ThemeContext.Provider value={theme}>
      <ThemedButton />
    </ThemeContext.Provider>
  );
};
const ThemedButton = () => {
  const theme = userContext(ThemeContext);
  return <Button theme={theme} />;
};
```

## フロントエンドのマイクロサービスアーキテクチャ

SPA への要求が高度化するにつれて機能が増えていくと、
モノリシックなアーキテクチャではいつか無理がきてしまう。
究極的にはそれぞれのドメインに分割したコンポーネント群を
Web Components の形式で出力し、それらを束ねてブラウザに
読み込ませるようにすれば、各ドメインがどんなフレームワークで作られようが関係なくなる。

フロントエンドで本格的にマイクロサービスアーキテクチャが導入されれば、
中央集権的な状態管理手法派生率しなくなる。
ドメイン単位で分割され、それぞれが依存することなく動作し、
ときには異なるフレームワークで作られていることもあるコンポーネントなので
個々がうまく競合することなく動作しなくてはならない。

## GraphQL Apollo Client の可能性

Apollo Client は Redux を縫合する形で構築されていたが、
2017 年 10 月リリースの ver2 からは Redux をパージして、自前で状態管理システム
を持つ様になった。
ローディング状態やエラー状態の管理加え、データはキャッシュベースで
取り回され、そのキャッシュは Apollo が勝手に正規化してくれたりする。
