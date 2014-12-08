<properties title="Hive を HDInsight と共に使用したセンサー データの分析" pageTitle="Hive と Microsoft Azure HDInsight (Hadoop) を使用したセンサー データの分析" description="Hive と Excel を使用して HDInsight (Hadoop) でセンサー データを分析し視覚化する方法について説明します。" metaKeywords="Azure hdinsight hive, Azure hdinsight hive sensor, azure hadoop hive, azure hadoop sensor, azure hadoop excel, azure hdinsight excel" services="hdinsight" solutions="" documentationCenter="big-data" authors="larryfr" videoId="" scriptId="" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/30/2014" ms.author="larryfr" />

# Hive を HDInsight と共に使用したセンサー データの分析

Hive を HDInsight (Hadoop) と共に使用してセンサー データを分析してから、Microsoft Excel の Power View を使用してデータを視覚化する方法について説明します。

このサンプルでは、Hive を使用して、暖房、換気、および空調 (HVAC) システムによって生成された履歴データを処理し、設定した温度を正確に維持できないシステムを識別します。学習内容:

-   値をコンマで区切った (CSV) 形式で格納されたデータを照会するための HIVE テーブルの作成
-   データを分析するための HIVE クエリの作成
-   分析したデータを取得するための、Microsoft Excel を使用した HDInsight への接続 (ODBC 接続を使用)
-   Power View を使用したデータの視覚化

![A diagram of the solution architecture][A diagram of the solution architecture]

## 前提条件:

-   HDInsight (Hadoop) クラスター - クラスター作成の詳細については、「[HDInsight での Hadoop クラスターのプロビジョニング][HDInsight での Hadoop クラスターのプロビジョニング]」を参照してください。

-   Microsoft Excel 2013

    > [WACOM.NOTE] Microsoft Excel は、[Power View][Power View] を使用したデータ視覚化のために使用します。Power View は現在、Windows でのみ利用できます。

-   [Microsoft Hive ODBC ドライバー][Microsoft Hive ODBC ドライバー]

## サンプルを実行するには

1.  Azure の管理ポータルから、サンプルの実行に使用するクラスターを選択し、下部にある **[クエリ コンソール]** をクリックします。または、次に示す URL を使用してクエリ コンソールを直接開くこともできます。

        https://<clustername>.azurehdinsight.net

    入力を要求されたら、このクラスターをプロビジョニングするときに使用した管理者ユーザー名とパスワードを使用して認証を実行します。

2.  表示された Web ページで **[概要ギャラリー]** タブをクリックし、**[サンプル]** カテゴリにある **[Web サイト ログ分析]** サンプルを選択します。

3.  Web ページに記載されている手順に従って、サンプルを完了します。

  [A diagram of the solution architecture]: ./media/hdinsight-use-hive-sensor-data-analysis/hvac-architecture.png
  [HDInsight での Hadoop クラスターのプロビジョニング]: /ja-jp/documentation/articles/hdinsight-provision-clusters/
  [Power View]: https://support.office.com/ja-jp/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=ja-jp&rs=ja-jp&ad=US
  [Microsoft Hive ODBC ドライバー]: http://www.microsoft.com/ja-jp/download/details.aspx?id=40886