# インストール
## AWS CLI
~~~
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
sudo installer -pkg AWSCLIV2.pkg -target /
~~~

問題なく入っていれば、以下のコマンドで保存先の確認、バージョンの確認ができると
~~~
MacBook-Air ~ % which aws
/usr/local/bin/aws
MacBook-Air ~ % aws --version
aws-cli/2.4.19 Python/3.8.8 Darwin/19.6.0 exe/x86_64 prompt/off
~~~

- [参考](https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/install-cliv2-mac.html)

## Amplify CLI
Node.jsをインストールした上で下記のコマンドを実行する
~~~
npm install -g @aws-amplify/cli
~~~

# 環境設定
## AWSのプロファイル作成
~~~
$ aws configure
~~~
aws configure で設定されたデフォルトプロファイルの AWS CLI によって生成されたファイルは、次のようになります。

~/.aws/credentials
~~~
[default]
aws_access_key_id=AKIAIOSFODNN7EXAMPLE
aws_secret_access_key=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
~~~

~/.aws/config
~~~
[default]
region=us-west-2
output=json
~~~

以下のコマンドで確認できる
~~~
aws configure list
~~~