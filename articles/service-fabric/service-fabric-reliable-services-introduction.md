<properties
   pageTitle="Service Fabric の Reliable Service プログラミング モデルの概要"
   description="Service Fabric の Reliable Service プログラミング モデルについて学び、独自のサービスを作成しましょう。"
   services="Service-Fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor="jessebenson; mani-ramaswamy"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/26/2015"
   ms.author="masnider;jesseb"/>

# Reliable Service の概要
Service Fabric を使用すると、信頼性の高いステートレス サービスとステートフル サービスを簡単に作成および管理できます。このドキュメントでは、以下について説明します。

1. ステートレス サービスおよびステートフル サービスの Reliable Service プログラミング モデル。
2. Reliable Service を作成するうえでの異なる選択肢。
3. Reliable Service を使用する場合のさまざまなシナリオと例、および Reliable Service の作成方法

Reliable Service は、Service Fabric で使用できるプログラミング モデルの 1 つです。Reliable Actor プログラミング モデルの詳細については、[その概要](service-fabric-reliable-actors-introduction.md)を参照してください。

Service Fabric では、サービスは構成およびアプリケーション コードで構成されますが、オプションで状態を追加することもできます。

Service Fabric では、プロビジョニングからデプロイまでのサービスの有効期間と削除が[Service Fabric のアプリケーション管理](service-fabric-deploy-remove-applications.md)で管理されます。

## Reliable Service について
Reliable Service は、重要機能をアプリケーションに組み込むためのシンプルかつ強力な最上位レベルのプログラミング モデルを提供します。Reliable Service プログラミング モデルを使用すると、以下のことを実現できます。

1. ステートフル サービスの場合、Reliable Service プログラミング モデルを使用すると、Reliable Collection によって状態がサービス内に一貫して確実に格納されます。Reliable Collection は、C# コレクションを使用したことがあるユーザーには馴染みのある可用性が高い一連のコレクション クラスです。これまでは、サービスにおいて確かな方法で状態を管理をするには、外部システムが必要でした。Reliable Collection を使用するとユーザーのコンピューターの隣に状態を格納できるようになると同時に、高可用性の外部ストアと同じレベルの高い可用性と確実性を実現できます。また、コンピューターと状態を併置することで遅延時間も短縮します。

2. 見慣れたプログラミング モデルに似た、コードを実行するためのシンプルなモデル。コードには、適切に定義されたエントリ ポイントと管理が簡単なライフ サイクルが含まれます。

3. プラグ可能な通信モデル - HTTP と [Web API](service-fabric-reliable-services-communication-webapi.md)、Websocket、カスタム TCP プロトコルなど、好みのトランスポートを使用できます。Reliable Service には、すぐに使用できる優れたオプションも用意されていますが、自身のオプションを提供することもできます。

## Reliable Service の特長
Service Fabric の Reliable Service は、以前に作成されたことがあるサービスとは異なります。Service Fabric は信頼性、可用性、整合性、およびスケーラビリティを提供します。

+ <u>信頼性</u> - コンピューターの障害やネットワーク問題の発生などによって環境が悪化しても、サービスは維持されます。

+ <u>可用性</u> - サービスにアクセス可能で、応答性が維持されます (見つからないサービスや、外部からアクセスできないサービスがないことを意味するわけではありません)。

+ <u>スケーラビリティ</u> - 特定のハードウェアからサービスを分離し、必要に応じて、ハードウェア リソースまたは仮想リソースを追加したり削除したりすることで拡張や縮小ができます。サービスは、サービスの独立部分で個別にスケーリングしたり障害に対応したりできるように簡単に分割できます (特にステートフル サービスの場合)。最後に、Service Fabric では、数千単位のサービスをプロビジョニングする場合、すべての OS インスタンスを特定ワークロードの単一インスタンスに使用するのではなく 1 つのプロセスで実行できるため、軽量なサービスが作成される傾向があります。

+ <u>整合性</u> - このサービスに格納されたすべての情報に一貫性があることが保証されているということです (これはステートフル サービスに限定され、詳細については後ほど説明します)。

