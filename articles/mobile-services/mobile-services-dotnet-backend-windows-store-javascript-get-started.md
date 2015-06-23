<properties 
	pageTitle="Windows ストア アプリ用 Mobile Services の使用 | モバイル デベロッパー センター" 
	description="このチュートリアルでは、C#、VB、または JavaScript で Windows ストア用の開発を行う場合に Azure Mobile Services を使用する方法を示します。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="javascript" 
	ms.topic="hero-article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>


# <a name="getting-started"> </a>Mobile Services の使用

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

このチュートリアルでは、Azure Mobile Services を使用してユニバーサル Windows アプリにクラウドベースのバックエンド サービスを追加する方法を示します。このチュートリアルでは、新しい Mobile Services と、新しい Mobile Services にアプリケーション データを保存する HTML と JavaSript の簡単な *To do list* アプリケーションの両方を作成します。作成する Mobile Services は、サーバー側ビジネス ロジックのために Visual Studio を使用してサポートされる .NET 言語を使用し、Mobile Services を管理します。JavaScript でサーバー側ビジネス ロジックを記述できる Mobile Services を作成する方法については、このトピックの「JavaScript バージョン」をご覧ください。

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../../includes/mobile-services-windows-universal-get-started.md)]

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合は、無料の試用アカウントを数分で作成できます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-jp%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F)をご覧ください
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> が必要です。無料評価版が利用できます。


## 新しい Mobile Services を作成する

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## 新しいユニバーサル Windows アプリを作成する

Mobile Services を作成したら、管理ポータルの簡単なクイック スタートに従って、新しいアプリケーションを作成するか、既存のアプリケーションを変更して Mobile Services に接続できます。 

ここでは、Mobile Services に接続された新しいユニバーサル Windows アプリを作成します。

1. 管理ポータルで、**[Mobile Services]** をクリックし、先ほど作成した Mobile Services をクリックします。
   
2. [クイック スタート] タブの **[プラットフォームの選択]** で **[Windows]** を選択し、**[新しい Windows ストア アプリを作成する]** を展開します。

   	![][6]

   	これにより、Mobile Services に接続された Windows ストア アプリを作成するための簡単な 3 つの手順が表示されます。

  	![][7]

3. <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> をまだインストールしていない場合は、ダウンロードし、ローカル コンピューターまたは仮想マシンにインストールします。

4. **[アプリケーションとサービスをダウンロードしてローカルに実行する]** で、Windows ストア アプリの言語を選択し、**[ダウンロード]** をクリックします。 

  	これにより、Mobile Services と Mobile Services に接続されている _To do list_ サンプル アプリケーションの両方のプロジェクトを含むソリューションがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。


## ローカル  Mobile Services に対してアプリをテストする

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

>[AZURE.NOTE] Mobile Services にアクセスして default.js ファイルにあるデータを照会および挿入するコードを確認できます。

## Mobile Services を発行する

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

<ol start="4">
<li><p>共通コード プロジェクトで、default.js ファイルを開き、<a href="http://msdn.microsoft.com/library/azure/jj554219.aspx" target="_blank">WindowsAzure.MobileServiceClient</a> インスタンスを作成するコードを見つけて、<em>localhost</em> を使用してこのクライアントを作成するコードをコメント アウトし、リモート Mobile Services URL を使用してクライアントを作成するコードのコメントを解除すると、次のようになります。</p>

        <pre><code>var client = new WindowsAzure.MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXXXX-APPLICATION-KEY-XXXXXX"
        );</code></pre>

	<p>クライアントは Azure に発行された Mobile Services にアクセスするようになります。</p></li>

<li><p><strong>F5</strong> キーを押してプロジェクトをリビルドし、アプリケーションを開始します。</p></li>

<li><p>アプリケーションで、<strong>[Insert a TodoItem]</strong> ボックスに意味のあるテキスト (たとえば、「<em>チュートリアルの完了</em>」) を入力し、<strong>[Save]</strong> をクリックします。</p>

<p>これで、Azure でホストされている新しい Mobile Services に POST 要求が送信されます。</p>
</li>
<li><p>(省略可能) 汎用的な Windows ソリューションで、既定のスタートアップ プロジェクトをもう 1 つのアプリケーションに変更し、<strong>F5</strong> をもう一度押します。</p>

	<p>アプリケーションが開始すると、前の手順で保存したデータが、Mobile Services から読み込まれる点に注目してください。</p></li>
</ol>

ユニバーサル Windows アプリの詳細については、「[シングル Mobile Services から複数のデバイス プラットフォームをサポートする](mobile-services-how-to-use-multiple-clients-single-service.md#shared-vs)」をご覧ください。

<!-- Anchors. -->
[Mobile Services の使用]:#getting-started
[新しい Mobile Services を作成する]:#create-new-service
[Mobile Services インスタンスの定義]:#define-mobile-service-instance
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-completed.png

[6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-portal-quickstart.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-steps.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-service-startup.png

[10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-startup.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-publish.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-data-browse.png


<!-- URLs. -->
[データの使用]: ../mobile-services-dotnet-backend-windows-universal-javascript-get-started-data.md
[認証の使用]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md
[プッシュ通知の使用]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript と HTML]: mobile-services-win8-javascript/
[管理ポータル]: https://manage.windowsazure.com/
[JavaScript バージョン]: ../mobile-services-windows-store-get-started.md
[Visual Studio 2012 を使用した Mobile Services でのデータの使用]: ../mobile-services-windows-store-dotnet-get-started-data-vs2012.md


<!--HONumber=52--> 