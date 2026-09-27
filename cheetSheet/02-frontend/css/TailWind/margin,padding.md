# Tailwind CSS - margin / padding

## 概要

Tailwind CSS では、`m-*` / `p-*` を使って余白を指定する。

```text
m = margin  : 要素の外側
p = padding : 要素の内側
```

## まず覚えること

| 指定  | 意味                 |
| ----- | -------------------- |
| `m`   | 四方向の margin      |
| `mt`  | 上                   |
| `mr`  | 右                   |
| `mb`  | 下                   |
| `ml`  | 左                   |
| `mx`  | 左右                 |
| `my`  | 上下                 |
| `p-*` | padding も同じ考え方 |

## 基本例

```html
<div class="m">外側に余白</div>
<div class="p">内側に余白</div>
<div class="mx-auto">左右marginをauto</div>
<div class="px py-2">左右4・上下2</div>
```

## 値の考え方

Tailwind の数字は原則そのまま px ではない。

たとえば標準設定では、

```text
1  = 0.25rem
2  = 0.5rem
4  = 1rem
8  = 2rem
```

のように spacing scale へ変換される。

## 実務でよく使う形

```html
<button class="px py-2">保存</button>
```

```text
px
→ ボタン左右の余白

py-2
→ ボタン上下の余白
```

カード。

```html
<div class="p-6">
  <h2 class="mb">タイトル</h2>
  <p>本文</p>
</div>
```

## 負の margin

```html
<div class="-mt-2"></div>
```

位置をずらせるが、レイアウト調整を負の margin だけで無理に解決しない。

## arbitrary value

既定 scale にない値。

```html
<div class="mt-[18px]"></div>
```

便利だが、多用するとデザインルールが崩れやすい。

## 覚えておくこと

```text
m = 外
p = 内

t = top
r = right
b = bottom
l = left
x = 左右
y = 上下
```