## サービスのライフサイクル
サービスがステートフルまたはステートレスかどうかにかかわらず、Reliable Service は、コードをすばやく追加して使用開始できるというシンプルなライフサイクルを提供します。サービスを稼動開始するために実装しなければならないメソッドは 1 つまたは 2 つしかありません。

+ CreateCommunicationListener - サービスで使用される通信スタックがここで定義されます。[Web API](service-fabric-reliable-services-communication-webapi.md) などの通信スタックによって、サービスをリッスンするエンドポイント (クライアントによるアクセス方法) が定義されるほか、表示されるメッセージがサービス コードの残りの部分と最終的にやり取りする方法が定義されます。

+ RunAsync - ここでサービスによってビジネス ロジックが実行されます。ここで提供されているキャンセル トークンは、そのサービス実行を停止する信号となります。たとえば、常に ReliableQueue からメッセージを取得して処理する必要があるサービスの場合、ここでサービスが実行されます。

Reliable Service のライフサイクル内での主要イベントを次に示します。

1. サービス オブジェクト (StatelessService または StatefulService から派生したもの) が構築されます。

2. CreateCommunicationListener メソッドが呼び出されると、任意の通信リスナーを返すことができます。
  + これはオプションですが、サービスのほとんどで一部のエンドポイントが直接公開されます。

3. CommunicationListener は、作成された時点で開始されます。
  + CommunicationListeners に含まれる Open() と呼ばれるメソッドはこのときに呼び出され、サービスのリスニング アドレスを返します。組み込まれている ICommunicationListeners の 1 つを Reliable Service で使用した場合は、この処理はサービス側で実行されます。

4. 通信リスナーが Open() によって開始されると、主要サービスで RunAsync() が呼び出されます。
  + RunAsync はオプションであることに注意してください。サービスがユーザーの呼び出しにのみ応答してすべての処理を直接実行する場合、RunAsync() を実装する必要はありません。

サービスをシャットダウンする場合も (削除したり、単に特定の場所から移動したりする場合)、呼び出しの順序は同じで、最初に CommunicationListener で Close() が呼び出され、次に RunAsync() に渡されたキャンセル トークンが取り消されます。

## サービスの例
このプログラミング モデルを理解した上で、2 つのサービスを見ながら、これらがどのように機能するかを説明します。

### ステートレス Reliable Service
ステートレス サービスは、文字通りサービス内で状態が保持されないサービス、または存在する状態が完全に破棄可能なため同期、レプリケーション、永続化、または高可用性を必要としないサービスです。

たとえば、メモリのない電卓を考えてみてください。電卓は、同時に処理しなければならないすべての項と演算を受け取ります。

この場合は、サービスの RunAsync() を空にしておくことができます。これは、サービスが実行する必要のあるバック グラウンドのタスク処理がないためです。電卓サービスを作成すると、CommunicationListener (たとえば [Web API](service-fabric-reliable-services-communication-webapi.md)) が返され、このメソッドが任意のポートでリッスン エンドポイントを開始します。このリッスン エンドポイントは別のメソッド (例: "Add (n1, n2)") に接続して、電卓のパブリック API を定義します。

クライアントから呼び出しが行われた場合は、適切なメソッドが呼び出され、電卓サービスは提供されたデータに対する操作を実行して、結果を返します。この処理では、状態はまったく保存されません。

内部の状態がまったく格納されないため、この電卓の例は非常にシンプルです。しかしながら、大半のサービスは完全にステートレスではありません。実際のところ、状態が外部ストアに格納されているからです (たとえば、セッション状態を主にバッキング ストアまたはキャッシュに格納しているすべての Web アプリ)。

Service Fabric でのステートレス サービスの使用方法を示す一般的な例は、Web アプリケーションの公開 API を公開するフロントエンド サービスです。フロントエンド サービスは、ステートフル サービスと通信してユーザーの要求を完了します。この場合、クライアントからの呼び出しは、ポート 80 のようにステートレス サービスがリッスンしている既知のポートに送られます。このステートレス サービスは呼び出しを受信し、その呼び出しが信頼済みの利用者からのものなのか、またどのサービスに向けて送信されたのかを判断します。次にステートレス サービスは、その呼び出しをステートフル サービスの正しいパーティションに転送して、応答を待ちます。応答を受信すると、ステートレス サービスは元のクライアントに応答します。

