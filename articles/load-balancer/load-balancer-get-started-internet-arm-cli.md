<properties 
   pageTitle="Azure CLI を使用し、リソース マネージャーでインターネットに接続するロード バランサーを作成する | Microsoft Azure"
   description="Azure CLI を使用し、リソース マネージャーでインターネットに接続するロード バランサーを作成する方法について説明します"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2015"
   ms.author="joaoma" />

# Azure CLI を使用したインターネットに接続するロード バランサーの作成の開始

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]この記事では、リソース マネージャーのデプロイ モデルについて説明します。Azure クラシック デプロイ モデルについて確認したい場合は、「[クラシック デプロイを使用したインターネットに接続するロード バランサーの作成の開始](load-balancer-get-started-internet-classic-portal.md)」を参照してください。


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## インターネットに接続するロード バランサーを作成するために必要な項目

ロード バランサーをデプロイするには、次のオブジェクトを作成して構成する必要があります。

- フロント エンド IP 構成 - 受信ネットワーク トラフィックのパブリック IP アドレスが含まれます。 

- バック エンド アドレス プール - ロード バランサーからトラフィックを受信するためのネットワーク インターフェイス (NIC) が含まれます。

- 負荷分散の規則 - ロード バランサーのパブリック ポートをバック エンド アドレス プール内の NIC のポートにマッピングする規則が含まれます。

- 受信 NAT 規則 - ロード バランサーのパブリック ポートをバック エンド アドレス プール内の個々の NIC のポートにマッピングする規則が含まれます。

- プローブ - バック エンド アドレス プール内の NIC にリンクされている VM の可用性を確認するための正常性プローブが含まれます。

Azure リソース マネージャーでのロード バランサー コンポーネントの詳細については、「[Azure リソース マネージャーによる Load Balancer のサポート](load-balancer-arm.md)」をご覧ください。

## リソース マネージャーを使用するための CLI のセットアップ

1. Azure CLI を初めて使用する場合は、[Azure CLI のインストールと構成](xplat-cli.md)に関するページを参照して、Azure のアカウントとサブスクリプションを選択する時点までの指示に従います。

2. 次に示すように、**azure config mode** コマンドを実行してリソース マネージャー モードに切り替えます。

		azure config mode arm

	予想される出力:

		info:    New mode is arm

## 仮想ネットワークと、フロント エンド IP プールのパブリック IP アドレスの作成

### 手順 1

*NRPVnet* という名前のリソース グループを使用して、米国東部の場所に *NRPRG* という名前の仮想ネットワーク (VNet) を作成します。

	azure network vnet create NRPRG NRPVnet eastUS -a 10.0.0.0/16

*NRPVnetSubnet* という名前のサブネットを作成し、*NRPVnet* に 10.0.0.0/24 の CIDR ブロックを設定します。

	azure network vnet subnet create NRPRG NRPVnet NRPVnetSubnet -a 10.0.0.0/24

### 手順 2

フロントエンド IP プールで使用される *NRPPublicIP* という名前のパブリック IP アドレスを作成します。DNS 名は *loadbalancernrp.eastus.cloudapp.azure.com* です。次のコマンドでは、静的な割り当てタイプと 4 分のアイドル タイムアウトを使用しています。

	azure network public-ip create -g NRPRG -n NRPPublicIP -l eastus -d loadbalancernrp -a static -i 4


>[AZURE.IMPORTANT]ロード バランサーはその FQDN としてパブリック IP のドメイン ラベルを使用します。これはロード バランサー FQDN としてクラウド サービスを使用する従来のデプロイからの変更点です。この例では、FQDN は *loadbalancernrp.eastus.cloudapp.azure.com* になります。

## ロード バランサーの作成

次の例のコマンドは、*米国東部*の Azure の場所の *NRPRG* リソース グループに *NRPlb* という名前のロード バランサーを作成します。

	azure network lb create NRPRG NRPlb eastus

## フロント エンド IP プールとバックエンド アドレス プールの作成

次の例では、ロード バランサーへの受信ネットワーク トラフィックを受信するフロント エンド IP プールと、負荷分散されたネットワーク トラフィックをフロント エンド プールが送信するバックエンド IP プールを作成します。

### 手順 1 

前の手順で作成したパブリック IP とロード バランサーを関連付けてフロント エンド IP プールを作成します。

	azure network lb frontend-ip create nrpRG NRPlb NRPfrontendpool -i nrppublicip

### 手順 2 

フロント エンド IP プールから受信トラフィックを受け取るために使用するバック エンド アドレス プールをセットアップします。

	azure network lb address-pool create NRPRG NRPlb NRPbackendpool

## LB 規則、NAT 規則、およびプローブの作成

次の例では、以下の項目が作成されます。

