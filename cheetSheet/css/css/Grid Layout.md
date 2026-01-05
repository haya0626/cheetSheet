# Grid Layout

## Grid Layout とは

Grid Layout(display: grid)は、CSS でレイアウトを組む手法の 1 つで、要素を格子状に並べて配置することができるもの。

Grid Layout では列数と行数をあらかじめ指定し、水平線と垂直線が交差してできたエリアにアイテムを配置していくことでレイアウトを組んでいくのが特徴。

![gridLayout(1).png](<images/gridLayout(1).png>)

**`グリッドコンテナ`**

グリッドコンテナグリッドの親要素です。子要素はグリッドアイテムとして扱われます。

**`グリッドライン`**

グリッドの行や列を定義する線のことです。図のグリッドライン上にある番号はライン番号になります。ライン番号はマイナスでも表す事ができ、最終ラインを-1 としてカウントしています。これらのラインに沿ってグリッドアイテムが配置されます。

**`グリッドトラック`**

グリッドコンテナ内の行または列のことです。

**`グリッドアイテム`**

実際に配置されるコンテンツ要素です。これらはグリッドコンテナ内の特定のセルに配置され、グリッドの中で位置を決定します。

**`グリッドセル`**

グリッドコンテナ内の単一の交差点です。グリッドラインが交わる場所であり、そこにグリッドアイテムが配置されます。

`溝`

グリッドトラック間の隙間です。

## グリッドの使い方

```TypeScript
<div class="container">
  <div class="item1">1</div>
  <div class="item2">2</div>
  <div class="item3">3</div>
  <div class="item4">4</div>
  <div class="item5">5</div>
  <div class="item6">6</div>
  <div class="item7">7</div>
  <div class="item8">8</div>
  <div class="item9">9</div>
</div>
```

レイアウトイメージ

![gridLayout(2).png](<images/gridLayout(2).png>)

## グリッドコンテナを作成する

グリッドコンテナを作成するには親要素に`display: grid;`を設定する。

```TypeScript
.container {
  display: grid;
}
```

display: grid;を設定しただけでは見た目には変化はないが、適応はされる。

![gridLayout(3).png](<images/gridLayout(3).png>)

## 列を作成する

`grid-template-columns`プロパティを使用する事で、指定する値の数に応じて列が作成される。（横）

次のコードでは利用可能なスペース幅を 3 等分し、グリッドアイテムを配置します。

```TypeScript
.container {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
}
```

![gridLayout(4).png](<images/gridLayout(4).png>)

第一引数に<span style="color:red;">繰り返す回数</span>、第二引数に<span style="color:red;">幅</span>を指定

repeat 関数を使う事で、簡潔に記述する事も可能

`fr(fraction)`はグリッドコンテナ内のスペースを計算し、比率に応じてグリッドアイテムを伸縮してくれる単位。

![gridLayout(5).png](<images/gridLayout(5).png>)

`grid-template-rows`で明示的に行数と幅を指定する事もできる。（必要だったら書くぐらい）

```TypeScript
.container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: 50px 50px;
}
```

![gridLayout(6).png](<images/gridLayout(6).png>)

行は「必要な分だけ自動生成」される
高さは `grid-auto-rows` に従う（デフォ auto）

## グリッドアイテムを配置する

特に指定がない場合に各グリッドアイテムはグリッドセルに自動配置される。

位置を指定して配置する場合は次のようにします。

```TypeScript
.item1 {
  grid-column-start: 2;
  grid-column-end: 3;
  grid-row-start: 2;
  grid-row-end: 3;
}
```

![gridLayout(7).png](<images/gridLayout(7).png>)

各プロパティ値は<span style="color:red;">ライン番号</span>を指しており、それぞれのラインが交差するセルにグリッドアイテムを配置する。

![gridLayout(8).png](<images/gridLayout(8).png>)

grid-column、grid-row プロパティで start / end を同時に指定することもできる

```TypeScript
.item1 {
  grid-column: 2 / 4;
  grid-row: 2 / 4;
}
```

![gridLayout(9).png](<images/gridLayout(9).png>)

## グリッドアイテムの配置方向を変更する

グリッドアイテムは左から右に、上から下へ自動配置されるが、

`grid-auto-flow`で配置方向の変更ができる。

```TypeScript
.container {
  display: grid;

  /* 左から右 上から下 */
  grid-auto-flow: row;

  /* 上から下 左から右 */
  grid-auto-flow: column;

  /* 右から左 上から下 */
  grid-auto-flow: row;
  direction: rtl;

  /* 上から下 右から左 */
  grid-auto-flow: row;
  direction: rtl;
}
```

![gridLayout(10).png](<images/gridLayout(10).png>)
