<properties 
	pageTitle="HDInsight での Hadoop ジョブ用データのアップロード | Microsoft Azure" 
	description="Azure ストレージ エクスプローラー、Azure PowerShell、Hadoop コマンド ライン、または Sqoop を使用して、データを HDInsight にアップロードする方法と HDInsight のデータにアクセスする方法について説明します。" 
	services="storage, hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="jgao"/>



#HDInsight での Hadoop ジョブ用データのアップロード

Azure の HDInsight では、Azure BLOB ストレージ上でフル機能の Hadoop 分散ファイル システム (HDFS) を利用できます。HDFS 拡張機能として、シームレスなエクスペリエンスを顧客に提供するように設計されています。Hadoop エコシステムのすべてのコンポーネントを使用し、管理対象のデータを直接操作できます。Azure BLOB ストレージと HDFS は、データの保管と計算処理のために最適化された別個のファイル システムです。Azure BLOB ストレージを使用するメリットの詳細については、「[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]」をご覧ください。

通常、Azure の HDInsight クラスターは MapReduce ジョブを実行するためにデプロイされ、それらのジョブが完了すると削除されます。計算処理が完了した後もデータを HDFS クラスターに保持しておくと、このデータの格納コストがかさんでしまいます。Azure BLOB ストレージは、可用性と拡張性に優れ、大容量、低コストの共有可能なデータ ストレージ手段であり、HDInsight で処理されるデータも格納できます。BLOB ストレージにデータを格納すれば、データを失うことなく、計算処理で使用した HDInsight クラスターを安全に解放できます。

Azure BLOB ストレージにアクセスするには、[AzCopy][azure-azcopy]、[Azure PowerShell][azure-powershell]、[Azure の .NET 用ストレージ クライアント ライブラリ][azure-storage-client-library]、[Mac、Linux、Windows 用 Azure コマンドライン インターフェイス][xplatcli]、エクスプローラー ツールを使用する方法があります。次に、使用可能なツールの例を示します。

