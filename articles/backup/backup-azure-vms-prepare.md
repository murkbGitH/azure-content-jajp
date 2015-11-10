<properties
	pageTitle="Azure Virtual Machines をバックアップする環境の準備 | Microsoft Azure"
	description="Azure Virtual Machines をバックアップする環境を準備します"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/23/2015"
	ms.author="trinadhk; aashishr; jimpark; markgal"/>

# Azure Virtual Machines をバックアップする環境の準備
Azure Virtual Machines をバックアップする環境を準備するには、次の前提条件を満たす必要があります。前提条件を満たしている場合は、[VM をバックアップ](backup-azure-vms.md)できます。満たしていない場合は、次の手順で環境を準備してください。

## 1\.バックアップ資格情報コンテナー
Azure Virtual Machines のバックアップを開始するにはまずバックアップ資格情報コンテナーを作成する必要があります。資格情報コンテナーは、時間の経過と共に作成されるすべてのバックアップと回復ポイントを格納するエンティティです。資格情報コンテナーには、バックアップ対象の仮想マシンに適用されるバックアップ ポリシーも含まれています。

以下の図は、さまざまな Azure Backup エンティティの関係を示したものです。![Azure Backup のエンティティとの関係](./media/backup-azure-vms-prepare/vault-policy-vm.png)

バックアップ資格情報コンテナーを作成するには:

