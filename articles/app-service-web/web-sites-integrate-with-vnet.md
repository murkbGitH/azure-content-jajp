<properties 
	pageTitle="Web アプリを Azure Virtual Network に統合する" 
	description="Azure App Service の Azure Web アプリを新規または既存の Azure 仮想ネットワークに接続する方法を説明します。" 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/16/2015" 
	ms.author="cephalin"/>

# Web アプリを Azure Virtual Network に統合する #

このドキュメントでは、仮想ネットワークの統合プレビュー機能について説明し、この機能を [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) の Web Apps 用に設定する方法を示します。Azure Virtual Network は、Azure リソースやオンプレミスのリソースを利用するハイブリッド ソリューションを構築できる機能です。

この統合機能を使用することにより、自分の Web アプリから仮想ネットワーク内のリソースにアクセスできるようにする一方で、仮想ネットワークからその Web アプリへのアクセス権は付与しないようにすることが可能です。標準的なシナリオは、仮想ネットワーク内の仮想マシンあるいは独自のデータ センターで実行されているデータベースや Web サービスへのアクセスを Web アプリが必要とするケースです。ドライブのマウントはできません。また、現在のところ、仮想ネットワーク内の認証システムとの統合は可能になっていません。この機能はプレビュー段階であり、GA に達する前にさらに改善が加えられる予定です。

Azure Virtual Network の詳細については、「Virtual Networkの概要」を参照してください。Azure Virtual Network の利用例、また利点について説明されています。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## 使用の開始 ##
ここでは、Web アプリを仮想ネットワークに接続する前に考慮する必要がある点について説明します。

1.	Web Apps を仮想ネットワークに接続できるのは、**Standard** レベルの App Service プラン上でアプリが実行されている場合のみです。Free、Shared、および Basic プランの Web Apps は仮想ネットワークに接続できません。
2.	ターゲットの仮想ネットワークが既に存在している場合、Web アプリに接続するためには、事前に動的ルーティング ゲートウェイによりポイント対サイトが有効になっていなければなりません。静的ルーティング ゲートウェイが構成されている場合は、ポイント対サイト仮想プライベート ネットワーク (VPN) を有効にすることはできません。
3.	App Service プランの中で設定できるネットワークは 5 つまでです。1 つの Web アプリは、一度に 1 つのネットワークにのみ接続可能です。設定した 5 つのネットワークは、同じ App Service プランに含まれる任意の数の Web アプリで使用できます。  

新しい仮想ネットワークまたは既存の仮想ネットワークを接続先とするオプションがあります。新しいネットワークを作成する場合、ゲートウェイは事前設定済みになります。新しい仮想ネットワークを作成して構成する処理には数分かかることに注意してください。

Web アプリが Standard レベルの App Service プランでない場合、UI による通知がなされ、アップグレードする価格レベルへのアクセスが提供されます。

![](./media/web-sites-integrate-with-vnet/upgrade-to-standard.png)

## システムの動作 ##
内部的には、この機能はポイント対サイト VPN テクノロジを使用して Web アプリを仮想ネットワークに接続します。Azure App Service の Web Apps のシステム アーキテクチャは本来、仮想マシンの場合のように仮想ネットワークに Web アプリを直接プロビジョニングすることができないようにするマルチ テナントです。ポイント対サイト テクノロジを使用することにより、Web アプリをホスティングする仮想マシンだけにネットワーク アクセスを制限します。さらに、それらの Web アプリ ホスト上では、Web アプリからのアクセス先として設定するネットワークにのみアクセス可能となるようにネットワークへのアクセスが制限されます。

アクセスの必要な Web アプリのみにネットワークを限定する作業を行うことにより、SMB 接続の作成はできなくなります。リモート リソースへのアクセスは可能ですが、リモート ドライブのマウントは可能になりません。

![](./media/web-sites-integrate-with-vnet/how-it-works.png)
 
仮想ネットワークで DNS サーバーを設定していない場合、IP アドレスを使用することが必要です。希望するエンドポイントのポートを、ファイアウォール経由で公開するようにします。接続テストをすることになった場合、現時点で唯一の方法は、希望するエンドポイントを呼び出す Web アプリまたは Web ジョブを使用することです。現在のところ、ping や nslookup などのツールは、Kudu コンソールでは動作しません。これは、近い将来に改善の余地のある分野です。

## 既存ネットワークへの接続 ##
Web アプリを仮想ネットワークに接続するには、Web アプリのブレードに移動し、ネットワーキングのセクションで仮想ネットワークのタイルをクリックした後、既存のネットワークの 1 つを選択します。

![](./media/web-sites-integrate-with-vnet/connect-to-existing-vnet.png)
 
サブスクリプションの中でそのネットワークに接続する最初の Web アプリの場合は、仮想ネットワークでの認証のための証明書がシステムにより作成されます。証明書を確認するには、[Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)で Virtual Network に移動し、ネットワークを選択してから [証明書] タブを選択します。

上の図に示されている cantConnectVnet という名前のネットワークは、淡色表示になっており、選択できません。このようになる理由として考えられるのは、2 つだけです。ネットワーク上でポイント対サイト VPN が有効になっていないか、または、仮想ネットワークに動的ルーティング ゲートウェイがプロビジョニングされていないかのいずれかです。このいずれも該当しない場合、Web アプリの統合対象として、その仮想ネットワークを選択することができます。

