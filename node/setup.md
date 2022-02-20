
# Node.js
## Nodebrewをインストールする
- インストール
    ~~~
    brew install nodebrew
    ~~~
- 確認
    ~~~
    nodebrew -v
    ~~~
- 環境変数の追加

    vim ~/.bash_profile
    ~~~
    export PATH=$HOME/.nodebrew/current/bin:$PATH
    ~~~
    bash_profileを更新して設定を反映させる。
    ~~~
    source ~/.bash_profile
    ~~~
## バージョン指定してnodeをインストールする
- インストール可能なバージョンを確認
    ~~~
    nodebrew ls-remote
    ~~~
- nodeのインストール
    ~~~
    nodebrew install <version>
    ~~~
- インストールしたバージョンを確認
    ~~~
    nodebrew ls
    ~~~
- 使いたいバージョンを指定
    ~~~
    nodebrew use v10.15.0
    ~~~

## バージョン確認
- 現在のバージョンを確認する
    ~~~
    node -v
    ~~~
- nodeメインフォルダ
    ~~~
    where node
    ~~~
## nodeのアンインストール

[node.js](https://nodejs.org/ja/download/)からダウンロードとnodebrewからダウンロードしたもの混在する場合、node.jsからダウンロードしたものをアンインストールする。

- npmをアンインストール
    ~~~
    sudo npm uninstall npm -g
    sudo rm -rf ~/.npm
    ~~~
- node.jsをアンインストール
    ~~~
    lsbom -f -l -s -pf /var/db/receipts/org.nodejs.pkg.bom | while read i; do sudo rm /usr/local/${i}; done$ sudo rm -rf /usr/local/lib/node /usr/local/lib/node_modules /var/db/receipts/org.nodejs.*
    ~~~

- 確認

    下記のコマンドで確認して、「command not found」が出て入ればOKです。  
    ~~~
    npm -v
    node -v
    ~~~ 

## シンボリックリンク
nodebrewの現バージョンをusr/localにシンボリックリンクをつける、そうすると、node -vで現バージョンを確認できる
~~~
ln -s /User/*****/.nodebrew/current/bin/node /usr/local/bin/node
~~~
     
