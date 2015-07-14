<properties
   pageTitle="作業開始: SQL Data Warehouse のプロビジョニング | Microsoft Azure"
   description="これらの手順とガイドラインに従って、SQL Data Warehouse をプロビジョニングします。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/23/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# 作業開始: SQL Data Warehouse のプロビジョニング #

この記事は、Azure での SQL Data Warehouse の迅速なプロビジョニングに役立ちます。このガイドでは、次のタスクを実行します。

1. 新しい SQL Data Warehouse データベースを作成する
2. 新しい論理サーバーを構成する
3. 外部クライアント アクセスを有効にするための Azure ファイアウォール規則を設定する

## Azure 無料評価版 ##
以下のタスクを完了するには、Azure サブスクリプションが必要になります。Azure サブスクリプションにアクセスできない場合は、まず、これを解決する必要があります。

[無料評価版][]を是非ご利用ください。この評価版では、SQL Data Warehouse を含め、Azure のすべてのサービスを試すことができます。


## Azure ポータルにログインする ##

サブスクリプションを入手したら、[Azure ポータル][]にログインできます。今すぐログインしましょう。

次の一連の手順では、新しい論理サーバーを迅速に起動し、新しい SQL Data Warehouse データベースを作成します。

## SQL Data Warehouse サービスを特定する

まず、Azure ポータルで SQL Data Warehouse サービスを特定する必要があります。

Azure ポータルの左下隅には新しいボタンがあります。Azure では新しいサービスを作成するときは、最初にこのボタンを使用します。

- 新しいボタンをクリックします。

### データ + ストレージ

新しいボタンをクリックすると、Azure 内のすべてのサービス カテゴリが開きます。SQL Data Warehouse は "データ + ストレージ" カテゴリに含まれます。

- "データ + ストレージ" をクリックしてドリル ダウンし、Azure で提供されるこのカテゴリのサービスを表示します。

### SQL Data Warehouse

Azure では、データおよびストレージ エンジンが多数提供されていることがわかりますが、この概要ガイドで扱うのは SQLDW です。

- そこで、SQL Data Warehouse を選択します。

## SQL Data Warehouse を構成する

最後に SQL Data Warehouse を構成すれば、プロビジョニング プロセスは完了です。


### データベース名

最初の構成作業として、データベースに名前を付けます。



- このクイック スタートでは、"MySQLDW" という名前をデータベースに付けます。


> [AZURE.NOTE]独自のデータベースを作成するときは、もちろん好きな名前を付けることができます。ただし、この名前は Azure の基本的な名前付け要件に準拠している必要があります。

### パフォーマンス

パフォーマンス オプションは**重要**です。SQL Data Warehouse では、このスライダーによって高い拡張性が実現しています。パフォーマンスは、クラスターを構成するときだけでなく、いつでも調整することができます。スライダーを右側に動かすと、自由に使えるリソースが増えます。そのリソースが不要になったら、すぐにスライダーを元に戻すことができるため、コストを節約できます。SQL Data Warehouse では、オン デマンドでパフォーマンス プロファイルを変更できます。その際、クラスターを再作成したりデータを移動したりする必要はありません。

- スライダーを右に動かして Data Warehouse ユニットが増加する様子を確認します。その後、左に戻して減少する様子を確認します。

- この手順を終了する場合は、必ずスライダーを左に戻してください。新しいデータベースは小さなデータ ウェアハウスなので、たくさんのリソースは必要ありません。評価版をこの先も使用するためにリソースを節約します。

### ソースを選択する

このオプションでは、空のデータベースでの開始を選択します。開始点として、新しいデータベースを選択します。

> [AZURE.NOTE]他のオプションも利用できます。つまり、既存の復元ポイントからデータベースを作成できます。これは復元オプションです。

### 論理サーバー

SQL Data Warehouse データベースは、論理サーバーに常駐します。論理サーバーによって、複数のデータベースにおけるインスタンス レベルでの構成の整合性が実現し、サービスが Azure データ センターに配置されます。

