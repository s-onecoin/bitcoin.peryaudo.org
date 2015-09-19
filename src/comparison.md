## Bitcoinウォレットの比較

この章では、Bitcoinのウォレットを仕組みの面から解説するため、代表的なもの同士を比較していく。一見どれも同じように見えるデスクトップ向けウォレットだが、その動作原理によって数種類に分類でき、導入にかかる時間や使われるディスク容量は大きく異なる。

はじめ、Bitcoinのウォレットは、オリジナルの実装一つしか存在しなかった。しかし現在では、Webベースの物も含めると無数のウォレットの実装が存在する。（問題となったMt.Goxも、交換所であるとともに、Webベースのウォレットの一種でもあった。）

現在、デスクトップ向けウォレットには、大きく「完全クライアント」「SPVクライアント」「サーバー・クライアント型クライアント」の三種類が存在する。


|名前		|対象利用者	|セットアップ	|ディスク容量	|対応OS			|種類		|
|---------------|---------------|---------------|---------------|-----------------------|---------------|
|Bitcoin-Qt	|一般		|数時間以上	|数GB		|Win, Mac, Linux	|完全		|
|bitcoind	|プログラマー	|数時間以上	|数GB		|Win, Linux		|完全		|
|Electrum	|一般		|数分		|数十MB		|Win, Mac, Linux	|Server-Client	|
|MultiBit	|一般		|数分		|数十MB		|Win, Mac, Linux	|SPV		|
|Bitcoin<br>Wallet|一般		|数分		|数十MB		|Android, BlackBerry	|SPV		|

<!--TOC-->

### 完全クライアント: Bitcoin-Qt, bitcoind
<div class="figure"><img src="res/fig_bitcoin-qt.png" alt="図: Bitcoin-Qt"><p class="caption">図: Bitcoin-Qt</p></div>

完全クライアントは、ブロックチェーンの全てのデータをダウンロードするという特徴がある。これは最も標準的ではあるが、問題もある。Bitcoin開始以来の全ての取引履歴（ブロックチェーン）は数十GBにも達するため、これらを全てダウンロードするのには、かなりの時間を要する。そのため、インストールから実際に使いはじめることができるまでには、何時間もかかってしまうのである。また、これに応じて、ディスクにも相応の空き容量が必要となる。

[Bitcoin-Qt](https://bitcoin.org/en/download)やbitcoindは、Bitcoin発案者のSatoshi Nakamotoによる、オリジナルのBitcoinクライアントのコードをベースにしたクライアントである。Bitcoin-Qtは個人の使うBitcoinウォレットの中で最も一般的なものである。

bitcoindは名前の通りunixのデーモンとして動作する事を目的とするBitcoinのクライアントで、JSON-RPCで開発者向けのAPIを提供する。したがって、Webサービスとして動作するBitcoinウォレットのバックエンドとしてや、マイニングプールのサーバーとして使われる。

<!--ADS-->

### SPVクライアント: MultiBit, Bitcoin Wallet
<div class="figure"><img src="res/fig_multibit.png" alt="図: MultiBit"><p class="caption">図: MultiBit</p></div>

SPV（Simplified Payment Verification）クライアントは、原則的に各ブロックのヘッダーしかダウンロードしない。これは、完全クライアントが、ブロックチェーンの全データをダウンロードするのと対照的である。これにより、SPVクライアントは数十MBのデータをダウンロードするだけで済み、セットアップ時間も数分単位まで短縮される。

なぜSPVクライアントが実現できるのだろうか。実はBitcoinでは、ごく僅かなデータだけで、ある取引がブロックに含まれるかどうかを検証することができる。これは次章、「[Bitcoinの細部](detail.html)」で説明する、マークルツリーと呼ばれる仕組みを用いている。

全てのクライアントをSPVクライアントにすればよいように聞こえるかもしれない。しかし、SPVクライアントが正しく動作するためには、あくまで信用できる完全クライアントのノードの存在が不可欠である。

[MultiBit](https://multibit.org/)や[Bitcoin Wallet](https://play.google.com/store/apps/details?id=de.schildbach.wallet&hl=ja)は、代表的なSPVクライアントだ。SPVクライアントの性質によって、インストールに時間をかけたくないライトユーザーや、回線の細いスマートフォンの環境でも、実用的にBitcoinを使えるようにしている。

「[Bitcoinウォレットを実装する](implement.html)」の章で実際に実装してみるウォレットも、SPVクライアントである。


### サーバー・クライアント型: Electrum 
<div class="figure"><img src="res/fig_electrum.png" alt="図: Electrum"><p class="caption">図: Electrum</p></div>

[Electrum](https://electrum.org/)もたしかにSPVの仕組みを用いているが、もはやP2Pのクライアントではない。Electrumは起動した時、ボランティアによって運営されるいくつかの決まったElectrumサーバーに接続して、そこを通して全ての情報を受取る。サーバーはbitcoindを使って実装されている。このことで、Electrumは他のクライアントより、はるかに軽く動作することができる。

これではElectrumは一見、他のWebウォレットと同じに思えてしまうかもしれない。しかし、秘密鍵も含めてサーバーが預かっているWebウォレットとは異なり、Electrumでは、秘密鍵を持っているのはあくまで自分だけである。このため、Webウォレットと比較した場合には、はるかに安全であり、他のデスクトップウォレットと同様に信頼して使うことができる。

参考までに、筆者はElectrumをメインで使用していることを述べておく。

## ウォレットの関連知識

Bitcoinウォレットの動作原理とは関係がないが、実際にウォレットを使う上で知っていると役に立つ概念をいくつか紹介する。

### bootstrap.dat
通常のステップを踏んだ場合、Bitcoin-Qtのセットアップには数時間以上かかる。しかし、Bitcoinのプロトコルは高速転送のためには設計されていないので、実際にはブロックチェーンをより速くダウンロードすることが可能である。

そこで、セットアップのために、ブロックチェーンのデータをBitTorrentなどの、より高速な手段でダウンロードし、それをインポートすることで、セットアップ時間を短縮することができる。.torrentファイルは以下のリンクからダウンロードできる。

* [Bitcoin - Browse /Bitcoin/blockchain at SourceForge.net](http://sourceforge.net/projects/bitcoin/files/Bitcoin/blockchain/)

### デターミニスティック・ウォレット

デターミニスティック・ウォレット（Deterministic Wallet; 決定論的ウォレット）は、シード（seed; 種）と呼ばれる単一のパスフレーズから、複数のBitcoinアドレスを生成する仕組みである。

Bitcoin-Qtなどでは、ウォレットにパスワードを設定することができる。しかし、これらのクライアントでは、あくまで秘密鍵はファイルに保存されている。したがって、鍵ファイルを紛失してしまえば、仮にパスワードを覚えていても、全てのウォレットの残高が消えてしまうという問題点があった。

デターミニスティック・ウォレットであれば、全ての秘密鍵・アドレスは1つのパスフレーズから生成される。そのため、このパスフレーズさえ失念・紛失しなければ、絶対に残高を失うことがない。ただし、この場合のパスフレーズは、通常のウォレットのパスワード以上に重要となるため、保管には十分に注意を払う必要がある。

デターミニスティック・ウォレットを実際に採用しているクライアントとしては、上で挙げたElectrumなどがある。

「[Bitcoinの細部](detail.html)」に進む
