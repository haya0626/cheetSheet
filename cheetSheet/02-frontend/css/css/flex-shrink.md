# flex-shrink

### 「親の幅が足りないときに、どれだけ縮むか」を決める係数

```css
.item {
  flex-shrink: 1; /* 初期値 */
}
```

| 値  | 意味             |
| --- | ---------------- |
| `0` | 絶対に縮まない   |
| `1` | 通常の割合で縮む |
| `2` | 他より 2 倍縮む  |
| `n` | n 倍の重みで縮む |

## いつ flex-shrink が効くのか？

### 条件（超重要）

以下すべてを満たした時のみ発動：

- display: flex
- 主軸方向（flex-direction）に対して
- 子要素の合計サイズ > 親のサイズ

<u>余裕がある場合は一切無視される</u>

## サンプル

### 均等比率の場合

```css
.container {
  display: flex;
  width: 300px;
}

.item {
  width: 200px;
  flex-shrink: 1;
}
```

```html
<div class="container">
  <div class="item"></div>
  <div class="item"></div>
</div>
```

- 合計：400px
- 親：300px
- 不足：100px

1 + 1 = 2（
1/2 で比率がかかる）

<u>2 要素で均等に 50px ずつ縮む</u>

### 0 と 1 の比率の場合

```css
.red {
  width: 200px;
  flex-shrink: 0;
}

.blue {
  width: 200px;
  flex-shrink: 1;
}
```

```html
<div class="container">
  <div class="red"></div>
  <div class="blue"></div>
</div>
```

- 合計：400px
- 親：300px
- 不足：100px

赤：200px（完全に守られる）

青：200 - 100 = 100px

| 要素 | shrink | 縮む量 |
| ---- | ------ | ------ |
| 赤   | 0      | 0px    |
| 青   | 1      | 100px  |

### 1 と 2 の比率の場合

```css
.red {
  flex-shrink: 1;
}

.blue {
  flex-shrink: 2;
}
```

- 不足：100px
- shrink 合計：1 + 2 = 3

| 要素 | 割合 | 縮む量  |
| ---- | ---- | ------- |
| 赤   | 1/3  | 約 33px |
| 青   | 2/3  | 約 67px |
