<properties
 pageTitle="A8、A9、A10、A11 インスタンスについて | Microsoft Azure"
 description="この記事では、Azure A8、A9、A10、A11 コンピューティング集中型インスタンスの背景情報および使用上の注意事項について説明します。"
 services="virtual-machines, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="06/09/2015"
 ms.author="danlep"/>

# A8、A9、A10、A11 コンピューティング集中型インスタンスについて

この記事では、*コンピューティング集中型*インスタンスとも呼ばれる Azure A8、A9、A10、A11 インスタンスの背景情報および使用上の注意事項について説明します。他のインスタンスとは異なるこれらのインスタンスの重要な機能は次のとおりです。

* **高性能のハードウェア** - これらのインスタンスを実行する Azure データ センター ハードウェアは、ハイ パフォーマンス コンピューティング (HPC) クラスター アプリケーション、モデリング、シミュレーションなど、コンピューティングおよびネットワーク集中型アプリケーション用に設計および最適化されています。

* **RDMA ネットワーク接続** - A8 および A9 インスタンスは、リモート ダイレクト メモリ アクセス (RDMA) テクノロジに基づく Azure の低待機時間で高スループットのネットワークを介して通信できます。この機能により、並列 Message Passing Interface (MPI) アプリケーションのパフォーマンスが向上します (RDMA ネットワーク アクセスは、現在、クラウド サービスおよび Windows Server ベースの VM に対してのみサポートされています)。

>[AZURE.NOTE]A10 および A11 インスタンスのパフォーマンス最適化と仕様は、A8 および A9 インスタンスと同じです。ただし、Azure での RDMA ネットワークへのアクセスは含まれません。A10 と A11 のインスタンスは、ノード間で一定した待ち時間の少ない通信を必要としない HPC アプリケーション (パラメトリックまたは驚異的並列アプリケーションとも呼ばれる) 向けに設計されています。

[他の Azure VM のサイズ](virtual-machines-size-specs.md)と同様に、A8、A9、A10、および A11 インスタンスは、Azure VM (IaaS) での標準またはカスタムの Windows Server および Linux オペレーティング システム イメージ、またはクラウド サービス (PaaS) での Azure Guest OS リリースをサポートします。

## 仕様

### CPU とメモリ

次の表に示すように、Azure の A8、A9、A10、A11 コンピューティング集中型インスタンスは、高速のマルチコア CPU と大容量のメモリを備えています。

サイズ | CPU | メモリ
------------- | ----------- | ----------------
A8 および A10 | Intel® Xeon® E5-2670<br/>8 コア @ 2.6 GHz | DDR3-1600 MHz<br/>56 GB
A9 および A11 | Intel® Xeon® E5-2670<br/>16 コア @ 2.6 GHz | DDR3-1600 MHz<br/>112 GB


>[AZURE.NOTE]サポートされる命令セット拡張機能など、プロセッサに関する詳細については、Intel.com の Web サイトを参照してください。


### ネットワーク アダプター

A8 および A9 インスタンスは 2 個のネットワーク アダプターを備え、次の 2 つのバックエンド Azure ネットワークに接続します。


ネットワーク | 説明
-------- | -----------
10 Gbps イーサネット | Azure サービス (ストレージや仮想ネットワークなど) およびインターネットに接続します。
32 Gbps バックエンド、RDMA 対応 | 1 つのクラウド サービス内のインスタンス間での低待機時間、高スループットのアプリケーション通信を可能にします。


