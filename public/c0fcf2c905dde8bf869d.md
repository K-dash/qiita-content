---
title: 'CourseraのGCPコースを受けてみた（Google Cloud Platform Fundamentals: Core Infrastructure）'
tags:
  - 初心者
  - coursera
  - GoogleCloud
private: false
updated_at: '2020-05-10T16:40:15+09:00'
id: c0fcf2c905dde8bf869d
organization_url_name: null
slide: false
ignorePublish: false
---
新型コロナウィルスの影響でオンライン学習サービス等、様々な分野で期間限定の無料サービスが次々と登場していますが、その中で、4月30日までに登録したら[GCPのトレーニング](https://cloud.google.com/blog/ja/topics/training-certifications/expanding-at-home-learning)([Coursera](https://www.coursera.org/))が1ヶ月間受講料金無料という情報を発見したので早速登録して受けてみました。

ここでは学習した内容の復習も兼ねて、学んだことを（個人的に気になったことをメモ程度で）ざっくりアウトプットします。

# 受講した講座内容
[Architecting with Google Cloud Platform 日本語版専門講座](https://www.coursera.org/specializations/gcp-architecture-jp?)
この講座は以下の6つのコースで構成されています。

|  コース名  |
| ---- | 
|  [Google Cloud Platform Fundamentals: Core Infrastructure 日本語版](https://www.coursera.org/learn/gcp-fundamentals-jp?specialization=gcp-architecture-jp)  |
|  Essential Cloud Infrastructure: Foundation 日本語版  | 
|  Essential Cloud Infrastructure: Core Services 日本語版  | 
|  Elastic Cloud Infrastructure: Scaling and Automation 日本語版  | 
|  Elastic Cloud Infrastructure: Containers and Services 日本語版  | 
|  Reliable Cloud Infrastructure: Design and Process 日本語版  | 


本投稿はタイトルの通り、  

**「Google Cloud Platform Fundamentals: Core Infrastructure 日本語版」** 

のまとめになります。

**コース修了までの所要時間：** 公式の目安としては9時間のようでしたが、私の場合、5時間程度で修了できました。

# 受けてみた所感
- GCPとは？なところから、クラウドアーキテクチャの概論、コース中盤からはGCPのマネージドなDB、K8s、PaaSを使ったアプリの公開等のカリキュラムも含まれており、多くのことを広く浅く学ぶことができた。
- 座学だけでなく、クイズ形式のテストとハンズオンがあるため学んだ知識が定着しやすい。
- Qwiklabsのラボ環境を使ったハンズオンの質が高くておもしろい。
    - ハンズオンの説明がわかりやすくて迷わず進められる。ボリュームもちょうどいい。
    - ハンズオンには制限時間があり、採点までしてくれる。
    - ハンズオンで利用するGCPのログインアカウント/プロジェクトは毎回一時的なものが払い出されるため、GCPのアカウントを持っていない人でもすぐに体験することができる。

# Google Cloud Platform Fundamentals: Core Infrastructure

## 1. Google Cloud Platform の紹介
クラウドの概要・利用するメリットの紹介から始まり、GCPとは何なのかを学ぶ

### ■GCPのコンピューティングアーキテクチャは主に５つ
![gcpコンピューティングアーキテクチャ.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/319375/cd9d0616-ff10-dd6b-e0db-b45694cff522.png)

### ■GCPのゾーン/リージョンの考え方
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/319375/aa805b34-607d-ca24-1216-a2597a94434b.png)

- ゾーンはGCPリソース（VM等）のデプロイ領域。リージョン内の 単一障害点ドメイン。
- 同一リージョン内にあるゾーン間では、高速ネットワーク接続を利用することができる。
- マルチリージョンにすることでリソースを世界中のユーザの近くに配置し、かつ、自然災害などによる予期せぬ障害から保護できるメリットがある。
- 2020年5月現在、日本のリージョンは東京と大阪の２つを選択できる。

### ■オープンなAPI
- GCPのサービスはオープンソースを利用していることでベンダーロックインを回避している。これにより、Googleが最適なプロパイダでなくなった場合も移植して継続利用することができる。

## 2. Google Cloud Platform リソース階層
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/319375/ad1c44d4-1573-310f-a229-9915143e853a.png)

- ProjectはGCPを利用する際の基本単位となり、Resource（VMとか）はProject内に作るイメージ。
- OrganizationやFolderは必須ではない。ポリシーを一元的に適用する場合に作成する。

## 3. [Identity and Access Management（IAM）](https://cloud.google.com/iam?hl=ja)
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/319375/ded8e1ed-3164-beec-dc8a-ea17f2e66993.png)

