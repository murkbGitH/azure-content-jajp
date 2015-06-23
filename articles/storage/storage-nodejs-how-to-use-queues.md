<properties 
	pageTitle="Node.js から Queue ストレージを使用する方法 | Microsoft Azure" 
	description="Azure Queue サービスを使用して、キューの作成と削除のほか、メッセージの挿入、取得、および削除を行う方法を説明します。サンプルは Node.js で記述されています。" 
	services="storage" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="mwasson"/>


# Node.js から Queue ストレージを使用する方法

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## 概要

このガイドでは、Windows Azure キュー サービスを使用して
一般的なシナリオを実行する方法について説明します。サンプルは Node.js 
API を使用して記述されています。紹介するシナリオは、キュー メッセージの**挿入**、**ピーク**、
**取得**、および**削除**と、**キューの作成および
キューの削除**です。

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Node.js アプリケーションの作成

空の Node.js アプリケーションを作成します。Node.js アプリケーションを作成する手順については、[Node.js アプリケーションの作成と Azure Web サイトへのデプロイ]、[Node.js クラウド サービスへのデプロイ][Node.js Cloud Service](Windows PowerShell を使用)、または [WebMatrix による Web サイトの作成とデプロイ]に関するページをご覧ください。

## アプリケーションからストレージへのアクセスの構成

Azure Storage を使用するには、Azure Storage SDK for Node.js が必要です。ここには、ストレージ REST サービスと通信するための
便利なライブラリのセットが含まれています。

### ノード パッケージ マネージャー (NPM) を使用してパッケージを取得する

1.  **PowerShell** (Windows)、**Terminal** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用して、サンプル アプリケーションを作成したフォルダーに移動します。

2.  コマンド ウィンドウに「**npm install azure-storage**」と入力すると
    次のような出力が生成されます。

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  手動で **ls** コマンドを実行して、
    **node_modules** フォルダーが作成されたことを確認できます。フォルダー内で
    **azure-storage** パッケージを検索します。これには、ストレージにアクセスするために必要なライブラリが
    含まれています。

### パッケージをインポートする

メモ帳などのテキスト エディターを使用して、ストレージを使用するアプリケーションの
**server.js** ファイルの先頭に次の内容を追加します。

    var azure = require('azure-storage');

## Azure のストレージ接続文字列の設定

azure モジュールは、Azure のストレージ アカウントに接続するために必要な情報として、環境変数 AZURE_STORAGE_ACCOUNT および AZURE_STORAGE_ACCESS_KEY、または AZURE_STORAGE_CONNECTION_STRING を読み取ります。これらの環境変数が設定されていない場合、**createQueueService** を呼び出すときにアカウント情報を指定する必要があります。

Azure Web サイトの管理ポータルで環境変数を設定する例については、「[ストレージを使用する Node.js Web アプリケーション]」をご覧ください。

## 方法:キューを作成する

次のコードは、**QueueService** オブジェクトを作成し、
これによってキューを操作できるようにします。

    var queueSvc = azure.createQueueService();

**createQueueIfNotExists** メソッドを使用します。このメソッドは、
既に存在する場合はキューを返し、まだ存在しない場合は指定された
名前の新しいキューを作成します。

	queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
      if(!error){
        // Queue created or exists
	  }
	});

キューが作成されると、 `result` は true になります。キューが存在する場合は、 `result` は false です。

### フィルター

オプションのフィルター操作は、**QueueService** を使用して行われる操作に適用できます。フィルター操作には、ログ、自動的な再試行などが含まれる場合があります。フィルターは、次のようなシグネチャを実装するオブジェクトです。

		function handle (requestOptions, next)

要求オプションに対するプリプロセスを行った後で、このメソッドは "next" を呼び出して、次のシグネチャのコールバックを渡す必要があります。

		function (returnObject, finalCallback, next)

このコールバックで、returnObject (サーバーへの要求からの応答) の処理の後に、コールバックは next を呼び出すか (他のフィルターの処理を続けるために next が存在する場合)、単に finalCallback を呼び出す必要があります (サービス呼び出しを終了する場合)。

再試行のロジックを実装する 2 つのフィルター (**ExponentialRetryPolicyFilter** と **LinearRetryPolicyFilter**) が、Azure SDK for Node.js に含まれています。次のコードは、**ExponentialRetryPolicyFilter** を使う **QueueService** オブジェクトを作成します。

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var queueSvc = azure.createQueueService().withFilter(retryOperations);