### ステートフル Reliable Service
ステートフル サービスは、サービス提供するために、状態の一部の整合性を維持して永続化しておかなければならないサービスです。受け取る最新情報に基づいて値の移動平均を断続的に計算するサービスを考えてみてください。このサービスを提供するには、最新の平均の他に、処理する必要がある一連の現在の受信要求の両方がなければなりません。情報を取得して処理し、外部ストアに格納するサービス (現在の Azure Blob またはテーブル ストアなど) はステートフル サービスです。状態が外部の状態ストアに格納されます。

現在のほとんどのサービスでは状態が外部ストアに格納されますが、その理由は、状態の信頼性、可用性、スケーラビリティ、および整合性を提供できるのが外部ストアだからです。Service Fabric では、ステートフル サービスで状態を外部ストアに格納することは義務づけられていません。サービス コードとサービス状態の両方についてこれらの要件が Service Fabric 側で満たされているためです。

たとえば、イメージで実行する必要がある一連の変換要求および変換する必要があるイメージを受信するサービスを作成するとします。このようなサービスの場合、通信ポートを開始する CommunicationListener (たとえば、WebAPI) が返され、`ConvertImage(Image i, IList<Conversion> conversions)` のような API を使用した送信が可能になるようにします。この API では、サービスが情報を取得して要求を ReliableQueue に格納してから、クライアントに何らかのトークンを返すことで、この要求が追跡できるようになります (要求に時間がかかる場合があるため)。

このサービスでは、RunAsync が複雑になる可能性があります。というのも、ReliableQueue から要求を取得し、リストされた変換を実行し、その結果を ReliableDictionary に格納するというループが RunAsync 内に追加されるからです。これによって、クライアントは戻ってきたときに、変換されたイメージを取得できます。何らかの障害が発生した場合でもイメージが失われないように、この Reliable Service では情報がキューから取得され、変換してからその結果がトランザクションに格納されます。これによって、キューから実際に削除されるのはメッセージのみで、変換が完了すると結果は結果ディクショナリに格納されます。途中で障害が発生した場合 (コードのこのインスタンスが実行されてるマシンなどで)、要求はキューに残ったままで、再度処理されるのを待ちます。

このサービスの注目すべき点は、これらの処理が通常の .NET サービスに似ているということです。唯一異なる点は、使用されているデータ構造 (ReliableQueue および ReliableDictionary) が Service Fabric によって提供されているため、信頼性、可用性、および整合性が高いということです。

## Reliable Service API を使用する場合
ご使用のアプリケーション サービスのニーズに次のいずれかが当てはまる場合は、Reliable Service API を検討してください。

- 複数の状態に対するアプリケーションの動作を提供する必要がある (例: 注文や注文品目)

- アプリケーションの状態を、Reliable Dictionary と Reliable Queue として自然にモデル化することができる

- 状態の可用性を高め、アクセスの待機時間を短くする必要がある

- アプリケーションでは、1 つまたは複数の Reliable Collection 間で、トランザクション操作の同時実行または粒度を制御する必要がある

- サービスの通信を管理するか、パーティション構成を制御したい

- コードにはフリー スレッドの実行時環境が必要である

- アプリケーションで、実行時に Reliable Dictionary または Reliable Queue を動的に作成または破棄する必要がある

- サービスの状態について、Service Fabric が提供するバックアップ機能および復元機能を、プログラム的に制御する必要がある*

- アプリケーションで、複数の状態に関する変更履歴を維持する必要がある*

- カスタム状態プロバイダーを開発するか、サード パーティが開発したカスタム状態プロバイダーを使用したい*

> [AZURE.NOTE]*SDK で利用できる機能で一般公開されているもの


## 次のステップ
+ [Reliable Service のクイック スタート](service-fabric-reliable-services-quick-start.md)
+ [Reliable Service の詳細な使用方法](service-fabric-reliable-services-advanced-usage.md)
+ [Reliable Actor プログラミング モデルについて](service-fabric-reliable-actors-introduction.md)
 

<!---HONumber=Oct15_HO3-->