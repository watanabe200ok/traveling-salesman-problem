# [traveling-salesman-problem](https://traveling-salesman-probl-885eb.firebaseapp.com/)

![](https://img.shields.io/badge/Vue.js-000.svg?logo=Vue.js&style=plastic)
![](https://img.shields.io/badge/JavaScript-000000.svg?logo=JavaScript&style=plastic)
![](https://img.shields.io/badge/macOS-000.svg?logo=macOS&style=plastic)


![](./docs/image/sample.png)

## What
[巡回セールスマン問題](https://ja.wikipedia.org/wiki/%E5%B7%A1%E5%9B%9E%E3%82%BB%E3%83%BC%E3%83%AB%E3%82%B9%E3%83%9E%E3%83%B3%E5%95%8F%E9%A1%8C)を[自己組織化マップ](https://ja.wikipedia.org/wiki/%E8%87%AA%E5%B7%B1%E7%B5%84%E7%B9%94%E5%8C%96%E5%86%99%E5%83%8F)によって短時間でそれらしい経路を求めるプログラムです。

## Demo

1. [デモページ](https://traveling-salesman-probl-885eb.firebaseapp.com/)を開きます。
1. 任意の場所(背景色がグレーの領域内)をクリックし、都市(赤い四角)を配置します。

* 都市を2つ以上配置すると経路を導き始めます。
* 青い線が経路です。
  * 時間経過によってリアルタイムで変化します。
  * 都市を追加する度に経路はリセットされます。
  * 最初から試す場合は画面をリロードします(手抜きですみません)。
* 緑色の背景色は、プログレスバーです。
  * 都市の数が少ない場合は、進捗率が100%に達する前に経路が収束します。
  * 進捗率100%になると、それ以上経路の更新はされません。

## 解説
準備中

## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn serve
```

### Compiles and minifies for production
```
yarn build
```
