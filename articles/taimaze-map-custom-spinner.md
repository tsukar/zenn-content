---
title: "台湾まぜそばアプリ用にSVGとCSSで「麺をまぜる」ローディングスピナーを自作する"
emoji: "🥢"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["react", "svg", "css", "antigravity"]
published: true
---

# はじめに

台湾まぜそば研究家の [@mazetaro](https://zenn.dev/mazetaro) です。[タイマゼマップ](https://map.taiwan-mazesoba.com/) というアプリを開発し、自分が食べてきた台湾まぜそば店の記録を公開しています。

アプリ本体については以前の記事を参照ください。
@[card](https://zenn.dev/mazetaro/articles/making-of-taimaze-map)

これまでこのマップでは、画像のロード中に [Chakra UI](https://chakra-ui.com/) 標準のスピナーを表示していたのですが、台湾「まぜそば」なのだから、ロード中も「麺をまぜる」動きで待ち時間を演出したい！と思い立ち、オリジナルのローディングスピナーを実装することにしました。

完成したのはこちらのような、「箸が丼の中で回る（まぜる）」イメージのアニメーションです。
![](/images/spinner.gif)

実装の大部分は Antigravity にやってもらい、細かい部分の調整は手でやりました。今回はこの実装内容について、自分の復習も兼ねて説明記事にします。

# 実装内容

実装には SVG によるパス描画と CSS アニメーション（合成による楕円運動）という2つのポイントがあります。

## 箸らしい形状の作成（SVG によるパス描画）

箸を単純な棒2本組と見なすなら `<line>` タグでも描けますが、試してみたところあまりそれらしく見えませんでした。今回は、先端にかけて少し細くなるという形状にこだわりたかったため `<path>` タグを使用しました。

```tsx
<g transform="translate(30, 30)">
  {/* 箸の左部分 */}
  <path
    d="M-5.5,-20 L-1,20 L-4,-20 Z" // 上辺(-5.5)から下辺(-1)へ、そして上辺(-4)に戻る細い三角形のような形
    fill={color}
    stroke={color}
  />
  {/* 箸の右部分 */}
  <path
    d="M5.5,-20 L1,20 L4,-20 Z"
    fill={color}
    stroke={color}
  />
</g>
```

`d` 属性でパスの座標を指定し、箸の持ち手側（Y: -20）を太く、先端（Y: 20）を細くすることで、極力シンプルでありながらそれらしく見える箸のシルエットを作っています。

## 「まぜる」動きの実現（CSS アニメーション）

箸を単にグルグル回す（ `rotate` ）だけだと、「まぜている」感じが出ません。また、単純な円運動だと機械的な印象になってしまいます。

そこで、X軸とY軸のアニメーションを分離して合成することで、滑らかな楕円運動（＝丼の中でかきまぜる動き）を表現しました。

### ステップ1: X軸とY軸のキーフレームを別々に定義

X軸方向（左右）とY軸方向（上下）の動きをそれぞれ `keyframes` で定義します。

```tsx
// X軸の動き（左右に大きく振れる）
const moveX = keyframes`
  0% { transform: translateX(-30px); }
  100% { transform: translateX(30px); }
`;

// Y軸の動き（上下の振れ幅は小さめにする）
const moveY = keyframes`
  0% { transform: translateY(-9px); }
  100% { transform: translateY(9px); }
`;
```

### ステップ2: 2つの箱でアニメーションを合成

React コンポーネント側で、これらのアニメーションを組み合わせて適用します。 

```tsx
export const ChopsticksSpinner = memo(function ChopsticksSpinner() {
  const duration = "0.6s"; // 半周にかかる時間

  return (
    <Box>
      {/* X軸方向の動きを担当する箱 */}
      <Box
        animation={`${moveX} ${duration} ease-in-out infinite alternate`}
        ...
      >
        {/* Y軸方向の動きを担当する箱 */}
        <Box
          animation={`${moveY} ${duration} ease-in-out infinite alternate`}
          // ★重要: 位相を90度（周期の半分）ずらすことで楕円運動になる
          style={{ animationDelay: `calc(-${duration} / 2)` }}
        >
          {/* 描画本体（箸） */}
          <svg ... style={{ transform: "rotate(45deg)" }}>
            {/* ... */}
          </svg>
        </Box>
      </Box>
    </Box>
  );
});
```

ポイントは `animationDelay` で指定している位相のズレです。X軸とY軸の動きが完全に同期していると、物体は斜めに直線移動するだけです。ここでY軸のアニメーションを `duration / 2` だけずらす（＝サインとコサインの関係にする）ことで、動きが合成されて滑らかな楕円軌道になります。

# まとめ

* SVG `<path>` を使うことで、単純な線ではなく箸らしい形状を簡単に描画できた。
* X軸とY軸のアニメーションを分離・合成し、位相をずらすテクニックを使うことで、滑らかな「まぜる」楕円運動を CSS だけで実現できた。

ローディング時間はユーザーにとって退屈な時間ですが、アプリの世界観に合ったアニメーションを入れることで、少しでも楽しい体験になれば嬉しいです。
