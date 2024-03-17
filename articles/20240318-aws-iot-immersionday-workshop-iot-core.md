---
title: "AWS IoT Immersion Day ワークショップでIoT Core学び直し"
emoji: "🐕"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["aws", "iot"]
published: true
---

# はじめに

過去に業務でIoT Core周りの一部に触れたことがありますが、改めて過去に触れてこなかったIoTサービスも含めIoT Coreを学び直すことにしました。

[AWS IoT Immersion Day ワークショップ](https://catalog.workshops.aws/aws-iot-immersionday-workshop/en-US)はそんな私にぴったりのワークショップで、IoT CoreだけじゃなくてGreengrassやTimestream、Analyticsなども触れて学べそうなので、このワークショップをやりながらIoTサービスへの理解を深めていきたいと思います。

# AWS IoT Core

[AWS IoT Core](https://catalog.workshops.aws/aws-iot-immersionday-workshop/en-US/aws-iot-core/device-sdk-v2)では、3つのことが学べました。
IoTデバイスとMQTT通信できるようになること、Device Shadowを使ってIoT機器の状態を変更できるようになること、MQTTのLWTを使ってデバイスの切断を検知してログ出力できることです。

## IoTデバイスとMQTT通信できるようになること(Lab 1.1)

Thing作成ウィザードでThingを作成し、秘密鍵とクライアント証明書を発行してThingに紐付け、クライアント証明書にセキュリティポリシーを設定してアクセス制御を設定し、デバイスからその秘密鍵とクライアント証明書を使って接続するというもの。

![](https://static.us-east-1.prod.workshops.aws/public/ad525722-3c40-4597-896b-12e87eb36ab1/static/lab1-architecture.png)

今回、私は手元に古い[Rasberry Pi](https://www.raspberrypi.com/products/raspberry-pi-3-model-b/)があったので、Raspberry PiをIoT Coreと繋げてMQTT接続しました。

![](/images/20240318-aws-iot-immersionday-workshop-iot-core/0001.jpg)

### セキュリティポリシーを最小権限に変更してみる
証明書に設定するポリシーは、MQTTメッセージブローカーに接続してメッセージを送受信するために必要最低限のアクションを許可するポリシーになっていました。
リソースはこの後さまざまなトピックを使いたいためなのか制限はないモノでした。

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "iot:Publish",
        "iot:Subscribe",
        "iot:Connect",
        "iot:Receive"
      ],
      "Resource": [
        "*"
      ]
    }
  ]
}
```
そこで最小権限のポリシーに変更してアクセス制御の振る舞いを確認してみました。
各アクションに指定するリソースは決まっているので、[こちら](https://jdocs.aws.amazon.com/iot/latest/developerguide/iot-action-resources.html)を参考に設定しました。

AWS Account ID: 123456789012、AWS IoT Thing name: ratchetの場合のセキュリティーポリシー例です。
許可するトピックは[接続テストのサンプルコード](https://catalog.workshops.aws/aws-iot-immersionday-workshop/en-US/aws-iot-core/device-sdk-v2/lab11-gettingstarted2#step-6-test-your-aws-iot-connection)から持ってきています。

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "iot:Connect"
      ],
      "Resource": [
        "arn:aws:iot:us-east-1:123456789012:client/ratchet"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
        "iot:Publish"
      ],
      "Resource": [
        "arn:aws:iot:us-east-1:123456789012:topic/device/ratchet/data"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
        "iot:Receive"
      ],
      "Resource": [
        "arn:aws:iot:us-east-1:123456789012:topic/app/data"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
        "iot:Subscribe"
      ],
      "Resource": [
        "arn:aws:iot:us-east-1:123456789012:topicfilter/app/data"
      ]
    }
  ]
}
```
注意点としては、`iot:Subscribe`アクションの場合はリソースタイプは`topicfilter`であること。

ポリシーを更新すると新バージョンが作られますが、`active`にしないと新バージョンは使われません。デバイスがすでに接続中であってもこのバージョン切り替えは即座に反映され、`active`になったセキュリティポリシーに従って動作することがわかりました。
一方、証明書をdeactiveにした場合、すでに接続済みのデバイスは即座にアクセスが拒否されるわけではないようでした。接続時に証明書を検証しているはずなので、接続後に証明書が失効しても即座に反映されないのだと思います。

## Device Shadowを使ってIoT機器の状態を変更できるようになること(lab5.1)

