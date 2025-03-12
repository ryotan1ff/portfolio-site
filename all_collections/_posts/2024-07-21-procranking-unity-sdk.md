---
layout: post
title: ProcRanking Unity SDK (PRUS)
date: 2024-07-21
categories: ["SDK", "Unity Package"]
thumbnail: ./assets/images/prus-thumbnail.png
---

![{{page.title}}]({{site.baseurl}}/assets/images/prus-thumbnail.png)

サークル内製バックエンドシステムであるProcRankingのAPIを簡単に扱うためのUnity向けSDK (Software Develop Kit) です．略称はPRUS（プルース）です．サークルメンバーのみに向け，Unity Package Manager上に公開しています．[すイカゲーム]({% post_url 2023-10-28-su-ika-game %})を含む，オンラインランキング機能を持つUnityゲームたちの開発に貢献しました．

## Source Code

:bowing_man:サークル内部限定公開

## Information

### 技術スタック

- C#
- Unity
  - Unity Package Manager
- RESTful API

### 制作期間

v1.0.0公開まで半年程度

以降，メンテナンス（最終更新：2024年11月20日）

### 制作体制

ソロ
（ただし，ProcRankingシステム本体は私ではない別の部員による開発）

---

## Story

2023年の大学祭で大人気だった[すイカゲーム]({% post_url 2023-10-28-su-ika-game %})のオンラインランキングでは[ニフクラ mobile backend](https://mbaas.nifcloud.com/)を利用していましたが，このmBaaS (mobile Backend as a Service) は2024年3月にサービス終了してしまいました．そこで，サークル部員用のオンラインランキングバックエンドシステムである『ProcRanking』を後輩が新しく作ってくれました．ProcRanking（以降『プロクラ』）は，部室サーバーへデータの読み書きができるRESTful APIと，データを簡単に管理できるWebアプリケーションを提供する非常によくできたシステムです．

しかし，APIを叩くにはWebの知識が必要なため，利用が難しいという問題がありました．実際のところ，私はプロクラを利用するためにHTTPリクエスト/レスポンスの理解から始めなければなりませんでした．他の部員からは敬遠されていたのか，プロクラがリリースされてからずっと利用者が私しかいないというもったいない状況が続いていました．他の部員にもプロクラを利用してもらうためには，UnityとWebの両方をある程度理解している私が行動を起こすしかないと思うようになりました．

そこで，Unity向けSDKの開発に取り組むことにしました．
まずは，ニフクラSDKのソースコードを読み込み，内容を理解するところから始めました．コールバック関数やラムダ式，Assembly Definition，`internal`，バージョニング問題，XML Documentなど，様々な概念について学ぶことができました．
次に，必要な機能を再構築していきました．ゲームデータを受け取ると，JSON形式にパースしてプロクラサーバーに送信します．データ受信時は逆の手順をたどります．プログラミングに慣れていない部員もいることを考慮し，簡潔に送信を行える拡張メソッドを独自に実装しました．（コードブロックを参照ください．）
最後に，サークル内限定で配布する方法を模索しました．プロクラサーバーに認証機能があるとはいえ，[Basic SecCap](https://www.seccap.jp/basic/index.html)でセキュリティの重要性を学んでいたため，むやみに公開はできないと判断しました．秘匿性とアクセスしやすさを考慮した結果，サークルのGitHub Organizationを利用し，プライベートリポジトリからUnity Package Manager経由で配布することにしました．ファイルの直接的なやり取りではなくUPMを採用したことで，アップデートを配信しやすいというメリットも生まれました．

```csharp
// 拡張メソッドの実装
public static ProcRaData SaveToProcRaAsync(this object value, string storeName, string key)
{
    ProcRaData procRaData = new ProcRaData(storeName).Add(key, value);
    procRaData.SaveAsync();
    return procRaData;
}

// 利用例（ProcRaDataクラスを意識せずに書ける）
using ProcRanking;
int score = 123;
score.SaveToProcRaAsync("ExampleDataStore", "score");
```

リリースとほぼ同時に，すイカゲームのランキング機能をPRUSに移行しました．プロクラ本体側でニフクラとのデータ移行に対応してくれていたので，以前のスコアデータを保持したままオンライン機能を継続することができました．リリース後には，早速2つのゲームにPRUSを利用してもらえました．ちなみに，リリース当初はMac環境でバグがありましたが，アップデートにより解決しています．プロジェクトを振り返ると，システム提供者と利用者との架け橋になって双方の役に立てたうえに，より多くのオンラインゲームをお客さんに届けるための力添えができた点で，やりがいのある開発だったと思います．
