<properties title="Manage your Search service on Microsoft Azure" pageTitle="Manage your Search service on Microsoft Azure" description="Manage your Search service on Microsoft Azure" metaKeywords="" services="" solutions="" documentationCenter="" authors="heidist" videoId="" scriptId="" />

# Microsoft Azure で検索サービスを管理する

[WACOM.INCLUDE [この記事では Azure プレビュー ポータルを使用します][]]

Azure Search は、カスタムの検索アプリケーションで使用できるクラウドベースのサービスと HTTP ベースの API です。この検索サービスでは、全文検索テキスト分析のためのエンジン、高度な検索機能、ストレージ、クエリ コマンド構文が用意されています。

この記事では、Microsoft Azure で検索サービスを管理する方法について説明します。

前述のように、管理タスクのために新しいプレビュー ポータルが必要です。Azure Search は、以前のバージョンのポータルでは利用できません。共通管理タスクのスクリプトを作成できる管理 API が、間もなく利用可能になります。

<!--TOC-->

-   [サブスクリプションに検索サービスを追加する][]
-   [管理タスク][]
-   [サービス URL][]
-   [API キーを管理する][]
-   [リソース使用量を監視する][]
-   [拡大または縮小][]
-   [サービスを開始または停止する][]

## サブスクリプションに検索サービスを追加する

管理者として、新しい [Azure プレビュー ポータル][]を使用して既存の Azure サブスクリプションに検索を追加できます。サブスクリプションに機能を追加できるのは、管理者だけです。サービスを設定する場合は、2 種類の料金レベルから選択できます。

既存の登録者に課金することなく、共有サービスを使用できます。学習目的、概念実証のテスト、小規模な開発用プロジェクトの場合に適しています。共有サービスでは、ストレージが最大 50 MB、インデックスが最大 3 つ、ハードウェアの上限としてドキュメント数が 10,000 個 (ストレージの使用が許容最大限の 50 MB に達していない場合であっても) という制約があります。共有サービスではパフォーマンスは保証されないため、運用検索アプリケーションを構築する場合は、代わりに標準検索を検討してください。

サブスクリプションのみで使用される専用のリソースとインフラストラクチャにサインアップするため、標準検索は課金の対象です。標準検索は、パーティション (ストレージ) とレプリカ (サービス ワークロード) のユーザー定義バンドルで割り当てられ、検索単位ごとに価格設定されます。パーティションまたはレプリカで個別に規模を拡大して、必要とされるリソースを追加することができます。

容量を考慮して計画し、課金の影響を理解するには、以下のリンクをお勧めします。

-   [Limits and constraints (Azure Search API) (制限と制約 (Azure Search API))][]
-   [料金の詳細][]

サインアップの準備が整った場合は、「[Azure プレビュー ポータルで Search を構成する][]」を参照してください。

## 管理タスク

共同管理者に対応したサービスもありますが、Azure Search サービスの管理者はサブスクリプションごとに 1 人です。このセクションで説明するタスクは、管理者が実行する必要があります。
サブスクリプションへの Search の追加に加えて、管理者は以下のタスクを実行します。

-   サービス URL を配布する (サービス プロビジョニング中に定義される)。
-   API キーを管理および配布する。
-   リソース使用量を監視する
-   拡大または縮小 (標準検索のみに適用される)
-   サービスを開始または停止する

## サービス URL

サービス URL は、サービスを作成するときに固定プロパティとして定義され、後で変更できません。

検索アプリケーションを構築する開発者は、HTTP 要求のためのサービス URL を知っている必要があります。サービス URL をすばやく見つけるには、サービス ダッシュボードを使用します。

サービス ダッシュボードからサービス URL を取得するには:

1.  [Azure プレビュー ポータル][]にサインインします。
2.  **[参照]**、**[すべて]**、**[検索サービス]** の順にクリックします。
3.  目的の検索サービスの名前をクリックして、ダッシュボードを開きます。
4.  **[プロパティ]** をクリックしてプロパティ ページを開きます。ページの上部にサービス URL が表示されています。後ですばやくアクセスできるように、このページを固定できます。

    ![][]

開発者から API バージョンに関する問い合わせがある可能性もあります。Azure Search API のコーディング要件として、要求では常に API バージョンを指定する必要があります。これは、開発者が以前のバージョンを引き続き使用して、適時に新しいバージョンに移行できるようにするための要件です。

