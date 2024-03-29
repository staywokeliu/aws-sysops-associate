[戻る](../README.md)
## 第3章 信頼性とビジネス継続性

[3-1 ユースケースに応じた拡張性と伸縮性の実現](#3-1)
- [AWS Auto Scalingプランの作成と維持](#3-1-1)
- [キャッシングの実装](#3-1-2)
- [Amazon RDSレプリカとAmazon Auroraレプリカの実装](#3-1-3)
- [疎結合アーキテクチャの実装](#3-1-4)
- [水平方向のスケーリングと垂直方向のスケーリングの区別](#3-1-5)

[3-2 高可用性と耐障害性のある環境の構築](#3-2)
- [ELBとRoute53のヘルスチェックの設定](#3-2-1)
- [単一のAZの使用とマルチAZのデプロイメントの違い](#3-2-2)
- [フォールトトレラントなワークロードの実装](#3-2-3)
- [Route53ルーティングポリシーの実装](#3-2-4)

[3-3 バックアップ・リストア戦略の導入](#3-3)
- [ユースケースにもとづいたスナップショットとバックアップの自動化](#3-3-1)
- [データベースのリストア](#3-3-2)
- [バージョニングとライフサイクルルールの実装](#3-3-3)
- [Amazon S3クロスリージョンレプリケーションの設定](#3-3-4)
- [災害復旧手順の実行](#3-3-5)

-----

<a id="3-1"></a>

### 3-1 ユースケースに応じた拡張性と伸縮性の実現

<a id="3-1-1"></a>

**AWS Auto Scalingプランの作成と維持**

Auto Scalingには次の３種類があります。
- AWS Auto Scaling
- Amaozn EC2 Auto Scaling
- Applicaiton Auto Scaling

それぞれの違いは次の２点です。
- スケーリングのオプション
- スケーリング対象

この３つの関係性を示したものが次の図です。  
![chapter3_Page1.drawio.png](../drawio/chapter3/chapter3-Page-1.drawio.png)

ELBとの関係

Auto ScalingとALBとの関係  
![chapter3_Page2.drawio.png](../drawio/chapter3/chapter3-Page-2.drawio.png)

<a id="3-1-2"></a>

**キャッシングの実装**

キャッシングが行えるAWSサービスには下記があります。
- Amazon ElastiCache
- Amazon DynamoDB Accelerator(DAX)
- Amazon CloudFront
- Amazon MemoryDB

ElastiCacheの概要  
![chapter3_Page3.drawio.png](../drawio/chapter3/chapter3-Page-3.drawio.png)


ElastiCacheのスケーリング

Memcachedのスケーリング  
![chapter3_Page4.drawio.png](../drawio/chapter3/chapter3-Page-4.drawio.png)


Redisのスケール  
![chapter3_Page5.drawio.png](../drawio/chapter3/chapter3-Page-5.drawio.png)


ElastiCacheの障害時の挙動  
![chapter3_Page6.drawio.png](../drawio/chapter3/chapter3-Page-6.drawio.png)

<a id="3-1-3"></a>

**Amazon RDSレプリカとAmazon Auroraレプリカの実装**

レプリカ

RDSのレプリカ  
![chapter3_Page7.drawio.png](../drawio/chapter3/chapter3-Page-7.drawio.png)

AuroraのリードレプリカはAWS Auto Scalingでオートスケーリングが可能で、
次のメトリクスが利用可能です。
- CPU使用率
- 平均接続数

<a id="3-1-4"></a>

**疎結合アーキテクチャの実装**

Amazon SQS

疎結合アーキテクチャを代表するAWSサービスの１つは**Amazon SQS**です。
Amazon SQSは、フルマネージドなメッセージキューイングサービスで、分散されたシステム間で非同期メッセージが行えるキューを提供します。

![chapter3_Page8.drawio.png](../drawio/chapter3/chapter3-Page-8.drawio.png)

重い処理などを実行したい場合、タスクを分割してSQSへキューを送信し、それを複数のコンシューマーが処理するようなスケーリングも可能になります。

![chapter3_Page9.drawio.png](../drawio/chapter3/chapter3-Page-9.drawio.png)

コンシューマーのスケーリングはAWS Auto Scalingで可能です。  
キュー数などのメトリクスを取得し、その値をカスタムメトリクスとして、CloudWatchに入れるアプリケーションを作成することで可能になります。

![chapter3_Page10.drawio.png](../drawio/chapter3/chapter3-Page-10.drawio.png)

<a id="3-1-5"></a>

**水平方向のスケーリングと垂直方向のスケーリングの区別**

**垂直スケーリング**とは、CPUやメモリなどのリソースのスペックを変更する方法で、スペックを上げることをスケールアップ、スペックを下げることをスケールダウンと呼びます。

**水平スケーリング**とは、リソースの数を増減する方法で、増やすことをスケーリングアウト、減らすことをスケールインと呼びます。

一般的には、スケートフルなアプリケーションは水平スケーリングではなく、垂直スケーリングがすすめられている。

例えば、サーバ内にログイン情報などのセッション情報を持っている場合を考えます。もし、スケールインで設計すると、auto scalingが収束時、自動でダウンされますので、そのインスタンス内に保存した情報が失ってしまいます。

![chapter3_Page11.drawio.png](../drawio/chapter3/chapter3-Page-11.drawio.png)

対処法として、ステートフルになっているセッション情報をスケーリングの外にあるElastiCacheへ出して上げることが考えられます。これにより、サーバーはステートレスとなるため、水平スケーリングが可能になります。

![chapter3_Page12.drawio.png](../drawio/chapter3/chapter3-Page-12.drawio.png)


<a id="3-2"></a>

### 3-2 高可用性と耐障害性のある環境の構築

可用性とは、システムが使用できる状態を維持できる能力のことです。  
耐障害性とは、障害が起きでもシステムを維持できる能力のことで、フェイルソフト・フェイルセーフ・フールプルーフを含んだ総称になります。  
フェイルソフトは、稼働の継続を優先し性能の低下を許容すること、 
フェイルセールは、安全優先として稼働停止を許容すること、  
フールプルーフは、誤操作しても危険がおきない、もしくは誤操作できない設計にすることです。

<a id="3-2-1"></a>

**ELBとRoute53のヘルスチェックの設定**

ELBには下記コンポーネントが存在します。
- ロードバランサー
- リスナー
- ターゲットグループ(CLBは存在しない)

![chapter3_Page13.drawio.png](../drawio/chapter3/chapter3-Page-13.drawio.png)


ロードバランサーはDNSやVPC、セキュリティグループ、リスナーを管理しています。

リスナーは、プロトコルとポート、ルールを管理しています。

ターゲットグループは、ターゲットとヘルスチェックを管理しています。

ALBのプロトコル：HTTP、HTTPS  
NLBのプロトコル：TCP、UDP、TLS、TCP_UDP

**ALBについて**

![chapter3_Page14.drawio.png](../drawio/chapter3/chapter3-Page-14.drawio.png)

ALBのターゲットは次のように様々な選択肢が用意されています。
- EC2
- ECS
- Lambda
- IP

NLBはALBとの違いは、Lambdaがないことです。

<a id="3-2-2"></a>

**単一のAZの使用とマルチAZのデプロイメントの違い**

EC2のマルチAZ化  
![chapter3_Page15.drawio.png](../drawio/chapter3/chapter3-Page-15.drawio.png)

RDSのマルチAZ化  
![chapter3_Page16.drawio.png](../drawio/chapter3/chapter3-Page-16.drawio.png)

Amazon FSxのマルチAZ化  
![chapter3_Page17.drawio.png](../drawio/chapter3/chapter3-Page-17.drawio.png)

<a id="3-2-3"></a>

**フォールトトレラントなワークロードの実装**

フォールトトレラントとは、耐障害性があることで、コンポーネントの一部が故障しても、システムがパフォーマンスを落とさずに動作し続けることです。Amazon EFSやElastic IPアドレスを利用することで、フォールトトレラントを向上できます。

![chapter3_Page18.drawio.png](../drawio/chapter3/chapter3-Page-18.drawio.png)

![chapter3_Page19.drawio.png](../drawio/chapter3/chapter3-Page-19.drawio.png)


Elastic IPアドレス、別のインスタンスに割り当て直すことが可能です。

![chapter3_Page20.drawio.png](../drawio/chapter3/chapter3-Page-20.drawio.png)


<a id="3-2-4"></a>

**Route53ルーティングポリシーの実装**

ルーティングポリシーはレコード作成時に決定でき、次の７種類があります。
- シンプルルーティングポリシー
- 加重 〜
- フェイルオーバー 〜
- 複数値回答 〜
- レイテンシー 〜
- 位置情報 〜
- 地理的接近性 〜

シンプルルーティングポリシー  
標準のDNSレコードを設定でき、トラフィックを１つのリソースにルーティングする場合利用します。  
複数のレコードを同じ名前と型で作成できないが、同一レコードで複数のIPアドレスなど複数の値を指定できます。  
<ins>複数の値を設定すると**ランダムに**値が返される、**DNSラウンドロビン**と呼ばれる負荷分散が可能です。</ins>

加重ルーティングポリシー  
割合を決めて負荷分散する方式です。

![chapter3_Page21.drawio.png](../drawio/chapter3/chapter3-Page-21.drawio.png)

フェイルオーバールーティングポリシー  
リソースが正常な方へトラフィックをルーティングできます。正常かどうかはRoute 53ヘルスチェックを利用します。<ins>このルーティングポリシーを利用すれば、リージョン障害への対策も可能です。</ins>

![chapter3_Page22.drawio.png](../drawio/chapter3/chapter3-Page-22.drawio.png)


複数値回答ルーティングポリシー  

![chapter3_Page23.drawio.png](../drawio/chapter3/chapter3-Page-23.drawio.png)

レイテンシールーティングポリシー  

![chapter3_Page24.drawio.png](../drawio/chapter3/chapter3-Page-24.drawio.png)


<a id="3-3"></a>

### 3-3 バックアップ・リストア戦略の導入


<a id="3-3-1"></a>

**ユースケースにもとづいたスナップショットとバックアップの自動化**

![chapter3_Page25.drawio.png](../drawio/chapter3/chapter3-Page-25.drawio.png)

<a id="3-3-2"></a>

**データベースのリストア**

![chapter3_Page26.drawio.png](../drawio/chapter3/chapter3-Page-26.drawio.png)


![chapter3_Page27.drawio.png](../drawio/chapter3/chapter3-Page-27.drawio.png)


<a id="3-3-3"></a>

**バージョニングとライフサイクルルールの実装**

![chapter3_Page28.drawio.png](../drawio/chapter3/chapter3-Page-28.drawio.png)

アクセス制限の比較

| アクセス制限  | 制限対象 | 制限の単位 | 主な用途 |
| ------------- | ------------- | ------------- | ------------- |
| IAMポリシー | アクセス元  | IAMユーザーまたはIAMロール  | ユーザー・ロール単位の制限 |
| パケットポリシー | アクセス先  | S3バケット | バケット単位の制限・クロスアカウントの制限 |
| ACL | アクセス先 | S3バケットまたはオブジェクト | オブジェクト単位の制限 |


<a id="3-3-4"></a>

**Amazon S3クロスリージョンレプリケーションの設定**


![chapter3_Page29.drawio.png](../drawio/chapter3/chapter3-Page-29.drawio.png)


<a id="3-3-5"></a>

**災害復旧手順の実行**


- バックアップ・リストア
- パイロットライト
- ウォームスタンバイ
- マルチサイト


バックアップ・リストア

![chapter3_Page30.drawio.png](../drawio/chapter3/chapter3-Page-30.drawio.png)

![chapter3_Page31.drawio.png](../drawio/chapter3/chapter3-Page-31.drawio.png)

パイロットライト

![chapter3_Page32.drawio.png](../drawio/chapter3/chapter3-Page-32.drawio.png)


![chapter3_Page33.drawio.png](../drawio/chapter3/chapter3-Page-33.drawio.png)

ウォームスタンバイ

![chapter3_Page34.drawio.png](../drawio/chapter3/chapter3-Page-34.drawio.png)

![chapter3_Page35.drawio.png](../drawio/chapter3/chapter3-Page-35.drawio.png)

マルチサイト

![chapter3_Page36.drawio.png](../drawio/chapter3/chapter3-Page-36.drawio.png)

![chapter3_Page37.drawio.png](../drawio/chapter3/chapter3-Page-37.drawio.png)


<br>

-----