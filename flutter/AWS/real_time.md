- [利用するサービス](#利用するサービス)
- [システム構造](#システム構造)
- [詳細設定](#詳細設定)


# 利用するサービス
AWS AppSync のGraphQLを利用します。AWS AppSync で構築された GraphQL API により、フロントエンドデベロッパーは単一の GraphQL エンドポイントから複数のデータベース、マイクロサービス、および API にクエリを実行できます。 

# システム構造

- [参考サイト](https://docs.aws.amazon.com/appsync/latest/devguide/real-time-websocket-client.html)

# 詳細設定

## 実施方式
1. Amazon Cognito user pools and OpenID Connect (OIDC)
2. IAM
3. API Key

## フロントエント
- flutter SDK package
    ```ymal
    # For Webrtc
    web_socket_channel: ^2.1.0
    # For Appsync authorization
    amazon_cognito_identity_dart_2: ^1.0.3
    ```

    web_socket_channelは名前通り、web socketに接続のためのpackageです。

    amazon_cognito_identity_dart_2はcognito認証header情報を取得するためのpackageです。

- 必要なAWS情報
    下記の情報は必須です。
    ```bash
    1. AWS AppSync graphql endpoint
    2. graphql https endpoint
    3. graphql https connect endpoint
    4. graphql wss endpoint
    ```
    下記の順番でAWSのAppSync API URLを取得する、HealthcareGraphQL-devはHealth CareのAPI名です。
    ```bash
    AWS AppSync＞AWS AppSync API名＞Settings
    ```

    上記で取得したものはgraphql https endpointです。

    形式は：
    ```bash
    https://xxxxxxxxxxxxxxxxx.appsync-api.ap-northeast-1.amazonaws.com/graphql
    ```
    - AWS AppSync graphql endpoint
        - xxxxxxxxxxxxxxxxx.appsync-api.ap-northeast-1.amazonaws.com
    - graphql https endpoint
        - https://xxxxxxxxxxxxxxxxx.appsync-api.ap-northeast-1.amazonaws.com/graphql
    - graphql https connect endpoint
        - https://xxxxxxxxxxxxxxxxx.appsync-api.ap-northeast-1.amazonaws.com/graphql/connect
    - graphql wss endpoint
        - wss://xxxxxxxxxxxxxxxxx.appsync-realtime-api.ap-northeast-1.amazonaws.com/graphql

## 1. Amazon Cognito user pools and OpenID Connect (OIDC)形式
1. AWS AppSync graphql endpointを使い、AWS AppSync APIの認証headerを設定する。
    ```dart
    {
        "Authorization":"xxxxxxxxxxxxxxxxxxxxxxxx",
        "host":"example1234567890000.appsync-api.us-east-1.amazonaws.com"
    }
    ```

    Authorizationはcognitoの認証トークンです。

2. 上記のheader情報とgraphql wss endpointを使い、request URLを作成する

    下記通りです。
    ```dart
    Map<String, String> apiHeader = {
        "Authorization":"xxxxxxxxxxxxxxxxxxxxxxxx",
        "host":"example1234567890000.appsync-api.us-east-1.amazonaws.com"
    }

        List<int> headerBytes = utf8.encode(jsonEncode(apiHeader));
        List<int> payloadBytes = utf8.encode(jsonEncode({}));
        String requestUrl = graphql wss endpoint +  <--- graphql wss endpoint 
            '?header=' +
            base64.encode(headerBytes) +
            '&payload=' +
            base64.encode(payloadBytes);
    ```

3. 上記で生成したrequest URLを使い、web socketを接続する

    下記の順番でweb socketを接続し、リアルタイムのチャット実現できる。
    ```bash
    Initialize connection (connection_init)
    Connection acknowledgment (connection_ack)
    Subscription registration (start)
    Subscription acknowledgment (start_ack)
    Processing subscription (data)
    Subscription unregistration (stop)
    ```
4. connection_ack

    connection初期化の後は接続の認定段階です。

    当ステータスではsubscriptionと上記１で生成したAWS AppSync APIの認証headerを使って、subscriptionの登録処理を行う

    requestデータは下記通りです。
    ```dart
    {
    "id": "ee849ef0-cf23-4cb8-9fcb-152ae4fd1e69",
    "payload": {
        "data": "{\"query\":\"subscription onCreateMessage {\\n onCreateMessage {\\n __typename\\n message\\n }\\n }\",\"variables\":{}}",
        "extensions": {
            "authorization": {
            "Authorization": "AuthorizationTokenExample"
            }
        }
    },
    "type": "start"
    }
    ```
5. data

    リアルタイムチャットの実現の重要ステータスです。

    当ステータスでは監視していたsubscriptionのメッセージを取得する。
    ```dart
    {
    "type": "data",
    "id": "ee849ef0-cf23-4cb8-9fcb-152ae4fd1e69",
    "payload": {
        "data": {
        "onCreateMessage": {
            "__typename": "Message",
            "message": "test"
        }
        }
    }
    }
    ```
    map["payload"]["data"]["onCreateChatMessage"]はチャットメッセージ情報です。

6. stop

    当ステータスではweb socketのクローズです。

7. subscriptionのテスト

    AWSのAppSyncからスキーマで定義されているsubscriptionを選択し、実行すると、該当テーブルの変更を検知できる

    検知したデータは右のLOGSのところに表示される

AWS AppSync>AWS AppSync API名>Queries

## 2. IAM
IAM形式とCognito user pools形式主な違いは認証形式です。

Cognito user poolsだと、認証Tokenとhostがあれば十分ですが、IAMだとAWSのアクセスキーとシークレットキーは必要です。

web socketを接続ためのrequest URLを生成のに、下記の手順は必要です。

1. AWS AppSync APIの認証header
    ```dart
    {
    "accept": "application/json, text/javascript",
    "content-encoding": "amz-1.0",
    "content-type": "application/json; charset=UTF-8",
    "host": "example1234567890000.appsync-api.us-east-1.amazonaws.com",       ＜ー　AWS AppSync graphql endpoint             
    "x-amz-date": "20200401T001010Z",　                      ＜ー　x-amz-dateのフォーマットに合わせないと、だめぽいです。
    "Authorization": "認証トークン"
    }
    ```


    上記マップデータの認証トークン生成するため、下記の手順が必要です。
    ```dart
    String amzDate = "20200401T001010Z";

    // SigV4署名情報
    // subscriptionとAppSync API認証のendpointが異なる
    AwsSigV4Client sigV4Client = AwsSigV4Client(
    AWS_ACCESS_KEY_ID,
    AWS_SECRET_ACCESS_KEY,
    endpoint,   　　　　　　　　　　　　　　　　　　　　　　＜ーgraphql https connect endpoint
    serviceName: "appsync",
    region: "ap-northeast-1",
    defaultContentType: "application/json; charset=UTF-8",
    defaultAcceptType: "application/json, text/javascript",
    );

    Map<String, String> headers = {
    'content-encoding': 'amz-1.0',
    };

    SigV4Request sigV4Request = SigV4Request(
    sigV4Client,
    path: "",
    method: "POST",
    headers: headers,
    body: body,
    );

    String? authorization = sigV4Request.headers!["Authorization"];
    ```

    上記生成したRequest URLを使い、web socketに接続します。それ以降の手順はAmazon Cognito user pools and OpenID Connect (OIDC)形式と同じです。

## 3. API KEY
上記2つ形式と異なるポイントは認証形式です。

1. AWS AppSync APIの認証header
    ```dart
    {
        "host":"example1234567890000.appsync-api.us-east-1.amazonaws.com",
        "x-api-key":"da2-12345678901234567890123456"
    }
    ```
2. 上記のheader情報とgraphql wss endpointを使い、request URLを作成する

    下記通りです。
    ```
    Map<String, String> apiHeader ={
        "host":"example1234567890000.appsync-api.us-east-1.amazonaws.com",
        "x-api-key":"da2-12345678901234567890123456"
    };

    List<int> headerBytes = utf8.encode(jsonEncode(apiHeader));
        List<int> payloadBytes = utf8.encode(jsonEncode({}));
        String requestUrl = graphql wss endpoint +  <--- graphql wss endpoint 
            '?header=' +
            base64.encode(headerBytes) +
            '&payload=' +
            base64.encode(payloadBytes);
    ```

## Backend
AWS optional settings
```bash
AWS AppSync＞AWS AppSync API名＞Settings
```
AppSyncにアクセスするときに他の認証モードを使用する場合は、AppSync設定で複数の認証モードを設定する（認証プロバイダーを追加する）必要があります。

次に、追加の認証モードを使用するためにスキーマのテーブルで@authディレクティブを指定し、最新バージョンのAmplifyでpushコマンドを使用してAPIをデプロイする必要があります。

For details on how to use the @auth directive, refer to the following document.
https://docs.amplify.aws/cli/graphql/authorization-rules/

e.g. schema.graphql
```graphql

type Todo

@model

@auth(rules: [{ allow: private, provider: iam },{ allow: private, provider: userPools }])

{ id: ID!

name: String!

description: String

}
```