* [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/)
* [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/)
* [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer)
* [Azure 用エクスプローラー](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx)
* [Azure 用エクスプローラー (PRO)](http://www.cloudberrylab.com/microsoft-azure-explorer-pro.aspx)

##前提条件

開始する前に、次の要件にご注意ください。

* Azure HDInsight クラスター。手順については、「[Azure HDInsight の概要][hdinsight-get-started]」または「[HDInsight クラスターのプロビジョニング][hdinsight-provision]」を参照してください。


##<a id="azcopy"></a> AzCopy を使用して Azure BLOB ストレージにデータをアップロードする##

AzCopy は、Azure Storage アカウントでデータの送受信タスクが簡単になるコマンドライン ツールです。スタンドアロン ツールとして使用することも、既存のアプリケーションに組み込むこともできます。[AzCopy をダウンロード][azure-azcopy-download]してください。

AzCopy の構文は次のとおりです。

	AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

詳細については、「[AzCopy - Uploading/Downloading files for Azure Blobs (AzCopy - Azure BLOB のファイルのアップロード/ダウンロードについて)][azure-azcopy]」をご覧ください。

##<a id="powershell"></a>Azure PowerShell を使用して Azure BLOB ストレージにデータをアップロードする##

Azure PowerShell は、Azure のワークロードの展開と管理を制御し自動化するために使用できる強力なスクリプティング環境です。Azure PowerShell を使用して、Azure BLOB ストレージにデータをアップロードして、データを MapReduce ジョブで処理可能にすることができます。ワークステーションを構成して Azure PowerShell を実行する方法については、「[Azure PowerShell のインストールおよび構成](../powershell-install-configure.md)」をご覧ください。

**ローカル ファイルを Azure BLOB ストレージにアップロードするには**

1. Azure PowerShell コンソールを開きます。手順については「[Azure PowerShell のインストールおよび構成](../powershell-install-configure.md)」をご覧ください。
2. 次のスクリプトで最初の 5 つの変数の値を設定します。

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"
		
		$fileName ="<LocalFileName>"
		$blobName = "<BlobName>"
		
		# Get the storage account key
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		
		# Create the storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
		
		# Copy the file from local workstation to the Blob container		
		Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
		
3. スクリプトを Azure PowerShell コンソールに貼り付けて実行します。

Azure BLOB ストレージ コンテナーには、キーと値のペアとしてデータが格納されます。ディレクトリ階層はありません。ただし、キー名でスラッシュ (/) を使用すれば、ファイルがディレクトリ階層に保存されているように見せかけることができます。たとえば、BLOB のキー名を「*input/log1.txt*」とします。この場合、"input" ディレクトリは実際に存在しませんが、キー名でスラッシュ (/) が使用されているのでファイル パスのように見えます。先のスクリプトでは、**$blobname** 変数を設定することで、ファイルにフォルダー構造を与えることができます。たとえば、*$blobname="myfolder/myfile.txt"* のように設定します。

Azure エクスプローラー ツールを使用すると、サイズが 0 バイトのファイルがあることに気付きます。これらのファイルには 2 つの目的があります。

- 空のフォルダーがある場合、フォルダーの存在をマークします。Azure BLOB ストレージでは、foo/bar と呼ばれる BLOB が存在している場合、**foo** と呼ばれるフォルダーも存在すると見なされます。しかし、**foo** と呼ばれる空のフォルダーが必要な場合、それを示すには、この特殊な 0 バイトのファイルを配置するしか方法はありません。
- Hadoop ファイル システムが必要とする特殊なメタデータ、特に、フォルダーのアクセス許可と所有者を保持します。


##<a id="xplatcli"></a>Azure CLI を使用して Azure BLOB ストレージにデータをアップロードする

Mac、Linux、Windows 用 Azure CLI は、Azure サービスを管理できるクロスプラット フォーム ツールです。次の手順を使用して、Azure Blob ストレージにデータをアップロードします。

1. [Mac、Linux、Windows 用の Azure CLI をインストールして構成する](../xplat-cli.md)

2. コマンド プロンプト、bash、その他のシェルを開き、次を使用して、Azure サブスクリプションを認証します。

		azure login

	メッセージが表示されたら、サブスクリプションのユーザー名とパスワードを入力します。

3. サブスクリプションのストレージ アカウントを表示するには、次のコマンドを使用します。

		azure storage account list

4. 作業対象の BLOB を含むストレージ アカウントを選択し、次のコマンドを使用して、このアカウントのキーを取得します。

		azure storage account keys list <storage-account-name>

	これは、**プライマリ** キーと **セカンダリ** キーを返します。次の手順で使用するために、**プライマリ** キーの値をコピーします。

5. 次のコマンドを使用してストレージ アカウント内の blob コンテナーの一覧を取得します。

		azure storage container list -a <storage-account-name> -k <primary-key>

6. 次のコマンドを使用して、blob に対するファイルのアップロードとダウンロードを行います。

	* ファイルをアップロードするには、次を実行します。

			azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>

	* ファイルをダウンロードするには、次を実行します。

			azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [AZURE.NOTE]常に同じストレージ アカウントで処理する場合は、各コマンドにアカウントとキーを指定する代わりに、次の環境変数を設定することができます。
> 
> * **[AZURE_STORAGE_ACCOUNT]**: ストレージ アカウント名
> 
> * **[AZURE_STORAGE_ACCESS_KEY]**: ストレージ アカウント キー名

##<a id="storageexplorer"></a>Azure ストレージ エクスプローラーを使用して BLOB ストレージにデータをアップロードする

*Azure ストレージ エクスプローラー*は、Azure ストレージ内のデータを調べたり、変更したりするときに役立つツールです。このツールは、CodePlex: [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer") から無料でダウンロードできます。

Azure Storage エクスプローラーを使用するには、Azure Storage のアカウント名とアカウント キーを確認しておく必要があります。この情報を取得する方法については、「[ストレージ アカウントの作成、管理、削除][azure-create-storage-account]」の「方法:ストレージ アクセス キーを表示、コピー、再生成する」をご覧ください。

1. Azure ストレージ エクスプローラーを実行します。

	![HDI.AzureStorageExplorer][image-azure-storage-explorer]

2. **[アカウントの追加]** をクリックします。アカウントを Azure ストレージ エクスプローラーに追加した後は、この手順を再び実行する必要はありません。

	![HDI.ASEAddAccount][image-ase-addaccount]

3. **ストレージ アカウント名**と**ストレージ アカウント キー**を入力して、**[ストレージ アカウントの追加]** をクリックします。複数のストレージ アカウントを追加できます。その場合、各アカウントがタブに表示されます。
4. **[ストレージの種類]** で **[BLOB]** をクリックします。

	![HDI.ASEBlob][image-ase-blob]

5. **[コンテナー]** で、HDInsight クラスターに関連付けられているコンテナーの名前をクリックします。HDInsight クラスターを作成するときにコンテナーを指定する必要があります。コンテナーを指定しない場合、そのユーザー専用のクラスターが作成されます。
6. **[BLOB]** で **[アップロード]** をクリックします。
7. アップロードするファイルを指定して、**[開く]** をクリックします。








































































##<a id="commandline"></a> Hadoop コマンド ラインを使用して Azure BLOB ストレージにデータをアップロードする

Hadoop コマンド ラインを使用する前に、リモート デスクトップを使用してクラスターに接続する必要があります。



1. [Azure ポータル][azure-management-portal]にサインインします。
2. **[HDINSIGHT]** をクリックします。デプロイした Hadoop クラスターが一覧表示されます。
3. データのアップロード先となる HDInsight クラスターをクリックします。
4. ページの上部にある **[構成]** をクリックします。
5. まだリモート デスクトップを有効にしていない場合は、**[リモートを有効にする]** をクリックして、指示に従います。そうでない場合は、次の手順に進みます。
4. ページの下部にある **[接続]** をクリックします。
7. **[開く]** をクリックします。
8. ユーザー名とパスワードを入力して **[OK]** をクリックします。
9. **[はい]** をクリックします。
10. デスクトップで **[Hadoop コマンド ライン]** をクリックします。
12. 次のコード例は、HDInsight ヘッド ノード上のローカル ファイル システムから /example/data ディレクトリへ davinci.txt ファイルをコピーします。

		hadoop dfs -copyFromLocal C:\temp\davinci.txt /example/data/davinci.txt

	既定のファイル システムが Azure BLOB ストレージ上にあるため、/example/data/davinci.txt は実際には Azure BLOB ストレージ上に存在します。このファイルは次のように表すこともできます。

		wasb:///example/data/davinci.txt 

	または

		wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

	*wasbs* を使用するときは完全修飾ドメイン名を指定する必要があります。

13. 次のコマンドを使用して、アップロードしたファイルを一覧表示します。

		hadoop dfs -lsr /example/data


##<a id="sqoop"></a> Sqoop を使用して Azure SQL データベースか SQL Server から HDFS にデータをインポートする

Sqoop は、Hadoop とリレーショナル データベース間でデータを転送するためのツールです。このツールを使用して、SQL、MySQL、Oracle などのリレーショナル データベース管理システム (RDBMS) から Hadoop 分散ファイル システム (HDFS) へデータをインポートしたり、MapReduce または Hive を使用して Hadoop のデータを変換し、そのデータを RDBMS へ取り込んだりできます。詳細については、[Sqoop のユーザー ガイド][apache-sqoop-guide]を参照してください。

データをインポートする前に、Azure SQL データベース、データベース アカウント名、アカウント パスワード、データベース名をホストしているサーバー名を確認しておく必要があります。

既定では、Azure SQL データベースは Azure HDinsight のような Azure サービスからの接続を許可します。このファイアウォール設定が無効になっている場合は、Azure ポータルから有効にする必要があります。Azure SQL データベースの作成方法とファイアウォール ルールの構成方法については、「[SQL データベースの作成と構成][sqldatabase-create-configure]」をご覧ください。

次の手順では、Azure PowerShell を使用して Sqoop ジョブを送信します。

**Sqoop と Azure PowerShell を使用してデータを HDInsight にインポートするには**

1. Azure PowerShell コンソールを開きます。手順については「[Azure PowerShell のインストールおよび構成](../powershell-install-configure.md)」をご覧ください。
2. 次のスクリプトで最初の 8 つの変数の値を設定します。

		$subscriptionName = "<AzureSubscriptionName>"
		$clusterName = "<HDInsightClusterName>"
		
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseUserName = "<SQLDatabaseDatabaseName>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$sqlDatabaseDatabaseName = "<SQLDatabaseDatabaseName>"
		$tableName = "<SQLDatabaseTableName>"
		
		$hdfsOutputDir = "<OutputPath>"  # This is the HDFS path for the output file, for example "/lineItemData".
		
		Select-AzureSubscription $subscriptionName		
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseDatabaseName --table $tableName --target-dir $hdfsOutputDir -m 1" 

		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

3. スクリプトを Azure PowerShell コンソールに貼り付けて実行します。Azure BLOB ストレージからデータ ファイルを取得するためのサンプルについては、「[HDInsight の概要][hdinsight-get-started]」をご覧ください。

Sqoop の使用法の詳細については、「[HDInsight と Hive の使用][hdinsight-use-sqoop]」をご覧ください。

## 次のステップ
ここでは、HDInsight にデータを取り込む方法を説明しました。次の記事でデータの分析方法を学習してください。

* [Azure HDInsight の概要][hdinsight-get-started]
* [プログラムによる Hadoop ジョブの送信][hdinsight-submit-jobs]
* [HDInsight での Hive の使用][hdinsight-use-hive]
* [HDInsight での Pig の使用][hdinsight-use-pig]




[azure-management-portal]: https://manage.windowsazure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage-create-storage-account.md
[azure-azcopy-download]: ../storage-use-azcopy.md
[azure-azcopy]: ../storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight/hdinsight-use-sqoop.md

[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-use-hive]: hdinsight/hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight/hdinsight-use-pig.md
[hdinsight-provision]: hdinsight/hdinsight-provision-clusters.md

[sqldatabase-create-configure]: ../sql-database/sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: ../powershell-install-configure.md

[xplatcli]: ../xplat-cli.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png


<!--HONumber=54--> 