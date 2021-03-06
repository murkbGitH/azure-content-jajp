<properties services="virtual-machines" title="Using Azure CLI with Azure Resource Manager" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## Azure リソース マネージャー (ARM) での Azure CLI の使用

リソース マネージャーのコマンドとテンプレートで Azure CLI を使用して、リソース グループを使用する Azure リソースとワークロードをデプロイできるようにするには、(当然のことですが) Azure のアカウントが必要です。アカウントがない場合、[ここから無料の Azure 試用版](http://azure.microsoft.com/pricing/free-trial/)を入手できます。

> [AZURE.NOTE]Azure アカウントはまだ取得しておらず、MSDN サブスクリプションのサブスクリプションのみがある場合は、[ここから MSDN サブスクリプション会員の特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)をアクティブ化して無料の Azure クレジットを取得するか、無料のアカウントを使用できます。どちらを使用しても Azure にアクセスできます。

### 手順 1. Azure CLI のバージョンを確認する

命令型コマンドと ARM テンプレートに Azure CLI を使用するには、バージョン 0.8.17 以降が必要です。バージョンを確認するには、「`azure --version`」と入力します。次のような結果が表示されます。

    $ azure --version
    0.8.17 (node: 0.10.25)

Azure CLI のバージョンを更新する必要がある場合は、[Azure CLI](https://github.com/Azure/azure-xplat-cli) に関するページを参照してください。

### 手順 2. Azure で職場または学校の ID を使用していることを確認する

[Azure Active Directory テナント](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)または[サービス プリンシパル名](https://msdn.microsoft.com/library/azure/dn132633.aspx)(これらは*組織 ID* とも呼ばれます) を使用している場合は、ARM コマンド モードのみを使用できます。

この ID があることを確認するには、「`azure login`」と入力し、画面の指示に従って、職場または学校のユーザー名とパスワードを使用してサインインします。この ID がある場合は、次のような結果が表示されます。

    $ azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@contoso.onmicrosoft.com
    Password: *********
    |info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription Visual Studio Ultimate with MSDN as default
    info:    Added subscription Azure Free Trial
    +
    info:    login command OK

結果が表示されない場合は、Microsoft アカウント ID を使用して、新しいテナント (またはサービス プリンシパル) を作成する必要があります(個人の MSDN サブスクリプションや無料試用版サブスクリプションの場合は、結果が表示されないことがよくあります)。 Microsoft ID で作成された Azure のアカウントから、職場または学校の ID を作成する場合は、[Azure AD ディレクトリと新しい Azure サブスクリプションの関連付け](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)に関するページを参照してください。既に組織 ID があると思われる場合は、アカウントを作成した担当者に直接確認してください。

### 手順 3. Azure サブスクリプションを選択する

Azure アカウントでのサブスクリプションが 1 つだけの場合、Azure CLI 自体が既定でそのサブスクリプションに関連付けられます。サブスクリプションが複数ある場合は、「`azure account set <subscription id or name> true`」と入力して、使用するサブスクリプションを選択する必要があります。ここで _subscription id or name_ は、現在のセッションで使用するサブスクリプション ID またはサブスクリプション名です。

次のように表示されます。

    $ azure account set "Azure Free Trial" true
    info:    Executing command account set
    info:    Setting subscription to "Azure Free Trial" with id "2lskd82-434-4730-9df9-akd83lsk92sa".
    info:    Changes saved
    info:    account set command OK

### 手順 4. ARM モードで Azure CLI を使用する

Azure リソース管理 (ARM) モードで Azure CLI を使用するには、「`azure config mode arm`」と入力します。次のように表示されます。

    $ azure config mode arm
    info:    New mode is arm

> [AZURE.NOTE]「`azure config mode asm`」と入力すると、Azure サービス管理モードに戻してそのコマンドを再度使用できます。

<!---HONumber=Oct15_HO3-->