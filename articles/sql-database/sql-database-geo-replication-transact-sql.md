<properties
    pageTitle="Transact-SQL を使用して Azure SQL Database の geo レプリケーションを構成する | Microsoft Azure"
    description="Transact-SQL を使用して Azure SQL Database の geo レプリケーションを構成します。"
    services="sql-database"
    documentationCenter=""
    authors="carlrabeler"
    manager="jeffreyg"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management"
    ms.date="11/10/2015"
    ms.author="carlrab"/>

# Transact-SQL を使用して Azure SQL Database の geo レプリケーションを構成する



> [AZURE.SELECTOR]
- [Azure preview portal](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


この記事では、Transact-SQL を使用して Azure SQL Database の geo レプリケーションを構成する方法について説明します。


geo レプリケーションでは、さまざまなデータ センターの場所 (リージョン) に最大 4 つのレプリカ (セカンダリ) データベースを作成できます。セカンダリ データベースは、データ センターで障害が発生した場合やプライマリ データベースに接続できない場合に使用できます。

geo レプリケーションは、Standard データベースと Premium データベースにのみ使用できます。

Standard データベースでは、読み取り不可のセカンダリ データベースを 1 つ置くことができますが、その場合は推奨されたリージョンを使用する必要があります。Premium データベースでは、利用可能な任意のリージョンに、最大 4 つの読み取り可能なセカンダリ データベースを置くことができます。


geo レプリケーションを構成するには、次のものが必要です。

- Azure サブスクリプション - Azure サブスクリプションをお持ちでない場合、このページの上部の **[無料試用版]** をクリックしてからこの記事に戻り、最後までお読みください。
- Azure SQL Database 論理サーバー <MyLocalServer> と SQL データベース <MyDB> - 別の地理的リージョンにレプリケートするプライマリ データベースです。
- 複数の Azure SQL Database 論理サーバー <MySecondaryServer(n)> - geo レプリケーション セカンダリ データベースの作成先であるパートナー サーバーとなる論理サーバーです。
- プライマリ上の DBManager であるログイン。geo レプリケートするローカル データベースの db\_ownership を所有し、geo レプリケーションを構成するパートナー サーバー上の DBManager になります。
- 最新バージョンの SQL Server Management Studio - 最新バージョンの SQL Server Management Studio (SSMS) を入手するには、[SQL Server Management Studio のダウンロード](https://msdn.microsoft.com/library/mt238290.aspx) ページに移動します。SQL Server Management Studio を使用した Azure SQL Database の論理サーバーとデータベースの管理については、「[SQL Server Management Studio を使用した Azure SQL Database の管理](sql-database-manage-azure-ssms.md)」を参照してください。



## SQL Database 論理サーバーに接続する

SQL Database に接続するには、Azure 上のサーバー名を把握しておくほか、Management Studio を使用して接続する際の接続元クライアントの IP アドレスについてファイアウォール規則を作成しておく必要があります。この情報を入手し、このタスクを実行するには、ポータルへのサインインが必要になる場合があります。

1.  [Azure 管理ポータル](http://manage.windowsazure.com)にサインインします。

2.  左のウィンドウで、**[SQL データベース]** をクリックします。

3.  SQL データベースのホーム ページで、ページ最上部の **[サーバー]** をクリックし、自分のサブスクリプションに関連付けられている全サーバーの一覧を表示します。接続先のサーバーの名前を探し、その名前をクリップボードにコピーします。

	次に、SQL データベース ファイアウォールを構成して、ローカル コンピューターからの接続を許可します。それには、ローカル コンピューターの IP アドレスをファイアウォールの例外一覧に追加します。

1.  SQL データベースのホーム ページで **[サーバー]** をクリックし、接続するサーバーをクリックします。

2.  ページの上部にある **[構成]** をクリックします。

3.  [現在のクライアント IP アドレス] の IP アドレスをコピーします。

4.  [構成] ページの **[使用できる IP アドレス]** には、ルールの名前、IP アドレス範囲の開始値と終了値を指定できる 3 つのボックスがあります。ルール名としては、コンピューター名などを入力します。範囲の開始値および終了値としてコンピューターの IP アドレスを両方のボックスに入力し、表示されるチェック ボックスをオンにします。

	ルール名は一意である必要があります。開発コンピューターで設定している場合は、[IP 範囲の開始] ボックスと [IP 範囲の終了] ボックスの両方に同じ IP アドレスを入力してもかまいません。それ以外の場合は、組織の他のコンピューターからの接続に対応するために、より広い範囲の IP アドレスの入力が必要になる可能性があります。

5. ページの下部にある **[保存]** をクリックします。

    **注:** ファイアウォール設定の変更が反映されるまで 5 分程度かかる場合があります。

	これで Management Studio を使用して SQL データベースに接続する準備ができました。

1.  タスク バーで、**[スタート]** をクリックし、**[すべてのプログラム]**、**[Microsoft SQL Server 2014]** の順にポイントして、**[SQL Server Management Studio]** をクリックします。

2.  **[サーバーへの接続]** で、完全修飾サーバー名として <MyLocalServer>.database.windows.net と指定します。Azure では、サーバー名は英数字から成る自動生成文字列です。

3.  **[SQL Server 認証]** を選択します。

4.  **[ログイン]** ボックスに、サーバーの作成時にポータルで指定した SQL Server 管理者ログインを入力します。

5.  **[パスワード]** ボックスに、サーバーの作成時にポータルで指定したパスワードを入力します。

8.  **[接続]** をクリックして接続を確立します。



## セカンダリ データベースを追加する

**ALTER DATABASE** ステートメントを使用して、geo レプリケートされたセカンダリ データベースをパートナー サーバー上に作成できます。このステートメントは、レプリケートされるデータベースが含まれているサーバーの master データベースに対して実行します。geo レプリケートされたデータベース ("プライマリ データベース") は、レプリケートされているデータベースと同じ名前になります。また、既定では、サービス レベルがプライマリ データベースと同じになります。セカンダリ データベースは、読み取り可能または読み取り不可とすることができるほか、単一データベースまたはエラスティック データベースとすることができます。詳細については、[ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) に関するページと[サービス階層](https://azure.microsoft.com/documentation/articles/sql-database-service-tiers/)に関するページを参照してください。セカンダリ データベースが作成され、シード処理が行われると、データはプライマリ データベースから非同期にレプリケートを開始します。以下の手順では、Management Studio を使用して geo レプリケーションを構成する方法について説明します。単一データベースまたはエラスティック データベースとして、読み取り不可のセカンダリと読み取り可能なセカンダリを作成する手順について説明します。

> [AZURE.NOTE](たとえば、geo レプリケーション リレーションシップが現在存在しているか、以前に存在していたことが原因で) 指定したパートナー サーバー上にセカンダリ データベースが存在する場合、コマンドは失敗します。


### 読み取り不可のセカンダリ (単一データベース) の追加

読み取り不可のセカンダリを単一データベースとして作成するには、次の手順に従います。

1. Management Studio で Azure SQL Database 論理サーバーに接続します。

2. [データベース] フォルダーを開き、**[システム データベース]** フォルダーを展開し、**[master]** を右クリックして、**[新しいクエリ]** をクリックします。

3. 次の **ALTER DATABASE** ステートメントを使用して、ローカル データベースを、<MySecondaryServer1> 上に読み取り不可のセカンダリ データベースを備えた geo レプリケーション プライマリに変更します。

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer1> WITH (ALLOW_CONNECTIONS = NO);

4. **[実行]** をクリックしてクエリを実行します。


### 読み取り可能なセカンダリ (単一データベース) の追加
読み取り可能なセカンダリを単一データベースとして作成するには、次の手順に従います。

1. Management Studio で Azure SQL Database 論理サーバーに接続します。

2. [データベース] フォルダーを開き、**[システム データベース]** フォルダーを展開し、**[master]** を右クリックして、**[新しいクエリ]** をクリックします。

3. 次の **ALTER DATABASE** ステートメントを使用して、ローカル データベースを、セカンダリ サーバー上に読み取り可能なセカンダリ データベースを持つ geo レプリケーション プライマリにします。

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);

4. **[実行]** をクリックしてクエリを実行します。



### 読み取り不可のセカンダリ (エラスティック データベース) の追加
読み取り不可のセカンダリをエラスティック データベースとして作成するには、次の手順に従います。

1. Management Studio で Azure SQL Database 論理サーバーに接続します。

2. [データベース] フォルダーを開き、**[システム データベース]** フォルダーを展開し、**[master]** を右クリックして、**[新しいクエリ]** をクリックします。

3. 次の **ALTER DATABASE** ステートメントを使用して、ローカル データベースを、エラスティック プール内のセカンダリ サーバー上に読み取り不可のセカンダリ データベースを持つ geo レプリケーション プライマリにします。

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer3> WITH (ALLOW_CONNECTIONS = NO)
           , ELASTIC_POOL (name = MyElasticPool1);

4. **[実行]** をクリックしてクエリを実行します。



### 読み取り可能なセカンダリ (エラスティック データベース) の追加
読み取り可能なセカンダリをエラスティック データベースとして作成するには、次の手順に従います。

1. Management Studio で Azure SQL Database 論理サーバーに接続します。

2. [データベース] フォルダーを開き、**[システム データベース]** フォルダーを展開し、**[master]** を右クリックして、**[新しいクエリ]** をクリックします。

3. 次の **ALTER DATABASE** ステートメントを使用して、ローカル データベースを、エラスティック プール内のセカンダリ サーバー上に読み取り可能なセカンダリ データベースを持つ geo レプリケーション プライマリにします。

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = NO)
           , ELASTIC_POOL (name = MyElasticPool2);

4. **[実行]** をクリックしてクエリを実行します。



## セカンダリ データベースを削除する

**ALTER DATABASE** を使用して、セカンダリ データベースとそのプライマリの間のレプリケーション パートナーシップを完全に終了させることができます。このステートメントは、プライマリ データベースが存在する master データベースに対して実行されます。リレーションシップの終了後、セカンダリ データベースは通常の読み取り/書き込みデータベースになります。セカンダリ データベースへの接続が切断された場合、コマンドは成功します。ただし、接続が復元されると、セカンダリ データベースは読み取り/書き込みデータベースになります。詳細については、[ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) に関するページと[サービス階層](https://azure.microsoft.com/documentation/articles/sql-database-service-tiers/)に関するページを参照してください。

geo レプリケートされたセカンダリを geo レプリケーション パートナーシップから削除するには、次の手順に従います。

1. Management Studio で Azure SQL Database 論理サーバーに接続します。

2. [データベース] フォルダーを開き、**[システム データベース]** フォルダーを展開し、**[master]** を右クリックして、**[新しいクエリ]** をクリックします。

3. 次の **ALTER DATABASE** ステートメントを使用して、geo レプリケートされたセカンダリを削除します。

        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;

4. **[実行]** をクリックしてクエリを実行します。


## セカンダリ データベースを昇格させて新しいプライマリにする、計画されたフェールオーバーを開始する

**ALTER DATABASE** ステートメントを使用すると、計画的にセカンダリ データベースを昇格させて新しいプライマリ データベースにすることができます。これにより、既存のプライマリはセカンダリに降格します。このステートメントは、昇格の対象となる geo レプリケートされたセカンダリ データベースが存在する Azure SQL Database 論理サーバー上の master データベースに対して実行されます。この機能は、DR ドリル時など、計画されたフェールオーバー用に設計されており、プライマリ データベースが使用できることが必要条件となります。

コマンドによって、次のワークフローが実行されます。

1. 一時的にレプリケーションを同期モードに切り替えます。未処理のトランザクションはすべてセカンダリにフラッシュされ、新しいトランザクションはすべてブロックされます。

2. geo レプリケーション パートナーシップにおける 2 つのデータベースのロールを切り替えます。

このシーケンスによってデータが失われることはありません。ロールの切り替え中に、わずかですが両方のデータベースが使用できなくなる期間 (0 ～ 25 秒程度) が生じます。通常の状況では、操作全体が完了するのに 1 分かかりません。詳細については、[ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) に関するページと[サービス階層](https://azure.microsoft.com/documentation/articles/sql-database-service-tiers/)に関するページを参照してください。


> [AZURE.NOTE]コマンド発行時にプライマリ データベースが使用できない場合、コマンドは失敗し、プライマリ サーバーが使用できないことを示すエラー メッセージが表示されます。まれに、操作が完了しないで、スタックしたような状態になる場合があります。この場合、ユーザーは強制フェールオーバー コマンドを実行できますが、データが失われることを容認する必要があります。

計画されたフェールオーバーを開始するには、次の手順に従います。

1. Management Studio で、geo レプリケートされたセカンダリ データベースが存在する Azure SQL Database 論理サーバーに接続します。

2. [データベース] フォルダーを開き、**[システム データベース]** フォルダーを展開し、**[master]** を右クリックして、**[新しいクエリ]** をクリックします。

3. 次の **ALTER DATABASE** ステートメントを使用して、geo レプリケートされたデータベースを、<ElasticPool2> 内の <MySecondaryServer4> 上に読み取り可能なセカンダリ データベースを持つ geo レプリケーション プライマリにします。

        ALTER DATABASE <MyDB> FAILOVER;

4. **[実行]** をクリックしてクエリを実行します。



## プライマリ データベースからセカンダリ データベースへの計画外のフェールオーバーを開始する

**ALTER DATABASE** ステートメントを使用すると、計画外にセカンダリ データベースを昇格させて新しいプライマリ データベースにすることができます。これにより、プライマリ データベースが使用できなくなると、既存のプライマリが強制的にセカンダリに降格されます。このステートメントは、昇格の対象となる geo レプリケートされたセカンダリ データベースが存在する Azure SQL Database 論理サーバー上の master データベースに対して実行されます。

この機能は、データベースの可用性を復元することが重要で、多少のデータ損失は許容されるような障害復旧を目的として設計されています。強制フェールオーバーが呼び出されると、指定したセカンダリ データベースはすぐにプライマリ データベースになり、書き込みトランザクションの受け入れを開始します。元のプライマリ データベースがこの新しいプライマリ データベースと再接続できるようになるとすぐに、元のプライマリ データベース上で増分バックアップが行われます。古いプライマリ データベースは新しいプライマリ データベースのセカンダリ データベースとなり、それ以降、新しいプライマリの単なる同期用レプリカになります。

ただし、セカンダリ データベースではポイントインタイム リストアがサポートされていません。そのため、古いプライマリ データベースにコミットされたデータのうち、強制フェールオーバーが発生する前に新しいプライマリ データベースにレプリケートされなかったデータを復元しようとする場合、ユーザーは、この失われたデータを復元するようサポートに問い合わせる必要があります。

> [AZURE.NOTE]プライマリとセカンダリの両方がオンラインのときにコマンドを発行すると、古いプライマリは新しいセカンダリになりますが、データの同期は行われません。そのため、一部のデータは失われる可能性があります。


プライマリ データベースに複数のセカンダリ データベースがある場合、コマンドは、そのコマンドが実行されたセカンダリ サーバーでのみ成功します。ただし、その他のセカンダリ データベースは、強制フェールオーバーが発生したことを認識しません。ユーザーは、"セカンダリを削除する" API を使用してこの構成を手動で修復した後、これら残りのセカンダリ データベースで geo レプリケーションを再構成する必要があります。

geo レプリケートされたセカンダリを geo レプリケーション パートナーシップから強制的に削除するには、次の手順に従います。

1. Management Studio で、geo レプリケートされたセカンダリ データベースが存在する Azure SQL Database 論理サーバーに接続します。

2. [データベース] フォルダーを開き、**[システム データベース]** フォルダーを展開し、**[master]** を右クリックして、**[新しいクエリ]** をクリックします。

3. 次の **ALTER DATABASE** ステートメントを使用して、<MyLocalDB> を、<ElasticPool2> 内の <MySecondaryServer4> 上に読み取り可能なセカンダリ データベースを持つ geo レプリケーション プライマリにします。

        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;

4. **[実行]** をクリックしてクエリを実行します。

## geo レプリケーションの構成と正常性を監視する

監視タスクには、geo レプリケーションの構成の監視と、データ レプリケーションの正常性の監視が含まれます。master データベースの **sys.dm\_geo\_replication\_links** 動的管理ビューを使用すると、Azure SQL Database 論理サーバー上の各データベースについて、既存のレプリケーション リンクすべてに関する情報が返されます。このビューでは、プライマリ データベースとセカンダリ データベースの間の各レプリケーション リンクについて 1 行表示されます。**sys.dm\_replication\_status** 動的管理ビューを使用すると、レプリケーション リンクに現在関係している Azure SQL Database ごとに行が返されます。これには、プライマリ データベースとセカンダリ データベースの両方が含まれます。特定のプライマリ データベースについて複数の連続レプリケーション リンクが存在する場合、このテーブルには、各リレーションシップについて 1 行が含まれます。このビューは、すべてのデータベース (論理 master データベースを含む) で作成されます。ただし、論理 master データベースでこのビューにクエリを実行しても、空のセットが返されます。**sys.dm\_operation\_status** 動的管理ビューを使用すると、レプリケーション リンクの状態など、すべてのデータベース操作の状態を表示できます。詳細については、[sys.dm\_geo\_replication\_links (Azure SQL Database)](https://msdn.microsoft.com/library/mt575501.aspx)、[sys.dm\_geo\_replication\_link\_status (Azure SQL Database)](https://msdn.microsoft.com/library/mt575504.aspx)、[sys.dm\_operation\_status (Azure SQL Database)](https://msdn.microsoft.com/library/dn270022.aspx) に関するページを参照してください。

geo レプリケーション パートナーシップを監視するには、次の手順に従います。

1. Management Studio で Azure SQL Database 論理サーバーに接続します。

2. [データベース] フォルダーを開き、**[システム データベース]** フォルダーを展開し、**[master]** を右クリックして、**[新しいクエリ]** をクリックします。

3. 次のステートメントを使用して、geo レプリケーション リンクと共にすべてのデータベースを表示します。

        SELECT database_id,start_date, partner_server, partner_database,  replication_state, is_target_role, is_non_redable_secondary FROM sys.geo_replication_links;

4. **[実行]** をクリックしてクエリを実行します。
5. [データベース] フォルダーを開き、**[システム データベース]** フォルダーを展開し、**[MyDB]** を右クリックして、**[新しいクエリ]** をクリックします。
6. 次のステートメントを使用して、MyDB のセカンダリ データベースのレプリケーション ラグと前回のレプリケーション時刻を表示します。

        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_ replication_status

7. **[実行]** をクリックしてクエリを実行します。
8. 次のステートメントを使用して、MyDB データベースに関連付けられた最近の geo レプリケーション操作を表示します。

        SELECT * FROM sys.dm_ operation_status where major_resource_is = 'MyDB'
        ORDER BY start_time DESC

9. **[実行]** をクリックしてクエリを実行します。


## 次のステップ

- [障害復旧訓練](sql-database-disaster-recovery-drills.md)


## その他のリソース

- [新しい geo レプリケーション機能に関するスポットライト](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication)
- [geo レプリケーションを使用してビジネス継続性を実現するクラウド アプリケーションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=Nov15_HO3-->