- IAMポリシーは「誰が」「何を」「どのリソースに対して」行えるかを定義する
    - 「誰が」→ Googleアカウント/グループ、サービスアカウント(VM等のリソース)
    - 「何を」→ IAM役割で定義する（例えばVMの作成/削除/起動/停止の役割など）
        - IAM役割には、`基本の役割`、`事前定義された役割`、`カスタムの役割`がある

## 4. Google Cloud Platform の操作
- GCPは、`GCP Console(Web)` `SDK/Cloud Shelll` `API` `モバイルアプリ` の４つから操作が可能

## 5. [Cloud Marketplace](https://cloud.google.com/marketplace?hl=ja)
- Cloud Marketplaceには、160 以上のよく使われる開発スタック、ソリューション、サービスが提供されてあいて、即座にソフトウェアパッケージをデプロイすることができる。
- 商用ライセンスソフトウェアで公開されているものは別途追加料金がかかる。
- デプロイ後のソフトウェアの更新は顧客側で行う必要がある。

## ～ハンズオン①～
Cloud MarketplaceからLAMPスタックのVMを起動し、SSH接続する。その後、PHPのテストページを変更し、ブラウザから実際にアクセスできるかを確認する。

## 6. [Virtual Private Cloud（VPC）ネットワーク](https://cloud.google.com/vpc?hl=ja)
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/319375/53dcd192-5753-8081-746e-022963155cce.png)

- VPCを利用して同じプロジェクト内のリソースを相互に接続することができる。
- サブネットは**リージョンのリソース**となる。

## 7. [Compute Engine](https://cloud.google.com/compute/?hl=ja)
- IaaSの仮想マシン。
- VM起動スクリプトを使用することで、起動時にソフトウェアをインストールすることができる。

## 8. VPCの重要な機能
### [■Cloud Load Balancing](https://cloud.google.com/load-balancing?hl=ja)
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/319375/4c59a2a6-99bc-4c07-59a0-3a930697756f.png)

- グローバルなHTTP(S) 負荷分散だけでなく、SSLオフロードやTCP/UDP専用、内部用の負荷分散と、ケースに応じて幅広いオブションを選択することができる。

### [■Cloud DNS](https://cloud.google.com/dns?hl=ja)
- かの有名な8.8.8.8のGoogleパブリックDNSと同じインフラで実行されるDNSサービス。  
- GoogleのSLAとして、**100%** の可用性と低レイテンシを保証しているらしい。（すごい）

### [■Cloud CDN](https://cloud.google.com/cdn?hl=ja)
- キャッシュがほぼすべての主要なエンドユーザーの ISP とグローバルにピアリングされているため高速。

### [■Cloud Router](https://cloud.google.com/router/docs/concepts/overview?hl=ja)
- オンプレとGCPを接続するためのVPN接続サービス。
- 静的ルーティング／動的ルーティングを選択できる。（BGPが使える）

## ～ハンズオン②～
2台のVMを同一リージョンで異なるゾーンに配置し、ファイアウォールのルールを変更してお互いで疎通確認（ping、SSH、HTTP）を行う。

## 9. [Cloud Storage](https://cloud.google.com/storage/?hl=ja)
- オブジェクトストレージ（AWSだとS3）。ファイルシステムではない。
- データは暗号化してから保存される。
- ユースケースとしては、画像やメディアファイル、バックアップデータ等の保存に最適。
- バージョニングもオプションで有効にすることができる。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/319375/e39f44b2-96a7-8c94-0e5a-0b86944c87ad.png)

