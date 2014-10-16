<properties title="Step 2: Upload existing data into an Azure Machine Learning experiment" pageTitle="Step 2: Upload data into a Machine Learning experiment | Azure" description="Step 2: Upload existing public data into Azure Machine Learning Studio" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

これは、チュートリアル「[Azure Machine Learning を使用した予測ソリューションの開発][]」の 2 番目の手順です。

1.  [ML ワークスペースを作成する][]
2.  **既存のデータをアップロードする**
3.  [新しい実験を作成する][]
4.  [モデルをトレーニングして評価する][]
5.  [Web サービスを発行する][]
6.  [Web サービスにアクセスする][]

------------------------------------------------------------------------

# 手順 2.Azure Machine Learning の実験に既存のデータをアップロードする

信用リスクの予測モデルを開発するために、UCI Machine Learning Repository の "UCI Statlog データ セット (ドイツの信用貸付データセット)" を使用します。詳細についてはこちらをご覧ください。
<http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)>

**german.data** という名前のファイルを使用します。このファイルをローカル ハード ドライブにダウンロードします。

このデータセットの行には、信用貸付のための 1,000 人の過去の申請者に関する 20 の変数が含まれています。これらの 20 の変数は、データセットの特徴ベクトルを表し、各信用貸付申請者を識別する特徴を提供します。各行の追加の列は、申請者の信用リスクを表し、700 人の申請者が低リスクとして、300 人が高リスクとして識別されています。

UCI の Web サイトでは、財務情報、信用貸付の履歴、雇用状況、および個人情報を含む特徴ベクトルの属性について説明されています。各申請者について、申請者の信用リスクの高低を示す二項評価が与えられています。

予測分析モデルをトレーニングするために、このデータを使用します。トレーニングが終了すると、このモデルで新しい個人情報を受け入れて信用リスクの高低を予測できるようになります。

興味深い展開を次に示します。データセットの説明から、実際は信用リスクの高い個人が誤って低く分類された場合、低い信用リスクを誤って高く分類したときと比較して、金融機関は 5 倍のコストを強いられることがわかります。実験でこれを考慮するための 1 つの簡単な方法に、高い信用リスクを示す個人のエントリを重複 (5 回) させる方法があります。このとき、このモデルが、高い信用リスクを誤って低く分類すると、重複エントリごとに 1 回、合計 5 回分類を誤ることになります。これにより、トレーニング結果でこのエラーのコストが増加します。

## データセットの形式の変換

元のデータセットは、空白で区切られた形式を使用しています。ML Studio で使用するにはコンマ区切り (CSV) ファイルの方が適しているため、空白をコンマに置き換えてデータセットを変換します。

これは、次の PowerShell コマンドを使用して実行できます。

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

また、次の Unix の sed コマンドを使用して実行することもできます。

    sed 's/ /,/g' german.data > german.csv  

## ML Studio へのデータセットのアップロード

データを CSV 形式に変換したら、それを ML Studio にアップロードする必要があります。

1.  ML Studio で、ウィンドウの下部にある **[+新規]** をクリックします。
2.  **[データセット]** を選択します。
3.  **[ローカル ファイルから]** を選択します。
4.  **[新しいデータセットをアップロードする] ダイアログ**で、**[参照]** をクリックし、作成した **german.csv** ファイルを検索します。
5.  データセットの名前を入力します。この例では、「UCI German Credit Card Data」としています。
6.  データ型として、"ヘッダーなしの汎用 CSV ファイル (.nh.csv)" を選択します。
7.  必要に応じて説明を追加します。
8.  **[OK]** をクリックします。
    ![Upload the dataset][]

これにより、データが、実験で使用できるデータセット モジュールにアップロードされます。

  [Azure Machine Learning を使用した予測ソリューションの開発]: ../machine-learning-walkthrough-develop-predictive-solution/
  [ML ワークスペースを作成する]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [新しい実験を作成する]: ../machine-learning-walkthrough-3-create-new-experiment/
  [モデルをトレーニングして評価する]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
  [Web サービスを発行する]: ../machine-learning-walkthrough-5-publish-web-service/
  [Web サービスにアクセスする]: ../machine-learning-walkthrough-6-access-web-service/
  [Upload the dataset]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png