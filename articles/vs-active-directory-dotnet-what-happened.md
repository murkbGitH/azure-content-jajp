<properties title="Active Directory 認証の使用 - 変更内容" pageTitle="Active Directory 認証の使用 - 変更内容" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Getting Started (概要)][Getting Started (概要)]
> -   [変更内容][変更内容]

### <span id="whathappened">プロジェクトの変更点</span>

リファレンスが追加されました。

##### NuGet パッケージのリファレンス

-   `Microsoft.IdentityModel.Protocol.Extensions`
-   `Microsoft.Owin`
-   `Microsoft.Owin.Host.SystemWeb`
-   `Microsoft.Owin.Security`
-   `Microsoft.Owin.Security.Cookies`
-   `Microsoft.Owin.Security.OpenIdConnect`
-   `Owin`
-   `System.IdentityModel.Tokens.Jwt`

##### .NET のリファレンス

-   `Microsoft.IdentityModel.Protocol.Extensions`
-   `Microsoft.Owin`
-   `Microsoft.Owin.Host.SystemWeb`
-   `Microsoft.Owin.Security`
-   `Microsoft.Owin.Security.Cookies`
-   `Microsoft.Owin.Security.OpenIdConnect`
-   `Owin`
-   `System`
-   `System.Data`
-   `System.Drawing`
-   `System.IdentityModel`
-   `System.IdentityModel.Tokens.Jwt`
-   `System.Runtime.Serialization`

##### コード ファイルがプロジェクトに追加された

認証スタートアップ クラス `App_Start/Startup.Auth.cs` が Azure AD 認証のスタートアップ ロジックを含むプロジェクトに追加されました。さらに、`SignIn()` メソッドおよび `SignOut()` メソッドを含むコントローラー クラス Controllers/AccountController.cs が追加されました。最後に、SignIn/SignOut のアクション リンクを含む部分ビュー `Views/Shared/_LoginPartial.cshtml` が追加されました。

##### スタートアップ コードがプロジェクトに追加された

既にプロジェクトに Startup クラスがある場合、**Configuration** メソッドが更新されて `ConfigureAuth(app)` 呼び出しが追加されています。それ以外の場合は、Startup クラスがプロジェクトに追加されました。

##### app.config または web.config に新しい構成値が含まれる

次の構成エントリが追加されました。

     <appSettings>
	    <add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
	    <add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
	    <add key="ida:AADInstance" value="https://login.windows.net/{0}" /> 
	    <add key="Ida:PostLogoutRedirectURI" value="Your project start page" /> 
	</appSettings> 

</p>
##### Azure Active Directory (AD) アプリが作成された

ウィザードで選択したディレクトリに Azure AD アプリケーションが作成されました。

[Azure Active Directory の詳細を確認する][Azure Active Directory の詳細を確認する]

  [Getting Started (概要)]: /documentation/articles/vs-active-directory-dotnet-getting-started/
  [変更内容]: /documentation/articles/vs-active-directory-dotnet-what-happened/
  [Azure Active Directory の詳細を確認する]: http://azure.microsoft.com/services/active-directory/