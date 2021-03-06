<properties
	pageTitle="Azure の SharePoint Server 2013 ファーム | Microsoft Azure"
	description="Microsoft Azure で開発/テスト環境または運用 SharePoint Server 2013 ファームをセットアップする方法について説明する記事を紹介します。"
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="10/20/2015"
	ms.author="josephd"/>

# Azure インフラストラクチャ サービスでホストされる SharePoint ファーム

[AZURE.INCLUDE [learn-about-deployment-models-both-include](../../includes/learn-about-deployment-models-both-include.md)]

Microsoft Azure インフラストラクチャ サービスで、最初または次の開発/テスト環境または運用環境の SharePoint ファームを設定します。ここでは、簡単に構成できる機能と、新機能を追加したり主要な機能を最適化するためにファームを迅速に拡張する機能を活用することができます。

> [AZURE.NOTE]Microsoft は SharePoint Server 2016 IT Preview をリリースしています。SharePoint Server 2016 IT Preview とその前提条件があらかじめインストールされた Azure 仮想マシン ギャラリー イメージを使用すると、このプレビュー版を簡単にインストールしてテストすることができます。詳細については、「[SharePoint Server 2016 IT Preview を Azure でテストする](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/)」を参照してください。

## 基本的な SharePoint 開発/テスト ファーム

自動作成されるこの環境は、クラウド専用の Azure Virtual Network 内のドメイン コントローラー、SQL Server、および SharePoint サーバーの 3 つのサーバーで構成されています。

Azure プレビュー ポータルの Azure Marketplace の「[SharePoint 2013 non-HA Farm (SharePoint 2013 非高可用性ファーム)](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/)」項目を参照してください。これは、インターネットに接続された SharePoint Web サイトの基本的な開発/テスト ファームを作成します。詳細については、「[SharePoint サーバー ファームの作成](virtual-machines-sharepoint-farm-azure-preview.md)」を参照してください。

Azure リソース マネージャーのテンプレートを使用することもできます。「[Deploy a three-server SharePoint farm (3 つのサーバーで構成された SharePoint ファームのデプロイ)](virtual-machines-workload-template-sharepoint.md#deploy-a-three-server-sharepoint-farm)」参照してください。

> [AZURE.NOTE]Azure プレビュー ポータルの Azure Marketplace の **SharePoint サーバー ファーム**項目は削除されました。

## 高可用性 SharePoint 開発/テスト ファーム

この自動作成されるこの環境は、クラウド専用の Azure Virtual Network 内の 9 つのサーバーで構成されています。内訳は、ドメイン コントローラー 2 つ、SQL サーバー クラスター 3 つ、アプリケーション層の SharePoint サーバー 2 つ、Web 層の SharePoint サーバー 2 つです。

Azure プレビュー ポータルの Azure Marketplace の「[SharePoint 2013 HA Farm (SharePoint 2013 高可用性ファーム)](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/)」項目を参照してください。これは、インターネットに接続された SharePoint Web サイトの高可用性の開発/テスト ファームを作成します。詳細については、「[SharePoint サーバー ファームの作成](virtual-machines-sharepoint-farm-azure-preview.md)」を参照してください。

Azure リソース マネージャーのテンプレートを使用することもできます。「[Deploy a nine-server SharePoint farm (9 つのサーバーで構成された SharePoint ファームのデプロイ)](virtual-machines-workload-template-sharepoint.md#deploy-a-nine-server-sharepoint-farm)」を参照してください。

> [AZURE.NOTE]Azure プレビュー ポータルの Azure Marketplace の **SharePoint サーバー ファーム**項目は削除されました。

## ハイブリッド クラウドの開発/テスト ファーム

[ハイブリッド クラウドの開発/テスト環境での SharePoint イントラネット ファーム](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)では、シミュレートされたハイブリッド クラウド構成を作成します。この構成はシンプルな 2 階層の SharePoint ファームをホストし、このファームを使用して、Azure でホストされているイントラネット SharePoint ファームをインターネット上の自分の場所からテストできます。

この構成では、クラシック デプロイメント モデルを使用します。

## 高可用性イントラネット SharePoint 運用ファーム

[Azure で SharePoint 2013 と SQL Server AlwaysOn 可用性グループ](virtual-machines-workload-intranet-sharepoint-overview.md)をデプロイすることで、運用環境で利用できる、高可用性のイントラネット SharePoint Server 2013 ファームを Azure で構築できます。

この構成では、クラシック デプロイメント モデルを使用します。

## その他のリソース

[SharePoint 2013 用の Microsoft Azure アーキテクチャ](https://technet.microsoft.com/library/dn635309.aspx)

[SharePoint Server 2013 を使用した Microsoft Azure のインターネット サイト](https://technet.microsoft.com/library/dn635307.aspx)

[Microsoft Azure での SharePoint Server 2013 の障害復旧](https://technet.microsoft.com/library/dn635313.aspx)

[SharePoint 2013 認証に Microsoft Azure Active Directory を使用する](https://technet.microsoft.com/library/dn635311.aspx)

[Microsoft Azure での Office 365 ディレクトリ同期 (DirSync) のデプロイ](https://technet.microsoft.com/library/dn635310.aspx)

<!---HONumber=Nov15_HO2-->