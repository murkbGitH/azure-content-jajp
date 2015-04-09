﻿## キュー ストレージとは

Azure キュー ストレージは、HTTP または HTTP を使用した
認証された呼び出しを介して世界中のどこからでもアクセスできる
大量のメッセージを格納するためのサービスです。キューの 1 つのメッセージの最大サイズは 64 KB で、
1 つのキューには、ストレージ アカウントの合計容量の上限に達するまで、
数百万のメッセージを格納できます。1 つのストレージ アカウントに最大 500 TB の BLOB、キュー、およびテーブルのデータを格納できます。ストレージ アカウントの容量の詳細については、[Azure のストレージの拡張性とパフォーマンスのターゲットに関するページ](http://msdn.microsoft.com/library/azure/dn249410.aspx)を参照してください。

キュー ストレージの一般的な用途には、次のようなものがあります。

-   <span>非同期に処理する作業のバックログを作成する</span>
-   Azure Web ロールから Azure にメッセージを渡す
    Worker ロール

## キュー サービスの概念

キュー サービスには、次のコンポーネントが含まれます。

![Queue1](./media/storage-queue-concepts-include/queue1.png)


- **URL 形式:** キューは、次の URL 形式を使用してアドレス指定できます:   
	http://`<storage account>`.queue.core.windows.net/`<queue>` 
      
次の URL を使用すると、図のいずれかのキューをアドレス指定できます。  
	http://myaccount.queue.core.windows.net/imagesToDownload

-**ストレージ アカウント:**Azure のストレージにアクセスする場合には必ず、ストレージ アカウントを使用します。ストレージ アカウントの容量の詳細については、[Azure のストレージの拡張性とパフォーマンスのターゲットに関するページ](http://msdn.microsoft.com/library/azure/dn249410.aspx)を参照してください。

- **キュー:** キューは、メッセージのセットを格納します。すべてのメッセージはキューに格納されている必要があります。

- **メッセージ:** 形式を問わず、メッセージのサイズは最大で 64 KB です。



<!--HONumber=49-->