API バージョンはポータル ページに表示されないため、管理者はこの情報を提供できません。現在および以前の API バージョンについては、「[Azure Search REST API (Azure Search REST API)][]」を参照してください。

<!---->

## API キーを管理する

検索アプリケーションを構築する開発者は、Search にアクセスするために API キーが必要です。検索サービスへのすべての HTTP 要求には、対象のサービス用に生成された API キーが必要です。この API キーは、その検索サービス URL に対する認証のための唯一のメカニズムです。

検索サービスへのアクセスには 2 種類のキーが使用されます。

-   管理 (すべての操作に有効)
-   クエリ (クエリ要求に対してのみ有効)

管理 API キーは、サービスで作成されます。プライマリ キーとセカンダリ キーがあります。どちらかのアクセスのレベルが高いということはなく、対等に使用できるため、キーをロール オーバーする場合に便利です。どちらの管理キーも再生成できますが、管理キーの合計数は追加できません。検索サービスごとに最大 2 個の管理キーを保持できます。

クエリ キーは、検索を直接呼び出すクライアント アプリケーションで使用するために設計されています。クエリ キーは最大 50 個まで作成できます。

API キーを取得または再生成するには、サービス ダッシュボードを開きます。**[キー]** をクリックし、キー管理ページを開きます。キーの再生成または作成のコマンドがページ上部にあります。

![][1]

<!---->

## リソース使用量を監視する

このパブリック プレビューでは、監視リソースの対象が、サービス ダッシュボードに表示される情報と、サービスのクエリで取得できるいくつかのメトリックに限定されます。

サービス ダッシュボードの [使用] セクションで、パーティション リソース レベルが自分のアプリケーションに適しているかどうかをすばやく決定できます。

Search Service API を使用して、ドキュメントとインデックスの数を取得できます。料金レベルに基づいて、これらの数に関連付けられたハードウェアの制限があります。詳細については、「[Limits and constraints (Azure Search API) (制限と制約 (Azure Search API))][]」を参照してください。

-   [Get Index Statistics (Azure Search API) (インデックス統計の取得 (Azure Search API))][]
-   [Count Documents (Azure Search) (ドキュメントのカウント (Azure Search))][]

> [WACOM.NOTE] キャッシング動作により、一時的に制限を超える場合があります。たとえば、共有サービスの使用時に、ドキュメント数がハードウェアの制限である 10,000 個を超える場合があります。この超過は一時的であり、次の制限強制チェックで検出されます。

<!---->

## 拡大または縮小

すべての検索サービスは、最小限の 1 つのレプリカと 1 つのパーティションで開始されます。標準の料金レベルを使用して専用のリソースにサインアップした場合は、サービス ダッシュボードの **[規模の設定]** タイルをクリックして、サービスで使用するパーティションとレプリカの数を再調整できます。

いずれかのリソースを追加すると、それらは自動的にサービスに使用されます。それ以外のアクションは必要ありませんが、新しいリソースの影響が現れるまでわずかに遅延が発生します。追加のリソースのプロビジョニングには 15 分以上かかる場合があります。

![][2]

### レプリカの追加

1 秒あたりのクエリ (QPS) の増加や高可用性の実現は、レプリカの追加により達成できます。各レプリカにはインデックスのコピーが 1 つあるため、レプリカを 1 つ追加することは、サービス クエリ要求で使用できるインデックスを 1 つ追加することです。現在、一般的には、高可用性のために少なくとも 3 つのレプリカが必要です。

より多くのレプリカを持つ検索サービスでは、より多くのインデックスに対してクエリ要求を負荷分散できます。特定のクエリの数量のレベルで、要求のサービスで利用できるインデックスのコピーが多いほど、クエリ スループットはより高速になります。クエリの遅延が発生した場合は、追加のレプリカをオンラインにすることでパフォーマンスへの良い影響を期待できます。

レプリカを追加するとクエリのスループットは向上しますが、サービスにレプリカを追加することにより厳密に 2 倍や 3 倍になるわけではありません。すべての検索アプリケーションは、クエリ パフォーマンスに影響を与える外部要因の影響を受けます。クエリの応答時間を変動させる 2 つの要因として、複雑なクエリおよびネットワークの遅延があります。

