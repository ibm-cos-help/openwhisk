---

copyright:
  years: 2016, 2018
lastupdated: "2018-02-14"

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# {{site.data.keyword.openwhisk_short}} の概説

{{site.data.keyword.openwhisk}} は、サーバーレス・コンピューティングや FaaS (Function as a Service) とも呼ばれる、イベント・ドリブンの分散コンピュート・サービスです。{{site.data.keyword.openwhisk_short}} は、イベントに応えて、または、Web アプリやモバイル・アプリからの HTTP を介した直接起動に応えて、アプリケーション・ロジックを実行します。イベントは、{{site.data.keyword.Bluemix}} サービス (Cloudant など) および外部ソースから提供できます。開発者は、アプリケーション・ロジックの作成や、オンデマンドで実行されるアクションの作成に集中できます。
この新しいパラダイムの主な利点は、サーバーを明示的にプロビジョンしないことです。そのため、自動スケーリング、高可用性、更新、保守、およびサーバーが実行されているが要求を処理していないときのプロセッサー時間のコストについて心配する必要がなくなります。
HTTP 呼び出し、データベース状態変更、または、コードの実行をトリガーするその他のタイプのイベントがあると、コードが実行されます。
課金は実行時間のミリ秒単位で行われ (最も近い 100 ms に切り上げ)、VM が有益な作業をしているかどうかに関係なく VM 使用の時間単位で課金されるのではありません。
{: shortdesc}

