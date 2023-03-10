[戻る](../README.md)
## 第2章 モニターリング、ロギング、修復

[2-1 AWSのモニタリング及びロギングサービスの概要](#2-1)
- メトリクスの監視  
- ログの監視  
- イベントの監視  

[2-2 Amazon ClousWatch](#2-2)

[2-3 AWS CloudTrail](#2-3)

[2-4 AWS Config](#2-4)

[2-5 AWS Personal Heath Dashboard](#2-5)

<br>

-----

<br>

<a id="2-1"></a>
### 2-1_AWSのモニタリング及びロギングサービスの概要

| モニタリング・ロギング対象  | AWSサービス |
| ------------- | ------------- |
| AWSリソースの使用状況  | Amazon CloudWatch  |
| AWSのAPI実行状況  | AWS CloudTrail  |
| AWSリソースの設定状況  | AWS Config  |
| AWSイベントの予定・状況  | AWS Personal Health Dashboard  |

そのなか、一番重要なのは`CloudWatch`です。

![chapter2_Page1.drawio.png](../drawio/chapter2/chapter2-Page-1.drawio.png)

<a id="2-2"></a>
### 2-2_Amazon CloudWatch

CloudWatchが収集・可視化・障害検知を行う対象データは以下に分類されます。
- メトリクス
- ログ
- イベント

**メトリクスの監視**

![chapter2_Page2.drawio.png](../drawio/chapter2/chapter2-Page-2.drawio.png)

**ログの監視**

![chapter2_Page3.drawio.png](../drawio/chapter2/chapter2-Page-3.drawio.png)

**イベントの監視**

![chapter2_Page4.drawio.png](../drawio/chapter2/chapter2-Page-4.drawio.png)


<a id="2-3"></a>
### 2-3_AWS CloudTrail

CloudTrailによるAPI利用状況の収集と証跡ログの出力

![chapter2_Page5.drawio.png](../drawio/chapter2/chapter2-Page-5.drawio.png)


<a id="2-4"></a>
### 2-4_AWS Config

AWS Configの動作の概要

![chapter2_Page6.drawio.png](../drawio/chapter2/chapter2-Page-6.drawio.png)

AWS Configルールの動作の概要

![chapter2_Page7.drawio.png](../drawio/chapter2/chapter2-Page-7.drawio.png)


<a id="2-5"></a>
### 2-5_AWS Personal Heath Dashboard

AWS Personal Health Dashboardに表示されるAWSイベントの種類

![chapter2_Page8.drawio.png](../drawio/chapter2/chapter2-Page-8.drawio.png)


<br>

-----