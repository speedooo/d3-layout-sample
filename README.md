# [D3.js Advent Calendar 2013](http://www.adventar.org/calendars/117)

## Day 11: 12/11/2013 スクリプトを書かずに各種グラフデータをD3.jsで扱える形式に書き出す




### はじめに
私はアメリカの大学の医学部で、お医者さんや生物学者向けのソフトウェア開発を行っているものです。ずっとJavaやC++などの世界に居ましたので、Webの世界はまだまだ勉強中です。その勉強の過程ででっち上げたツールなどを少し公開させていただきます。一部「生煮え」のものも含まれるので、徐々に改善していきます。



### D3.jsとグラフデータ
D3.jsには「元になるデータのフォーマットに関する制約は最小限にする」と言う思想があるようです。基本的にはCSVなどのテーブル形式でデータを用意すれば大抵の場合は間に合います。そんな中[グラフ](http://ja.wikipedia.org/wiki/%E3%82%B0%E3%83%A9%E3%83%95_(%E3%83%87%E3%83%BC%E3%82%BF%E6%A7%8B%E9%80%A0))形式のデータに関しては、並び順(ordinal)をIDとみなすという、冗長性は少なくなる一方、若干可読性に欠ける方式となっています。もちろんそれでもシンプルなので、ちょっとしたスクリプトを書いてしまえば済む作業ですが、目で見ながらグラフィカルにグラフ構造をエディットし、それを自動的に書き出せたらそれも便利です。

というわけで作りました。これは、[Cytoscape](http://cytoscape.org/)と言うグラフ解析・可視化アプリケーションの上で動くプラグイン(App)で、読み込まれたグラフのトポロジと属性データをそのままD3.jsで読み込める形式に書き出すものです。



### ソフトウェアのダウンロードとインストール
現在このAppは正式発表前のCytoscape 3.1.0以降にのみの対応しているため、ベータ版のダウンロードが必要となります。
(注: どちらも、最終版がリリースされた時にこのドキュメントもアップデートします。）

http://chianti.ucsd.edu/~kono/data/cy3latest/build-2013-12-04-21-32-34-PST/  (12/4/2013の最新ビルド）

そして、このAppをインストールする。

https://github.com/keiono/cytoscape-d3/releases/tag/1.0.0-BETA

App-->App ManagerでAppのインストール画面を開いて、***Install from File...*** ボタンをクリックすれば、あとはダウンロードした.jarファイルを指定するだけです。



### ネットワークの作成
Cytoscapeで読み込める形式なら何でもかまいませんが、最もシンプルなものはエッジのリストを書いただけのテーブル形式でしょう。

例えば、このリストは

```
1	2
2	3
3	1
1	4
4	3
```

以下のように読み込む事ができます:

![Small Network Sample](https://raw.github.com/keiono/d3-layout-sample/develop/doc/img/small.png)


ネットワークの読み込み方は、[こちら](http://opentutorials.cgl.ucsf.edu/index.php/Tutorial:Introduction_to_Cytoscape_3-part2#Loading_a_Simple_Network)を参考にして下さい。


#### 各種データの追加
D3.jsに限りませんが、複雑な可視化を目指す場合、各種統計量や、その他ノードやエッジに付随するデータを一緒に使うのが一般的です。Cytoscape上であれば、そういったものを比較的簡単に統合することが出来、それをそのままD3.jsように書き出されるJSONに含めることが可能です。以下は、読み込んだ比較的小規模なネットワークを、NetworkAnalyzerと言う機能で各種統計量を計算して、それをそのまま書き出した例です:


```javascript
  "nodes" : [ {
    "id" : "1480",
    "NeighborhoodConnectivity" : 2.0,
    "NumberOfDirectedEdges" : 1,
    "SUID" : 1480,
    "Stress" : 0,
    "SelfLoops" : 0,
    "IsSingleNode" : false,
    "PartnerOfMultiEdgedNodePairs" : 0,
    "selected" : false,
    "name" : "YDL194W",
    "Degree" : 1,
    "TopologicalCoefficient" : 0.0,
    "BetweennessCentrality" : 0.0,
    "Radiality" : 0.55122461,
    "Eccentricity" : 26,
    "NumberOfUndirectedEdges" : 0,
    "ClosenessCentrality" : 0.07623732,
    "shared_name" : "YDL194W",
    "AverageShortestPathLength" : 13.11693548,
    "ClusteringCoefficient" : 0.0
  }]
```

このように、各種統計量が含まれた状態でネットワークを書き出すことができれば、D3.jsの基本的な機能を使って、それらを視覚効果へとマッピングすることが容易です。

この記事の主眼は、あくまでCytoscape上でエディットしたデータを簡単にD3.js形式に書き出すという点なので、Cytoscapeに関する詳しい説明はしませんが、興味のある方は以下のリンクを参考にして下さい。

 * [Cytoscape Official Web Site](http://cytoscape.org/)


いくつかプレゼンテーションスライドもありますので、興味のある方はどうぞ:

 * [繋がりを見る](http://www.slideshare.net/keiono/cytoscape)
 * [Cytoscape Presentations at SpeakerDeck](https://speakerdeck.com/keiono)



### D3.jsへの書き出し
File-->Export-->Networkで、D3.js形式のフォーマットを選択するだけです。書き出しの結果は[こんな感じ](https://github.com/keiono/d3-layout-sample/blob/develop/app/data/net1.json)になります:


### 書き出されるデータのサンプルと実際の可視化
一度様々な属性値(attributes)を付随したグラフデータをJSON化すれば、それをマッピングするのは非常に容易で、例えば[Edge Betweenness](http://med.bioinf.mpi-inf.mpg.de/netanalyzer/help/2.7/index.html#attributes)をエッジの太さとしたいときは、*stroke-width* attributeの返り値にそのデータを指定するだけで良い（そのままでは太さの値としては大きすぎるので、底を10とする対数としました）:

```javascript
var link = svg.selectAll(".link")
    .data(graph.links)
    .enter().append("line")
    .attr("class", "link")
    .attr('stroke-width', function(d) {
        return Math.log(d.EdgeBetweenness)/ Math.LN10;
    });
```

更にシンプルな例として、ノードの次数をそのままノードの大きさにマッピングすることも、同じように返り値を半径にマップするだけで可能です:

```javascript
node.append("circle")
    .attr("class", "node")
    .attr("r", function(d) {
        return d.Degree;
    });
```

書き出した結果を単純に可視化したものは[こちら](http://chianti.ucsd.edu/~kono/js/sample1/)です。このレポジトリにサンプルコードを含めておきます。Yeomanで作成したテンプレートほぼそのままです。ビルドにはnode.jsとGruntが必要です。

![net1.json](https://raw.github.com/keiono/d3-layout-sample/develop/doc/img/sample1.png)


このように、予め外部でネットワークに関する詳細なデータを付加しておくことにより、より複雑な可視化のアイデアを実装していくことが可能になります。

Tree形式の書き出しと、更に複雑な可視化例は次回の記事に。

質問等は、kono アットマークucsdドットeduまでお願いします。



## Day 20: 12/20/2013 D3.jsと周辺ツールを使った(グラフ)データの可視化

今回は、実際に可視化アプリケーションを作成する時に関係するツールや話題を、D3.jsに限らず扱ってみようと思います。

### 前回の落ち穂拾い
前回の続きです。先に紹介した[Cytoscape]()用のAppは、グラフの特殊形態の一つとみなすことが出来るTree形式のデータの書き出しにも使えます。D3.jsには、様々なTreeデータを可視化するプリセットレイアウトが含まれていますので、これを利用することにより、ひとつの形式のデータを様々な方式でレンダリングすることが可能になります。同一のデータを様々な形で簡単に見ることが出来るのは、D3.jsの利点の一つです。


#### Tree形式での書き出し
基本的にグラフと同じですが、rootノードを自分で選択しておく必要があります。例えば以下の例だと、ROOTとラベルの付いたノードをクリックすることによりそれがルートノードとして指定され、それに基づいてTree形式のJSONとして出力されます。書きだされたツリー形式のデータはこんな感じになります。

![tree1.json](https://raw.github.com/keiono/d3-layout-sample/develop/doc/img/tree1.png)


```json
{
  "name" : "ROOT",
  "shared_name" : "ROOT",
  "SUID" : 63,
  "selected" : true,
  "name" : "ROOT",
  "children" : [ {
    "name" : "N3",
    "shared_name" : "N3",
    "SUID" : 67,
    "selected" : false,
    "name" : "N3"
  }, {
    "name" : "N4",
    "shared_name" : "N4",
    "SUID" : 69,
    "selected" : false,
    "name" : "N4",
    "children" : [ {
      "name" : "N7",
      "shared_name" : "N7",
      "SUID" : 75,
      "selected" : false,
      "name" : "N7"
    } ]
  }, {
    "name" : "N2",
    "shared_name" : "N2",
    "SUID" : 65,
    "selected" : false,
    "name" : "N2",
    "children" : [ {
      "name" : "N8",
      "shared_name" : "N8",
      "SUID" : 77,
      "selected" : false,
      "name" : "N8"
    } ]
  }, {
    "name" : "N1",
    "shared_name" : "N1",
    "SUID" : 62,
    "selected" : false,
    "name" : "N1",
    "children" : [ {
      "name" : "N5",
      "shared_name" : "N5",
      "SUID" : 71,
      "selected" : false,
      "name" : "N5",
      "children" : [ {
        "name" : "N9",
        "shared_name" : "N9",
        "SUID" : 79,
        "selected" : false,
        "name" : "N9"
      } ]
    }, {
      "name" : "N6",
      "shared_name" : "N6",
      "SUID" : 73,
      "selected" : false,
      "name" : "N6"
    } ]
  } ]
}
```


### データを様々な方式でレンダリングする
Tree(もしくは階層構造)は、現実のデータを扱う時に様々な場面で現れる形式です。プログラマの方なら、オブジェクト指向プログラミング自体が、抽象化のプロセスで世界を階層構造とみなすことにより行っていると感じておられるでしょう。ただし多くの場合は、きれいなツリーではなくDAG(Directed Acyclic Graph、つまり無閉路有向グラフ)になるでしょうが...　この辺りのデータに興味のあるかは、オントロジーやSemantic Web周辺のデータセットを覗いてみると面白いでしょう。

さて、書きだされたTreeデータを可視化する場合の最も典型的な例はこれでしょう：

![treeview1]()

しかしこの表示方法がいつも適切かというとそうではありません。同じデータを様々な方式で眺めたいとき、D3のプリセットは威力を発揮します。

このように、同一データをD3.jsの基本機能を使ってレンダリングするだけでも様々な表現が可能です。しかし、最終的なアウトプットを得るのに、ひとつのライブラリだけで完結させる必要はありません。


### 適切なツールを適切な場面で使う
D3.jsは非常に強力なツールですが、使い所を間違えると、結果は同じなのに余計な労力がかかっただけという場合もあります。D3.jsが最も力を発揮するのは、複雑なインタラクションが必要な場合や、既存のツールでは表現しきれない可視化のアイデアがあるときです。

本質的に全ての可視化アプリケーションやライブラリは、たったひとつのことをしているだけです。それは、**データを視覚要素にマッピングする**事です。ここでいう視覚要素とは、色、線分の長さ、形、位置などといったものを指します。全てのライブラリは、その作業をより抽象度の高いレベルで行う事ができる手助けをしているだけとも言えます。見方によっては、D3.jsは、生のSVG構文に対するシンタックスシュガーの集合とみなすことも出来ます。

私事ですが、私のバックグラウンドは計算機科学です。しかし全くコンピュータグラフィックスの高等教育を受けたことはありません。それでも最近の急激なオープンソースツールの進歩により、かなり様々な事をそれなりに形にできるようになってきました。



#### シナリオを描く

なぜ可視化を行うのか？といえば、当然そこに表示すべきデータがあるからだ、ということになります。ただし、これが時々「そこにツールがあるからだ」になってしまいがちです。D3.jsはなまじプリセットが充実しているために、取り敢えずそれを使っておしまい、ということにもなりかねません。

これだけで一冊の本になるくらい大きなトピックなので、興味のある方は、[このカレンダー内]()でも紹介されている定番の本などを読んでみてください。
英語ですが、この教授のスライドや著作は非常に参考になるため一読をおすすめします。残念ながら、可視化という分野の文献は、圧倒的に英語のものが多く、この分野に挑戦しようとすれば、基本的に英語は避けられません。

またまた手前味噌で申し訳ないですが、私がワークショップで使った資料にもその辺りのことを書きました。英語ですが、平易な表現のみですので興味のある方は眺めてみてください：



D3と組み合わせて力が発揮できるライブラリには以下の様な物があります。

#### 全体と詳細
よくある手法としては、何らかの方法で

これはプロパティシートなどと呼ばれるオブジェクトで、たいていのGUIツールキットに用意されています。Webアプリケーションだと、


#### チャートの描画
棒グラフ、折れ線グラフ、パイチャートといったものはほぼどんなツールでもサポートされている基本機能ですが、D3.jsの設計思想は、完全な

その場合はこういったツールの利用も検討すべきです。

* [highchart.js]()




#### グラフの描画
グラフ（ネットワーク）も様々なツールが有りますが、

* [cytoscape.js]() - 手前味噌で申し訳ないが、我々の作成しているグラフ描画に特化したライブラリ。階層構造(Compound Node)の描画などにも対応
* [sigma.js]() - レンダリングの自由度は低いがハイパフォーマンスで、大規模グラフの一括描画に向いています

#### スクラッチからの作成
* [processing.js]() - Java版で有名な[Processing]()の移植。比較的ローレベルな描画に特化してあるので、基本的には自分で一から作る時に使います。元々はメディアアート系の利用を念頭に作られていますので、データ可視化に使おうとすると、かなり自分で書く分量が多くなってしまいますが、自由度が必要な時に。




### おわりに
今回は詰め込みすぎで話題が発散してしまいました。
機会があればもう少し具体的なコードに重きをおいた記事も書こうと思います。



