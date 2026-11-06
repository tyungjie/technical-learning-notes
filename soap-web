JavaにおけるSOAP Web Service通信の仕組み

Apache Axis2を利用したクライアント側の通信処理

1. はじめに

本記事では、Javaを利用したシステム間連携において使用されるWeb Service（SOAP）通信について解説する。

特に、Apache Axis2を利用したSOAPクライアントの実装を中心に、以下の内容を整理する。

* Web ServiceおよびSOAPの基本概念
* WSDLとStubの役割
* Apache Axis2の基本構成
* クライアント側の初期化処理
* SOAPリクエストの送信とレスポンスの受信
* 通信例外の処理とリトライ制御
* Stubおよび通信リソースの管理

実際のJavaシステムのソースコードを調査する際に、各クラスの役割と通信処理全体の流れを理解することを目的とする。

※本記事では、特定の業務システムに依存しない一般化した構成・サンプルコードを使用する。

⸻

2. Web ServiceとSOAPの基本概念

2.1 Web Serviceとは

Web Serviceとは、ネットワークを介して異なるシステム間でデータを交換し、サービスを相互利用するための仕組みである。

クライアント側はサーバー側の内部実装を意識することなく、定義されたインターフェースに従ってリクエストを送信し、レスポンスを受け取ることができる。

2.2 SOAPとは

SOAP（Simple Object Access Protocol）は、XML形式のメッセージを使用してシステム間通信を行うためのプロトコルである。

主な特徴は以下のとおり。

* XML形式でメッセージを構成する。
* HTTPやHTTPSなどのトランスポートを利用できる。
* HeaderとBodyによってメッセージを構造化する。
* 異なるプログラミング言語やプラットフォーム間で通信できる。
* WSDLによってサービスのインターフェースを定義できる。

2.3 SOAPメッセージの構造

SOAPメッセージは主にEnvelope、Header、Bodyで構成される。

<?xml version="1.0" encoding="UTF-8"?>
<soapenv:Envelope
    xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/">
    <soapenv:Header>
        <!-- 通信制御や認証などの付加情報 -->
    </soapenv:Header>
    <soapenv:Body>
        <!-- 業務リクエストデータ -->
    </soapenv:Body>
</soapenv:Envelope>

各要素の役割は以下のとおり。

要素 役割
Envelope SOAPメッセージ全体を定義する
Header 認証情報や通信制御情報などを格納する
Body 実際のリクエストまたはレスポンスデータを格納する

2.4 WSDLとは

WSDL（Web Services Description Language）は、Web ServiceのインターフェースをXML形式で記述するための言語である。

主に以下の情報を定義する。

* サービスが提供する操作（Operation）
* リクエストおよびレスポンスのデータ構造
* メッセージの形式
* 通信プロトコルとBinding
* サービスのEndpoint

WSDLを基にクライアント側のStubを生成することで、Javaのメソッド呼び出しを通じてリモートサービスを利用できる。

⸻

3. Apache Axis2の役割

Apache Axis2は、JavaでWeb Serviceを実装するためのSOAPフレームワークである。

クライアント側では、主に以下の処理を担当する。

1. SOAPリクエストメッセージの生成
2. JavaオブジェクトからXMLへの変換
3. HTTPを利用したリクエスト送信
4. SOAPレスポンスの受信
5. XMLからJavaオブジェクトへの変換
6. 通信エラーの処理

Axis2を利用することで、開発者がSOAP XMLやHTTP通信処理をすべて手動で実装する必要がなくなる。

3.1 Stubとは

Stubとは、リモートWeb Serviceのインターフェースをクライアント側から利用するためのプロキシクラスである。

一般的にはWSDLを基に生成される。

例えば、以下のようなコードでリモートサービスを呼び出すことができる。

ServiceStub stub = new ServiceStub();
Request request = new Request();
Response response = stub.execute(request);

一見すると通常のJavaメソッド呼び出しに見えるが、内部ではSOAPメッセージの生成やHTTP通信が実行される。

3.2 ServiceClientとは

ServiceClientは、Axis2における通信処理の中心的なクライアントコンポーネントである。

主な役割は以下のとおり。

* 通信先Endpointの管理
* SOAP通信オプションの管理
* リクエスト送信
* レスポンス受信
* Transportリソースの管理

StubとServiceClientの関係を理解することは、Axis2の通信処理を調査するうえで重要である。

⸻

4. SOAP通信のシステム構成

