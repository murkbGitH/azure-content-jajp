<properties 
	pageTitle="iOS で特定のユーザーにクロスプラット フォーム通知を送信する" 
	description="特定のユーザーのすべてのデバイスにプッシュ通知を送信する方法について説明します。"
	services="app-service\mobile" 
	documentationCenter="ios" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-dotnet" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="09/25/2015"
	ms.author="yuaxu"/>

# 特定のユーザーにクロスプラット フォーム通知を送信する

[AZURE.INCLUDE [app-service-mobile-selector-push-users](../../includes/app-service-mobile-selector-push-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

このトピックでは、特定のユーザーのすべての登録済みデバイスにモバイル バックエンドから通知を送信する方法について説明します。[テンプレート]の概念が導入されました。これにより、クライアント アプリケーションで、登録時にペイロードの形式と変数プレースホルダーを自由に指定することができます。送信すると、これらのプレースホルダーにより、すべてのプラットフォームにヒットし、クロスプラットフォーム通知が有効になります。

> [AZURE.NOTE]クロスプラットフォーム クライアントでプッシュ操作を有効にするには、有効にする各プラットフォームに対してこのチュートリアルを完了する必要があります。同じモバイル バックエンドを共有しているクライアントに対して、[モバイル バックエンドの更新](#backend)を 1 回だけ実行する必要があります。
 
##前提条件 

このチュートリアルを開始する前に、作業する各クライアント プラットフォームで次の App Service チュートリアルが既に完了している必要があります。

+ [認証の使用]<br/>TodoList サンプル アプリケーションにログイン要件を追加します。

+ [プッシュ通知の使用]<br/>プッシュ通知用に TodoList サンプル アプリケーションを構成します。

##<a name="client"></a>クロスプラットフォームのプッシュを処理するためのテンプレートを登録するように、クライアントを更新する

1. **QSAppDelegate.m** の **application:didFinishLaunchingWithOptions** の APNs の登録スニペットを **QSTodoListViewController.m** の **loginWithProvider** の呼び出しに移動します。これにより、認証の完了後に APNs の登録が行われます。

        [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
            [self refresh];
            
            // register iOS8 or previous devices for notifications
            if ([[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)]) {
                [[UIApplication sharedApplication] registerForRemoteNotifications];
            }
            else {
                // Register for remote notifications
                [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
                 UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            }
        }];

2. テンプレートを使用するために、**application:didRegisterForRemoteNotificationsWithDeviceToken** の **registerDeviceToken** 呼び出しを以下で置き換えます。

        NSDictionary *templates = @{
                               @"testNotificationTemplate": @{ @"body" : @{ @"aps" : @{ @"alert": @"$(message)" } } }
                               };
    
        // register with templates
        [client.push registerDeviceToken:deviceToken template:templates completion:^(NSError *error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

##<a name="backend"></a>特定のユーザーに通知を送信するようにサービス バックエンドを更新する

1. Visual Studio で、`PostTodoItem` メソッド定義を次のコードで更新します。  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            // get notification hubs credentials associated with this mobile app
            string notificationHubName = this.Services.Settings.NotificationHubName;
            string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

            // connect to notification hub
            NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName)

            // get the current user id and create tag to identify user
            ServiceUser authenticatedUser = this.User as ServiceUser;
            string userTag = "_UserId:" + authenticatedUser.Id;

            // build dictionary for template
            var notification = new Dictionary<string, string>{{"message", item.Text}};

            try
            {
            	await Hub.Push.SendTemplateNotificationAsync(notification, userTag);
            }
            catch (System.Exception ex)
            {
                throw;
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

##<a name="test"></a>アプリケーションをテストする

モバイル バックエンド プロジェクトを再発行し、設定してあるクライアント アプリケーションのいずれかを実行します。項目を挿入すると、バックエンドにより、ユーザーがログインしているすべてのクライアント アプリケーションに通知が送信されます。

<!-- URLs. -->
[認証の使用]: app-service-mobile-ios-get-started-users.md
[プッシュ通知の使用]: app-service-mobile-ios-get-started-push.md
[テンプレート]: https://msdn.microsoft.com/ja-JP/library/dn530748.aspx
 

<!---HONumber=Nov15_HO1-->