このプログラミング・モデルは、マイクロサービス、モバイル、IoT、およびその他の多くのアプリケーションに最適です。はじめから備わっている自動スケーリングおよびロード・バランシングがすぐに使用可能であり、クラスター、ロード・バランサー、HTTP プラグインなどを手動で構成する必要はありません。{{site.data.keyword.openwhisk}} で実行する場合、ゼロ管理のメリットも享受できます。つまり、すべてのハードウェア、ネットワーキング、およびソフトウェアが IBM によって保守されます。必要な作業は、実行したいコードを用意し、それを {{site.data.keyword.openwhisk}} に渡すことだけです。残りはまるで魔法のように完了します。サーバーレス・プログラミング・モデルについての優れた概説が、[Martin Fowler のブログ](https://martinfowler.com/articles/serverless.html)にあります。

[Apache OpenWhisk ソース・コード](https://github.com/openwhisk/openwhisk)を入手して、自身でシステムを実行することもできます。

{{site.data.keyword.openwhisk_short}} の動作について詳しくは、『[{{site.data.keyword.openwhisk_short}} の概要](./openwhisk_about.html)』を参照してください。

ブラウザーおよび CLI を使用して、{{site.data.keyword.openwhisk_short}} アプリケーションを開発できます。
この 2 つには、類似したアプリケーション開発機能がありますが、CLI では、デプロイメントおよび操作をより詳細に制御できます。

## ブラウザーで開発
{: #openwhisk_start_editor}

[ブラウザー](https://console.{DomainName}/openwhisk/actions)で {{site.data.keyword.openwhisk_short}} を試して、アクションの作成、トリガーを使用したアクションの自動化、パブリック・パッケージの探索を行ってください。{{site.data.keyword.openwhisk_short}} ユーザー・インターフェースのクイック・ツアーについては、[詳細](https://console.{DomainName}/openwhisk/learn)ページを参照してください。

## CLI を使用して開発
{: #openwhisk_start_configure_cli}

{{site.data.keyword.openwhisk_short}} コマンド・ライン・インターフェース (CLI) を使用して、名前空間および許可キーをセットアップできます。
[「CLI の構成」](https://console.{DomainName}/openwhisk/cli)に移動し、手順に従ってインストールしてください。

## 概要
{: #openwhisk_start_overview}
- [OpenWhisk の動作](./openwhisk_about.html)
- [サーバーレス・アプリケーションの一般的なユース・ケース](./openwhisk_use_cases.html)
- [OpenWhisk CLI のセットアップと使用](./openwhisk_cli.html)
- [iOS アプリからの OpenWhisk の使用](./openwhisk_mobile_sdk.html)
- [記事、サンプル、およびチュートリアル](https://github.com/openwhisk/openwhisk-external-resources)
- [Apache OpenWhisk の FAQ](http://openwhisk.org/faq)
- [料金](https://console.ng.bluemix.net/openwhisk/learn/pricing)

## プログラミング・モデル
{: #openwhisk_start_programming}
- [システムの詳細](./openwhisk_reference.html)
- [OpenWhisk 提供サービスのカタログ](./openwhisk_catalog.html)
- [アクション](./openwhisk_actions.html)
- [トリガーおよびルール](./openwhisk_triggers_rules.html)
- [フィード](./openwhisk_feeds.html)
- [パッケージ](./openwhisk_packages.html)
- [アノテーション](./openwhisk_annotations.html)
- [Web アクション](./openwhisk_webactions.html)
- [API ゲートウェイ](./openwhisk_apigateway.html)
- [エンティティー名](./openwhisk_reference.html#openwhisk_entities)
- [アクションの意味](./openwhisk_reference.html#openwhisk_semantics)
- [制限](./openwhisk_reference.html#openwhisk_syslimits)

## {{site.data.keyword.openwhisk_short}} Hello World 例
{: #openwhisk_start_hello_world}
{{site.data.keyword.openwhisk_short}} の入門として、まず次の JavaScript コード例を試してみてください。

```javascript
/**
 * Hello world as an OpenWhisk action.
 */
function main(params) {
    var name = params.name || 'World';
    return {payload:  'Hello, ' + name + '!'};
}
```
{: codeblock}

この例を使用するには、以下の手順を実行してください。

1. コードをファイルに保存します。例えば、*hello.js* などです。

2. {{site.data.keyword.openwhisk_short}} CLI コマンド・ラインで、以下のコマンドを入力してアクションを作成します。
    ```
    wsk action create hello hello.js
    ```
    {: pre}

3. 次に、以下のコマンドを入力して、アクションを呼び出します。
    ```
    wsk action invoke hello --blocking --result
    ```
    {: pre}  

    このコマンドの出力は以下のとおりです。
    ```json
    {
        "payload": "Hello, World!"
    }
    ```
    
    ```
    wsk action invoke hello --blocking --result --param name Fred
    ```
    {: pre}  

    このコマンドの出力は以下のとおりです。
    ```json
    {
        "payload": "Hello, Fred!"
    }
    ```

また、{{site.data.keyword.openwhisk_short}} のイベント・ドリブン機能を使用して、イベントに対する応答としてこのアクションを呼び出すこともできます。[アラーム・サービスの例](./openwhisk_packages.html#openwhisk_package_trigger)に従って、定期的なイベントが生成されるたびに `hello` アクションを呼び出すようにイベント・ソースを構成します。

[OpenWhisk のチュートリアルおよびサンプルの全リストがここにあります](https://github.com/openwhisk/openwhisk-external-resources#sample-applications)。サンプルのほかに、記事、プレゼンテーション、ポッドキャスト、ビデオ、その他の {{site.data.keyword.openwhisk_short}} 関連リソースへのリンクが、このリポジトリーに含まれます。

## API リファレンス
{: #openwhisk_start_api notoc}
* [REST API の資料](./openwhisk_reference.html#openwhisk_ref_restapi)
* [REST API](https://console.{DomainName}/apidocs/98)

## 関連リンク
{: #general notoc}
* [ディスカバー: {{site.data.keyword.openwhisk_short}}](http://www.ibm.com/cloud-computing/bluemix/openwhisk/)
* [IBM developerWorks の {{site.data.keyword.openwhisk_short}}](https://developer.ibm.com/openwhisk/)
* [Apache {{site.data.keyword.openwhisk_short}} プロジェクト Web サイト](http://openwhisk.org)