## 方法:メッセージをキューに挿入する

キューにメッセージを挿入するには、**createMessage** メソッドを使用して
新しいメッセージを作成し、キューに追加します。

	queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
	  if(!error){
	    // Message inserted
	  }
	});

## 方法:次のメッセージをピークする

キューの先頭にあるメッセージをキューから削除せずにピークするには、
**peekMessages** メソッドを呼び出すと、キューの先頭にあるメッセージをキューから削除せずにピークできます。既定では、
**peekMessages** は 1 つのメッセージを対象としてピークします。

	queueSvc.peekMessages('myqueue', function(error, result, response){
	  if(!error){
		// Messages peeked
	  }
	});

 `result` にはメッセージが含まれます。

> [AZURE.NOTE] キューにメッセージがないときに **peekMessages** を使用した場合、エラーは返されませんが、メッセージも返されません。

## 方法:次のメッセージをデキューする

メッセージは、次の 2 段階のプロセスで処理されます。

1. メッセージをデキューする。

2. メッセージを削除する。

メッセージをデキューするには、**getMessage** を使用します。これによりメッセージはキューで参照できなくなるため、他のクライアントが処理できます。アプリケーションでメッセージが処理されたら、**deleteMessage** を呼び出してキューからこのメッセージを削除します。次に、メッセージを取得してから削除する例を示します。

	queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
	    // message dequed
        var message = result[0];
        queueSvc.deleteMessage('myqueue', message.messageid, message.popreceipt, function(error, response){
	      if(!error){
		    //message deleted
		  }
		});
	  }
	});

> [AZURE.NOTE] 既定では、メッセージが非表示になるのは 30 秒間のみで、それ以降は他のクライアントから参照できます。 `options.visibilityTimeout` を **getMessages** で使用すると、別の値を指定できます。

> [AZURE.NOTE]
> キューにメッセージがないときに <b>getMessages</b> を使用した場合、エラーは返されませんが、メッセージも返されません。

## 方法:キューに配置されたメッセージの内容を変更する

**updateMessage** を使用すると、キュー内のメッセージの内容をインプレースで変更できます。次に、メッセージのテキストを更新する例を示します。

    queueSvc.getMessages('myqueue', function(error, result, response){
	  if(!error){
		// Got the message
		var message = result[0];
		queueSvc.updateMessage('myqueue', message.messageid, message.popreceipt, 10, {messageText: 'new text'}, function(error, result, response){
		  if(!error){
			// Message updated successfully
		  }
		});
	  }
	});

## 方法:メッセージをデキューするための追加オプション

キューからのメッセージの取得をカスタマイズする方法は 2 つあります。

* `options.numOfMessages` - メッセージをバッチで取得する (最大 32 個)。
* `options.visibilityTimeout` - 非表示タイムアウトを長くするか、短くする。

次のコード例では、**getMessages** メソッドを使用して、1 回の呼び出しで 15 個のメッセージを取得します。その後、for ループを使用して、
各メッセージを処理します。また、このメソッドで返されるすべてのメッセージの非表示タイムアウトを 5 分に設定します。

    queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, result, response){
	  if(!error){
		// Messages retreived
		for(var index in result){
		  // text is available in result[index].messageText
		  var message = result[index];
		  queueSvc.deleteMessage(queueName, message.messageid, message.popreceipt, function(error, response){
			if(!error){
			  // Message deleted
			}
		  });
		}
	  }
	});

## 方法:キューの長さを取得する

**getQueueMetadata** は、キューで待機中のおおよそのメッセージ数など、キューに関するメタデータを返します。

    queueSvc.getQueueMetadata('myqueue', function(error, result, response){
	  if(!error){
		// Queue length is available in result.approximatemessagecount
	  }
	});

## 方法:キューを一覧表示する

キューの一覧表示を取得するには、**listQueuesSegmented** を使用します。特定のプレフィックスでフィルター処理した一覧を取得するには、**listQueuesSegmentedWithPrefix** を使用します。

	queueSvc.listQueuesSegmented(null, function(error, result, response){
	  if(!error){
	    // result.entries contains the list of queues
	  }
	});

すべてのキューを返すことができない場合は、 `result.continuationToken` を **listQueuesSegmented** の最初のパラメーターとして使用するか、**listQueuesSegmentedWithPrefix** の 2 つ目のパラメーターとして使用すると、さらに多くの結果を取得することができます。

