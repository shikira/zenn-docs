---
title: "Security-JAWS #30 Day2 CTF"
emoji: "💡"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["aws", "security", "ctf"]
published: true
---

# Security JAWS初参戦！
Security JAWS初参加で、ついでにCTFも初体験してきました。
問題やその解き方については作問者や他の方から詳細なwrite upsが出ているので、それ以外のJAWS&CTF初心者が感じ取ったものを共有したいと思います。

https://s-jaws.doorkeeper.jp/events/155025

# 雰囲気
YouTubeでCTF前のイベント説明やCTF後の解説や表彰の様子が見れます。
https://www.youtube.com/watch?v=y7RnYJq02iM

* 始まる前は雑談したり準備したり。CTF開始後は集中するからみんな会場にあった飲食物を確保してました。長丁場で電源の心配が出てくるので、コンセントが近くにあるところに座るのがおすすめです。
* CTF開始直後からみんなもくもく。終わりまでほとんど席をただず集中して難問を解いていました。
* 終了後は写真撮影して懇親会に！(なお、私は疲弊してしまって参加せず)

 # 飲食
食べ物や飲み物が充実していたのでこれは触れておきたいと思います。
* ジュースやお菓子が大量にあった。Security JAWS Day1ではお菓子はカントリーマアムだけで寂しいと突っ込まれて、CTF当日にめっちゃ買ってくれたらしい。焼き菓子が美味しかったです。
* 飲み物はお茶系。Day 1でうまく捌けなかったらしく、たくさん飲むよう推奨していた。
* さらにお昼を跨ぐ時間帯というのもあってか、ミニバーガーもたくさん用意してもらっていた。ヒレカツバーガー最高でした。

# 問題
Trivia/Warmup/Easy/Medium/Hardの5つの難易度を用意してもらっていたので、CTF初心者でも解く快感が味わえるようになっていました。

|難易度|ポイント|問題数|
|:--:|:--:|:--:|
|Trivia|10|13|
|Warmup|50|6|
|Easy|100|4|
|Medium|200|5|
|Hard|300|2|

ここからは答えられたものだけ
## Trivia
No1
```
Security-JAWS#01の開催年月日はいつでしょうか？
(yyyymmdd形式でご回答ください)
```
doorkeeperから辿って解答
https://s-jaws.doorkeeper.jp/events/past?page=5

No2
```
AWS WAFv2がリリースされたのは何年何月？
(yyyymm形式でご回答ください)
```
"waf v2 release date"でgoogleして解答
https://docs.aws.amazon.com/ja_jp/waf/latest/developerguide/aws-managed-rule-groups-changelog.html

No3
```
AWS Acount IDは何桁？
（x桁の数字の部分のみをご回答ください。）
```
覚えてなかったから、実環境にログインして確認

No4
```
このIAMユーザー（シークレットキー＋アクセスキー）が所属する
AWSアカウントIDは何？
```
cliでget-userしてarnからアカウントIDを特定

No5
```
IAM Policyで一番強い権限のAWSマネージドポリシー名は？
```
これは覚えてたのでそのまま解答

No6
```
AWS Security HubのAWS 基礎セキュリティのベストプラクティ
ス v1.0.0でチェックされるパスワードポリシーの最小桁数はいく
つ？ （x桁の数字の部分のみをご回答ください。）
```
"aws securityhub password policy"で検索

No7
```
AWS Config Rulesのrestricted-sshでNONCONPLIANTとなるCIDR
は？
```
これは想像ついたのでそのまま解答

No8
```
AWS WAFのfreeのManaged Rule GroupsにおいてもっともWCUの
高いルールのWCU値はいくつか？
```
"aws waf free managed rule group wcu"で検索
https://docs.aws.amazon.com/ja_jp/waf/latest/developerguide/aws-managed-rule-groups-baseline.html

No9
```
Amazon GuardDutyの追加で設定できる保護プランは、S3
Protection・EKS Protection・RDS Protection・Lambda
Protectionと後何？
(xxxxxxx Protectionのxxxxxxxの部分をご回答ください)
```
"amazon guardduty EKS RDS Lambda"で検索

No10
```
FIPS 準拠に関する国家安全保障局の CNSSP 15 と、2 層の CNSA
暗号化に関する保存データ機能パッケージ (DAR CP) バージョン
5.0 ガイダンスを満たすように設計されたS3のセキュリティ機能の
名称は？
```
"aws s3 FIPS 準拠に関する国家安全保障局の CNSSP 15"で検索

No11
```
Route53にてドメイン登録をする際に、ドメインのチェックを行う必
要がありますが、チェック可能なドメイン長は最大で何文字？
（x文字の数字の部分のみをご回答ください。）
```
覚えていたのでそのまま解答

No12
```
クラウドホスト型決済アプリケーションにおける暗号化オペレーショ
ンの簡素化を支援するAWSのマネージドサービスは？
```
"クラウドホスト型決済アプリケーションにおける暗号化オペレーションの簡素化を支援するAWSのマネージドサービスは？"で検索

