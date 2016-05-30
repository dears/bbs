## bbs

##### 環境構築
* Virtual Box
* vagrant 1.7.4
  * centos7(https://github.com/holms/vagrant-centos7-box/releases/download/7.1.1503.001/CentOS-7.1.1503-x86_64-netboot.box)
* git
* node v4.4.3
* mongoDB v3.2.6
* mocha //テスト用モジュール
* その他多数のmiddleware (package.jsonからinstall可能 ： npm install)

### 色々実用性と拡張を想定してやってみた
* githubによる管理
* MongoDBを使ったデータ保持
* クッキーセッションでのログイン
* パスワードへのセキュリティ強化(salt付加と暗号化) http://qiita.com/_daisuke/items/990513e89ca169e9c4ad
* 今後の諸々自動化等も考慮してのJSONスキーマ実装(現状バリデーションチェックに利用) https://www.npmjs.com/package/jsonschema
* testフォルダ配下にテストコードを追加(mochaについては後述)
* テストコードを元に、カバレッジを算出

### vagrant使い方
* vagrant up ... vagrant環境起動
* vagrant ssh ... vagrantの仮想OSにログイン
* vagrant@localhost /var/www/server内が共有フォルダ(Vagrantfileで定義)

### vagrantおまけ　参考文献
* 「Vagrantって流行ってるらしいけど何が便利なの？」　http://dev.classmethod.jp/server-side/virtual-box-vagrant/
* 「開発環境の構築・共有を簡単にするVagrant入門」　https://thinkit.co.jp/story/2015/03/19/5740
* 「Windows上でVirtualBox+Vagrant+CentOSによる仮想環境構築」 http://qiita.com/hiroyasu55/items/11a4c996b0c62450940f

### git / GitHubの設定
* 「Git For Windowsのインストール手順」　https://opcdiary.net/?page_id=27065
* 「GitHubの初期設定」　http://qiita.com/drapon/items/441e18452b25060d61f1
* gitパス通ってなかったらコマンド再起動
* 公開鍵の設定はPC毎にすること
* git clone [newworld-labのurl]  -- cloneでレポジトリを落としてくる
* git remote rename origin upstream   -- 使いやすいようにリネーム（備忘）
* git remote add origin [自分のoriginのurl]  -- originを追加

### アクセスについて
* vagrantのIPアドレス： 192.168.33.10 (Vagrantfile)
* ポート：　3000 (index.js)
* Vagrantfile内でprivate_networkにされてるので外部からのアクセス想定はなし

### vagrant sshで入ったら初回にやること
* nvmをinstall 「開発環境の構築・共有を簡単にするVagrant入門」参照
* nodeをnvmからinstall versionは前述の通り
* 作業フォルダ(server)配下で npm install してpackage.jonに記述あるものを一括インストール
* nodemon等は別途インストール(コマンドには-gを忘れずに)

### MongoDB
* インストール　https://docs.mongodb.org/manual/tutorial/install-mongodb-on-red-hat/
* mongoDBの起動　sudo service mongod start
* 自動起動設定　sudo chkconfig mongod on

### MongoDB使い方
* DBコマンド　mongo
* 中身確認　show dbs, show collections
* DB作成　use [db名]
* Collection作成　db.createCollection("[collection名]")
* Collectionリネーム　db.[Collection名].renameCollection("[新Collection名]")
* Collection削除　db.[Collection名].drop()
* 現在の位置と情報確認　db.stats()
* 現在のいるDB削除 db.dropDatabase()
* コマンドから抜ける exit

* データ挿入 db.[collection名].insert({name:"ogawa", score:30})
* （関数も使える）
* データ全削除 db.remove({})
* データ確認　db.[collection名].find()
* データ件数確認　db.[collection名].count()
* 正規表現、{$gte gt lte lt eq ne}, distinct等使える

### テストコードを使う
* serviceで使われる機能に対して単体テスト用のコードを作成
* テストはmochaを利用(AssertとしてはTDD/BDD両方に利用できるchaiを使った)
* コマンド： mocha --reporter spec [テストコードの存在するファイル]

### カバレッジを算出する
* カバレッジを取ることで、全体の品質を検証する
* 脆弱性が見つかれば、コードの見直しやテストの補足が出来る
* 必要なのはgulpfile.js　コマンド：gulp [検証するglupfile内のタスク名]
* coverageフォルダが生成され、lcov-report\index.htmlをブラウザで見ると脆弱性やカバレッジの低い要因が可視化される
* 「node.jsでテストのカバレッジを取る」 http://qiita.com/iwata-n@github/items/1e8f629eb5b429a49e6d
* 「grunt+istanbul+mochaでNode.jsのテスト＆カバレッジ計測を行う」　http://qiita.com/u-minor/items/35889e80fb5075d56903
* 「Gulp導入手順」　http://qiita.com/lab3-34/items/21fcdc95e29802382fda

### node.jsでのテストについて 参考文献
* 「node.jsのいろいろなモジュール22 – mochaでテストを書こう」　http://dev.classmethod.jp/server-side/node-mocha/
* 「node.js単体テスト入門」 http://qiita.com/oret/items/8c9a216865b088c15d68
* 「JavaScript テストフレームワーク『Mocha』の使い方メモ」　http://phiary.me/javascript-test-framework-mocha-usage/
* 「Mocha, Chai, Sinon を使ったNode.js のテスト自動化　実践編」　 http://qiita.com/TsutomuNakamura/items/0eb50bf7622a3906e101

### エラー対処
* vagrant upしようとしてタイムアウトで繋がらない
　→　boxファイル(centOS7)内部とのvagrantのバージョンが一致してない可能性。合わせることで対応
　コマンド：vagrant plugin install vagrant-vbguest
* vagrant上でnpm installが出来ない　⇒　--no-bin-links　つける
http://eiua-memo.tumblr.com/post/117361529158/npmvagrantvagrant%E3%81%AE%E5%85%B1%E6%9C%89%E3%83%95%E3%82%A9%E3%83%AB%E3%83%80%E4%B8%8A%E3%81%A7npm
* nvm初期設定 || vagrant立ち上げても入ってるはずのnvmが使えない
http://qiita.com/akippiko/items/3708016fc43da088021c
