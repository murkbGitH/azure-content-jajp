<properties 
	pageTitle="Azure Active Directory の専用グループ | Microsoft Azure" 
	description="Azure AD の専用グループとその作成方法の概要" 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/09/2015" 
	ms.author="femila"/>

# Azure Active Directory の専用グループ

Azure Active Directory では、専用グループが自動的に作成され、そのグループ メンバーシップも自動的に作成されます。専用グループのメンバーは、Microsoft Azure 管理ポータル、Windows PowerShell コマンドレット、プログラムのいずれの方法でも追加したり削除したりすることはできません。専用グループを有効にするには、Microsoft Azure 管理ポータルの [構成] タブで [専用グループを有効にする] を [はい] に設定します。

現在のパブリック プレビュー リリースで、[専用グループの有効化] を **[はい]** に設定した後、["すべてのユーザー" グループの有効化] を **[はい]** に設定することで、"すべてのユーザー" 専用グループをディレクトリで自動的に作成できるようになります。その後、["**すべてのユーザー**" グループの表示名] で、この専用グループの名前を編集できます。

All Users 専用グループは、ディレクトリ内のすべてのユーザーに同じアクセス許可を割り当てる場合に便利です。たとえば、ディレクトリ内のすべてのユーザーに SaaS アプリケーションへのアクセス権を与えるには、All Users 専用グループに対してこのアプリケーションへのアクセス権を割り当てます。

ここでは、Azure Active Directory の追加情報を提供するいくつかのトピックを紹介します。

* [Azure Active Directory グループによるリソースのアクセス管理](active-directory-manage-groups.md)
* [Azure Active Directory とは](active-directory-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)

<!---HONumber=Oct15_HO3-->