- ストレージクラス（種類）は上記の４つがある。
    - `Nealine`と`Coldline`は主にバックアップやアーカイブ用で利用する。

## 10. [Cloud BigTable](https://cloud.google.com/bigtable?hl=ja)
- フルマネージドなNoSQLデータベースサービス。
- ユースケースとしては、読み書きの多い分析データ（アドテク、金融、IoT等のデータ）の格納に最適。

## 11. [Cloud SQL](https://cloud.google.com/sql?hl=ja) と [Cloud Spanner](https://cloud.google.com/spanner?hl=ja)
- フルマネージドなリレーショナル データベース サービス。
- MySQL、PostgreSQL、SQL Serverが選択できる。
- 2TBを超える大規模なデータベースアプリの場合は、Cloud Spannerを利用する。

## 12. [Cloud Datastore](https://cloud.google.com/datastore?hl=ja)
- **アプリケーション向けの** NoSQL データベースサービス。
- Datastoreの次世代版が、Firestore。
- アプリの負荷に合わせて自動的にスケールする可用性を兼ね備えている。
- SQLライクなクエリやインデックスも使える。
- Compute EngineとApp Engineで構成するアプリの共有ストレージとしても利用可能。

## 13. 各ストレージの比較
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/319375/59744bcd-fa12-447f-21cc-cef732405c9d.png)

## ～ハンズオン③～
Compute EngineによるWebサーバ ＋ Cloud Storage/Cloud SQLを使ってファイルコンテンツを公開する。

## 14. コンテナ、Kubernetes、Kubernetes Engine
コンテナの概要からIaaS/PaaSとの違い、Google Kubernetes Engine（GKE）について学ぶ。  


### ■IaaSの特徴
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/319375/c0b3ae8d-f3d6-ab72-79cb-c1e29cbb0602.png)

- IaaSには独自のWebサーバやDB等のソフトウェアのインストールが柔軟に行えるため自由度が高いが、裏を返せば**OSレイヤから上を顧客が管理する必要がある。**また、需要の増加に対応するにはゲストOSを含むVMの単位でスケールアウトする必要があり、**リソース使用量が思いのほか急増する可能性がある。**

### ■PaaSの特徴
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/319375/0d980dd0-9d51-2b1b-ae74-9549787c4eb8.png)

- IaaSと対照的なのがPaaS。PaaSはアプリケーションのトラフィックに応じて自動的にスケールし、コードの実行中だけリソースを消費する。また、インフラの管理が不要で、開発者はアプリケーションをつくることだけに専念できる。一方、**インフラアーキテクチャや利用可能言語には制約があり、コントロールができない。**

### ■そこでコンテナ技術
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/319375/52223fb8-45dd-c799-64f5-9a8c62ca1c42.png)

- コンテナはPaaSのようなスケーラビリティとIaaSのような自由度の高い抽象化層を提供するもの。

### ■[Google Kubernetes Engine（GKE）](https://cloud.google.com/kubernetes-engine?hl=ja)
- KubernetesはOSSのコンテナオーケストレーターツール。
- GCPがマネージドしてくれるKubernetes環境が「GKE」。
- 講座内では、クラスタ/ノード/ポッドの概念、Deploymentによるポッドのスケールやアップデートの戦略、ロードバランサを使ったServiceの役割、kubectlを使った操作概要等の説明があった。  
（イメージ図等もありましたが、ここは正直、Kubernetesへの事前知識がないと理解に苦しむと感じました）

## ～ハンズオン④～
1. Cloud ShellからGKEクラスタを作り、LoadBalancer Serviceをデプロイする。
2. DeploymentとnginxのPod、外部アクセス用のServiceを作成し、自PCからpodにアクセスする。
3. オンライン状態でDeploymentのレプリカ数を増やし、Podが増えていること、ServiceのIPが変わっていないこと、アクセスできることを確認する。