設定する必要があるオプションは、1. サーバー名、2. サーバー管理者名、3. パスワード、4. データ センターの場所、5. Azure サービスがサーバーにアクセスするためのアクセス許可です。

必要に応じて、これらの値を自由に設定してください。サーバー名は一意である必要があります。また、近いデータ センターを選択して、ネットワークの待機時間を短縮することをお勧めします。SQL Data Warehouse には、Azure の他のサービスを利用する強力な機能も含まれます。したがって、Azure サービスのアクセスのチェック ボックスはオンのままにしておくことをお勧めします。

> [AZURE.NOTE]SQL Data Warehouse は V12 サーバーを使用する必要があります。このオプションが [はい] に設定されていることを確認します。Azure SQL Database および SQL Data Warehouse データベースが、論理サーバーを共有することもできます。ただし、これは V12 サーバーである必要があります。

> [AZURE.NOTE]サーバー名、サーバー管理者名、およびパスワードは、どこか安全な場所に記録して保管しておきます。この情報は、SQL Data Warehouse データベースに接続するときに必要です。

### リソース グループ
リソース グループは、Azure リソースのコレクション管理のサポートを目的としたコンテナーです。

このクイック スタートでは、リソース グループの構成は既定値のままでかまいません。

[リソース グループ]に関する詳細情報を参照してください。

### [サブスクリプション]
1 人のユーザーが、1 つ以上の Azure サブスクリプションを持っていることがあります。複数のサブスクリプションがログインに関連付けられている場合は、使用するサブスクリプションを選択できます。

ただし、このガイドでは既定値を使用します。

次は、SQL Data Warehouse を作成しましょう。

## データ ウェアハウスを作成する ##
あとは [作成] をクリックするだけで、データ ウェアハウスを作成できます。

ご利用ありがとうございます。 1 つ目の SQL Data Warehouse データベースの作成は完了しました。

ここで、[Azure ポータル][] ホーム ページに戻ります。SQL Data Warehouse データベースがページに追加されていることに注意してください。


この時点では、誰も SQL Data Warehouse にアクセスできません。すべてのアイテムの安全性を既定で確保するために、クライアントがデータベースにアクセスできるようにはまだ構成されていません。

したがって、プロビジョニング プロセスの最後の手順として、外部アクセス用にサービスを構成します。

## Azure ファイアウォールを構成する ##

Azure ファイアウォールを構成するのが初めての場合は、次の手順を実行します。

1. 左側のナビゲーションで [参照] をクリックします

2. [SQL Server] を選択します

3. ご利用の論理 SQL サーバーを選択します。

4. 設定を選択します

5. [ファイアウォール] をクリックします

6. ファイアウォール規則を作成します

    ここでは 2 つのことを行います。- ファイアウォール規則に名前を付けます- 静的 IP アドレスがない場合は IP 範囲を指定します

    > [AZURE.NOTE]指定する IP アドレス範囲は、外部 IP アドレスまたは公開されている IP アドレスです。外部 IP アドレスは、<a href="http://www.whatismyip.com" target="_blank">www.whatismyip.com</a> などの Web サイトで確認できます。

7. ファイアウォール規則を保存します


ファイアウォールの構成が完了したので、デスクトップから、作成した SQL Data Warehouse への接続を作成できます。

## 次のステップ

これで、SQLDW サービスが正常にプロビジョニングされました。次は、そのサービスを使用します。

したがって、次は 1. SQLDW データベースに[接続してクエリを実行する方法]、2. SQLDW データベースのデータを Azure BLOB ストレージにエクスポートする方法、3. より多くのデータを SQLDW データベースに読み込む方法について学習します。


<!--Image references-->


<!-- Articles -->
[接続してクエリを実行する方法]: ./sql-data-warehouse-get-started-connect-query/
[リソース グループ]: ./azure-preview-portal-using-resource-groups/

<!--External links-->
[無料評価版]: https://azure.microsoft.com/ja-jp/pricing/free-trial/
[Azure ポータル]: https://portal.azure.com/

<!---HONumber=July15_HO1-->