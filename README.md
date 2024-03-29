# DriveUnit Bseries
**[自作電源基板](https://github.com/Issaimaru/PowerSupply_v1)に刺して使う80A級モータードライバ**<br>
**[DUB with 遠野](https://github.com/Issaimaru/DUB_with_Tono)を使って遠野に刺すこともできます**<br>
**動作電圧 8[V]~30[V]**<br>
**PWM周波数 ~250kHz**<br>
**duty比 100%対応**<br>
**電圧監視機能搭載**<br>
**LAP/SM方式での駆動が可能**<br>
**制作期間:2022/01/18頃～**<br>
**名前:DriveUnit Bseries**<br>

## バージョンに対応するドキュメント
- ver1.0-->[README_v1.md](https://github.com/Issaimaru/MoterDriver_v1/blob/master/README_v1.md)
- ver2.0-->[README_v2.md](https://github.com/Issaimaru/MoterDriver_v1/blob/master/README_v2.md)

## 前モータードライバ(野獣)との違い
野獣がノートパソコンで，DUBがゲーミングパソコンと例えるとわかりやすいと思います．(やや過言かもしれないです...)<br>
野獣ができることは基本的にDUBでもできますし，野獣でできないような機能もDUBにはありますが，値段とサイズ的にはDUBが劣ります．<br>
- **PWM比1.0での動作が可能**<br>
このモータードライバで使用しているA3921というゲートドライバは，ハイサイドにチャージポンプを内蔵しています．そのため，PWM比が1.0の近くであっても正常に動作することができます．<br>

- **動作周波数帯域が増えた**<br>
    > 100Hz以上10000Hz未満なら一応動く。一番特性がいいのは2kHz。<br>
 
  富山本郷メカテック部wikiの野獣の説明にはこのように書いてありますが，20kHzまでは人間の可聴域なので20kHz以下でモータを駆動させると大きな音がモータから発せられてしまいます．      <br>DriveUnit Bseries(以下，DUBといいます．)は ~~**計算上では**100kHzでも動作します．これは実験で確かめます．~~　<br>
  実験したところ，mbedではPWM周波数に比例してPWM分解能が小さくなるため，20kHzを超えると明らかにモーターの動作が不安定になります．
  <br>STM32CubeIDEからHALドライバーを使用し，タイマーの設定を変更することでこの問題を解消することができますが([ここ](https://qiita.com/ShunHattori/items/68f099f1d77702d2535d)を参照)，普通はオンラインコンパイラから制御しますよね...(実際にHALドライバーを使用してやってみましたが色々めんどくさいです)<br>
  上記の問題から，HALドライバーを使用せずにこのモータードライバを使用する場合は10kHz~20kHzで使用することを推奨します．(可聴域内ですが，音は聞こえないです．)<br>
  また，HALドライバーを使用する場合は250kHz以下の周波数で使用するようにしてください．
  
- **LAP/SM方式の切り替えが可能**<br>
このモータードライバで使用しているA3921というゲートドライバの配線をうまくしてやると，PWM方式(LAP/SM)をスイッチ一つで切り替えることが実現できます．SM方式はPWM比がそのまま回転数と比例し，効率がよく，低周波でも線形性が比較的保たれるのに対して，LAP方式は正転と逆転の比率を調整してモータの回転方向と回転速度を変化させる方式であり，Duty比50%でモーターが停止するという違いがあります．

- **定格電圧が大きくなった**<br>
モータードライバでは逆起電力が生じるため加える電圧の2倍程度のマージンを取る必要がある(と他高専のOBの方からアドバイスを頂きました．)<br>そのため， $V_{DS}$ が30 $V$ のFETを使用し，コンデンサやゲートドライバの耐圧も30 $V$ 以上になるように選定しました．<br>また，パスコンには(これも他高専のOBの方のアドバイスで)高分子コンデンサを使用しました．<br>そのため，野獣よりも逆起電力に強くなりましたが，6セルのリポバッテリー等を使って急減速をすると逆起電力で普通に30 $V$ を超えます(上の「特性」を参照)．そのため，6セルのリポバッテリー等を使用する場合は電源ラインをオシロスコープで確認して，逆起電力で30 $V$ を超えないか確認することを推奨します．

- **ボタンでモータを制御できるようになった**<br>
MD10Cのように，ボタンでモータの正転，逆転を制御できるようにしました．

- **定格電流が大きくなった**<br>
DUBでは(これも実験で確かめますが)最大で連続80 $A$ の電流を流すことができます．しかし，ハピロボ以降のロボコンは30 $A$ 以下という電流制限があるため，電源基板側にリセッタブルヒューズがついています．そのためあまり意味はありません．<br>

- **電圧監視機能が付いた**<br>
これもA3921の機能ですが，ハイサイドのFETの $V_{DS}$ 間の電圧を監視することによって万が一短絡した場合でもすぐに動作を止めてくれるほか，オーバーヒートしてるような状態なども検知してFFXのLEDを光らせることで警告してくれます．<br>
A3921のデータシートから取ってきたものですが，各FFピンの出力に対する説明は以下の表の通りとなっています．<br>
![image](https://user-images.githubusercontent.com/80198387/176993479-7274177c-bcfc-4301-a632-d3f4a388567c.png)

- **逆接しにくくなった**<br>
エッジコネクタは物理的に逆接が不可能な状態になっていませんが，野獣は勿論のことこのモータードライバも逆接すると燃えます．<br>
そのため，このモータードライバと電源基板では三角の印をつけることで接続する向きが把握しやすいようにしました．<br>

## はんだ付けの仕方
長くなるのでたたみます．
<details><summary>はんだ付けの仕方</summary>
まず初めに，このモータードライバのはんだ付けはとてもめんどくさいです．<br>
なぜなら，はんだごてではんだ付けするにはあまりにも小さすぎる部品を使っているため，リフローをする必要があるからです(恐らく部内でリフローする基板はこれが初めて)．<br>
といっても，部内にはリフローオーブンなんてないので....<br>

![ホットプレートリフロー](https://user-images.githubusercontent.com/80198387/184127235-d75daf34-5911-499f-a96e-e77884c9cf0f.jpg)<br>
ホットプレートを使用したホットプレートリフローではんだ付けしています．<br>
ていっても，基板は両面ありますが片面をホットプレートリフローしてもう片面も同じようにやろうとすると，はんだ付け済みの面が先に温まってしまうので両面をこの方法ですることはできません．そこでもう片面は.......<br>

![61UcROmBZZL _SL1500_](https://user-images.githubusercontent.com/80198387/184128632-ebcc7b92-9ec6-4613-9512-be97cc4c03e9.jpg)
ヒートガンを使用したヒートガンリフローではんだ付けしています．<br>
そのため，~~本当にめんどくさい~~ですが一気にはんだ付けできるため手はんだとどっちがめんどくさいかと聞かれるとどっちもどっちかなと思います．<br>
とりあえず，はんだづけの仕方を書いておかないと正直量産は自分以外に難しくなってしまうのでここに書いておきます．<br>

まず，ホットプレートリフローをする面は裏面(下の画像で示している面)です．<br>
![IMG_20220811_203212](https://user-images.githubusercontent.com/80198387/184376633-34b332ff-c9ee-4f1f-83d8-1a7d0f8a757d.jpg)<br>

理由は，この基板ではんだ付けが一番難しい部品がこの面のICで，こっちの面をヒートガンでリフローするとリフローの風ではんだが飛んで隣同士がくっついてしまう可能性があるからです．<br>
ホットプレートリフローについては[ここ](https://qiita.com/azusa9/items/a2fc4f26799de5588c58)を見ればやり方は完全理解することができると思いますが，一応自分流のやり方を書いておきます．<br>

1. いらない基板を3つ用意してDUBを囲うように配置する．
    ![IMG_20220812_234620](https://user-images.githubusercontent.com/80198387/184380304-d15f9ff4-3c36-4882-958e-3c8e3c6c33b6.jpg)<br>
    画像のように，DUBより大きめの基板をDUBの周りに配置します．<br>

1. テープ(できれば養生)で固定する．
    ![IMG_20220812_235616](https://user-images.githubusercontent.com/80198387/184382833-980ed254-4e0b-48f1-ad04-f2c35a006cac.jpg)<br>
    こんな感じでDUBが動かないように固定してください．あとから剥がすので養生の方がいいです．自分は家に養生テープがなかったのでセロハンテープでやってます．

1. ステンシル(メタルマスク)をパッドに合わせて上だけテープで固定する
    ![IMG_20220813_000818](https://user-images.githubusercontent.com/80198387/184384862-6adb1461-b103-4e34-842f-626deebf5806.jpg)<br>
    ステンシルの穴がパッドにしっかりと合うようにして，上だけをテープで固定します．<br>
    上だけを固定する理由は後で説明します．

1. クリーム半田をいらないカード等で伸ばしながら塗る<br>
    
    ![417DpgW1wLL _AC_](https://user-images.githubusercontent.com/80198387/184386022-6cf25166-6cca-4c6a-97b9-776ff0a448c0.jpg)<br>
    部室に画像のようなクリームはんだがあると思います．(なければ買ってください．自分は[ここ](https://www.amazon.co.jp/gp/product/B09QCQ12RV/ref=ppx_yo_dt_b_asin_title_o00_s00?ie=UTF8&psc=1)で買いました．)<br>
    これをまずはステンシルに落として，いらないカードなどで伸ばして基板のパッド全てに塗られるようにしてください．<br>
    自分は部室にこれを忘れてきたので塗りませんが，こんな感じです([超簡単、ホットプレートでリフロー（はんだ付け）！](https://qiita.com/azusa9/items/a2fc4f26799de5588c58#%E3%81%84%E3%81%96%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%A0%E5%8D%8A%E7%94%B0%E5%A1%97%E3%82%8A)より引用)．<br>

    ![超簡単、ホットプレートでリフロー（はんだ付け）！より引用](https://user-images.githubusercontent.com/80198387/184408123-ec52b2f2-e3f5-4dca-b34c-331a58915716.jpg)<br>

1. ステンシルの上に貼ったテープを支点にステンシルを持ち上げ，部品を乗せる．<br>
    ステンシルの固定の際に上だけをテープで固定した理由はこれを支点にステンシルを持ち上げるからです．<br>
    塗ったクリームはんだを壊さないようにゆっくりと持ち上げたら，いよいよ部品をピンセットで載せていきます．<br>
    ここで注意点をまとめておきます．<br>
    
    - 部品を乗せる際，きっちり押さえつける必要はない．
    - リフローの際に自然にパターンの位置に部品が動くため，多少部品がずれていても修正しない．
    - 特にIC部ははんだが隣同士でくっつくが，修正せずにICを載せる．
    
    以上の注意点を頭に入れながら，部品を載せていきます．<br>
    ちなみに載せるのは表面実装部品のみで，スルーホール部品はまだはんだ付けしません．<br>
    いちいち回路図を参照しながら部品を載せるのはめんどくさいと思うのでここに載せる部品をまとめておきます．(これはver1.0の部品配置です．ver1.0以降のバージョンの部品配置は各ドキュメントに示しておきます．)<br>
    ![部品配置図(裏面)](https://user-images.githubusercontent.com/80198387/184405448-f6242255-e64a-44a2-8356-f7228cfc8863.png)<br>
    この画像の通りに部品を配置したら，いよいよホットプレートリフローに入ります．<br>

1. ホットプレートリフロー!<br>
    ホットプレートの中心に部品を載せた基板を置き，ホットプレートを加熱していきましょう．部室にあるホットプレートなら中~強の間に合わせればいいと思います．<br>
    そのまま待っていると，煙がでてきてクリームはんだが溶け，金属光沢が現れると思います．全体的にクリームはんだがこの状態になったらホットプレートの加熱をやめ，動かさずに冷えるまで待ってください．<br>
    冷えてはんだが固まったら裏面については終わりです．お疲れ様でした．最後に導通試験をしてください(特にIC)．<br>
    <br>
    **ICの隣同士がくっついていた場合**<br>
    これはよくあります．こうなった場合は，ハンダ吸い取り線で吸い取ってやるとうまく離れると思います．試してみてください．

1. 表面もはんだペーストを塗って部品を配置する<br>
    手順1~5と同様にして表面についてもはんだペーストを塗って，部品を載せます．<br>
    載せる部品をここにまとめておきます．<br>
    ここで，タクトスイッチの裏に丸い突起があるためそのまま置くとパッド面と密着しないと思います．<br>
    そのため突起をニッパー等で取ってから置くようにしてください．<br>

    ![モータードライバ部品裏面](https://user-images.githubusercontent.com/80198387/184542583-4aa4ec84-91bb-4135-bba8-5023fbaeac6f.png)

1. ヒートガンリフロー!<br>
    ヒートガンの風を直にあてて基板を加熱していきます．<br>
    設定は弱風にして，チップLEDの真上にヒートガンがあるようにしてください(チップLEDがヒートガンの風で吹き飛ばされる可能性があるため)．<br>
    このとき，机がヒートガンの熱で壊れる可能性があるので，可能であれば作業マットの上に基板をおいて作業をしましょう．<br>
    この状態でしばらくすると，チップLEDの周りの部品だけはんだが溶けてくると思います．<br>
    はんだが完全に溶けたら，ヒートガンを下にもっていって，タクトスイッチ等についても同様にリフローを行ってください．<br>
    全てのはんだペーストが溶けたら加熱をやめ，動かさずに冷えるまで待ってください．<br>
    冷えてはんだが固まったら表面実装部品のはんだ付けは終わりです．お疲れ様でした．<br>
    <br>
    **タクトスイッチがパッド面と密着せず，リフローに失敗した場合**<br>
    この場合は手はんだではんだ付けしてください．<br>

1. スルーホール部品を手はんだではんだ付けする<br>
    ここまで来たら楽です．あとはいつも通りスルーホール部品をはんだ付けしてください．<br>
    注意点をまとめておきます．<br>
    
    - FETのドレインのピン(3ピンあるうちの真ん中のピン)はニッパーで切る
    - 電解コンデンサは，基板が白で塗りつぶされている方がマイナス(紫色の印がある方)になるように付ける

1. 完成!<br>
    お疲れ様でした．最後に動作試験をしてみてちゃんと動いたらはんだ付けが正しくできています．    

</details>

## 回路図のダウンロード(クローン)方法
1. Gitをインストールします．<br>Gitのインストール方法は[ここ](https://www.sejuku.net/blog/73444)を参照してください．
1. コマンドプロンプトを立ち上げます．
1. `cd ”ダウンロード先フォルダの絶対パス” `<br>
1. `git clone https://github.com/Issaimaru/MoterDriver_v1.git`<br>