一般的なSOAP通信の構成は以下のとおり。

[業務アプリケーション]
          |
          v
[通信管理クラス]
          |
          v
[Connection実装クラス]
          |
          v
[Stub]
          |
          v
[Axis2 ServiceClient]
          |
          v
[HTTP / HTTPS]
          |
          v
[リモートWeb Service]

各コンポーネントの役割を整理する。

コンポーネント 主な役割
Manager 通信コンポーネントの初期化・管理
Connector 接続インターフェースの提供
Connection 通信処理の具体的な実装
Stub SOAPサービスを呼び出すプロキシ
ServiceClient Axis2の通信処理
Web Service リモート側の業務処理

なお、Manager、Connector、Connectionなどのクラス構成はシステム固有の設計であり、Axis2自体が必須とする構成ではない。

⸻

5. クライアント側の初期化処理

SOAP通信を開始する前に、クライアント側では通信に必要なコンポーネントを初期化する。

一般的な初期化処理は以下のとおり。

5.1 Managerの初期化

Managerクラスで通信関連コンポーネントを管理する。

5.2 Connectorの生成

通信インターフェースを実装したConnectorを生成し、初期化する。

5.3 Connectionの生成

Connectorから具体的なConnection実装クラスを利用できるようにする。

5.4 Stubの生成

WSDLを基に生成されたStubをインスタンス化する。

5.5 Client Optionsの設定

Axis2の通信設定を行う。

代表的な設定項目は以下のとおり。

設定項目 内容
Endpoint Reference 接続先Web ServiceのURL
Timeout 通信タイムアウト
Transport設定 HTTPなどの通信方式
SOAP設定 SOAPバージョンなどの通信設定

設定方法はAxis2のバージョンやStubの生成方式によって異なる。

⸻

6. SOAPリクエストの送信処理

SOAP通信の主要な処理は、リクエストの生成、送信、レスポンスの受信および変換である。

6.1 リクエストオブジェクトのチェック

通信処理の開始前に、リクエストオブジェクトがnullではないことを確認する。

if (request == null) {
    throw new IllegalArgumentException(
        "Request must not be null"
    );
}

不正なリクエストによる不要な通信を防止する。

6.2 Stubの取得

Stub Poolを利用する場合、Poolから利用可能なStubを取得する。

ServiceStub stub = stubPool.takeOut();

Stubを再利用することで、インスタンス生成に伴う処理負荷を軽減できる。

ただし、Stubの再利用可否やスレッドセーフ性については、利用しているAxis2の実装およびシステム側の管理方法を確認する必要がある。

6.3 リクエストオブジェクトの変換

業務ロジックで使用するRequestオブジェクトを、SOAP通信で使用するRequestオブジェクトに変換する。

SoapRequest soapRequest =
    createSoapRequest(request);

この処理によって、業務ロジックとSOAP通信のデータ構造を分離できる。

6.4 SOAPリクエストの送信

生成したStubを利用してWeb Serviceを呼び出す。

SoapResponse soapResponse =
    stub.execute(soapRequest);

このメソッド呼び出しを契機として、Axis2がSOAPメッセージを生成し、HTTPなどのトランスポートを通じてリモートサーバーへ送信する。

6.5 SOAPレスポンスの変換

サーバーから受信したレスポンスを業務用Responseオブジェクトに変換する。

BusinessResponse response =
    createBusinessResponse(
        soapResponse.get_return()
    );

これにより、業務側はSOAP固有のデータ構造を意識せずに処理を継続できる。

⸻

7. SOAP通信の全体的なデータフロー

リクエストからレスポンスまでの処理を整理すると、以下のようになる。

① 業務側Request
       |
       v
② SOAP Requestへの変換
       |
       v
③ Stubによるメソッド呼び出し
       |
       v
④ SOAP XMLの生成
       |
       v
⑤ HTTP / HTTPSで送信
       |
       v
⑥ リモートサーバーで業務処理
       |
       v
⑦ SOAP Responseの受信
       |
       v
⑧ Javaオブジェクトへの変換
       |
       v
⑨ 業務側Responseへの変換
       |
       v
⑩ 業務処理へ返却

このように、SOAP通信では業務データと通信データを段階的に変換する構造になっている。

⸻

8. 通信例外とリトライ制御

ネットワークを介したシステム間通信では、接続エラーやタイムアウトなどの例外が発生する可能性がある。