- ポート 3441 のすべての受信トラフィックをポート 3389 に転送する NAT 規則<sup>1</sup>。
- ポート 3442 のすべての受信トラフィックをポート 3389 に転送する NAT 規則。
- ポート 80 のすべての受信トラフィックをバック エンド プールのアドレスのポート 80 に負荷分散するロード バランサー規則。
- *HealthProbe.aspx* という名前のページで正常性状態を確認するプローブ規則。

<sup>1</sup> NAT 規則はロード バランサー内の特定の仮想マシン インスタンスに関連付けられています。ポート 3341 への着信ネットワーク トラフィックは、以下の例の NAT 規則に関連付けられている特定の仮想マシンのポート 3389 に送信されます。NAT 規則、UDP または TCP のプロトコルを選択する必要があります。両方のプロトコルを、同じポートに割り当てることはできません。

### 手順 1.

NAT 規則を作成します。

	azure network lb inbound-nat-rule create -g nrprg -l nrplb -n rdp1 -p tcp -f 3441 -b 3389
	azure network lb inbound-nat-rule create -g nrprg -l nrplb -n rdp2 -p tcp -f 3442 -b 3389

パラメーター:

- **-g** - リソース グループ名
- **-l** - ロード バランサー名 
- **-n** - NAT 規則、プローブ規則、またはロード バランサー規則を表すリソースの名前
- **-p** - プロトコル (TCP または UDP)  
- **-f** - 使用するフロント エンド ポート (probe コマンドは、-f を使用してプローブ パスを定義します)
- **-b** - 使用するバック エンド ポート

### 手順 2

ロード バランサー規則を作成します。

	azure network lb probe create -g nrprg -l nrplb -n healthprobe -p "http" -o 80 -f healthprobe.aspx -i 15 -c 4

### 手順 3.

正常性プローブを作成します。

	azure network lb rule create -g nrprg -l nrplb -n HTTP -p tcp -f 80 -b 80

### 手順 4.

設定を確認します。

	azure network lb show nrprg nrplb

予想される出力:

	info:    Executing command network lb show
	+ Looking up the load balancer "nrplb"
	+ Looking up the public ip "NRPPublicIP"	
	data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb
	data:    Name                            : nrplb
	data:    Type                            : Microsoft.Network/loadBalancers
	data:    Location                        : eastus
	data:    Provisioning State              : Succeeded
	data:    Frontend IP configurations:
	data:      Name                          : NRPfrontendpool
	data:      Provisioning state            : Succeeded
	data:      Public IP address id          : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/publicIPAddresses/NRPPublicIP
	data:      Public IP allocation method   : Static
	data:      Public IP address             : 40.114.13.145
	data:
	data:    Backend address pools:
	data:      Name                          : NRPbackendpool
	data:      Provisioning state            : Succeeded
	data:
	data:    Load balancing rules:
	data:      Name                          : HTTP
	data:      Provisioning state            : Succeeded
	data:      Protocol                      : Tcp
	data:      Frontend port                 : 80
	data:      Backend port                  : 80
	data:      Enable floating IP            : false
	data:      Idle timeout in minutes       : 4
	data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
	data:      Backend address pool          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
	data:
	data:    Inbound NAT rules:
	data:      Name                          : rdp1
	data:      Provisioning state            : Succeeded
	data:      Protocol                      : Tcp
	data:      Frontend port                 : 3441
	data:      Backend port                  : 3389
	data:      Enable floating IP            : false
	data:      Idle timeout in minutes       : 4
	data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
	data:
	data:      Name                          : rdp2
	data:      Provisioning state            : Succeeded
	data:      Protocol                      : Tcp
	data:      Frontend port                 : 3442
	data:      Backend port                  : 3389
	data:      Enable floating IP            : false
	data:      Idle timeout in minutes       : 4
	data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
	data:
	data:    Probes:
	data:      Name                          : healthprobe
	data:      Provisioning state            : Succeeded
	data:      Protocol                      : Http
	data:      Port                          : 80
	data:      Interval in seconds           : 15
	data:      Number of probes              : 4
	data:
	info:    network lb show command OK

## NIC の作成

NIC を作成し (または既存の NIC を変更し)、それを NAT 規則、ロード バランサー規則、プローブに関連付ける必要があります。

### 手順 1 

*lb-nic1-be* という名前の NIC を作成し、それを *rdp1* NAT 規則と *NRPbackendpool* バック エンド アドレス プールに関連付けます。
	
	azure network nic create -g nrprg -n lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

パラメーター:

