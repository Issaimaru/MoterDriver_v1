# DriveUnit Bseries(ver 1.0)のドキュメント<br>
ver1.0の使い方，特性などについて解説していきます．<br>
最初のバージョンなのでガバが多少見つかりました．<br>
このドキュメントに書いてあること以外でこのバージョンにおけるガバが見つかれば僕に連絡するかIssuesを投げていただけると助かります．

## 基板外形図<br>
**表**<br>
![DriveUnit_B-series(ver1 0)](https://user-images.githubusercontent.com/80198387/183240083-dd1afdff-b96e-46e0-af43-96bf9f36109b.png)<br>
<br>
**裏**<br>
![DriveUnit_B-series(ver1 0)](https://user-images.githubusercontent.com/80198387/183240160-14de8d7c-38e6-4ea1-bfb7-7ee1aa5dac68.png)<br>

基板サイズ:*65.68[mm]×51[mm]*

## 使い方
野獣にはない機能があるので使用者は必ず確認してください．
長くなるため折りたたみます．
<details><summary>使い方</summary>

まずはLAP/SM切り替えスイッチについて説明していきます．
- LAP/SM切り替えスイッチ
![image](https://user-images.githubusercontent.com/80198387/178092940-8fb36315-81cc-43eb-a8e8-c8441fdcd832.png)
このスイッチは名前の通り，LAP方式とSM方式を切り替えることできるスイッチです．<br>
基板にあるシルクの通り，スライドスイッチを"SM"の文字の方にスライドするとSM方式，"LAP"の文字の方にスライドするとLAP方式となります．<br>
SM方式を採用する場合にはPWMピンにはPWM信号を流し，DIRピンには回転方向の信号を流してください(下の表参照)<br>
LAP方式を採用する場合はPWMピンには何も繋げずに**DIRピンに**PWM信号を流してください．<br>
LAP/SM方式の場合の各ピンの真理値表は以下のようになっています．<br>
    
    |PWM|DIR|出力(SM)|
    |:---:|:---:|:---:|
    |1~100%|0|正回転|
    |1~100%|1|負回転|
    |0%|$\phi$|ブレーキ|

    |DIR|出力(LAP)|
    |:---:|:---:|
    |0~49%|正回転|
    |50%|無回転|
    |51~100%|負回転|
    
    ※LAP方式についてはMD10C等一般的なモータードライバと正転・逆転が逆だと思うので注意してください．<br><br>
    LAP方式とSM方式の違いは[このページ](https://note.suzakugiken.jp/motordriver-sm-and-lap-tutorial-a/)を参照してください．<br> 
    安全性や効率の観点から基本的にはSM方式を採用してください．
    
- 手動・テストボタン
    ![image](https://user-images.githubusercontent.com/80198387/178093436-772c6a50-b641-4d57-8cd7-2d6d2c4e6749.png)
    
    このボタンを押すことで手動でモーターを動かすことができます．<br>
    モータが駆動しないとき，このモータードライバの問題かその他の問題かを判別したり，機構がちゃんと動くかのテスト等に活用してください．<br>
    SAボタンを押すと正回転，SBボタンを押すと負回転になります．<br>
     **このボタンを使用する際にはPWMピンをLOW，DIRピンをHIGHにして使ってください．それ以外の場合は正常に動作しません．**


    > **Warning** <br>
    >LAP方式の場合はSAボタンで正回転，負回転を一応切り替えられますが，モーター及びDUBに極めて大きい負荷がかかるため非推奨
    
- ファルトフラグ，RESETボタン
    
    ![image](https://user-images.githubusercontent.com/80198387/178094577-9a4c742c-3c82-49d1-ab6b-630c1d87ff60.png)<br>
    ![image](https://user-images.githubusercontent.com/80198387/178094596-dddb6ece-b245-49a3-9a2e-d4b836d327e5.png)


    このLEDが光っているということはなにかこのモータードライバに不具合があるということです．<br>
    なんの不具合があるかは下の表を参考にしてください．<br>
    ![image](https://user-images.githubusercontent.com/80198387/178094685-a0b9effb-837c-4c6a-9758-a55f66cc4fcf.png)<br>
    
    ちなみに，この表のFault LatchedにYesがついているエラーは，もしエラーの状態が治ったとしてもリセットボタンを押さないとエラーが出力されたまま動作しなくなるので，リセットボタンを押してください．<br>
    
    また，この表だけじゃ情報不足でわからないと思います．<br>全部英語ですがA3921のデータシートのp12~p13に乗っています．A3921のデータシートは[公式サイト](https://www.allegromicro.com/ja-jp/products/motor-drivers/brush-dc-motor-drivers/a3921/)からダウンロードしてください．<br>
    ちなみに，このモータードライバを設計するときにA3921のデータシートを読むモチベのためにQiitaで記事を書いたので，A3921を使いたい!みたいなことがあれば[ここ](https://qiita.com/issaimaru/items/3c1aff6e6718ecfb7793)から飛んでください．<br>
    リセットボタンを押すとA3921がスリープモードに入り，動作しなくなります．(離すと再び動作します．)<br>
    スリープモードに入ることで，A3921が検知していた異常が全てリセットされます．<br>
    
- 回転方向表示LED
    ![image](https://user-images.githubusercontent.com/80198387/178212711-23b652d6-f1a5-4b40-8ea8-704727f38dc4.png)<br>
    このLEDの表示を見ることで，モーターがどちらの方向に回転しているかを知ることができます．<br>
    PowerUnit BseriesのOUT+の電位がOUT-よりも高くなっているときの回転方向を正とすると，SAが光っているときは正回転を，SBが光っているときは負回転をしています．<br>
    ちなみに，SM方式の場合は片方のみLEDが光りますが，LAP方式の場合は両方のLEDが光るはずです．<br>
    LAP方式の場合はLEDの光の強さで回転方向を読み取ってください．(SAの光の方が強ければ正回転，SBの光の方が強ければ負回転です．)<br>
    
- 電源表示LED

    ![image](https://user-images.githubusercontent.com/80198387/178217821-9565e8af-c1f3-4cd2-8ac3-2305feaeb357.png)<br>
    DUBがちゃんと電源に接続されているなら，このLEDが光っているはずです．<br>
    PowerUnit Bseriesか遠野に挿している場合で，電源に接続しているのにこのLEDが光らない場合は非常停止スイッチが接続されていない可能性があります．<br>
    PowerUnit Bseriesか遠野の「SWITCH」に非常停止スイッチがしっかりと接続されているかを確認してください．<br>
    
 - GitHub QRコード
    ![image](https://user-images.githubusercontent.com/80198387/178219109-8c712de0-c709-4ab8-afa5-2b3c41f050dc.png)<br>
    ~~このページにアクセスしているということはこのQRコードから飛んできたということは置いといて~~一応説明しておきます．<br>
    このQRコードを読み込むことでこのページに飛ぶことができます．<br>
    使用前には極力このページに飛んで仕様を再確認しておくと良いでしょう．<br>
    ちなみに，レポジトリ名が「DriveUnit Bseries」ではなく「MoterDriver_v1」なのはリポジトリ名を変更するとURLが変更されるためこのQRコードからこのページに飛ぶことができなくなるからです......(ver1.0以降どうするんだこれ...)
    
    基本的には以上がver1.0のDUBの機能です．<br>
    機能など，このモータドライバについて質問があれば何らかの手段で僕に連絡していただければ答えます．<br>
    
</details>

## 特性
このモータードライバの特性についてはここに示します．<br>
~~どちらかというとモーターの特性になってるかもしれないです．~~<br>
これも長くなるため折りたたみます．  
<details><summary>ver1.0の特性</summary>

- 逆起電力の特性<br>
    >使用モータ:RS-555VC-5524<br>
    >使用マイコン:NUCLEO-F401RE<br>
    >印加電圧:12V<br>
    >PWM方式:Sign Magnitude(SM)<br>
    >PWM周波数:10kHz

    上記のものを使用してこの特性の調査を行いました．<br>
    また，調査の流れとしてはマイコンとこのモータードライバを使って一定のペース(これをx[ $ms$ ]とします)で加速→定格(12V)で運転→急減速の流れでモータを運転し，急減速の際の電源ラインの波形を読み取る，といった感じです．<br>

    - x=100の時の波形<br>
        ![x=100](https://user-images.githubusercontent.com/80198387/182985812-a9463f45-5fb8-4090-b836-3d4c6afe0bb1.jpg)<br>
        画像の通り，リップルは発生してないです．

    - x=50の時の波形<br>
        ![x=50](https://user-images.githubusercontent.com/80198387/182987201-535c8906-3971-4acb-8c8a-cacd2ab8618e.jpg)<br>
        最大で電源ラインが15Vになっています．

    - x=25の時の波形<br>
        ![x=25](https://user-images.githubusercontent.com/80198387/182987900-9519b26b-1cbb-4268-a90c-7edc9b5ecfab.jpg)<br>
        最大で電源ラインが23V程度になっています．

    - x=13の時の波形<br>
        ![x=13](https://user-images.githubusercontent.com/80198387/182988352-b8c37b4b-30cf-445c-9cbc-c0031bb25bcb.jpg)<br>
        最大で電源ラインが28V程度になっています．

    - x=7の時の波形<br>
        ![x=7](https://user-images.githubusercontent.com/80198387/182989731-cd5bbb5a-40b2-4dec-85bc-e1c53e1c72ee.jpg)<br>
        x=13の時と同様に最大で電源ラインが28V程度になっています．
    - x=3の時の波形<br>
        ![x=3](https://user-images.githubusercontent.com/80198387/182991113-b4ae91a1-4a11-44b4-a84c-c407ca96b7e1.jpg)<br>
        x=7の時に比べるとかなり発生する逆起電力が小さくなっていることがわかります．
    - x=0の時の波形<br>
        ![x=0](https://user-images.githubusercontent.com/80198387/182991616-8460b92d-82c3-4d67-badc-56032ba1fc27.jpg)<br>
        逆起電力が見られなくなりました．(多分回生ブレーキに使われるから？)

    ちなみに野獣でも同じ条件でこの実験をすると，最大の電源ラインの電圧はほぼ変わりませんでしたが，野獣は耐圧がこのモータードライバよりも低いため逆起電力で壊れる可能性があります．(実際に実験の最中に壊れました...)<br>
    なので，急停止するような動作が必要な場合には12[ $V$ ]でもこのモータードライバを使用することを推奨します．<br>

- 周波数-回転数特性
    >使用モータ:RS-555VC-5524<br>
    >使用マイコン:NUCLEO-F401RE，NUCLED-F411RE<br>
    >印加電圧:12V<br>
    >PWM方式:Sign Magnitude(SM)<br>

    上記のものを使用してこの特性の調査を行いました．<br>
    また，調査の流れとしては特定のPWM周波数(これをfとします．)で，0[ $V$ ]から10[ $s$ ]で定格(12V)になるようにduty比を一定間隔で増やしているPWM信号をこのモータードライバに送り，エンコーダから算出した回転数をマイコンからPCにprintfで出力しTera Termから値を読み取ってそれを折れ線グラフにします．<br>
    このとき，モータを駆動するマイコンと，エンコーダのパルスを読み取り回転数をprintf出力するマイコンを分けているため，printfの処理時間等は以下のグラフで見られる非線形性の要因とは無関係と言えます．

    - f=500Hz<br>
        ![500Hz](https://user-images.githubusercontent.com/80198387/183548902-dcd644af-2091-460c-bda2-88eb1fdf8b4b.png)<br>
    
    - f=1kHz
        ![1kHz](https://user-images.githubusercontent.com/80198387/183549244-27bae84c-ca21-47d3-802e-fc3511d249d6.png)

    - f=2kHz
        ![2kHz](https://user-images.githubusercontent.com/80198387/183549640-7ca782b7-5c9b-4afb-b82a-f3e97b1f9582.png)

    - f=5kHz
        ![5kHz](https://user-images.githubusercontent.com/80198387/183549957-5d107813-fa61-4a49-8015-8f440482c6ba.png)

    - f=10kHz
        ![10kHz](https://user-images.githubusercontent.com/80198387/183550415-350df81d-a199-4b15-b83a-3b2b1ac138af.png)

    - f=20kHz(HAL不使用)
        ![20kHz(HAL不使用)](https://user-images.githubusercontent.com/80198387/183550818-170466de-68f5-488f-b4cc-a5713ef85e67.png)<br>
        グラフを見てわかるように，20kHzになるとギザギザなグラフになっており，線形性が保たれていません．<br>
        これはPWM分解能が落ちている証拠です．なので，これ以降はHALを用いて計測していきます．<br>
        
    - f=20kHz(HAL使用)
        ![20kHz(HAL使用)](https://user-images.githubusercontent.com/80198387/183558264-45b01818-d7ad-4d94-9b67-9c9d54a731d9.png)
        
    - f=40kHz
        ![40kHz](https://user-images.githubusercontent.com/80198387/183558687-1b8317b6-676e-45d5-9960-7dd323bece66.png)

    - f=80kHz
        ![80kHz](https://user-images.githubusercontent.com/80198387/183559139-8c8bd680-a341-45b5-a2ab-4cd486f9bc68.png)<br>
        周波数が増えるにつれて，duty比が大きいところの線形性が保たれなくなってきています．
        
    - f=160kHz
        ![160kHz](https://user-images.githubusercontent.com/80198387/183559971-0cb3420f-6602-4833-89c4-568797694924.png)

    - f=250kHz(最大)
        ![250kHz](https://user-images.githubusercontent.com/80198387/183560373-defa38ae-9f31-4764-962a-651fdd1da3d1.png)
        
        グラフから，明らかに周波数が増えるにつれてduty比が0.9程度のところで回転数が一度落ちています．<br>これの原因は不明ですが，モーターの出力をオシロスコープで確認すると回転数が落ちるところで周波数が3kHz程度まで落ちることから，ブートストラップからチャージポンプに昇圧方式を変える時にコンデンサの容量が異なることでなにか問題が起きていると考えられます．<br>
        また，モータの両端にプロープをつけて波形を見てみたところ，ギブス現象の正弦波4本程度で矩形波を近似したときのような波形が得られ，電流が振動していました．<br>
        とりあえず，10kHzが一番線形性が保たれており，効率もいいので10kHzで使用するのが一番いいということがグラフから読み取れます．(あくまでこれは，RS555の場合です．)<br>
        <追記>RS-775を同じように250kHzで回してもギブス現象のような波形が得られず与えたPWM波形と同じ波形を出力していたことから，これはモータ側の問題であることが分かりました．~~しかし，duty比が0.9程度のところで回転数が落ちるのはこのモータードライバの問題のようです．~~<br>
        <追記>実験をしたところ，モータを接続したときのみ(高周波では)duty比0.9以上で電圧が12Vから10Vに落ち，またRS-775に変えるとこのような現象は起こらなかったことから，これもモータが原因だと思われます．<br>

</details>
          
## 現在分かっている設計のガバ

**使用者は必ずこれを見てください!!**

基板を発注する前にブレッドボードで回路が正しいかを確認するのが普通なのですが，A3921が極めて小さくて手はんだできないのもあり，実はブレッドボードで試作せずに基板を発注しています．<br>
しかも，A3921周りの回路は参考にできる記事や回路が見つからず，殆ど自分で組んでいます．<br>
そのため，実際に動かしてみてわかった設計のガバが少しあったのでそれを書いていきます．<br>
また，このガバは極力バージョンアップで無くしていきます．<br>

- 入力が浮くと全力で正回転する<br>
    マイコン側にノイズがいかないようにするためにフォトカプラで信号の入力線を絶縁しているのですが，フォトカプラはTLP2366(またはTLP2367)を使用していて，このフォトカプラは内部でNOTがかかり，内部のLEDが光っている(信号が入力されている)時にLOW，内部のLEDが光っていない(信号が入力されていない)時にHIGHになります．<br>
    従って，普通に繋ぐとPWM信号がHIGHの時にLOW，PWM信号がLOWの時にHIGHとなってしまい，仮にDuty比を20%にすると80%の出力になるため，制御性が悪くなってしまいます．<br>
    そのために，下の画像のようにマイコンのVCCとPWMピンに繋げることで，フォトカプラのNOTを打ち消すNOTを掛け，PWM信号が0の時に入力が0，PWM信号が1の時に入力が1となるようにしています．(DIRを反転していないのはA3921が「1で正転，0で逆転」になり，これは前モータードライバと逆であるので，前モータードライバと同じ入力で正転となるようにするためです．)

    ![image](https://user-images.githubusercontent.com/80198387/183225606-4af63141-bac9-493e-aa1e-5de479d5c88d.png)<br>
    
    ※修正前の回路なのでフォトカプラのLEDに抵抗が付いていませんが，実際には付いています．<br>
    <br>
    ここで，マイコンからの信号線を取り外し，入力が浮いている状態を考えてみましょう．<br>
    入力が浮いているので内部のLEDは当然光りません．<br>
    すると，下の画像のようにPWMへの入力が1になってしまいます．<br>
    すなわち，この状態で電圧をかけるとモータが全力で回転し始めます．

    ![image](https://user-images.githubusercontent.com/80198387/183225114-db796cfd-8571-4da2-8c3d-a085ae3e41e2.png)<br>

    これが，１つ目のガバです．<br>
    ロボットが暴走する原因になるので，以下のことを絶対に守ってください．<br>

    - マイコンをリセットする際は非常停止スイッチを押してからリセットを行う．
    - 入力を浮かせた状態でこのモータードライバを使用しない．
    
    ただ，以下のように回路を組めばこのガバをなくすことができます．<br>
    
    <details><summary>リセット時に暴走するガバを修正する回路</summary>
    
    ![image](https://user-images.githubusercontent.com/80198387/185751849-dc6df99f-e931-42ac-a87c-b51a25e62439.png)

    </details>

    ちなみに，このガバは出力がNOTではないフォトカプラを選べばいいのですが，高速なフォトカプラは自分が探した限り全て出力がNOTで，これを低速なものに変えると前モータードライバと同じくらいの周波数までしか対応できなくなることが予想されます．

- ボタンスイッチを使用できる入力の状態が制限されている<br>
    これも上記のガバに関連しているのですが，ボタンスイッチを正常に使用できるのは**入力が浮いていない状態で，PWMピンが0，DIRピンが1である状態です．**<br>
    このときの内部の状態は下の画像のようになっています．<br>
    ![image](https://user-images.githubusercontent.com/80198387/183227138-a9778602-e74a-452f-9774-5f29e2c7d10e.png)<br>

    入力が，浮いている状態を含むその他の状態ではPWMやDIRに既に1が入ってしまうので，SW1を押すと正転，SW2を押すと逆転，といった正常な動作をすることができなくなります．<br>
    けど，このスイッチの需要って大体入力が浮いている時，すなわちDIRとPWMが1の時にあると思うので....<br>
    回路の組み方を変えて全力で回転する問題と一緒に治す予定です．

## 使用電子部品，値段
チップ抵抗等大量に使用する部品の場合は1個あたりの値段を算出し，値段の横に個数を括弧で示します．<br>
又，同様の部品がある場合は部品番号をXと表記し，まとめて示します．<br>(例:3813-HA，3813-LAはどちらもIRLB3813PBFを使用しているため部品番号を3813-XXと示します．)<br>
|部品番号|部品名称|リンク|値段(一個)|
|:---------:|:---:|:---:|:---:|
|IC1|A3921KLPTR-T|https://www.digikey.jp/en/products/detail/A3921KLPTR-T/620-1523-6-ND/4318336?curr=usd&utm_campaign=buynow&utm_medium=aggregator&utm_source=octopart|¥476.3(10個)|
|3813-XX|IRLB3813PBF|https://akizukidenshi.com/catalog/g/gI-06270/|￥140 × 4|
|SW1，SW2|TVAF06-A020B-R|https://akizukidenshi.com/catalog/g/gP-14888/|￥22 × 2(5個)|
|SW3|THAF01-NC-R|https://akizukidenshi.com/catalog/g/gP-14887/|￥24(5個)|
|SW4|SS-12D00G3|https://akizukidenshi.com/catalog/g/gP-15707/|￥20|
|TRX|2SC3325|https://akizukidenshi.com/catalog/g/gI-00628/|￥5 × 2(20個)|
|TLPX|TLP2366|https://akizukidenshi.com/catalog/g/gI-11342/|￥120 × 2|
|LED1，LED2|SML-E12P8WT86(緑)|https://akizukidenshi.com/catalog/g/gI-11878|￥10 × 2(10個)|
|LED3|SML-E12Y8WT86|https://akizukidenshi.com/catalog/g/gI-11880|￥10(10個)|
|LED4，LED5|SML-E12V8WT86|https://akizukidenshi.com/catalog/g/gI-11879|￥10 × 2(10個)|
|DX|GS1010FL|https://akizukidenshi.com/catalog/g/gI-06014/|￥10 × 3(10個)|
|C1，C2，C3，C4，C5，C7，C8|GCM188L81H104KA57D|https://www.digikey.jp/en/products/detail/murata-electronics/GCM188L81H104KA57D/2591908|￥11.5 × 7(100個)|
|C6|GCM21BR71H474KA55L|https://www.digikey.jp/ja/products/detail/murata-electronics-north-america/GCM21BR71H474KA55L/1641707|￥26.2(10個)|
|C9|GRT31CC81H225KE01L|https://www.digikey.jp/ja/products/detail/murata-electronics/GRT31CC81H225KE01L/5416844|￥39.4(10個)|
|C10，C11|35PZJ330M10X9|https://akizukidenshi.com/catalog/g/gP-16867/|￥90 × 2|
|C12|GRM32ER71H106KA12|https://akizukidenshi.com/catalog/g/gP-16113/|￥40(5個)|
|C13|GRM21BC72A105KE01|https://akizukidenshi.com/catalog/g/gP-13699/|￥15(10個)|
|R1，R9，R11，R15，R16，R18，R19|RG2012-N-103-B-T5|https://akizukidenshi.com/catalog/g/gR-11797/|￥20 × 7(5個)|
|R2|RG2012N-332-W-T1|https://www.digikey.jp/ja/products/detail/susumu/RG2012N-332-W-T1/600888|￥107.9(10個)|
|R3，R22|RG2012P-681-B-T5|https://www.digikey.jp/ja/products/detail/susumu/RG2012P-681-B-T5/1266877|￥36.7 × 2(10個)|
|R5，R6|RG2012-N-102-B-T5|https://akizukidenshi.com/catalog/g/gR-11796/|￥20 × 2(5個)|
|R4，R7，R8，R17|RG2012N-201-W-T1|https://www.digikey.jp/ja/products/detail/susumu/RG2012N-201-W-T1/600671|￥107.9 × 4(10個)|
|R10，R12，R20，R21|RG2012Q-100-D-T5|https://jp.rs-online.com/web/p/surface-mount-resistors/6379813|￥47 × 4(50個)|
|R13|RG2012P-302-B-T5|https://www.digikey.jp/ja/products/detail/susumu/RG2012P-302-B-T5/1240739|￥48.7(10個)|
|R14|RG2012N-222-W-T1|https://www.digikey.jp/en/products/detail/susumu/RG2012N-222-W-T1/600696|￥107.9(10個)|
|総額|||￥2972.9
          
<details><summary>回路図(Eagle)</summary>

  ![schematic edit](https://user-images.githubusercontent.com/80198387/178212332-64d60d25-c5ea-4240-ba3e-3b22f239060b.png) 
    
</details>

今見つかっている不具合はこんなところです．<br>
このモータードライバを使用して何か不具合などが出てきたら教えてくださると助かります．

