<properties
	pageTitle="Azure の SharePoint Server 2013 ファーム | Microsoft Azure"
	description="Azure の SharePoint Server 2013 ファームの価値について説明し、テスト環境の設定および高可用性構成のデプロイを実行します。"
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/29/2015"
	ms.author="josephd"/>

# Azure インフラストラクチャ サービスのワークロード: イントラネット SharePoint ファーム

[AZURE.INCLUDE [learn-about-deployment-models-classic-include](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャーのデプロイ モデル。

Microsoft Azure で、最初または次の SharePoint ファームを設定し、簡単に構成できる機能と、新機能を追加したり主要な機能を最適化したりするためにファームを迅速に拡張する機能を活用します。多くの SharePoint ファームは、標準、高可用の 3 層構成から、分散キャッシュや検索などのパフォーマンスや個別の役割に合わせて、数十以上のサーバーを含むファームへと成長します。

Azure インフラストラクチャ サービスの仮想マシンと仮想ネットワークの機能を使用すると、オンプレミス ネットワークに透過的に接続された SharePoint ファームをすばやくデプロイし、実行できるようになります。セットアップできるネットワークの例を次に示します。

![](./media/virtual-machines-workload-intranet-sharepoint-farm/workload-spsqlao.png)

Azure Virtual Network は、すべての名前とトラフィック ルーティングを修正したオンプレミス ネットワークの拡張なので、ユーザーはオンプレミス データセンターにあるネットワークと同様にアクセスできます。

この構成では、新しい Azure 仮想マシンを追加することで、SharePoint ファームを簡単に拡張できます。この場合は、ハードウェアとメンテナンス両方の継続的なコストは、独自のデータセンターで同等のファームを実行するよりも低くなります。

Azure インフラストラクチャ サービスでイントラネット SharePoint ファームをホストする例として、基幹業務アプリケーションがあります。概要については、「[基幹業務アプリケーションのアーキテクチャ ブループリント](http://msdn.microsoft.com/dn630664)」を参照してください。

次の手順は、Azure でホストされている開発/テスト イントラネット SharePoint ファームのセットアップです。

> [AZURE.NOTE]Microsoft は、SharePoint Server 2016 IT Preview をリリースしました。SharePoint Server 2016 IT Preview とその前提条件があらかじめインストールされた Azure 仮想マシン ギャラリー イメージを使用すると、このプレビュー版を簡単にインストールしてテストすることができます。詳細については、「[SharePoint Server 2016 IT Preview を Azure でテストする](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/)」を参照してください。

## Azure でホストされている開発/テスト イントラネット SharePoint ファームを作成する

Azure でホストされている SharePoint の開発/テスト環境を作成するには、2 つの選択肢があります。

- クラウド専用仮想ネットワーク
- クロスプレミス仮想ネットワーク

[MSDN サブスクリプション](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/)または [Azure 試用サブスクリプション](http://azure.microsoft.com/pricing/free-trial/)を持っている場合、このような開発/テスト環境を無料で作成できます。

### クラウド専用仮想ネットワーク

クラウド専用仮想ネットワークは、オンプレミス ネットワークに接続されていません。基本的な、または高可用な SharePoint ファームをすばやく作成する場合は、「[SharePoint サーバー ファームの作成](virtual-machines-sharepoint-farm-azure-preview.md)」を参照してください。基本的な SharePoint ファームの構成例を次に示します。

![](./media/virtual-machines-workload-intranet-sharepoint-farm/Non-HAFarm.png)

### クロスプレミス仮想ネットワーク

クロスプレミス仮想ネットワークは、サイト間 VPN または ExpressRoute 接続を使用して、オンプレミス ネットワークに接続されています。最終的な構成に似せた開発/テスト環境を作成し、VPN 接続で SharePoint サーバーへのアクセスとリモート管理の実行を実験する場合は、「[テスト用のハイブリッド クラウドでの SharePoint イントラネット ファームの設定](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)」を参照してください。

![](./media/virtual-machines-workload-intranet-sharepoint-farm/CreateSPFarmHybridCloud.png)

次の手順では、Azure で高可用イントラネット SharePoint ファームを作成します。

## Azure でホストされているイントラネット SharePoint ファームをデプロイする

機能的で高可用なイントラネット SharePoint ファームの基本的で代表的な構成は、次の例のようになります。

![](./media/virtual-machines-workload-intranet-sharepoint-farm/workload-spsqlao.png)

構成は次のとおりです。

- Web、アプリケーション、データベース層に 2 つのサーバーがあるイントラネット SharePoint ファーム。
- クラスターに 2 つの SQL サーバーと 1 つのマジョリティ ノード コンピューターがある SQL Server AlwaysOn 可用性グループ構成。
- 仮想ネットワークに 2 つのレプリカ ドメイン コント ローラーがある Azure Active Directory。

この構成のインフォグラフィックを確認するには、「[SharePoint と SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)」を参照してください。

### 部品表

この基本的な構成には、次の一連の Azure サービスとコンポーネントが必要です。

- 9 つの仮想マシン
- 4 つの追加データ ディスク (ドメイン コント ローラーと、SQL サーバーを実行する仮想マシン用)
- 3 つのクラウド サービス
- 4 つの可用性セット
- 1 つのクロスプレミス仮想ネットワーク
- 1 つのストレージ アカウント
- 1 つの Azure サブスクリプション

こちらが仮想マシンとそれぞれのこの構成に対する既定のサイズになります。

項目 | 仮想マシンの説明 | ギャラリー イメージ | 既定サイズ
--- | --- | --- | ---
1\. | 最初のドメイン コントローラー | Windows Server 2012 R2 Datacenter | A2 (Medium)
2\. | 2 番目のドメイン コントローラー | Windows Server 2012 R2 Datacenter | A2 (Medium)
3\. | 最初のデータベース サーバー | Microsoft SQL Server 2014 Enterprise - Windows Server 2012 R2 | A5
4\. | 2 番目のデータベース サーバー | Microsoft SQL Server 2014 Enterprise - Windows Server 2012 R2 | A5
5\. | クラスターのマジョリティ ノード | Windows Server 2012 R2 Datacenter | A1 (Small)
6\. | 最初の SharePoint アプリケーション サーバー | Microsoft SharePoint Server 2013 評価版 - Windows Server 2012 R2 | A4 (ExtraLarge)
7\. | 2 番目の SharePoint アプリケーション サーバー | Microsoft SharePoint Server 2013 評価版 - Windows Server 2012 R2 | A4 (ExtraLarge)
8\. | 最初の SharePoint Web サーバー | Microsoft SharePoint Server 2013 評価版 - Windows Server 2012 R2 | A4 (ExtraLarge)
9\. | 2 番目の SharePoint Web サーバー | Microsoft SharePoint Server 2013 評価版 - Windows Server 2012 R2 | A4 (ExtraLarge)

この構成の推定コストを計算するには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を参照してください。

1. [**モジュール**] で、[**コンピューティング**] をクリックし、[**仮想マシン**] を必要なだけクリックして 9 つの仮想マシンを作成してください。
2. 各仮想マシンで次を選択してください。
	- 目的のリージョン
	- 種類には **Windows**
	- 価格レベルには**標準**
	- **インスタンス サイズ**には、上記の表でのデフォルト サイズもしくは目的のサイズ

> [AZURE.NOTE]Azure 料金計算ツールでは、SQL Server 2014 Enterprise を実行している 2 つの仮想マシンの SQL Server ライセンスの追加のコストは含まれません。詳細については、「[Virtual Machines Pricing-SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql)」を参照してください。

### デプロイ フェーズ

この構成をデプロイするには、次のプロセスを使用します。

- フェーズ 1: Azure を構成する。

	Azure ポータルと Azure PowerShell を使用して、ストレージ アカウント、クラウド サービス、およびクロスプレミス仮想ネットワークを作成します。詳細な構成手順については、「[フェーズ 1](virtual-machines-workload-intranet-sharepoint-phase1.md)」を参照してください。

- フェーズ2: ドメイン コントローラーを構成する

	仮想ネットワークの 2 つの Azure Active Directory レプリカ ドメイン コントローラーと DNS 設定を構成します。詳細な構成手順については、「[フェーズ 2](virtual-machines-workload-intranet-sharepoint-phase2.md)」を参照してください。

- フェーズ 3: SQL Server インフラストラクチャを構成する

	SharePoint に使用する SQL Server 仮想マシンを準備し、SQL Server クラスターを作成します。詳細な構成手順については、「[フェーズ 3](virtual-machines-workload-intranet-sharepoint-phase3.md)」を参照してください。

- フェーズ 4: SharePoint サーバーを構成する

	新しい SharePoint ファームの 4 つの SharePoint 仮想マシンを構成します。詳細な構成手順については、「[フェーズ 4](virtual-machines-workload-intranet-sharepoint-phase4.md)」を参照してください。

- フェーズ 5: AlwaysOn 可用性グループを作成する

	SharePoint データベースを準備し、AlwaysOn 可用性グループを作成し、SharePoint データベースを追加します。詳細な構成手順については、「[フェーズ 5](virtual-machines-workload-intranet-sharepoint-phase5.md)」を参照してください。

構成が完了したら、「[SharePoint 2013 用の Microsoft Azure アーキテクチャ](http://technet.microsoft.com/library/dn635309.aspx)」のガイドを参照して、この SharePoint ファームを拡張できます。

## その他のリソース

[Azure での SharePoint と SQL Server AlwaysOn 可用性グループのデプロイ](virtual-machines-workload-deploy-spsqlao-overview.md)

[テスト用のハイブリッド クラウドでの SharePoint イントラネット ファームの設定](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[SharePoint 2013 用の Microsoft Azure アーキテクチャ](https://technet.microsoft.com/library/dn635309.aspx)

[SharePoint と SQL Server AlwaysOn のインフォグラフィック](http://go.microsoft.com/fwlink/?LinkId=394788)

[Azure インフラストラクチャ サービスでホストされる SharePoint ファーム](virtual-machines-sharepoint-infrastructure-services.md)

[Azure インフラストラクチャ サービス実装ガイドライン](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure インフラストラクチャ サービスのワークロード: 高可用な基幹業務アプリケーション](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=Nov15_HO3-->