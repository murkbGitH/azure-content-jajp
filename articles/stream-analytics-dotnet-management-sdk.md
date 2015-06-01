﻿<properties 
	pageTitle="Azure Stream Analytics 管理用 .NET SDK の使用 | Azure" 
	description="Stream Analytics 管理用 .NET SDK を使用する方法を説明します" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="03/05/2015" 
	ms.author="jgao"/>


# Azure Stream Analytics 管理用 .NET SDK の使用

[これはプレリリース資料であり、今後のリリースで変更されることがあります。]

Azure Stream Analytics は、待機時間の短縮、高可用性、クラウド内のデータのストリーミング データに対する拡張性の高い複雑なイベント処理を実現する、十分に管理されたサービスです。プレビュー リリースでは、Stream Analytics により、ユーザーはデータ ストリームを分析するためにストリーミングのジョブをセットアップでき、ほぼリアルタイムで分析を実行できます。  

この記事では、Azure Stream Analytics 管理用 .NET SDK を使用する方法を示します。


## 前提条件
この記事を読み始める前に、次の項目を用意する必要があります。

- Visual Studio 2012 または 2013 のインストール。
- [Azure .NET SDK](http://azure.microsoft.com/downloads/) のダウンロードとインストール。 
- サブスクリプションに Azure リソース グループを作成する。次に、サンプルの Azure PowerShell スクリプトを示します。Azure PowerShell の詳細については、[Azure PowerShell のインストールおよび構成に関するページ](install-configure-powershell.md)を参照してください。  


		# Configure the Azure PowerShell session to access Azure Resource Manager
		Switch-AzureMode AzureResourceManager

		# Log in to your Azure account
		Add-AzureAccount

		# Select the Azure subscription you want to use to create the resource group
		Select-AzureSubscription -SubscriptionName <subscription name>

		# Create an Azure resource group	
		New-AzureResourceGroup -Name <YOUR RESORUCE GROUP NAME> -Location <LOCATION>


-	使用する入力ソースと出力ターゲットを設定します。この記事で使用するサンプルの入力または出力の設定方法については、「[Azure Stream Analytics の使用](stream-analytics-get-started.md)」を参照してください。


## プロジェクトの設定

1. Visual Studio C# .NET コンソール アプリケーションを作成します。
2. パッケージ マネージャー コンソールで、次のコマンドを実行して NuGet パッケージをインストールします。1 つ目は、Azure Stream Analytics 管理用 .NET SDK です。2 つ目は、認証で使用する Azure Active Directory クライアントです。

		Install-Package Microsoft.Azure.Management.StreamAnalytics -Pre
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

4. 次の **appSettings** セクションを App.config ファイルに追加します。

		<appSettings>
		  <!--CSM Prod related values-->
		  <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		  <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		  <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		  <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		  <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		  <add key="SubscriptionId" value="<YOUR AZURE SUBSCRIPTION>" />
		  <add key="ActiveDirectoryTenantId" value="<YOU TENANT ID>" />
		</appSettings>


	**SubscriptionId** および **ActiveDirectoryTenantId** の値を Azure サブスクリプションとテナント ID に置き換えます。次の Azure PowerShell コマンドレットを実行すると、これらの値を取得できます。

		Get-AzureAccount

5. 次の **using** ステートメントをプロジェクト内のソース ファイル (Program.cs) に追加します。

		using System;
		using System.Configuration;
		using System.Threading;
		using Microsoft.Azure;
		using Microsoft.Azure.Management.StreamAnalytics;
		using Microsoft.Azure.Management.StreamAnalytics.Models;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;

6.	次の認証ヘルパー メソッドを追加します。

		public static string GetAuthorizationHeader()
		{
		    AuthenticationResult result = null;
		    var thread = new Thread(() =>
		    {
		        try
		        {
		            var context = new AuthenticationContext(
						ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
						ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
		
		            result = context.AcquireToken(
		                resource:ConfigurationManager.AppSettings["WindowsManagementUri"],
		                clientId:ConfigurationManager.AppSettings["AsaClientId"],
		                redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
		                promptBehavior:PromptBehavior.Always);
		        }
		        catch (Exception threadEx)
		        {
		            Console.WriteLine(threadEx.Message);
		        }
		    });
		
		    thread.SetApartmentState(ApartmentState.STA);
		    thread.Name = "AcquireTokenThread";
		    thread.Start();
		    thread.Join();
		
		    if (result != null)
		    {
		        return result.AccessToken;
		    }
		
		    throw new InvalidOperationException("Failed to acquire token");
		}  


## Stream Analytics の管理クライアントの作成

**StreamAnalyticsManagementClient** オブジェクトを使用すると、入力、出力、変換などのジョブおよびジョブ コンポーネントを管理することができます。 

**Main** メソッドの先頭に次のコードを追加します。 

	string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
	string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
	string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
	string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
	string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";
	
	// Get authentication token
	TokenCloudCredentials aadTokenCredentials =
	    new TokenCloudCredentials(
	        ConfigurationManager.AppSettings["SubscriptionId"],
	        GetAuthorizationHeader());
	
	// Create Stream Analytics management client
	StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

**resourceGroupName** 変数の値は、前の手順で作成または選択したリソース グループの名前と一致している必要があります。

この記事の以降のセクションでは、このコードが **Main** メソッドに追加されていることを前提としています。

## Stream Analytics のジョブの作成

次のコードは、定義したリソース グループの下に Stream Analytics ジョブを作成します。ジョブへの入力、出力、変換の追加は後で行います。

	// Create a Stream Analytics job
	JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
	{
	    Job = new Job()
	    {
	        Name = streamAnalyticsJobName,
	        Location = "<LOCATION>",
	        Properties = new JobProperties()
	        {
	            EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
	            Sku = new Sku()
	            {
	                Name = "Standard"
	            }
	        }
	    }
	};
	
	JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);


## Stream Analytics の入力ソースの作成

次のコードは、BLOB 入力ソースの種類と CSV シリアル化が指定された Stream Analytics の入力ソースを作成します。イベント ハブ入力ソースを作成するには、**BlobStreamInputDataSource** の代わりに **EventHubStreamInputDataSource** を使用します。同様に、入力ソースのシリアル化の種類もカスタマイズすることができます。

	// Create a Stream Analytics input source
	InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
	{
	    Input = new Input()
	    {
	        Name = streamAnalyticsInputName,
	        Properties = new StreamInputProperties()
	        {
	            Serialization = new CsvSerialization
	            {
	                Properties = new CsvSerializationProperties
	                {
	                    Encoding = "UTF8",
	                    FieldDelimiter = ","
	                }
	            },
	            DataSource = new BlobStreamInputDataSource
	            {
	                Properties = new BlobStreamInputDataSourceProperties
	                {
	                    StorageAccounts = new StorageAccount[]
	                    {
	                        new StorageAccount()
	                        {
	                            AccountName = "<YOUR STORAGE ACCOUNT NAME>",
	                            AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
	                        }
	                    },
	                    Container = "samples",
	                    PathPattern = ""
	                }
	            }
	        }
	    }
	};
	
	InputCreateOrUpdateResponse inputCreateResponse = 
		client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);