1. [管理ポータル](http://manage.windowsazure.com/)にサインインします。

2. **[新規]** > **[Data Services]** > **[復旧サービス]** > **[バックアップ資格情報コンテナー]** > **[簡易作成]** の順にクリックします。組織のアカウントに関連付けられたサブスクリプションが複数ある場合は、正しいアカウントを選択してバックアップ資格情報コンテナーに関連付けてください。各 Azure サブスクリプションに複数のバックアップ資格情報コンテナーを保有し、保護する仮想マシンを編成できます。

3. **[名前]** ボックスに、コンテナーを識別する表示名を入力します。これは、サブスクリプションごとに一意である必要があります。

4. **[リージョン]** ボックスで、コンテナーのリージョンを選択します。資格情報コンテナーは、保護する仮想マシンと同じリージョンにある必要があります。仮想マシンが別のリージョンにある場合は、資格情報コンテナーをそれぞれ作成します。バックアップ データを格納するストレージ アカウントを指定する必要はありません。バックアップ資格情報のコンテナーと Azure Backup サービスはこれを自動的に処理します。

    ![バックアップ資格情報コンテナーの作成](./media/backup-azure-vms-prepare/backup_vaultcreate.png)

5. **[資格情報コンテナーの作成]** をクリックします。バックアップ資格情報コンテナーが作成されるまで時間がかかることがあります。ポータルの下部にある状態の通知を監視します。

    ![資格情報コンテナーのトースト通知の作成](./media/backup-azure-vms-prepare/creating-vault.png)

6. 資格情報コンテナーが正常に作成されたことを示すメッセージが表示され、[Recovery Services] ページに [アクティブ] と表示されます。コンテナーを作成したら、適切なストレージの冗長オプションが選択されていることを確認してください。詳細については、「[setting the storage redundancy option in the backup vault (バックアップ資格情報コンテナーのストレージ冗長オプションの設定)](backup-configure-vault.md#azure-backup---storage-redundancy-options)」をご覧ください。

    ![バックアップ資格情報コンテナーの一覧](./media/backup-azure-vms-prepare/backup_vaultslist.png)

7. バックアップ資格情報コンテナーをクリックして、**[クイック スタート]** ページに進むと、Azure 仮想マシンのバックアップ手順が表示されます。

    ![ダッシュボード ページの仮想マシンのバックアップ手順](./media/backup-azure-vms-prepare/vmbackup-instructions.png)

## 2\.VM エージェント
Azure 仮想マシンのバックアップを開始する前に、Azure VM エージェントが仮想マシンに正しくインストールされていることを確認してください。VM エージェントは仮想マシンを作成する際のオプション コンポーネントであるため、仮想マシンをプロビジョニングする前に、VM エージェントのチェック ボックスがオンになっていることを確認します。

詳細については、「[VM エージェント](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409)」と「[インストール方法](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)」に関するページをご覧ください。

### バックアップ拡張機能
仮想マシンをバックアップするために、Azure Backup サービスは VM エージェントの拡張機能をインストールします。Azure Backup サービスは、バックアップ拡張機能のアップグレードと修正プログラムの適用をシームレスに自動実行します。

VM が実行されている場合、バックアップ拡張機能がインストールされます。また、VM が実行されている場合は、アプリケーション整合性の復旧ポイントを取得できる可能性が高くなります。Azure Backup サービスは、VM がオフになっている場合は、VM のバックアップを継続しますが、拡張機能はインストールしません (オフライン VM と呼ばれます)。この場合、前述のように*クラッシュ整合性*を備えた復旧ポイントになります。

## 3\.ネットワーク接続
バックアップ拡張機能が正常に機能するには、インターネット接続が必要です。これは、Azure Storage エンドポイント (HTTP URL) に対して、VM のスナップショットを管理するコマンドを送信するためです。インターネットに接続できない場合、VM からの HTTP 要求はタイムアウトになり、バックアップ操作は失敗します。

## 制限事項

- Azure リソース マネージャーに基づく (別名 IaaS V2) 仮想マシンのバックアップはサポートされません。
- 16 台以上のデータ ディスクを搭載した仮想マシンのバックアップはサポートされません。
- Premium Storage を使用した仮想マシンのバックアップはサポートされません。
- 予約済み IP が複数ある仮想マシンのバックアップはサポートされません。
- 予約済み IP はあるがエンドポイントが定義されていない仮想マシンのバックアップはサポートされません。
- 複数の NIC を使用した仮想マシンのバックアップはサポートされません。
- 負荷分散の構成 (内部およびインターネット接続) の仮想マシンのバックアップはサポートされません。
- 復元中に既存の仮想マシンを置き換えることはサポートされません。まず既存の仮想マシンに関連付けられているディスクを削除し、次にバックアップからデータを復元します。
- リージョン間のバックアップと復元はサポートされません。
- Azure Backup サービスを使用した仮想マシンのバックアップは、Azure のすべてのパブリック リージョンでサポートされます。サポートされるリージョンについては、「[リージョン別のサービス](http://azure.microsoft.com/regions/#services)」を参照してください。目的のリージョンが現在サポートされていない場合は、資格情報コンテナーの作成時にドロップダウン リストに表示されません。
- オペレーティング システムのバージョンの選択でサポートされるのは、Azure Backup サービスを使用した仮想マシンのバックアップのみです。
  - **Linux**: Azure で動作保証済みのディストリビューションの一覧は、[こちら](../virtual-machines-linux-endorsed-distributions.md)でご確認ください。他の個人所有の Linux ディストリビューションも、仮想マシン上で VM エージェントが動作する限り使用できます。
  - **Windows Server**: Windows Server 2008 R2 より前のバージョンはサポートされていません。
- マルチ DC 構成の一部であるドメイン コントローラー VM の復元は、PowerShell を通じてのみサポートされます。詳細については、「[ドメイン コントローラーの VM の復元](backup-azure-restore-vms.md#restoring-domain-controller-vms)」を参照してください。

## 疑問がある場合
ご不明な点がある場合や確認したい機能が含まれている場合は、[フィードバックをお送りください](http://aka.ms/azurebackup_feedback)。

## 次のステップ

- [VM のバックアップ インフラストラクチャの計画](backup-azure-vms-introduction.md)
- [仮想マシンのバックアップ](backup-azure-vms.md)
- [仮想マシンのバックアップを管理する](backup-azure-manage-vms.md)

<!---HONumber=Nov15_HO1-->