>[AZURE.IMPORTANT]現在、RDMA ネットワークへのアクセスは、Microsoft Network Direct インターフェイスを使用するアプリケーション経由でのみ可能です。この記事の「[RDMA ネットワークへのアクセス](#access-the-RDMA-network)」を参照してください。


A10 および A11 インスタンスは、Azure サービスおよびインターネットに接続する 10 Gbps イーサネット ネットワーク アダプターを 1 個備えています。

## Azure サブスクリプションに関する考慮事項

* **Azure アカウント** - 少数のコンピューティング集中型インスタンスより多数のインスタンスをデプロイする場合は、従量課金のサブスクリプションまたは他の購入オプションを検討してください。MSDN サブスクリプションを使用することもできます。「[MSDN サブスクライバー向けの Azure の特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)」を参照してください。[Azure 無料評価版](http://azure.microsoft.com/pricing/free-trial/)を使用している場合は、使用できる Azure コンピューティング コアの数に制限があります。

* **コア クォータ** - Azure サブスクリプションのコア クォータを、既定値の 20 コアから増やすことが必要な場合があります。8 コアまたは 16 コア インスタンスの多くのシナリオでは不十分です。初期テストの場合、100 コアへのクォータ増量を要求することを検討してください。そのためには、「[Azure の制限と増設について](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)」に従って無償サポート チケットを開きます。

>[AZURE.NOTE]Azure のクォータは容量保証ではなくクレジット制限です。使用したコアに対してのみ課金されます。

* **アフィニティ グループ** - Azure アフィニティ グループは、同じ Azure データ センターのサービスまたは VM をグループ化することによって、パフォーマンスを最適化します。コンピューティング集中型インスタンスのグループ化では、インスタンスのデプロイを予定しているリージョンに新しいアフィニティ グループを作成することをお勧めします。ベスト プラクティスとして、アフィニティ グループはコンピューティング集中型インスタンスに対してのみ使用し、他のサイズのインスタンスには使用しないようにします。

* **仮想ネットワーク** - コンピューティング集中型インスタンスを使用するために、Azure Virtual Network は必要ありません。ただし、多くの IaaS シナリオの場合、またはオンプレミス リソースへのアクセスが必要なサイト間接続の場合は、少なくともクラウド ベースの Azure Virtual Network が必要になる可能性があります。インスタンスをデプロイする前に、新しい (リージョン) 仮想ネットワークを作成する必要があります。アフィニティ グループ内の仮想ネットワークに A8、A9、A10、または A11 VM を追加することはできません。詳細については、「[仮想ネットワークの作成](https://msdn.microsoft.com/library/azure/dn631643.aspx)」および「[サイト間 VPN 接続を使用する仮想ネットワークの構成](../vpn-gateway/vpn-gateway-site-to-site-create.md)」を参照してください。

## HPC Pack を使用する場合の考慮事項

[HPC Pack](https://technet.microsoft.com/library/cc514029) は、マイクロソフトの無料 HPC クラスターおよびジョブ管理ソリューションであり、A8、A9、A10、A11 インスタンスを使用するためには必要ありませんが、Azure で Windows HPC クラスターを作成する場合は推奨されるツールです。A8 および A9 インスタンスの場合、HPC Pack は、Azure の RDMA ネットワークにアクセスする Windows MPI アプリケーションを実行する最も効率的な方法です。HPC Pack には、マイクロソフトによる Windows 用の Message Passing Interface (MS-MPI) の実装のためのランタイム環境が含まれます。

HPC Pack でコンピューティング集中型インスタンスを使用するための詳細およびチェックリストについては、「[A8/A9 コンピューティング集中型インスタンス:HPC Pack 使用クイック スタート](https://msdn.microsoft.com/library/azure/dn594431.aspx)」を参照してください。

## RDMA ネットワークへのアクセス

単一のクラウド サービス内では、Microsoft Network Direct インターフェイスを使用してインスタンス間の通信を行う MPI アプリケーションが実行されていれば、A8 および A9 インスタンスは Azure の RDMA ネットワークにアクセスできます。現在、Network Direct は、マイクロソフトの Windows 用 MS-MPI によってのみサポートされます。A10 および A11 インスタンスには、RDMA ネットワークへのアクセスは含まれません。

>[AZURE.NOTE]他の Azure インスタンスと同様に、A8 および A9 インスタンスは、使用可能な CPU コア、メモリ、およびディスク領域を使用して、MPI アプリケーション以外のワークロードを実行できます。ただし、その場合、インスタンスは RDMA ネットワークに接続せず、A10 および A11 インスタンスと機能的に同等です。


A8 または A9 インスタンスの仮想マシン (IaaS) およびクラウド サービス (PaaS) デプロイ内の RDMA ネットワークにアクセスするための MPI アプリケーションの要件を、次の表に示します。標準的なデプロイ シナリオについては、「[A8/A9 コンピューティング集中型インスタンス: HPC Pack 使用クイック スタート](https://msdn.microsoft.com/library/azure/dn594431.aspx)」を参照してください。


前提条件 | 仮想マシン (IaaS) | クラウド サービス (PaaS)
---------- | ------------ | -------------
オペレーティング システム | Windows Server 2012 R2 または Windows Server 2012 VM | Windows Server 2012 R2 または Windows Server 2008 R2 Guest OS ファミリ
MPI | MS-MPI 2012 R2 以降、スタンドアロンまたは HPC Pack 2012 R2 以降によってインストール済み | MS-MPI 2012 R2 以降、HPC Pack 2012 R2 以降によってインストール済み


>[AZURE.NOTE]IaaS シナリオの場合、[HpcVmDrivers 拡張機能](https://msdn.microsoft.com/library/azure/dn690126.aspx)を VM に追加して RDMA 接続に必要な Windows ドライバーをインストールする必要があります。


## その他の注意事項

* 既存の Azure VM を A8、A9、A10、または A11 のサイズに変更することはできません。

* 現在、A8、A9、A10、および A11 インスタンスを、既存のアフィニティ グループの一部であるクラウド サービスを使用してデプロイすることはできません。同様に、A8、A9、A10、および A11 インスタンスを含むクラウド サービスのアフィニティ グループを、他のインスタンス サイズのデプロイに使用することはできません。これらのデプロイを試みると、「`Azure deployment failure (Compute.OverconstrainedAllocationRequest): The VM size (or combination of VM sizes) required by this deployment cannot be provisioned due to deployment request constraints.`」のようなエラー メッセージが表示されます。


## 次のステップ

* A8、A9、A10、および A11 インスタンスの可用性と料金の詳細については、「[仮想マシンの料金](http://azure.microsoft.com/pricing/details/virtual-machines/)」および「[クラウド サービスの料金](http://azure.microsoft.com/pricing/details/cloud-services/)」を参照してください。
* HPC Pack での A8 および A9 インスタンスのデプロイと使用については、「[A8/A9 コンピューティング集中型インスタンス: HPC Pack 使用クイック スタート](https://msdn.microsoft.com/library/azure/dn594431.aspx)」および「[A8 および A9 インスタンス上での MPI アプリケーションの実行](https://msdn.microsoft.com/library/azure/dn592104.aspx)」を参照してください。
 

<!---HONumber=62-->