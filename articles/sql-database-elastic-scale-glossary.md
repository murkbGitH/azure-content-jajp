﻿<properties title="Azure Elastic Scale Glossary" pageTitle="Azure Elastic Scale 用語集" description="Explanation of terms used for Elastic Scale feature of Azure SQL Database" metaKeywords="sharding,elastic scale, Azure SQL DB sharding" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Elastic Scale 用語集
Azure SQL Database の Elastic Scale 機能に関する用語の定義を次に示します。

![Elastic Scale terms][1]

**データベース**: Azure SQL データベース。 

**データ依存ルーティング**: アプリケーションが特定のシャーディング キーを持つシャードに接続することを可能にする機能。**マルチシャード クエリ**も参照。

**グローバル シャード マップ**: **シャード セット**内のシャーディング キーとそれに対応するシャードの間のマッピングのセット。GSM は、**シャード マップ マネージャー**に格納されます。**ローカル シャード マップ**も参照。

**リスト シャード マップ**: シャーディング キーが個別にマップされたシャード マップ。**範囲シャード マップ**も参照。   

**ローカル シャード マップ**: ローカル シャード マップは、シャードに格納され、シャードに存在するシャードレットのマッピングを含みます。

**マルチシャード クエリ**: 複数のシャードに対してクエリを発行する機能。結果セットは、UNION ALL セマンティクス ("ファンアウト クエリ" とも呼ばれます) を使用して返されます。**データ依存ルーティング**も参照。

**範囲シャード マップ**: 連続値の複数の範囲に基づくシャード分散戦略を持つシャード マップ。 

**参照テーブル**: シャード化されず、シャード間で複製されるテーブル。 

**シャード**: シャード化されたデータ セットのデータを格納する Azure SQL データベース。 

**シャードの弾力性** (SE): **水平スケーリング**と**垂直スケーリング**の両方を実行する機能。

**シャード化テーブル**: シャード化されたテーブル。つまり、データがシャーディング キー値に基づいて複数のシャードに分散されたテーブル。 

**シャーディング キー**: シャード間でどのようにデータを分散させるかを決める列値。値の型には、int、bigint、varbinary、uniqueidentifier のいずれかを使用できます。 

**シャード セット**: シャード マップ マネージャーの同じシャード マップに属するシャードのコレクション。  

**シャードレット**: シャード上のシャーディング キーの単一の値に関連付けられたデータのすべて。シャードレットは、シャード化テーブルを再分散する際に使用できる最小データ移動単位です。 

**シャード マップ**: シャーディング キーとそれに対応するシャードの間のマッピングのセット。

**シャード マップ マネージャー**: 1 つまたは複数のシャード セットのシャード マップ、シャードの場所、およびマッピングを含む管理オブジェクトおよびデータ ストア。


##動詞

**水平スケーリング**: シャード マップに対するシャードの追加や削除を行ってシャードのコレクションをスケール アウト (またはスケール イン) する操作。

**マージ**: 2 つのシャードから 1 つのシャードにシャードレットを移動し、それに応じてシャード マップを更新する操作。

**シャードレットの移動**: 1 つのシャードレットを異なるシャードに移動する操作。 

**シャード**: 同じ構造を持つデータをシャーディング キーに基づいて複数のデータベースに水平方向に分割する操作。

**分割**: 1 つのシャードから別の (通常は新しい) シャードに複数のシャードレットを移動する操作。シャーディング キーは、分割ポイントとしてユーザーによって指定されます。

**垂直スケーリング**: 個々のシャードのパフォーマンス レベルをスケール アップ (またはダウン) する操作。たとえば、(パフォーマンス上の理由で) Standard から Premium にシャードを変更するケースが該当します。 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]  

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png