## 方法:キューを削除する

キューおよびキューに含まれているすべてのメッセージを削除するには、
キュー オブジェクトで **deleteQueue** メソッドを呼び出します。

    queueSvc.deleteQueue(queueName, function(error, response){
		if(!error){
			// Queue has been deleted
		}
	});

すべてのメッセージを削除せずにキューからクリアするには、**clearMessages** を使用します。

## 方法:共有アクセス署名を操作する

共有アクセス署名 (SAS) は、ストレージ アカウントの名前またはキーを指定せずにキューへの細密なアクセスを提供する安全な方法です。SAS は、モバイル アプリによるメッセージの送信を許可する場合など、キューへの制限されたアクセスを提供する場合によく使用されます。

クラウドベースのサービスなどの信頼されたアプリケーションは、**QueueService** の **generateSharedAccessSignature** を使用して SAS を生成し、信頼されていないか、部分的に信頼されたアプリケーションにこれを提供します。たとえば、モバイル アプリなどです。SAS は、SAS が有効である期間の開始日と終了日のほか、SAS の保有者に付与されたアクセス レベルを示したポリシーを使用して生成されます。

次の例では、SAS の保有者によるキューへのメッセージの追加を許可する新しい共有アクセス ポリシーを生成します。このポリシーは作成後 100 分が経過すると期限切れになります。

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);
	
	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
	    Start: startDate,
	    Expiry: expiryDate
	  }
	};

	var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
	var host = queueSvc.host;

SAS の保有者がキューにアクセスするときに必要なホスト情報も提供する必要があることに注意してください。

その後、クライアント アプリケーションは、この SAS と **QueueServiceWithSAS** を使用してキューに対する操作を実行します。次に、キューに接続してメッセージを作成する例を示します。

	var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
	sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
	  if(!error){
	    //message added
	  }
	});

生成された SAS が持つ権限は追加アクセスであるため、メッセージの読み取り、更新、または削除を試行した場合は、エラーが返されます。

### アクセス制御リスト

SAS のアクセス ポリシーを設定するために、アクセス制御リスト (ACL) も使用できます。複数のクライアントにキューへのアクセスを許可し、各クライアントに異なるアクセス ポリシーを提供する場合に便利です。

ACL は、アクセス ポリシーの配列と、各ポリシーに関連付けられた ID を使用して実装されます。次の例では、2 つのポリシーを定義しています。1 つは "user1" 用、もう 1 つは "user2" 用です。

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

次の例では、**myqueue** の現在の ACL を取得してから、**setQueueAcl** を使用して新しいポリシーを追加します。この手法で以下を実行できます。

	queueSvc.getQueueAcl('myqueue', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		queueSvc.setQueueAcl('myqueue', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

ACL を設定した後で、ポリシーの ID に基づいて SAS を作成できます。次の例では、"user2" 用に新しい SAS を作成しています。

	queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });

## 次のステップ

これで、キュー ストレージの基本を学習できました。
さらに複雑なストレージ タスクについて学習するには、次のリンク先を参照してください。

-   MSDN リファレンス:[Azure のデータの格納とアクセス][]
-   [Azure のストレージ チーム ブログ][]
-   GitHub の [Azure Storage SDK for Node][] リポジトリ

  [Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node
  [REST API の使用]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure の管理ポータル]: http://manage.windowsazure.com
  [Node.js アプリケーションの作成と Azure の Web サイトへのデプロイ]: ../web-sites-nodejs-develop-deploy-mac.md
  [ストレージを使用する Node.js クラウド サービス]: ../storage-nodejs-use-table-storage-cloud-service-app.md
  [ストレージを使用する Node.js Web アプリケーション]: ../storage-nodejs-use-table-storage-web-site.md

  
  [Queue1]: ./media/storage-nodejs-how-to-use-queues/queue1.png
  [plus-new]: ./media/storage-nodejs-how-to-use-queues/plus-new.png
  [quick-create-storage]: ./media/storage-nodejs-how-to-use-queues/quick-storage.png
  
  
  
  [Node.js クラウド サービス]: ../cloud-services-nodejs-develop-deploy-app.md
  [Azure のデータの格納とアクセス]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Azure のストレージ チーム ブログ]: http://blogs.msdn.com/b/windowsazurestorage/
 [WebMatrix を使用した Web サイト]: ../web-sites-nodejs-use-webmatrix.md

<!--HONumber=49--> 