入力ソースは特定のジョブに関連付けられます。1 つの入力ソースを複数のジョブで使用するには、メソッドを再度呼び出して別のジョブ名を指定する必要があります。


## Stream Analytics の入力ソースのテスト

**TestConnection** メソッドは、Stream Analytics ジョブが入力ソースに接続できるかどうか、および入力ソースの種類別の側面についてテストします。たとえば、前の手順で作成した BLOB 入力ソースの場合、このメソッドでは、ストレージのアカウント名とキーのペアを使用してストレージ アカウントに接続できるかどうか、および、指定されたコンテナーが存在しているかどうかが確認されます。

	// Test input source connection
	DataSourceTestConnectionResponse inputTestResponse = 
		client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## Stream Analytics の出力ターゲットの作成

出力ターゲットの作成は、Stream Analytics の入力ソースの作成とよく似ています。入力ソースと同様、出力ターゲットも特定のジョブに関連付けられます。1 つの出力ターゲットを複数のジョブで使用するには、メソッドを再度呼び出して別のジョブ名を指定する必要があります。

次のコードで出力ターゲット (Azure SQL データベース) を作成します。出力ターゲットのデータとシリアル化の種類はカスタマイズできます。

	// Create a Stream Analytics output target
	OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
	{
	    Output = new Output()
	    {
	        Name = streamAnalyticsOutputName,
	        Properties = new OutputProperties()
	        {
	            DataSource = new SqlAzureOutputDataSource()
	            {
	                Properties = new SqlAzureOutputDataSourceProperties()
	                {
	                    Server = "<YOUR DATABASE SERVER NAME>",
	                    Database = "<YOUR DATABASE NAME>",
	                    User = "<YOUR DATABASE LOGIN>",
	                    Password = "<YOUR DATABASE LOGIN PASSWORD>",
	                    Table = "<YOUR DATABASE TABLE NAME>"
	                }
	            }
	        }
	    }
	};
	
	OutputCreateOrUpdateResponse outputCreateResponse = 
		client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## Stream Analytics の出力ターゲットのテスト

