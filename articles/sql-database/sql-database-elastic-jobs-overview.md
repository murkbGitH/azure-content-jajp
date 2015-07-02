<properties 
	title="Elastic database jobs overview" 
	pageTitle="エラスティック データベース ジョブの概要" 
	description="エラスティック データベース ジョブ サービスの説明" 
	metaKeywords="azure sql database elastic databases" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="sidneyh" />

# エラスティック データベース ジョブの概要

**エラスティック データベース ジョブ** (プレビュー) を使用すると、[エラスティック データベース プール (プレビュー)](sql-database-elastic-pool.md) のすべてのデータベースに対して T-SQL スクリプト (ジョブ) を実行できます。たとえば、すべてのデータベース内のスキーマを簡単に更新して新しいテーブルを含めることができます。通常、T-SQL ステートメントを実行するか、その他の管理タスクを実行するには、各データベースに個別に接続する必要があります。**エラスティック データベース ジョブ**は、各データベースの実行の状態のログ記録中に、ログインとスクリプトの実行タスクを処理します。プレビューをインストール手順については、「[エラスティック データベース ジョブ コンポーネントのインストール](sql-database-elastic-jobs-service-installation.md)」を参照してください。

![エラスティック データベース ジョブ][1]

## メリット

* エラスティック データベース プールで実行される T-SQL スクリプトの定義、保持、保存
* 自動再試行で、T-SQL スクリプトを大規模に確実に実行する
* ジョブの実行状態の追跡

## シナリオ

* 新しいスキーマのデプロイなどのパフォーマンスの管理タスク
* すべてのデータベース間の共通の製品情報などの参照データを更新する
* インデックスを再構築してクエリのパフォーマンスを向上させる

## ジョブの動作のしくみ

1.	エラスティック データベース ジョブで使用されるサービスをインストールする「[エラスティック データベース ジョブのインストール](sql-database-elastic-jobs-service-installation.md)」をご覧ください。インストールが失敗した場合は、「[アンインストールする方法](sql-database-elastic-jobs-uninstall.md)」をご覧ください。
2.	[各データベースにユーザーを追加して](sql-database-elastic-jobs-add-logins-to-dbs.md)ジョブ実行の エラスティック データベース プールを構成します。
3.	[エラスティック データベース プール] ビューで、**[ジョブの作成]** をクリックします。
4.	ジョブ管理データベース (ジョブのメタデータ ストレージ) のユーザー名とパスワードを入力します (エラスティック データベース ジョブをインストールする際に、ユーザー名とパスワードを作成しています)。
5.	**[ジョブの作成]** ブレードで、ジョブの名前と、ターゲット データベースのユーザー名およびパスワード (スクリプトを正常に実行できるアクセス許可を持つもの) を入力し、T-SQL スクリプトを貼り付けるか入力します。
6.	**[実行]** をクリックすると、ジョブは各データベースに対してスクリプトを実行します。
7.	**[ジョブの管理]** ビューに、実行中のジョブ、実行済みのジョブ、最新の実行状態が表示されます。
8.	任意のジョブをクリックして、ジョブ実行の詳細と各データベースのジョブ実行の状態を表示します。
9.	ジョブが失敗した場合は、その名前をクリックしてエラー ログを表示します。

## コンポーネントと価格 

次のコンポーネントは、連携して管理ジョブのアドホック実行を可能にする Azure クラウド サービスを作成します。コンポーネントはサブスクリプションで、セットアップ時に自動的にインストールされ、構成されます。これらはすべて同じ自動生成された名前を持っているため、サービスを識別できます。名前は一意であり、プレフィックス "edj" とその後に続くランダムに生成された 21 文字で構成されます。

* **Azure Cloud Service**: エラスティック データベース ジョブ (プレビュー) は、顧客によってホストされる AzureCloud Service として配信され、要求されたタスクを実行します。ポータルから、サービスは Microsoft Azure サブスクリプションにデプロイされ、ホストされます。デプロイされる既定のサービスは、高可用性のための 2 つの worker ロールの最小値で実行されます。各 worker ロール (ElasticDatabaseJobWorker) の既定のサイズは A0 インスタンスで実行されます。料金については、「[Cloud Services 料金](http://azure.microsoft.com/pricing/details/cloud-services/)」をご覧ください。 
* **Azure SQL Database**: このサービスは、**管理データベース**と呼ばれる Azure SQL Database を使用してすべてのジョブ メタデータを保持します。既定のサービス層は、S0 です。詳細については、「[SQL Database の料金](http://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。
* **Azure Service Bus**: Azure Service Bus は、Azure Cloud Service 内の作業を調整します。「[Service Bus 料金](http://azure.microsoft.com/pricing/details/service-bus/)」をご覧ください。
* **Azure Storage**: Azure ストレージ アカウントは問題にさらにデバッグが必要な場合に、診断出力のログ記録を格納するために使用されます ([Azure 診断](../cloud-services-dotnet-diagnostics.md)の一般的な方法)。料金については、「[Azure Storage の料金](http://azure.microsoft.com/pricing/details/storage/)」をご覧ください。

## 次のステップ
[コンポーネントをインストールし](sql-database-elastic-jobs-service-installation.md)、[プール内の各データベースにログインを作成し、追加します](sql-database-elastic-jobs-add-logins-to-dbs.md)。ジョブの作成と管理の詳細については、「[エラスティック データベース ジョブの作成と管理](sql-database-elastic-jobs-create-and-manage.md)」を参照してください。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=58_postMigration-->