### パーティションの追加

ほとんどのサービス アプリケーションには、パーティション数ではなくレプリカ数の増加への固有のニーズがあります。検索を使用するアプリケーションの大半は、最大 1,500 万個のドキュメントをサポートできる単一のパーティションに容易に収まるためです。

このようにドキュメント数を増やす必要がある状況で、パーティションを追加できます。パーティションが 12 の倍数 (具体的には、1、2、3、4、6、12) で追加されることに注意してください。これはシャーディングに起因するものです。インデックスは 12 個のシャードで作成され、これらはすべて 1 個のパーティションまたは均等に 2、3、4、6、12 個のパーティションに格納されます (パーティションごとに 1 つのシャード)。

### レプリカの削除

クエリの数量が多い期間の後 (たとえば、休日のセールの終了後) には、ほとんどの場合、検索クエリの負荷が正常化した時点でレプリカを削除します。

そのためには、レプリカ スライダーを少ない数の方に動かします。それ以外の手順は必要ありません。レプリカの数を減らすと、データ センター内の仮想マシンが解放されます。クエリとデータ インジェストの操作は、以前よりも少ない数の VM で実行されます。最小数は 1 つのレプリカです。

### パーティションの削除

追加の手順を必要としないレプリカの削除に比べ、削除できる量よりも多くのストレージを使用している場合の手順は少し複雑です。たとえば、3 つのパーティションを使用しているソリューションでは、パーティションを 1 つまたは 2 つに減らしたときに、それらのパーティションで格納できる量よりも多くのストレージ領域を使用している場合はエラーが生成されます。その場合は、インデックスまたは関連付けられたインデックス内のドキュメントを削減して領域を解放するか、現在の構成を維持するかを選択できます。

特定のパーティションにどのインデックス シャードが格納されているかを検出する方法はありません。各パーティションで約 25 GB のストレージが提供されるため、現在のパーティションの数で対応できるサイズまでストレージを削減する必要があります。1 つのパーティションに戻す場合は、12 個のシャードをすべて収める必要があります。

将来の計画を立てるには、ストレージをチェックして (「[Get Index Statistics (Azure Search API) (インデックス統計の取得 (Azure Search API))][]」を使用) 実際にどのくらい使用したかを確認します。

<!---->

## サービスを開始または停止する

サービス ダッシュボードのコマンドを使用して、サービスの開始と停止だけでなく削除も行うことができます。

![][3]

サービスの停止または開始により、課金がなくなるわけではありません。課金されないようにするには、サービスを削除する必要があります。サービスを廃止すると、サービスに関連付けられたデータはすべて削除されます。



  [この記事では Azure プレビュー ポータルを使用します]: ../includes/preview-portal-note.md
  [サブスクリプションに検索サービスを追加する]: #sub-1
  [管理タスク]: #sub-2
  [サービス URL]: #sub-3
  [API キーを管理する]: #sub-4
  [リソース使用量を監視する]: #sub-5
  [拡大または縮小]: #sub-6
  [サービスを開始または停止する]: #sub-7
  [Azure プレビュー ポータル]: https://portal.azure.com
  [Limits and constraints (Azure Search API) (制限と制約 (Azure Search API))]: http://msdn.microsoft.com/en-us/library/dn798934.aspx
  [料金の詳細]: http://go.microsoft.com/fwlink/p/?LinkdID=509792
  [Azure プレビュー ポータルで Search を構成する]: ../search-configure/
  []: ./media/search-manage/Azure-Search-Manage-1-URL.png
  [Azure Search REST API (Azure Search REST API)]: http://go.microsoft.com/fwlink/p/?LinkdID=509922
  [1]: ./media/search-manage/Azure-Search-Manage-2-Keys.png
  [Get Index Statistics (Azure Search API) (インデックス統計の取得 (Azure Search API))]: http://msdn.microsoft.com/en-us/library/dn798942.aspx
  [Count Documents (Azure Search) (ドキュメントのカウント (Azure Search))]: http://msdn.microsoft.com/en-us/library/dn798924.aspx
  [2]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png
  [3]: ./media/search-manage/Azure-Search-Manage-4-StartStop.png