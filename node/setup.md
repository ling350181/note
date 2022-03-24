
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

## Trouble
- nodejsインストールできない
    ~~~
    nodebrew intall v16.3.0
    ~~~
    上記のコマンドでnode.jsをインストールする際、warning出ました。
    ~~~
    % nodebrew install 16.3.0
    Fetching: https://nodejs.org/dist/v16.3.0/node-v16.3.0-darwin-arm64.tar.gz
    Warning: Failed to create the file 
    Warning: /Users/****/.nodebrew/src/v16.3.0/node-v16.3.0-darwin-arm64.tar.gz: 
    Warning: No such file or directory
    curl: (23) Failure writing output to destination

    download failed: https://nodejs.org/dist/v16.3.0/node-v16.3.0-darwin-arm64.tar.gz
    ~~~

    ディレクトリが無いと怒られるので、mkdir をしてから再挑戦。
    ~~~
    mkdir ~/.nodebrew
    mkdir ~/.nodebrew/src
    nodebrew intall v16.3.0
    ~~~
- amplifyコマンドnot found
    
    For mac and linux
    ~~~
    curl -sL https://aws-amplify.github.io/amplify-cli/install | bash && $SHELL
    ~~~
    For windows
    ~~~
    curl -sL https://aws-amplify.github.io/amplify-cli/install-win -o install.cmd && install.cmd
    ~~~
    ## 参考サイト
    https://docs.amplify.aws/start/getting-started/installation/q/integration/js/#option-1-watch-the-video-guide
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

- ソース版の場合

    node実行ファイルの存在場所を確認し、ファイル削除。
    ~~~
    $ which node
    /usr/local/bin/node
    $ rm -rf /usr/local/bin/node \
    >     node_modules
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
     