## 新しい Virtual Network の作成と接続 ##
既存の仮想ネットワークに接続することに加えて、Azure ポータル UI から新しい仮想ネットワークを作成し、それに自動接続するということも可能です。そのためには、Virtual Network UI に達する同じパスをたどり、[新しい Virtual Network の作成] を選択します。表示される UI で、ネットワークの名前を指定し、アドレス空間を指定し、仮想ネットワークで使用する DNS サーバーのアドレスを設定することができます。

![](./media/web-sites-integrate-with-vnet/create-new-vnet.png)
 
ゲートウェイを設定した新しい仮想ネットワークを作成するには、最大 30 分の時間がかかります。その間、UI に作業が進行中であることが表示され、以下のメッセージが表示されます。

![](./media/web-sites-integrate-with-vnet/new-vnet-progress.png)

ネットワークが Web アプリに結合されれば、その Web アプリから TCP または UDP を通じてその仮想ネットワーク内のリソースにアクセスできるようになります。サイト間 VPN 経由で仮想ネットワークから利用可能なオンプレミス システムのリソースにアクセスするには、独自の社内ネットワークから、仮想ネットワーク内で設定されているポイント対サイトのアドレスへのトラフィックが可能となるように、そのネットワーク上のルートを追加することが必要です。

統合処理が正常に完了したら、接続についての基本情報が Azure ポータルに表示され、ネットワークから Web アプリを切断する方法が示されます。さらに、接続の認証に使用される証明書の同期方法も示されます。証明書が期限切れになった場合、または失効した場合、同期が必要になることがあります。

![](./media/web-sites-integrate-with-vnet/vnet-status-portal.png)

##仮想ネットワーク接続の管理##
App Service プランのブレードに移動することにより、App Service プラン内の Web アプリに現在関連付けられているすべての仮想ネットワークのリストが表示されます。1 つの Standard レベルの App Service プランでは、最大 5 つのネットワークを関連付けることができます。

App Service プランを、Free、Shared、または Basic など、低いレベルのプランに縮小すると、そのプランに含まれる Web アプリで使用される仮想ネットワーク接続は無効になります。そのプランを再び Standard プランに合わせてスケーリングすると、それらのネットワーク接続は再確立されます。

この時点で、Azure において既存の仮想マシンを仮想ネットワーク内に含めることは不可能です。仮想マシンは、作成中に仮想ネットワーク内にプロビジョニングする必要があります。

## オンプレミスのリソースへのアクセス ##
サイト間 VPN で設定された仮想ネットワークの作業をする際、Web アプリからオンプレミスのリソースにアクセスするには、付加的なステップが必要になります。オンプレミスのネットワークにルートを追加し、ネットワークから仮想ネットワーク内で設定されているポイント対サイトのアドレスに至るトラフィックが可能になるようにすることが必要です。ポイント対サイト接続の IP 範囲を確認するには、ここに示されている Azure ポータルの [ネットワーク] 領域に移動します。

![](./media/web-sites-integrate-with-vnet/vpn-to-onpremise.png)

## 証明書 ##
仮想ネットワークの安全な接続を確立するには、証明書の交換が必要です。以下に示すように、現行のネットワーク ポータルから、Web Apps により生成される公開証明書のサムプリントを確認することができます。

![](./media/web-sites-integrate-with-vnet/vpn-to-onpremise-certificate.png)

何らかの理由で証明書の同期が失われてしまった場合 (誤ってネットワーク ポータルから削除した場合など)、接続が失われます。修正するために、Web アプリの仮想ネットワーク UI の中に、接続を再確立する [接続の同期] アクションがあります。

このアクションは、仮想ネットワークに DNS を追加した場合、またはサイト間 VPN をネットワークに追加した場合にも使用する必要があります。

![](./media/web-sites-integrate-with-vnet/vnet-sync-connection.png)

## ハイブリッド接続との比較 ##
Web Apps には、ハイブリッド接続と呼ばれる別の機能があります。これは、いくつかの点で Virtual Network 統合とよく似ています。共通の利用例があるとはいえ、それらの機能は互いに置き換え可能ではありません。ハイブリッド接続では、複数ネットワーク混合の中で複数のアプリケーション エンドポイントとの接続を確立できます。Virtual Network 機能は、Web アプリを、オンプレミスのネットワークに接続可能な仮想ネットワークに接続します。それは、リソースがすべてそのネットワークのスコープ内にある場合にはうまく機能します。

別の相違点は、ハイブリッド接続が動作するためには、リレー エージェントをインストールする必要があるということです。そのエージェントは、Windows Server インスタンス上で実行する必要があります。Virtual Network 機能の場合、インストールの必要なものは何もなく、ホスティング オペレーティング システムとは関係なくリモート リソースへのアクセスが可能です。

さらに、現時点では、2 つの機能の間に価格レベルの違いもあります。それは、最安価のレベルでは、開発/テストのシナリオにおいてハイブリッド接続機能が非常に便利であり、ごく少数のエンドポイントへのアクセスのみ可能になるからです。仮想ネットワーク機能の場合は、VNET 内にあるすべてのもの、または VNET に接続されているすべてのものへのアクセスが提供されます。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、「[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 変更内容
* Web サイトから App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* 古いポータルから新しいポータルへの変更ガイドについては、「[プレビュー ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)」を参照してください。
 

<!---HONumber=Oct15_HO3-->