そのため、通信例外の種類に応じた適切な処理が必要となる。

8.1 RemoteException

Axis2のStub呼び出しでは、通信処理に関連する例外としてRemoteExceptionが発生する場合がある。

try {
    Response response = stub.execute(request);
} catch (RemoteException e) {
    Throwable cause = e.getCause();
    // 原因に応じた例外処理
}

getCause()を利用することで、例外の原因となった下位例外を確認できる。

8.2 タイムアウト

代表的なタイムアウト例外は以下のとおり。

例外 意味
SocketTimeoutException ソケット通信のタイムアウト
ConnectTimeoutException 接続確立時のタイムアウト

タイムアウトが発生した場合、接続先の応答遅延やネットワーク状態などを確認する必要がある。

8.3 リトライ制御

一時的な通信障害に対応するため、リトライ処理を実装する場合がある。

for (int retry = 0;
     retry < maxRetryCount;
     retry++) {
    try {
        Response response =
            stub.execute(request);
        return response;
    } catch (RemoteException e) {
        // リトライ可否の判定
    }
}

リトライ処理を設計する際には、以下の点に注意する。

* 最大リトライ回数
* リトライ対象となる例外
* 待機時間
* タイムアウト時の処理
* 二重実行によるデータ不整合
* 業務処理の冪等性

特に、リクエスト送信後にレスポンスだけが受信できなかった場合、サーバー側の処理がすでに完了している可能性がある。

そのため、単純に同じリクエストを再送信すると、二重登録や二重更新が発生する可能性がある。

⸻

9. Stub Poolと通信リソースの管理

SOAP通信では、StubやTransportなどの通信リソースを適切に管理することが重要である。

9.1 Stub Pool

Stub Poolは、Stubインスタンスを管理し、必要に応じて再利用するための仕組みである。

主なメリットは以下のとおり。

* Stub生成処理の削減
* 初期化コストの削減
* 通信処理の効率化
* インスタンス管理の一元化

ただし、Poolの実装によっては、利用後のStubを破棄する場合もあるため、実際の返却処理を確認する必要がある。

9.2 Transportリソースのクリーンアップ

Axis2のServiceClientでは、通信終了後にTransport関連のリソースをクリーンアップする必要がある。

例えば、以下のような処理が存在する。

stub._getServiceClient()
    .cleanupTransport();

9.3 finallyによるリソース管理

例外が発生した場合でも後処理を実行できるように、finallyブロックを利用する。

ServiceStub stub = null;
try {
    stub = stubPool.takeOut();
    Response response =
        stub.execute(request);
    return response;
} finally {
    if (stub != null) {
        stubPool.cleanUp(stub);
    }
}

なお、cleanUp()がStubをPoolへ返却するのか、破棄するのかは独自実装によって異なる。

また、TransportのクリーンアップとStubのPool管理は別の責務である。

⸻

10. SOAP通信を調査する際のポイント

既存システムのSOAP通信を調査する際には、以下の観点からソースコードを確認すると理解しやすい。

調査対象 確認内容
Manager 初期化処理、コンポーネント管理
Connector インターフェースと実装クラス
Connection 通信処理の実装
Stub WSDLとの対応、生成方法
Client Options Endpoint、Timeoutなど
Request 業務データからSOAPデータへの変換
Response SOAPデータから業務データへの変換
Exception 通信例外の分類と処理
Retry 最大回数、待機時間、再送条件
Pool Stubの取得、返却、破棄
Cleanup Transportなどのリソース管理

⸻

11. まとめ

Apache Axis2を利用したSOAP通信では、WSDLに基づいて生成されたStubを通じて、JavaアプリケーションからリモートWeb Serviceを呼び出すことができる。

通信処理の基本的な流れは以下のとおりである。

1. ManagerやConnectorによる通信コンポーネントの初期化
2. Stubの生成および通信設定
3. 業務RequestからSOAP Requestへの変換
4. Stubを利用したSOAPリクエストの送信
5. SOAP Responseの受信
6. 業務Responseへの変換
7. 通信例外の処理とリトライ制御
8. StubおよびTransportリソースの管理

SOAP通信を理解するためには、単にStubのメソッド呼び出しを見るだけではなく、その背後にあるWSDL、SOAPメッセージ、HTTP通信、例外処理、リソース管理まで把握することが重要である。

今後もJava、Web Service、AWS、システム設計などの技術について、実装と仕組みの両面から理解を深めていきたい。