## 15. [Google App Engine（GAE）](https://cloud.google.com/appengine?hl=ja)
- PaaSは 「サービスとしてのプラットフォーム」。インフラを気にせずコードのみに集中できる。
- App Engineには多くのウェブアプリに必要なNoSQLデータベース、インメモリキャッシュ、負荷分散、ヘルスチェック、ロギング、ユーザー認証機能などが組み込まれている。
- 受信トラフィック量に応じてアプリを自動的にスケール。
- ユースケースとして、ワークロードが変動しやすいか予測できないWebアプリやモバイルバックエンドに特に適している。
- App Engineには、`スタンダード環境`と`フレキシブル環境`がある。

### ■App Engineスタンダード環境
- 使用できるランタイムは特定のバージョンの `Java`、`Python`、`PHP`、`Go`の４つ。
- アプリはサンドボックス内で実行されるので、アプリからOSリソースへのアクセスは不可。
- スタンダード環境の制約は以下。制約が問題になる場合はフレキシブル環境の利用を検討する。
    - 任意のサードパーティソフトウェアはインストール不可。
    - アプリが受信するすべてのリクエストは60秒でタイムアウトする。

### ■App Engineフレキシブル環境
- フレキシブル環境では、App Engineを実行するコンテナを指定する。アプリはCompute EngineのVM上の Dockerコンテナ内で実行されるため、自由度が高い。（Dockerでできることはフレキシブル環境でもできる）
- VMの管理（ヘルスチェックやOSの自動更新等）はGoogleが対応してくれるので、ユーザはコードに集中できる。
- スタンダード環境に対応していない独自のランタイムをアップロードして任意の言語のコードを実行することができる。
- フレキシブル環境のVMにSSHができ、ソフトウェアのインストールやローカルディスクへの保存もできる。  
(PaaSだけど、管理が不要もしくは容易なIaaSのような位置づけ）

### ■スタンダードとフレキシブルの比較
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/319375/1e5edaa2-5257-7100-ffca-bed219b0b9ff.png)

- スタンダードは従量課金、フレキシブルは常時起動で常時課金

## ■App EngineとKubernetes Engineの比較
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/319375/4dd35bb6-e222-4584-28de-fa277e46a37f.png)

- App Engine環境においてコンテナは目的を達成する手段として扱うが、Kubernetes Engineではコンテナを構築の基盤として取り扱う。 

## 16. [Cloud Endpoints](https://cloud.google.com/endpoints?hl=ja) と [Apigee Edge](https://docs.apigee.com/api-platform/get-started/what-apigee-edge?hl=ja)
- APIの開発、デプロイ、管理ができるサービス。
- Google Cloud EndpointsはバックエンドがGCP上にある場合に利用できる。
- Apigee EdgeはGCP外部のバックエンドでも利用可能。例えば、GCP外部のモノリシックアプリをGCPへ一度に移行することはリスクがあるが、Apigee Edgeを使用すればサービスを１つずつ分離し、レガシーアプリが完全に使用されなくなるまでマイクロサービスに順番に実装することができる。

## ～ハンズオン⑤～
GitHubからアプリのソースコードを取得し、Cloaud Storage + App Engineでアプリを公開する。

## 17. クラウドでの開発
開発、デプロイ、モニタリング用の 一般的なGCPツールを学ぶ。

### ■[Cloud Source Repositories（開発）](https://cloud.google.com/source-repositories?hl=ja)
- GCPが用意するフルマネージドなプライベートGitリポジトリ。
- Cloud IAM が統合されているので、コードの公開をGCPプロジェクトに限定できる。

### ■[Cloud Functions（開発）](https://cloud.google.com/functions?hl=ja)
- イベントドリブンのサーバーレスコンピューティングプラットフォーム。（FaaS）
- トリガーの基準はCloud Sotrage、Cloud Pub/Sub、HTTP呼び出しのイベント。

### ■[Deployment Manager（デプロイ）](https://cloud.google.com/deployment-manager?hl=ja)
- 命令型（CLI/GUIで手動でコマンドを実行する）ではなく、宣言型のテンプレートを使って、クラウドリソースを作成・管理することができる。
- テンプレートの構成ファイルを作成することにより、リソース作成プロセスを何度も繰り返して、常に同じデプロイ結果を再現できる。