Stream Analytics の出力ターゲットにも、接続をテストするための **TestConnection** メソッドがあります。

	// Test output target connection
	DataSourceTestConnectionResponse outputTestResponse = 
		client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## Stream Analytics の変換の作成

次のコードでは、"select * from Input" クエリで Stream Analytics の変換を作成し、Stream Analytics ジョブにストリーミング ユニットを 1 つ割り当てるように指定します。ストリーミング ユニットの詳細については、「[Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)」を参照してください。


	// Create a Stream Analytics transformation
	TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
	{
	    Transformation = new Transformation()
	    {
	        Name = streamAnalyticsTransformationName,
	        Properties = new TransformationProperties()
	        {
	            StreamingUnits = 1,
	            Query = "select * from Input"
	        }
	    }
	};
	
	var transformationCreateResp = 
		client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);

入力や出力と同様に変換も、その下に作成された特定の Stream Analytics ジョブに関連付けられます。

## Stream Analytics ジョブの開始
Stream Analytics ジョブとその入力、出力、変換を作成したら、**Start** メソッドを呼び出してジョブを開始できます。 

次のサンプル コードは、カスタムの出力開始時刻が 2012 年 12 月 12 日 12 時 12 分 12 秒 (UTC) に設定された Stream Analytics ジョブを開始します。

	// Start a Stream Analytics job
	JobStartParameters jobStartParameters = new JobStartParameters
	{
	    OutputStartMode = OutputStartMode.CustomTime,
	    OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
	};
	
	LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);



## Stream Analytics ジョブの停止
**Stop** メソッドを呼び出すと、実行中の Stream Analytics ジョブを停止できます。

	// Stop a Stream Analytics job
	LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## Stream Analytics ジョブの削除
**Delete** メソッドを実行すると、ジョブと基になるサブリソース (ジョブの入力、出力、変換など) が削除されます。

	// Delete a Stream Analytics job
	LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);


## 次のステップ

- [Azure Stream Analytics の概要][stream.analytics.introduction]
- [Azure Stream Analytics の使用][stream.analytics.get.started]
- [Azure Stream Analytics ジョブのスケーリング][stream.analytics.scale.jobs]
- [Azure Stream Analytics の制限事項と既知の問題][stream.analytics.limitations]
- [Azure Stream Analytics クエリ言語リファレンス][stream.analytics.query.language.reference]
- [Azure Stream Analytics 管理用 REST API リファレンス][stream.analytics.rest.api.reference] 



<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.limitations]: stream-analytics-limitations.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


<!--HONumber=52-->