<properties 
	pageTitle="Mobile Services を使用したイメージの BLOB ストレージへのアップロード (Windows ストア) | Mobile Services" 
	description="Mobile Services を使用して画像を Azure BLOB ストレージにアップロードし、その画像に Windows ストア アプリからアクセスする方法を説明します。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/22/2015" 
	ms.author="glenga"/>

# モバイル サービスを使用した Azure ストレージへのイメージのアップロード

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

このトピックでは、Azure モバイル サービスを使用して、ユーザーが生成したイメージをアプリケーションが Azure ストレージにアップロードおよび保存する方法を紹介します。モバイル サービスでは、SQL データベースを使用してデータを保存します。ただし、BLOB (Binary Large Object) データは、Azure BLOB ストレージ サービスに、より効率的に保存されます。

BLOB ストレージ サービスにデータを安全にアップロードするために必要な資格情報をクライアント アプリケーションで安全に配信できません。代わりに、これらの資格情報をモバイル サービスに保存し、それらを使用して、新しいイメージをアップロードするために使用される SAS (Shared Access Signature) を生成する必要があります。SAS は有効期間が短い資格情報 (この場合は 5 分間) であり、モバイル サービスによりクライアント アプリケーションに安全に返されます。アプリケーションは、この一時的な資格情報を使用してイメージをアップロードします。この例では、BLOB サービスからのダウンロードはパブリックです。

このチュートリアルでは、モバイル サービスにより生成された SAS を使用して、写真を撮影してイメージを Azure にアップロードする機能を、モバイル サービス クイック スタート アプリケーションに追加します。このチュートリアルでは、モバイル サービス クイック スタートを更新してイメージを BLOB ストレージ サービスにアップロードする、次のような基本的な手順について説明します。

1. [ストレージ クライアント ライブラリのインストール]
2. [SAS を生成する挿入スクリプトの更新]
3. [イメージをキャプチャするためのクライアント アプリケーションの更新]
4. [アプリケーションをテストするイメージのアップロード]

このチュートリアルには、次のものが必要です。

+ Microsoft Visual Studio 2012 Express for Windows 8 以降のバージョン
+ [Azure ストレージ アカウント][How To Create a Storage Account]
+ コンピューターに接続された、カメラなどのイメージ キャプチャ デバイス

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用]」を完了している必要があります。

##<a name="install-storage-client"></a>Windows ストア アプリ用のストレージ クライアントのインストール

SAS を使用して Blob ストレージにイメージをアップロードできるようにするには、最初に、Windows ストア アプリ用のストレージ クライアント ライブラリをインストールする NuGet パッケージを追加する必要があります。

1. Visual Studio の**ソリューション エクスプローラー**で、プロジェクト名を右クリックし、**[NuGet パッケージの管理]** をクリックします。

2. 左側のウィンドウで、[**オンライン**] カテゴリを選択し、`WindowsAzure.Storage` を探します。**Azure Storage** パッケージで [**インストール**] をクリックし、使用許諾契約に同意します。

  	![][2]

  	これにより、Azure のストレージ サービス用のクライアント ライブラリがプロジェクトに追加されます。

次に、イメージをキャプチャおよびアップロードするクイック スタート アプリケーションを更新します。

##<a name="update-scripts"></a>管理ポータルで登録されている挿入スクリプトを更新する

[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage](../../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md)]

## <a name="next-steps"> </a>次のステップ

モバイル サービスを Blob サービスと統合することによりイメージを安全にアップロードできました。他のバックエンド サービスおよび統合のトピックを参照してください。

+ [SendGrid を使用した Mobile Services からの電子メールの送信]
 
  SendGrid 電子メール サービスを使用して、モバイル サービスに電子メール機能を追加する方法について説明します。このトピックでは、サーバー側スクリプトを追加し、SendGrid を使用して電子メールを送信する方法を示します。

+ [Mobile Services でのバックエンド ジョブの計画]

  モバイル サービスのジョブ スケジューラ機能を使用して、定義したスケジュールに従って実行されるサーバー スクリプト コードを定義する方法について説明します。

+ [Mobile Services のサーバー スクリプト リファレンス]

  サーバー側のタスクを実行するサーバー スクリプトの使用および他の Azure コンポーネントおよび外部リソースとの統合に関するトピックを参照してください。
 
+ [Mobile Services .NET の使用方法の概念リファレンス]

  .NET でモバイル サービスを使用する方法について説明します
  
 
<!-- Anchors. -->
[ストレージ クライアント ライブラリのインストール]: #install-storage-client
[イメージをキャプチャするためのクライアント アプリケーションの更新]: #add-select-images
[SAS を生成する挿入スクリプトの更新]: #update-scripts
[アプリケーションをテストするイメージのアップロード]: #test
[Next Steps]: #next-steps

<!-- Images. -->

[2]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


<!-- URLs. -->
[SendGrid を使用した Mobile Services からの電子メールの送信]: /develop/mobile/tutorials/send-email-with-sendgrid/
[Mobile Services でのバックエンド ジョブの計画]: mobile-services-schedule-recurring-tasks.md
[Send push notifications to Windows Store apps using Service Bus from a .NET back-end]: http://go.microsoft.com/fwlink/?LinkId=277073&clcid=0x409
[Mobile Services のサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[モバイル サービスの使用]: ../mobile-services-windows-store-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: /manage/services/storage/how-to-create-a-storage-account
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Mobile Services .NET の使用方法の概念リファレンス]: /develop/mobile/how-to-guides/work-with-net-client-library
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!--HONumber=54--> 