### ■[Stackdriver（モニタリング）](https://cloud.google.com/stackdriver?hl=ja)
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/319375/9788a786-fd52-cd02-4360-71e4380a77fc.png)

- GCPのモニタリンググール。Stackdriverのコアコンポーネントは上図の6つ。
- Debuggerを使えば、本番環境の任意のコード位置でアプリの状態を調べることができる。  
→**ログステートメントの記述が不要！**

## ～ハンズオン⑥～
宣言型のテンプレートを使ってDeployment ManagerでVMを起動する。
また、起動したVMに人為的な負荷をかけ、Stackdriverでその様子をモニタリングする。


## 18. Google Cloud ビッグデータ プラットフォーム
> Googleは将来的にはすべての会社がデータ企業になると確信しています。
競争上優位性を得るには迅速かつ有効にデータを使用することが不可欠です。

データを迅速かつ最大限に活用するためのGoogleのデータテクノロジーについて学習する。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/319375/7b4e60cb-9a8e-49d2-4d30-37a4d7d03496.png)




### ■[Cloud Dataproc]()
- Apache SaprkとHadoopとのマネージド サービス。
- データセットのサイズが明らかな場合の分析では本サービスが役立つ。（データセットの数に応じてジョブを起動するクラスタ数を決められるため）
- Cloud Dataprocクラスタは １秒単位で課金される。
- プリエンプティブルインスタンスだとお得（約80％の費用を削減できる）

### ■[Cloud Dataflow](https://cloud.google.com/dataflow?hl=ja)
- ビッグデータのバッチおよびストリーミング処理を実現するサービス。
- 自動で必要なインスタンスが起動してジョブを実行するため、リアルタイムのデータやデータのサイズとレートが予測できない場合に役立つ。
- ユースケースとしては、不正検出、金融サービス、IoT分析、製造、ヘルスケア、ロジスティクス、クリックストリーム、小売業のPoSやセグメンテーション分析など

### ■[BigQuery](https://cloud.google.com/bigquery?hl=ja)
- ペタバイト規模で低料金のフルマネージド分析データウェアハウス。
- SQLクエリで膨大なデータセットを高速解析することができる。
- 料金は、実行されたクエリ数とストレージサイズの２つを別々に支払う。

### ■[Cloud Pub/Sub](https://cloud.google.com/pubsub?hl=ja)
- イベントを処理するサービスとイベントを生成するサービスを切り離す非同期メッセージングサービス。 
- １秒あたり100万件以上のメッセージを処理できる。

### ■[Cloud Datalab](https://cloud.google.com/datalab?hl=ja)
- データ探索、分析、可視化と機械学習のためのツール。
- Project Jupyterを用意する必要がない（本来の仕事に専念できる）。GCPのVM上で起動する。
- GCP上のサービスと連携しているので、データにアクセスする際の認証の煩わしさがない。

## 19. Google Cloud 機械学習プラットフォーム
様々なAPIが用意されている。

### ■[Vision AI](https://cloud.google.com/vision?hl=ja)
- 画像認識のサービス。
- 画像カタログのメタデータの作成や不適切なコンテンツの管理、画像の感情分析を行える。

## ■[Cloud Speech API](https://cloud.google.com/speech-to-text?hl=ja)
- 音声認識のサービス。
- 80を超える言語と方言を認識する。
- トークンを使って名詞、動詞、形容詞などの品詞を識別して単語間の関係を把握することができる。
- 人、組織、場所などのエンティティ認識や、感情を読み取ることも可能。

## ■[Cloud Translation API](https://cloud.google.com/translate/?hl=ja)
- 言語翻訳サービス。

## ～ハンズオン⑦～
大きめのログファイルをBigQueryにInsertし、SQLクエリでログに示されている分析情報を取得する。

# さいごに
次は、[「Essential Cloud Infrastructure: Foundation 日本語版」](https://www.coursera.org/learn/gcp-infrastructure-foundation-jp?specialization=gcp-architecture-jp)のまとめをアウトプットします！

