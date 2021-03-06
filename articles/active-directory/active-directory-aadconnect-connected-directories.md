<properties
	pageTitle="Azure AD Connect を使用してディレクトリに接続する | Microsoft Azure"
	description="Azure AD Connect で接続されたディレクトリのカスタム設定の説明です。"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="billmath"/>



# Azure AD Connect を使用してディレクトリに接続する

カスタム設定では、Active Directory フォレストへの接続にエンタープライズ管理者アカウントは必要ありません。ウィザードでは、ドメインまたはローカル ユーザー アカウントを使用できます。ただし、アカウントは、ローカルの AD コネクタのアカウント、つまり同期に必要なディレクトリ情報を読み書きするアカウントとして使用されます。

これは、次のシナリオを可能にするには、追加のアクセス許可を割り当てる必要があるということです。

シナリオ |アクセス許可
------------- | ------------- |
パスワードの同期| <li>ディレクトリの変更のレプリケート。</li> <li>ディレクトリの変更をすべてにレプリケート。</li>
Exchange ハイブリッドのデプロイ|「[Office 365 Exchange ハイブリッド AAD Sync の書き戻し属性とアクセス許可](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange)」を参照してください。
パスワードの書き戻し | <li>パスワードの変更</li><li>パスワードのリセット</li>
ユーザー、グループ、およびデバイスの書き戻し|"書き戻し" するディレクトリのオブジェクトと属性に対する書き込みアクセス許可
シングル サインオンおよび AD FS| フェデレーション サーバーが置かれているドメイン内のドメイン管理者のアクセス許可。





**その他のリソース**

* [Azure AD Connect アカウントとアクセス許可の詳細](active-directory-aadconnect-account-summary.md)
* [パスワード同期のアクセス許可](https://msdn.microsoft.com/library/azure/dn757602.aspx#psynch)
* [Exchange ハイブリッドのアクセス許可](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange)
* [パスワードの書き戻しのアクセス許可](https://msdn.microsoft.com/library/azure/dn757602.aspx#pwriteback)
* [Azure AD Connect のカスタム インストール](active-directory-aadconnect-get-started-custom.md)
* [MSDN の Azure AD Connect](active-directory-aadconnect.md)

<!---HONumber=Oct15_HO3-->