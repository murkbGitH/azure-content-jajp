<properties
	pageTitle="既存の Azure Mobile Services アプリへの認証の追加 (iOS) | .NET バックエンド | Microsoft Azure"
	description="Mobile Services を使用して、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて iOS アプリのユーザーを認証する方法について説明します。"
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="10/01/2015"
	ms.author="krisragh"/>

# 既存の Azure Mobile Services アプリへの認証の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

このチュートリアルでは、サポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。最初に、このチュートリアルの基になっている [Mobile Services のクイック スタート チュートリアル]を完了しておく必要があります。

##<a name="register"></a>アプリケーションを認証に登録し、Mobile Services を構成する

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

Xcode で、プロジェクトを開きます。**[実行]** をクリックし、アプリケーションを起動します。アプリケーションの起動後に、状態コード 401 (許可されていません) の例外が発生することを確認します。この問題は、認証されないユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしても、_TodoItem_ テーブルでは認証が要求されるために発生します。

##<a name="add-authentication"></a>アプリケーションに認証を追加する

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>アプリケーションに認証トークンを保存する

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

##<a name="next-steps"></a>次のステップ

次の「[Mobile Services ユーザーのサービス側の承認]」チュートリアルではユーザー ID 値を使用して、返されたデータをフィルター処理します。

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps
[Storing authentication tokens in your app]: #store-authentication

<!-- URLs. -->
[Mobile Services ユーザーのサービス側の承認]: mobile-services-dotnet-backend-service-side-authorization.md
[Mobile Services のクイック スタート チュートリアル]: mobile-services-dotnet-backend-ios-get-started.md
[Get started with authentication]: mobile-services-dotnet-backend-ios-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-ios-get-started-push.md
[Authorize users with scripts]: ../mobile-services-dotnet-backend-ios-authorize-users-in-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!---HONumber=Oct15_HO3-->