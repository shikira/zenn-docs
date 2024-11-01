---
title: "DynatraceでAWSを監視してみる"
emoji: "🐡"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["aws", "dynatrace"]
published: true
---

# はじめに
Dynatraceで何ができるのか実際に触って学びたいなと思い、トライアルを活用してAWSと連携させてみようと思います。

# Dynatraceとは

公式よると、

> 全ての必要な機能とセキュリティを搭載したオブザーバビリティ基盤
> クラウドの複雑さを解消し、より速く、より安全な運用を実現。AIを活用した分析と自動化プラットフォームで、オブザーバビリティとセキュリティを強化しましょう。

https://www.dynatrace.com/

オンプレからパブクラ、マルチクラウドを一元的に管理し、横断的に監視・対処するオブザーバビリティ基盤です。

# Dynatraceをセットアップする

既存のAWS環境をDynatraceで監視するまでのセットアップを実際にやってみました。
ざっくりとした手順は以下の通りです。

1. Dynatraceアカウントを作成する
1. Dynatraceでアカウント情報を取得する
1. AWSでアクセスに必要なIAMロールを作成する

## Dynatraceアカウントを作成する

Dynatraceアカウント作成は必要事項を入力すれば簡単に作成できます。
今回はトライアルを活用させてもらいました。

## Dynatraceでアカウント情報を取得する

DynatraceでAWSを監視する場合、Dynatraceが稼働するAWSアカウントから監視対象のAWSアカウントにアクセスすることになります。
そこで、まず作成したアカウントのAWSアカウントIDを取得し、次のステップでアクセス元のAWSアカウントとして入力します。

まず、`Settings` -> `Cloud and virtualization` -> `AWS` -> `Coonect new instance`で設定画面を開きます。
![](/images/3a1a71b9eebfcd/0001.png)

トークン生成ボタンを押下すると、DynatraceのAWSインスタンスIDが取得できるようになるので控えてください。
![](/images/3a1a71b9eebfcd/0002.png)

## AWSでアクセスに必要なIAMロールを作成する

Dynatraceが監視対象AWSにアクセスするためのIAMロールを作成します。
IAMロール作成手順は[公式](https://docs.dynatrace.com/docs/setup-and-configuration/amazon-web-services/integrate-with-aws/cloudwatch-metrics)を参考にしました。

> To create role-based access
> 
> Download a YAML file with CloudFormation template from [cloud-snippets/role_based_access_no_AG_template.yml](https://dt-url.net/8b23yuo).
> 
> Create the stack in your Amazon Console:
> 
> In your Amazon Console, go to CloudFormation.
> Go to Stacks and create a new stack with new resources.
> Select Template is ready, upload the template you've created above, and select Next.
> In Parameters, enter the External ID.
> Enter a name for your stack and select Next twice.
> Review your configuration and accept the policy terms.
> Select Create stack.

基本的なリソース監視であれば[CloudFormation template](https://dt-url.net/8b23yuo)でロール作るだけです。

前の手順で入手したDynatraceのAWSアカウントIDを入力してCloudFormationスタックをデプロイします。
![](/images/3a1a71b9eebfcd/0003.png)

## Dynatrace設定で監視対象AWSアカウントを設定する

前の手順で作成したIAMロールと監視対象AWSアカウントIDを入力して接続します。
![](/images/3a1a71b9eebfcd/0004.png)

数分するとAWSリソースをDynatraceで確認できるようになっているはずです。

# 監視する

ダッシュボードにはデフォルトでAWSのアカウントを確認できるビューが用意されていました。
![](/images/3a1a71b9eebfcd/0005.png)
![](/images/3a1a71b9eebfcd/0006.png)

特にネットワーク分析のビューは見ていて楽しい。
![](/images/3a1a71b9eebfcd/0007.png)

今回の検証ではダッシュボードは作成しませんでしたが、さらに作り込むとさらに構成や状態を把握しやすくなるようです。
![](/images/3a1a71b9eebfcd/0008.png)

# おわりに
DynatraceでAWS連携はとても簡単にセットアップできることがわかりました。
Dynatraceには認定資格があるようなので、体系的にDynatraceの機能を学び業務活用を検討していきたいと思います。

![](/images/3a1a71b9eebfcd/0009.png)

https://www.dynatrace.com/dynatrace-university/?utm_source=google&utm_medium=cpc&utm_term=dynatrace%20certification&utm_campaign=jp-en-brand-brand&utm_content=none&utm_campaign_id=16527149235&gclsrc=aw.ds&gad_source=1&gclid=CjwKCAjw-JG5BhBZEiwAt7JR6xN-ijlia3LNCosxlTAUOpT-USAaqiKs8gANdMWbE_6gg_Yx7nqKdBoC6-0QAvD_BwE