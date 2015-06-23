<properties 
	pageTitle="Media Services アカウントの監視 - Azure" 
	description="Azure における  Media Services アカウントの監視の構成方法について説明します。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2015" 
	ms.author="juliako"/>





# <a id="monitormediaservicesaccount"></a>Media Services アカウントの監視方法

この記事は、「[Media Services ビデオ オン デマンド ワークフロー](media-services-video-on-demand-workflow.md)」や「[Media Services のライブ ストリーミングのワークフロー](media-services-live-streaming-workflow.md)」シリーズの一部です。 

Azure Media Services のダッシュボードには、使用状況のメトリックとアカウント情報が表示され、それを Media Services アカウントの管理に利用できます。

監視できるのは、キューに格納されたエンコード ジョブの数、失敗したエンコード タスク、エンコーダーからの入出力データによって表されるアクティブなエンコード ジョブ、Media Services アカウントに関連付けられた BLOB ストレージの使用状況です。さらに、顧客に対してコンテンツをストリーミング配信している場合は、さまざまなストリーミング メトリックも取得できます。データの監視期間は、過去 6 時間、24 時間、または 7 日間から選択できます。
 
>[AZURE.NOTE] Azure 管理ポータルでストレージ データを監視すると、それに応じて追加のコストがかかります。詳細については、「[Storage Analytics と課金](http://go.microsoft.com/fwlink/?LinkId=256667)」をご覧ください。

## <a id="configuremonitoring"></a>方法:Media Services アカウントを監視する

1. [管理ポータル](http://go.microsoft.com/fwlink/?LinkID=256666)で、**[Media Services]** をクリックし、目的の Media Services アカウント名をクリックしてダッシュボードを開きます。 

	![MediaServices_Dashboard][dashboard]

2. エンコード ジョブまたはデータを監視するには、Media Services に対するエンコード ジョブの送信を開始するか、Azure メディア オンデマンド ストリーミングを使用して顧客に対するコンテンツのストリーミング配信を開始します。約 1 時間後に、ダッシュボードに監視データが表示されるようになります。

## <a id="configuringstorage"></a>方法:BLOB ストレージの使用状況を監視する (省略可能)
1. **[概要]** で、**ストレージ アカウント**の名前をクリックします。
2. ストレージ アカウントのページで、**[構成ページ]** リンクをクリックして、BLOB、テーブル、キューの各サービスの **[監視]** 設定まで下図のようにスクロールします。

	>[AZURE.NOTE] BLOB は、Media Services で唯一サポートされるストレージの種類です。

	![StorageOptions][storage_options_scoped]

3. **[監視]** で、BLOB の監視レベルとデータ保有ポリシーを設定します。

-  監視レベルを設定するには、以下のいずれかを選択します。

      **[最低限]** BLOB サービス、テーブル サービス、キュー サービスに集計される、受信/送信、可用性、遅延時間、成功のパーセンテージなどのメトリックを収集します。

      **[詳細]** 最小レベルのメトリックに加えて、Azure ストレージ サービス API のストレージ操作ごとに同じメトリックを収集します。詳細メトリックにより、アプリケーションの操作中に発生する問題を詳しく分析できます。 

      **[オフ]** 監視しません。既存の監視データは、保有期間が経過するまで残ります。

- データ保有ポリシーを設定するには、**[保有期間 (日)]** ボックスに、データを保有する日数を 1 ～ 365 日の範囲で入力します。保有ポリシーを設定しない場合は、「0」(ゼロ) を入力します。保有ポリシーがない場合、監視データを削除する責任はユーザーが負います。使用しない古い分析データがコストをかけずに自動的に削除されるように、アカウントのストレージ分析データをどの程度の期間保持するかに基づいて、データ保有ポリシーを設定することをお勧めします。

4. 監視の構成が完了したら、**[保存]** をクリックします。
Media Services のメトリックと同様に、約 1 時間後に、ダッシュボードに監視データが表示されるようになります。
メトリックは、ストレージ アカウントの $MetricsTransactionsBlob、$MetricsTransactionsTable、$MetricsTransactionsQueue、$MetricsCapacityBlob という名前の 4 つのテーブルに保存されます。詳細については、「[Storage Analytics Metrics について](http://go.microsoft.com/fwlink/?LinkId=256668)」をご覧ください。


<!-- Images -->
[dashboard]: ./media/media-services-monitor-services-account/media-services-dashboard.png
[storage_options_scoped]: ./media/media-services-monitor-services-account/storagemonitoringoptions_scoped.png



<!--HONumber=52--> 