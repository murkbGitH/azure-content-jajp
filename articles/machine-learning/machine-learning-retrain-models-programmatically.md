<properties 
	pageTitle="プログラムによる Machine Learning のモデルの再トレーニング |Azure" 
	description="Azure Machine Learning でプログラムによるモデルの再トレーニングをしてWeb サービスを更新し、新しくトレーニングを行ったモデルを使用する方法について説明します。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="raymondlaghaeian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services" 
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/22/2015"
	ms.author="raymondl;garye"/>


#プログラムによる Machine Learning のモデルの再トレーニング  
 
Azure Machine Learning における Machine Learning のモデル運用プロセスの一環として、モデルのトレーニングと保存を行う必要があります。その後モデルはスコア付けの Web サービスを作成するのに使用されます。これによって、Web サイト、ダッシュボード、モバイル アプリでこの Web サービスを使用できます。

多くの場合、新しいデータを使って最初の手順で作成したモデルで再トレーニングを行う必要があります。これはこれまで Azure ML UI によってのみできることでしたが、プログラムによる再トレーニング API 機能を導入することで、モデルの再トレーニングと Web サービスの更新ができるようになり、プログラムによる再トレーニング API を使って、新しくトレーニングを行ったモデルを使用できるようになりました。

このドキュメントでは上記のプロセスについて説明し、再トレーニング API を使用する方法を示します。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 
 

##再トレーニングを行う理由: 問題の定義  
ML のトレーニング プロセスの一環として、モデルのトレーニングではデータのセットを使用します。モデルの再トレーニングが必要とされるのは、新しいデータが有効になったシナリオや、API のコンシューマーがモデルのトレーニングを行う独自のデータを持っている場合、データをフィルター処理する必要があり、そのデータのサブセットでモデルのトレーニングを行う場合などです。

これらのシナリオでは、開発者または API のコンシューマーはプログラムによる API を通じて簡単にクライアントを作成でき、独自のデータを使用して 1 回のみまたは定期的に、モデルの再トレーニングを行うことができます。その後再トレーニングの結果を評価し、さらに Web サービス API を更新して、新しくトレーニングを行ったモデルを使用できます。

##再トレーニングを行う方法: エンド ツー エンドのプロセス  
開始にあたり、このプロセスには、Web サービスとして発行されたトレーニング実験とスコア付け実験が必要となります。トレーニング済みのモデルを再トレーニングできるようにするには、トレーニング済みのモデルの出力で、トレーニング実験を Web サービスとして発行する必要があります。これにより、モデルへの API アクセスが有効になり再トレーニングを行うことができます。再トレーニングを設定するプロセスには、次の手順が含まれます。

![][1]
 
図 1: 再トレーニング プロセスの概要

1. *トレーニング実験を作成する*  
	このサンプルでは Azure ML のサンプル実験の "Sample 5: Train, Test, Evaluate for Binary Classification: Adult Dataset" という実験を使います。次の図では、一部のモジュールを省略してサンプルを簡略化しています。また、この実験の名前を "Census Model" にします。

 	![][2]

	これらの要素がそろえば、画面の下部にある [実行] をクリックして、この実験を実行できます。  
2. *スコア付け実験を作成して Web サービスとして発行する*  
	
	![][3]

	実験の実行が完了したら、[スコア付け実験の作成] をクリックします。これによりスコア付け実験が作成され、モデルはトレーニング済みのモデルとして保存され、次に示すように "Web サービス入力" と "Web サービス出力" のモジュールが追加されます。次に、[実行] をクリックします。

	実験の実行が完了した後で [Publish Web Service] をクリックすると、スコア付け実験が Web サービスとして発行され、既定のエンドポイントが作成されます。この Web サービスのトレーニング済みのモデルは更新できます。以下をご覧ください。これで、このエンドポイントの詳細が画面に表示されます。  