No13
```
以下のCloudTrailログを読み取り、作成されたユーザー名を回答し
てください。
```
```
{
    "eventVersion": "1.08",
    "userIdentity": {
        "type": "AssumedRole",
        "principalId": "AROXXXXXXXXXXXXXXXXX:test-user",
        "arn": "arn:aws:sts::123456789012:assumed-role/test-user/test-user",
        "accountId": "123456789012",
        "accessKeyId": "ASIA44JPO63AVLDDIY5N",
        "sessionContext": {
            "sessionIssuer": {
                "type": "Role",
                "principalId": "AROXXXXXXXXXXXXXXXXX",
                "arn": "arn:aws:iam::123456789012:role/test-user",
                "accountId": "123456789012",
                "userName": "test-user"
            },
            "webIdFederationData": {},
            "attributes": {
                "creationDate": "2022-10-15T21:38:24Z",
                "mfaAuthenticated": "true"
            }
        }
    },
    "eventTime": "2022-10-15T22:05:16Z",
    "eventSource": "iam.amazonaws.com",
    "eventName": "CreateUser",
    "awsRegion": "us-east-1",
    "sourceIPAddress": "AWS Internal",
    "userAgent": "AWS Internal",
    "requestParameters": {
        "userName": "KRJXVVAWC13R",
        "tags": []
    },
    "responseElements": {
        "user": {
            "path": "/",
            "userName": "KRJXVVAWC13R",
            "userId": "AIDAXXXXXXXXXXXXXXXXX",
            "arn": "arn:aws:iam::123456789012:user/KRJXVVAWC13R",
            "createDate": "Oct 15, 2022 10:05:16 PM"
        }
    },
    "requestID": "5eda3a85-64ed-4c5f-8d0a-9497e05f99bd",
    "eventID": "5586ec9d-4e9d-431b-ad8e-e33a6c8ecb94",
    "readOnly": false,
    "eventType": "AwsApiCall",
    "managementEvent": true,
    "recipientAccountId": "123456789012",
    "eventCategory": "Management",
    "sessionCredentialFromConsole": "true"
}
```
リクエストっぽいところから名前を拾って解答

## warmup

### AWS CLI practice
```
このIAMユーザー（シークレットキー＋アクセスキー）が所属するAWSアカウントIDは何？
```
配布されたプロファイルを用いて`aws sts get-caller-identity`で確認

### Where is the password
```
AWSのとあるサービスからパスワードを取得してください！ヒント：AWSで安全にパスワードを管理したいときに利用するサービスといえば？」
```
SecretManager!ということでそれっぽいキーを発見

### Find data 1
```
FLAGはバケツに突っ込んであるので探してね！
```
マネコンからそれっぽいバケット(himituno-bucket1)いったらFLAGあったのでダウンロードして解答

### Find data 2
```
FLAGはバケツに突っ込んであるので探してね！
```
aws cliでhimituno-bucket2にアクセスできたと思ったら大量のディレクトリがあったので、とりあえずローカルに全てコピー。
とりあえずいくつか開いたら画像でハズレと書かれていたので、正解はサイズが違うのだろうと想像。
その後aws s3 lsでrecursiveすればファイルサイズ全て見れることがわかって、ひとつだけサイズが違うファイルを特定

これまでcliあまり叩いてこなかったので、今後は使いこなしたいなーと思いました。

### Run function
```
アクセスキーを調べてFLAGを入手せよ！
```
`aws sts get-caller-identity`でユーザ名を特定して、そのユーザに紐づくポリシーから実行可能なlambdaを特定。
そのlambdaをinvokeしたら結果がbase64でエンコードされているようだったのでデコードしたら答えが表示された。

この問題のおかげで、アクセスキー拾ったらそれで何をできるのか調べることがまずやるべきことなんだと学びました。

## easy

### Find data 3
```
FLAGはバケツに突っ込んであるので探してね！
```
himituno-bucket3バケットに行けばいいのだろうと予想したらreadmeが置いてあって、取り除いたって書いてあったのでバージョン管理しているのだろうと想像。
バージョン管理のコマンド検索してきて実行して前バージョンのファイルから解答。

バージョン管理のcliとかまず叩いたことなかったので勉強になりました。

# 問題の解説

https://speakerdeck.com/tigerszk/security-jaws-days-ctf-zuo-wen-zhe-jie-shuo

https://scgajge12.hatenablog.com/entry/security_jaws_days_2023

https://docs.google.com/presentation/d/1gYk2WeObvLYyPIc124S1UrP-0SUnFHKGRDy0yPKT5rA/edit?usp=sharing 

もうひとつ解答あったと思うんですが、web公開されていなかったっぽいので割愛。

# 感想
* 攻撃手法を学ぶことで、どういう点に注意して守ればいいか理解が深まった。
* 問題の特定や対策といった守る問題より、攻める問題解いている方が楽しい:)　CTFこれからもっとやってみたいと思った。
* 成績は中の下(62位)だったけど、次は解けるようになりたいという気持ちから学ぶモチベがぐんとUP
* CLIは普段あまり触れなかったけど、今後は積極的に使っていきたい