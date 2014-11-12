<properties linkid="mobile-services-dotnet-backend-xamarin-ios-get-started-users" urlDisplayName="Get Started with authentication in Mobile Services for Xamarin iOS apps" pageTitle="Get Started with authentication in Mobile Services for Xamarin iOS apps - Azure Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Xamarin iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get Started with authentication in Mobile Services" authors="donnam" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# モバイル サービスでの認証の使用

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users" title="Windows ストア C#">Windows ストア C#</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users" title="Windows ストア JavaScript">Windows ストア JavaScript</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users" title="iOS">iOS</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="Android">Android</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users" title="Xamarin.iOS" class="current">Xamarin.iOS</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a>    
</div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users/" title=".NET バックエンド" class="current">.NET バックエンド</a> |  <a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users/"  title="JavaScript バックエンド">JavaScript バックエンド</a></div>

このトピックでは、アプリケーションから Azure モバイル サービスのユーザーを認証する方法について説明します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。

このチュートリアルでは、アプリケーションでの認証を有効にするための、次の基本的な手順について説明します。

1.  [アプリケーションを認証に登録し、モバイル サービスを構成する][アプリケーションを認証に登録し、モバイル サービスを構成する]
2.  [テーブルのアクセス許可を、認証されたユーザーだけに制限する][テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3.  [アプリケーションに認証を追加する][アプリケーションに認証を追加する]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用][モバイル サービスの使用]」を完了している必要があります。

## <a name="register"></a>アプリケーションを認証に登録し、モバイル サービスを構成する

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

## <a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

1.  Visual Studio または Xamarin Studio で、デバイスまたはシミュレーターを使用してクライアント プロジェクトを実行します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。

    この問題は、認証されないユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしても、*TodoItem* テーブルでは認証が要求されるために発生します。

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

## <a name="add-authentication"></a>アプリケーションに認証を追加する

ここでは、データを表示する前にログイン画面を表示するようにアプリケーションを変更します。アプリケーションが起動したときには、モバイル サービスには接続されず、データも表示されません。ユーザーが最初に更新操作を実行した後で、ログイン画面が表示されます。ログインに成功すると、Todo 項目の一覧が表示されます。

1.  クライアント プロジェクトで、ファイル **QSTodoService.cs** を開き、QSTodoService に次の宣言を追加します。

        // Mobile Service logged in user
        private MobileServiceUser user; 
        public MobileServiceUser User { get { return user; } }

2.  次の定義を使用して、**QSTodoService** に新しいメソッド **Authenticate** を追加します。

        private async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    <div class="dev-callout"><b>注</b>
<p>Facebook 以外の ID プロバイダーを使用している場合は、上の <strong>LoginAsync</strong> メソッドに渡される値を <i>MicrosoftAccount</i>、<i>Twitter</i>、<i>Google</i>、<i>WindowsAzureActiveDirectory</i> のいずれかにします。</p>
</div>

3.  **QSTodoListViewController.cs** を開きます。**ViewDidLoad** のメソッド定義を変更して、終わり近くにある **RefreshAsync()** の呼び出しを削除します。

        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            todoService = QSTodoService.DefaultService;

            todoService.BusyUpdate += (bool busy) => {
                if (busy)
                    activityIndicator.StartAnimating ();
                else 
                    activityIndicator.StopAnimating ();
            };

            // Comment out the call to RefreshAsync
            // await RefreshAsync ();

            AddRefreshControl ();
        }

4.  メソッド **RefreshAsync** を変更して、**User** プロパティが null の場合にはログイン画面を表示して認証を行うようにします。メソッド定義の先頭に次のコードを追加します。

        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method

5.  **[実行]** ボタンを押してプロジェクトをビルドし、iPhone シミュレーターでアプリケーションを開始します。アプリケーションにデータが表示されないことを確認します。

    項目の一覧をプルダウンして更新操作を実行すると、ログイン画面が表示されます。有効な資格情報を正しく入力すると、Todo 項目の一覧が表示され、データを更新できるようになります。



  [アプリケーションを認証に登録し、モバイル サービスを構成する]: #register
  [テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
  [アプリケーションに認証を追加する]: #add-authentication
  [モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started/
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-dotnet-backend-aad-server-extension]: ../includes/mobile-services-dotnet-backend-aad-server-extension.md
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md