3. *トレーニング実験を Web サービスとして発行する*  
	トレーニング済みのモデルの再トレーニングを行うには、上記の手順 1 で Web サービスとして作成したトレーニング実験を発行する必要があります。この Web サービスでは、["トレーニング モデル"][train-model] モジュールにつながっている "Web サービス出力" モジュールが必要です。これにより新しいトレーニング済みのモデルを生成できます。左側のウィンドウの実験アイコンをクリックし、"Census Model" という名前の実験をクリックしてトレーニング実験に戻ります。  

	次に、1 個の "Web サービス入力" モジュールと 2 個の "Web サービス出力" モジュールをワークフローに追加します。"トレーニング モデル" 用の Web サービス出力により、新しいトレーニング済みのモデルを使用できます。"モデルの評価" につながっている出力は、モジュールにおける "モデルの評価" の出力を返します。

	ここまで完了したら [実行] をクリックします。実験の実行が完了すると、ワークフローは次のようになります。
 
	![][4]

	次に [Publish Web Service] ボタン、[はい] の順にクリックします。これでトレーニング実験が Web サービスとして発行され、トレーニング済みのモデルとモデル評価の結果を生成します。Web サービス ダッシュボードが、API キーとバッチ実行用 API ヘルプ ページとともに表示されます。トレーニング済みのモデルの作成に使用できるのはバッチ実行メソッドのみです。  
4. *新しいエンドポイントを追加する*  
	上記の手順 2で発行したスコア付け Web サービスは、既定のエンドポイントで作成されました。既定のエンドポイントは元の実験との同期が維持されるため、既定のエンドポイントにおけるトレーニング済みのモデルを置き換えることはできません。更新できるエンドポイントを作成するには、Azure ポータルにアクセスし [エンドポイントの追加] をクリックします (詳細は[こちら](machine-learning-create-endpoint.md))。	
5. *新しいデータと BES によりモデルの再トレーニングを行う*  
	再トレーニング API を呼び出せるように、Visual Studio で新しい C# コンソール アプリケーションが作成されました ([新規] > [プロジェクト] > [Windows デスクトップ] > [コンソール アプリケーション])。  

	次にトレーニング Web サービスのバッチ実行用 API ヘルプ ページ (上記の手順 3 で作成) から C# のコード サンプルをコピーして、Program.cs ファイルに貼り付けます。このとき、名前空間を変更しないように注意します。

	コード サンプルには、更新が必要なコードの箇所を示すコメントが含まれます。

	1. Azure Storage の情報を提供する。BES 用コード サンプルは、ローカル ドライブのファイル (たとえば "C:\\temp\\CensusIpnput.csv") を Azure Storage にアップロードして、処理し、結果を Azure Storage に戻して書き込みます。  

		これを完了するには、ストレージ アカウント用 Azure の管理ポータルでストレージ アカウント名、キー、コンテナー情報を取得して、このコードを更新する必要があります。また、入力ファイルがコードで指定した場所で有効であることを確認する必要があります。

		トレーニング実験を 2 個の出力で設定したことで、次のように、結果に双方のストレージの場所情報が含まれます。"output1" はトレーニング済みのモデルの出力であり、"output2" は "モデルの評価" の出力です。

		![][6]
 
6. *再トレーニングの結果を評価する*  
	上記の "output2" の出力結果の "BaseLocation"、"RelativeLocaiton"、"SasBlobToken" を組み合わせてブラウザーのアドレス バーに完全な URL を貼り付けると、再トレーニング済みのモデルのパフォーマンス結果を確認できます。

	これにより、新しくトレーニングを行ったモデルが、既存のモデルに代わるのに十分なパフォーマンスを行うかどうかがわかります。

7. *追加のエンドポイントにおけるトレーニング済みのモデルを更新する*  
	このプロセスを完了するには、上記の手順 4 で作成したスコア付けエンドポイントのトレーニング済みのモデルを更新する必要があります。

	上記の BES 出力は "output1" の再トレーニングの結果に関する情報を示しており、再トレーニング済みのモデルの場所情報が含まれます。ここで、このトレーニング済みのモデルを取得し、スコア付けエンドポイント (上記の手順 4 で作成) を更新する必要があります。

	![][7]
  
	この呼び出しの "ApiKey" と "endpointUrl" は、エンドポイントのダッシュボードに表示されます。

	この呼び出しが成功すると、新しいエンドポイントが約 15 秒以内に再トレーニング済みのモデルを使用して開始します。

##概要  
再トレーニング API を使用して、予測 Web サービスのトレーニング済みのモデルを更新することができ、新しいデータによる予測モデルの再トレーニングや、お客様独自のデータによるモデルの再トレーニングを目的としたモデルの配布といったシナリオを可能にします。

[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!--HONumber=54--> 