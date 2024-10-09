# モジュールの分離

FSDの枠組みの中で、すべてのモジュールは責任の領域（レイヤー、スライス、セグメント）に分配されています。

レイヤーは縦に組織されています。

- "下層"には再利用可能なモジュール（ui-kit、プロジェクトの内部ライブラリ）があり、最も抽象的です。
- "上層"に進むにつれて、より特定的なモジュールが配置されます。

どのスライスに属していても、各モジュールは[**公開アクセスインターフェースを提供する義務があります**][refs-public-api]。

## 要件 {#requirements}

各モジュールが他のアプリケーションと相互作用する際には、いくつかの要件を考慮して設計されます。

1. **他のモジュールとの弱い結合**
    - *1つのモジュールの変更は、他のモジュールに対して弱く予測可能な影響を与えるべきです。*
1. **高い結合性** - 各モジュールの責任は「1つのタスク」に焦点を当てています。
    - *モジュールがあまりにも多くの責任を持つ場合（「やりすぎる」場合）、それはできるだけ早く認識されるべきです。*
1. **アプリケーション全体での循環依存の不在**
    - *これはしばしば予期しない望ましくない動作を引き起こすため、完全に避けるべきです。*

## ルール {#rule}

これらの要件を満たすために、方法論の枠組みの中で基本的なルールを守る必要があります。

:::info 重要

モジュールは「下層」のモジュールにのみ依存でき、同じ層またはそれ以上の層のモジュールには依存できません。

:::

- `features/auth` **は** `features/filters` **に依存できません** **し、逆も同様です。**
- `features/auth` **は** `shared/ui/button` **に依存できますが、逆はできません。**

このルールに従うことで、依存関係を**「一方向」に保つ**ことができ、これにより**循環インポートを自動的に排除し**、モジュール間の依存関係の追跡を大幅に**簡素化**します。

## 問題の特定 {#identifying-problems}

<!-- 
TODO 方法論の経験を積んだ後、このセクションをより詳細にする
-->
このルールの違反は問題の信号です。

1. モジュールが自層の他のモジュールから**インポートを持つ**
    - モジュールが**過剰に細分化されている**か、**余分な責任を持っている**可能性があります。
    - インポートされたモジュールと**統合する**か、**部分的または完全に下層に移動する**か、上層のモジュールに依存関係のロジックを移動する必要があります。
1. モジュールが自層の多くのモジュールから**インポートされる**
    - モジュールが**余分な責任を持っている**可能性があります。
    - **部分的または完全に下層に移動する**か、上層のモジュールに依存関係のロジックを移動する必要があります。
1. モジュールが自層の多くのモジュールから**インポートを持つ**
    - モジュールが**別の責任領域に属している**可能性があります。
    - **部分的または完全に上層に移動する**必要があります。

## 参照 {#see-also}

- [(ガイド) 低い結合性の達成について][refs-low-coupling]
- [(ディスカッション) 方法論におけるエンティティとその結合性](https://github.com/feature-sliced/documentation/discussions/49)
- [(ディスカッション) クロスインポートと依存関係の分析について](https://github.com/feature-sliced/documentation/discussions/65#discussioncomment-480822)
- [パターン **GRASP**](https://ru.wikipedia.org/wiki/GRASP)

[refs-public-api]: /docs/reference/public-api
[refs-low-coupling]: /docs/reference/isolation/coupling-cohesion