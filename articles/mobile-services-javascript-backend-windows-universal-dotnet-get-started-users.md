<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/18/2014" ms.author="Glenn="" Gailey" />

# モバイル サービスでの認証の使用

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

このトピックでは、ユニバーサル Windows アプリから Azure モバイル サービスのユーザーを認証する方法を示します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。

このチュートリアルでは、アプリケーションでの認証を有効にするための、次の基本的な手順について説明します。

1.  [アプリケーションを認証に登録し、モバイル サービスを構成する][アプリケーションを認証に登録し、モバイル サービスを構成する]
2.  [テーブルのアクセス許可を、認証されたユーザーだけに制限する][テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3.  [アプリケーションに認証を追加する][アプリケーションに認証を追加する]
4.  [クライアント側で認証トークンを保存する][クライアント側で認証トークンを保存する]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用][モバイル サービスの使用]」を完了している必要があります。

> [WACOM.NOTE] このチュートリアルでは、Windows ストア アプリおよび Windows Phone Store 8.1 アプリでユーザーを認証する方法について説明します。Windows Phone 8.0 アプリまたは Windows Phone Silverlight 8.1 アプリの場合、このバージョンの「[モバイル サービスでの認証の使用][モバイル サービスでの認証の使用]」を参照してください。

> このチュートリアルでは、さまざまな ID プロバイダーを使用した、モバイル サービスによって管理される認証フローについて説明します。この方法は構成が容易で、複数のプロバイダーをサポートしています。クライアントによって管理される認証による Live Connect を使用して、Windows Phone アプリでシングル サインオンできるようにするには、トピック「[Live Connect を使用した Windows Phone アプリへのシングル サインオン][Live Connect を使用した Windows Phone アプリへのシングル サインオン]」を参照してください。クライアントによって管理される認証を使用することにより、アプリは ID プロバイダーが保持する追加のユーザー データにアクセスできます。サーバー スクリプトで **user.getIdentities()** 関数を呼び出すことにより、同じユーザー データをモバイル サービスで取得できます。　詳細については、[この記事][この記事]を参照してください。

## <a name="register"></a>アプリケーションを認証に登録し、モバイル サービスを構成する

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  (省略可能)「[Windows ストア アプリ パッケージを Microsoft 認証に登録する][Windows ストア アプリ パッケージを Microsoft 認証に登録する]」の手順を実行します。

    <div class="dev-callout"><b>注</b>
<p>この手順は Microsoft アカウント ログイン プロバイダーのみに適用されるため、省略可能です。Windows ストア アプリケーションのパッケージ情報をモバイル サービスに登録すると、クライアントはシングル サインオン サービス エクスペリエンスを実現するために Microsoft アカウント ログイン資格情報を再利用できます。この操作を行わない場合、login メソッドが呼び出されるたびに、Microsoft アカウント ログイン ユーザーにログイン プロンプトが表示されます。Microsoft アカウント ID プロバイダーを使用する場合は、この手順を実行してください。</p>
    </div>

## <a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  Visual Studio で、TodoList アプリの Windows ストア プロジェクトを右クリックして、**[スタートアップ プロジェクトに設定]** をクリックします。

2.  共有プロジェクトで、App.xaml.cs プロジェクト ファイルを開き、[MobileServiceClient][MobileServiceClient] の定義を見つけ、Azure で実行されているモバイル サービスに接続するように構成されていることを確認します。

    <div class="dev-callout"><strong>注</strong><p>Visual Studio ツールを使用してアプリをモバイル サービスに接続する場合、ツールはクライアント プラットフォームごとに 1 つずつ、<strong>MobileServiceClient</strong> 定義を合計 2 セット生成します。この機会に、<code data-inline="1">#if...#endif</code> でラップされた <strong>MobileServiceClient</strong> 定義を、両方のバージョンのアプリで使用される、ラップされていない 1 つの定義に統合することによって、生成されたコードを単純化することができます。</p></div>

3.  F5 キーを押して、Windows ストア アプリを実行します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。

    この問題は、認証されないユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしても、*TodoItem* テーブルでは認証が要求されるために発生します。

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

## <a name="add-authentication"></a>アプリケーションに認証を追加する

[WACOM.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)]

## <a name="tokens"></a>クライアント側で認証トークンを保存する

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>次のステップ

次の[モバイル サービス ユーザーのサービス側の認証][モバイル サービス ユーザーのサービス側の認証]チュートリアルでは、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。.NET でモバイル サービスを使用する方法の詳細については、「[モバイル サービス .NET の使用方法の概念リファレンス][モバイル サービス .NET の使用方法の概念リファレンス]」を参照してください。


  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [アプリケーションを認証に登録し、モバイル サービスを構成する]: #register
  [テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
  [アプリケーションに認証を追加する]: #add-authentication
  [クライアント側で認証トークンを保存する]: #tokens
  [モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started/
  [モバイル サービスでの認証の使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-users
  [Live Connect を使用した Windows Phone アプリへのシングル サインオン]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
  [この記事]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [Windows ストア アプリ パッケージを Microsoft 認証に登録する]: /ja-jp/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [MobileServiceClient]: http://msdn.microsoft.com/ja-jp/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
  [mobile-services-windows-universal-dotnet-authenticate-app]: ../includes/mobile-services-windows-universal-dotnet-authenticate-app.md
  [mobile-services-windows-store-dotnet-authenticate-app-with-token]: ../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md
  [モバイル サービス ユーザーのサービス側の認証]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts
  [モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/