温度メトリックを送るデバイスをClassic Shadowを作ってデバイスの状態を変更し、振動メトリックも送るようにするといった内容。
デバイスはDevice Shadowの`Desired status`を監視して、期待するステータスと現在のステータスに差異が生じたら状態を変更して`Reported state`に通知する。その状態によって`data/vibration`にメトリックを送ったり送らなかったりする。

![](https://static.us-east-1.prod.workshops.aws/public/ad525722-3c40-4597-896b-12e87eb36ab1/static/lab5-architecture.png)

### desiredだけを更新してみる
手順では`AWS IoT コンソール->管理-->モノ-->ラチェット-->デバイスシャドウ-->クラシックシャドウ`でdesiredステートを`ON`にすることで`data/vibration`トピックにメトリックが送られることを確認するんですが、このコンソール上でクラシックシャドウを変更すると、`desired`だけでなく`reported`も変更ないけど更新されてしまうようでした。
基本的にreportedはデバイスが更新する属性なのでユーザが変更すべきではなイノで、`MQTT test client`を使ってクラシックシャドウに対して以下のように`desired`ステートだけを更新することができました。

topic: `$aws/things/ratchet/shadow/update`
```
{
  "state": {
    "desired": {
      "MOTOR": "ON"
    }
  }
}
```
今回気づいたんですが、マネコンのDevice Shadowから各トピックへの`MQTT test client`リンクが用意されててとても便利でした。こういうの地味に助かる。

![](/images/20240318-aws-iot-immersionday-workshop-iot-core/0002.png)

### 権限を確認してみる

各アクションにDevice Shadowのトピックをリソースとして追加することで、Device Shadowに対する操作が通った。
つまり、MQTT経由でのDevice Shadow操作は他のMQTTトピックと同等の扱いだといえる。
`iot:GetThingShadow`や`iot:UpdateThingShadow`などDevice Shadow操作のアクションはREST API経由でのみ有効なアクションだということがわかった。

また、python SDKでpublishした結果を取得する場合、`iot:Receive`アクションにもトピックをリソース追加しないと失敗した。結果取得のために必要だと思われる。
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "iot:Connect",
      "Resource": "arn:aws:iot:us-east-1:123456789012:client/ratchet"
    },
    {
      "Effect": "Allow",
      "Action": "iot:Publish",
      "Resource": [
        "arn:aws:iot:us-east-1:123456789012:topic/data/temperature",
        "arn:aws:iot:us-east-1:123456789012:topic/data/vibration",
        "arn:aws:iot:us-east-1:123456789012:topic/$aws/things/ratchet/shadow/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": "iot:Receive",
      "Resource": [
        "arn:aws:iot:us-east-1:123456789012:topic/data/temperature",
        "arn:aws:iot:us-east-1:123456789012:topic/data/vibration",
        "arn:aws:iot:us-east-1:123456789012:topic/$aws/things/ratchet/shadow/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": "iot:Subscribe",
      "Resource": [
        "arn:aws:iot:us-east-1:123456789012:topicfilter/app/data",
        "arn:aws:iot:us-east-1:123456789012:topicfilter/$aws/things/ratchet/shadow/*"
      ]
    }
  ]
}
```

## MQTTのLWTを使ってデバイスの切断を検知してログ出力できること(Lab6.1)

MQTTのLWTを使うと、切断時に特定のトピックにメッセージを通知することができます。
そのトピックへの通知をトリガーにしてログをCloudWatchに出力する操作を学ぶことができます。

![](https://static.us-east-1.prod.workshops.aws/public/ad525722-3c40-4597-896b-12e87eb36ab1/static/lab6-architecture.png)

コネクションを切ると即座にトピックにメッセージがpublishされた。コネクションを切らなくてもデバイスの通信を一時的に切断することでメッセージがpublishされることを確認できました。

[ライフサイクルイベント](https://docs.aws.amazon.com/iot/latest/developerguide/life-cycle-events.html)でも切断をトリガーすることができますが、接続時のメッセージによってアクションを分けたいようなケースではLWTが使えるなと思いました。

# おわりに

IoT CoreでデバイスとMQTT通信するための設定や操作を学ぶことができました。
証明書に紐付けるセキュリティポリシーを検証することで、IoT Coreのアクションやリソースの理解が深まりました。
ワークショップの残りは後日記事にしたいと思います。