- **-g** - リソース グループ名
- **-n** - NIC リソースの名前
- **--subnet-name** - サブネットの名前 
- **--subnet-vnet-name** - 仮想ネットワークの名前
- **-d** - バック エンド プール リソースの ID。/subscription/{subscriptionID/resourcegroups/<resourcegroup-name>/providers/Microsoft.Network/loadbalancers/<load-balancer-name>/backendaddresspools/<name-of-the-backend-pool> で始まります。 
- **-e** - NIC リソースに関連付けられる NAT 規則の ID。/subscriptions/####################################/resourceGroups/<resourcegroup-name>/providers/Microsoft.Network/loadBalancers/<load-balancer-name>/inboundNatRules/<nat-rule-name> で始まります。


予想される出力:

	info:    Executing command network nic create
	+ Looking up the network interface "lb-nic1-be"
	+ Looking up the subnet "nrpvnetsubnet"
	+ Creating network interface "lb-nic1-be"
	+ Looking up the network interface "lb-nic1-be"
	data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
	data:    Name                            : lb-nic1-be
	data:    Type                            : Microsoft.Network/networkInterfaces
	data:    Location                        : eastus
	data:    Provisioning state              : Succeeded
	data:    Enable IP forwarding            : false
	data:    IP configurations:
	data:      Name                          : NIC-config
	data:      Provisioning state            : Succeeded
	data:      Private IP address            : 10.0.0.4
	data:      Private IP Allocation Method  : Dynamic
	data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
	data:      Load balancer backend address pools
	data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
	data:      Load balancer inbound NAT rules:
	data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
	data:
	info:    network nic create command OK

### 手順 2

*lb-nic2-be* という名前の NIC を作成し、それを *rdp2* NAT 規則と *NRPbackendpool* バック エンド アドレス プールに関連付けます。

 	azure network nic create -g nrprg -n lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

### 手順 3. 

*web1* という名前の仮想マシン (VM) を作成し、それを *lb-nic1-be* という名前の NIC に関連付けます。*web1nrp* と呼ばれるストレージ アカウントが次のコマンドを実行する前に作成されました。

	azure vm create --resource-group nrprg --name web1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

>[AZURE.IMPORTANT]ロード バランサーの VM は、同じ可用性セットに含まれている必要があります。可用性セットを作成するには、`azure availset create` を使用します。

出力は次のようになります。

	info:    Executing command vm create
	+ Looking up the VM "web1"
	Enter username: azureuser
	Enter password for azureuser: *********
	Confirm password: *********
	info:    Using the VM Size "Standard_A1"
	info:    The [OS, Data] Disk or image configuration requires storage account
	+ Looking up the storage account web1nrp
	+ Looking up the availability set "nrp-avset"
	info:    Found an Availability set "nrp-avset"
	+ Looking up the NIC "lb-nic1-be"
	info:    Found an existing NIC "lb-nic1-be"
	info:    Found an IP configuration with virtual network subnet id "/subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet" in the NIC "lb-nic1-be"
	info:    This is a NIC without publicIP configured
	+ Creating VM "web1"
	info:    vm create command OK

>[AZURE.NOTE]"**This is a NIC without publicIP configured**" という情報メッセージは、想定どおりの動作です。これは、ロード バランサー用に作成される NIC は、ロード バランサーのパブリック IP アドレス経由でインターネットに接続されるためです。

*lb-nic1-be* NIC は *rdp1* NAT 規則に関連付けられているため、ロード バランサーのポート 3441 で RDP を使用して *web1* に接続することができます。

### 手順 4.

*web2* という名前の仮想マシン (VM) を作成し、それを *lb-nic2-be* という名前の NIC に関連付けます。*web1nrp* と呼ばれるストレージ アカウントが次のコマンドを実行する前に作成されました。

	azure vm create --resource-group nrprg --name web2 --location eastus --vnet-	name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## 既存のロード バランサーの更新

既存のロード バランサーを参照する規則を追加できます。次の例では、既存のロード バランサー **NRPlb** に新しいロード バランサー規則を追加しました。

	azure network lb rule create -g nrprg -l nrplb -n lbrule2 -p tcp -f 8080 -b 8051 -t frontendnrppool -o NRPbackendpool

パラメーター:

**-g** - リソース グループ名<br> **-l** - ロード バランサー名<BR> **-n** - ロード バランサーの規則名<BR> **-p** - プロトコル<BR> **-f** - フロント エンド ポート<BR> **-b** - バック エンド ポート<BR> **-t** - フロント エンド プール名<BR> **-b** - バック エンド プール名<BR>

## ロード バランサーの削除 


ロード バランサーを削除するには、次のコマンドを使用します。

	azure network lb delete -g nrprg -n nrplb 

ここで **nrprg** はリソース グループで、**nrplb** はロード バランサーの名前です。

## 次のステップ

[内部ロード バランサーの構成の開始](load-balancer-internal-getstarted.md)

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)

<!---HONumber=Nov15_HO3-->