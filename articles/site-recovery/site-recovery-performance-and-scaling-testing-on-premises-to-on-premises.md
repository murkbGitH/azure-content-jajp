<properties
	pageTitle="Azure Site Recovery: パフォーマンスとスケーリングのテスト: オンプレミス間"
	description="この記事では、オンプレミス間のデプロイで、Azure Site Recovery を使用したレプリケーションのパフォーマンスへの影響に関するテストについて説明します。"
	services="site-recovery"
	documentationCenter=""
	authors="csilauraa"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="10/07/2015"
	ms.author="lauraa"/>

# パフォーマンスとスケーリングのテスト: オンプレミス間

Microsoft Azure Site Recovery は、計画済みおよび計画外の停止の場合に、データをバックアップして復旧できるように、プライマリ データ センターから 2 次拠点へのレプリケーションを調整し、管理します。System Center Virtual Machine Manager (VMM) に配置したオンプレミスのプライベート クラウドをオンプレミスの別の場所に、または Microsoft Azure ストレージにバックアップすることができます。レプリケーションを実行するため、VMM は Windows Server 2012 および Windows Server 2012 R2 の Hyper-V に組み込まれているレプリケーション メカニズムである、Hyper-V レプリカを使用します。2 つのホスト サーバー間での Hyper-V 仮想マシンの非同期レプリケーションを実現します。Hyper-V で仮想化可能なサーバー ワークロードは、すべてレプリケートすることができます。レプリケーションは、通常のあらゆる IP ベースのネットワークで機能します。また、Hyper-V レプリカは、スタンドアロン サーバー、フェールオーバー クラスター、または両方の組み合わせと共に動作します。

このトピックでは、オンプレミス間のデプロイで、Azure Site Recovery を使用したレプリケーションのパフォーマンスへの影響に関するテストについて説明します。テストで使用するパラメーターと構成設定に関する詳細な情報を提供し、テスト デプロイの手順と詳細なテスト結果を示します。

## テストの目的

目的は、安定状態のレプリケーション中の Azure Site Recovery のパフォーマンスを確認することです。安定状態のレプリケーションは、仮想マシンが初期レプリケーションを完了し、差分変更を同期しているときに発生します。予期しない停止が発生しない限り、ほとんどの仮想マシンは安定状態になるため、安定状態を利用してパフォーマンスを測定することが重要です。

## テスト デプロイの実行

テスト デプロイは、それぞれに VMM サーバーをデプロイした 2 つのオンプレミスのサイトで構成しました。両方の VMM サーバーとも、Azure Site Recovery コンテナーに登録されています。このテスト デプロイは、プライマリ サイトとして機能する本店と、セカンダリまたは復旧サイトとして機能するブランチ オフィスで構成される、本店/ブランチ オフィス デプロイの一般的な例です。

### テスト デプロイの手順

1. VMM テンプレートを使用して仮想マシンを作成しました。

1. 仮想マシンを起動し、12 時間にわたって基準のパフォーマンス メトリックを取得しました。

1. プライマリ VMM サーバーおよび復旧 VMM サーバー上にクラウドを作成しました。

1. Azure Site Recovery で、ソース クラウドと復旧クラウドのマッピングを含むクラウドの保護を構成しました。

1. 仮想マシンの保護を有効にして、初期レプリケーションを完了できるようにしました。

1. システムが安定するまで数時間待機しました。

1. 12 時間にわたってパフォーマンス メトリックを取得し、すべての仮想マシンがこの 12 時間 に、期待されたレプリケーションの状態を維持していることを確認しました。

1. 基準となるパフォーマンス メトリックとレプリケーションのパフォーマンス メトリックの差を測定します。

## テスト デプロイの結果

### プライマリ サーバーのパフォーマンス

- Hyper-V レプリカは、プライマリ サーバーでのストレージ オーバーヘッドを最小限にしながら、ログ ファイルへの変更を非同期的に追跡します。

- Hyper-V レプリカは、自己保持型のメモリ キャッシュを活用し、追跡に対する IOPS オーバーヘッドを最小化します。復旧サイトにログを送信する時刻になる前に、メモリ内に VHDX への書き込みを格納し、ログ ファイルにフラッシュします。あらかじめ設定された制限に書き込みが達した場合も、ディスク フラッシュが発生します。

- 次のグラフは、レプリケーションに対する安定状態の IOPS オーバーヘッドを示しています。レプリケーションによる IOPS オーバーヘッドは約 5% と、非常に低いことがわかります。

![プライマリの結果](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744913.png)

Hyper-V レプリカは、ディスク パフォーマンスを最適化するために、プライマリ サーバー上のメモリを利用します。次のグラフに示すように、プライマリ クラスター内にあるすべてのサーバー上のメモリ オーバーヘッドはわずかです。示されているメモリ オーバーヘッドは、Hyper-V サーバーに装着されたメモリの合計と比較した、レプリケーションによって使用されるメモリの割合です。

![プライマリの結果](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744914.png)

Hyper-V レプリカの CPU オーバーヘッドは最小限です。グラフに示すように、レプリケーションのオーバーヘッドは 2 ～ 3% の範囲内です。

![プライマリの結果](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744915.png)

### セカンダリ (復旧) サーバーのパフォーマンス

Hyper-V レプリカが使用する復旧サーバーのメモリは少なく、ストレージ操作の数を最適化できます。グラフは、復旧サーバーのメモリ使用量をまとめたものです。示されているメモリ オーバーヘッドは、Hyper-V サーバーに装着されたメモリの合計と比較した、レプリケーションによって使用されるメモリの割合です。

![セカンダリの結果](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744916.png)

復旧サイトでの I/O 操作数は、プライマリ サイトでの書き込み操作の数と相関関係があります。復旧サイトでの合計 I/O 操作数を、プライマリ サイトでの合計 I/O 操作数および書き込み操作数と比較してみましょう。グラフでは、復旧サイトでの合計 IOPS は次のとおりになっています。

- プライマリ サイトでの書き込み IOPS の約 1.5 倍

- プライマリ サイトでの合計 IOPS の約 37%

![セカンダリの結果](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744917.png)

![セカンダリの結果](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744918.png)

### ネットワーク使用率に対するレプリケーションの影響

プライマリ ノードおよび復旧ノード間で、既存の 1 秒あたり 5 GB の帯域幅に対して、1 秒あたり平均 275MB のネットワーク帯域幅が使用されました (圧縮有効時)。

![結果のネットワーク使用率](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744919.png)

### 仮想マシンのパフォーマンスに対するレプリケーションの影響

重要な考慮事項として、仮想マシンで実行されている運用ワークロードに対するレプリケーションの影響が挙げられます。プライマリ サイトがレプリケーション用に十分にプロビジョニングされている場合は、これらのワークロードに影響はありません。Hyper-V レプリカの軽量な追跡メカニズムにより、安定状態のレプリケーション中に、仮想マシンで実行されているワークロードは影響を受けません。このことを、次のグラフで示します。

このグラフは、レプリケーションを有効にする前と後で、さまざまなワークロードを実行する仮想マシンが実行した IOPS を示しています。2 つの間に差がないことを確認できます。

![レプリカ効果の結果](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744920.png)

次のグラフは、レプリケーションを有効にする前と後で、さまざまなワークロードを実行する仮想マシンのスループットを示しています。レプリケーションが大きな影響を与えないことを確認できます。

![結果のレプリカ効果](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744921.png)

### まとめ

結果では、Hyper-V レプリカと組み合わせた Azure Site Recovery には、大規模なクラスター構成でオーバーヘッドを最小限にする優れた拡張性があることが明確に示されました。Azure Site Recovery は、シンプルなデプロイ、レプリケーション、管理および監視を実現します。Hyper-V レプリカは、レプリケーションを正常にスケーリングするために必要なインフラストラクチャを提供します。最適なデプロイを計画するために、[Hyper-V Replica Capacity Planner](https://www.microsoft.com/ja-jp/download/details.aspx?id=39057) をダウンロードすることをお勧めします。

## テスト デプロイ環境

### プライマリ サイト

- プライマリ サイトは、470 台の仮想マシンを実行している 5 つの Hyper-V サーバーを含むクラスターです。

- 仮想マシンは、さまざまなワークロードを実行し、すべて Azure Site Recovery 保護を有効にしています。

- クラスター ノード用のストレージは、iSCSI SAN により提供されます。モデル – Hitachi HUS130。

- 各クラスター サーバーは、1 Gbps の 4 つのネットワーク カード (NIC) を搭載しています。

- 2 つのネットワーク カードは、iSCSI プライベート ネットワークに接続され、2 つは外部のエンタープライズ ネットワークに接続されています。外部ネットワークのうち 1 つは、クラスター通信専用として予約されています。

![プライマリのハードウェア要件](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744922.png)

|サーバー|RAM|モデル|プロセッサ|プロセッサの数|NIC|ソフトウェア|
|---|---|---|---|---|---|---|
|クラスター内の Hyper-V サーバー: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25|128ESTLAB HOST25 に 256|Dell ™ PowerEdge ™ R820|Intel(R) Xeon(R) CPU E5-4620 0 (2.20 GHz)|4|I Gbps x 4|Windows Server Datacenter 2012 R2 (x64) + Hyper-V ロール|
|VMM サーバー|2|||2|1 Gbps|Windows Server Database 2012 R2 (x64) + VMM 2012 R2|

### セカンダリ (復旧) サイト

- セカンダリ サイトは 6 ノードのフェールオーバー クラスターです。

- クラスター ノード用のストレージは、iSCSI SAN により提供されます。モデル – Hitachi HUS130。

![プライマリのハードウェア仕様](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744923.png)

|サーバー|RAM|モデル|プロセッサ|プロセッサの数|NIC|ソフトウェア|
|---|---|---|---|---|---|---|
|クラスター内の Hyper-V サーバー: <br />ESTLAB HOST07<br />ESTLAB HOST08<br />ESTLAB HOST09<br />ESTLAB HOST10|96|Dell ™ PowerEdge ™ R720|Intel(R) Xeon(R) CPU E5-2630 0 (2.30 GHz)|2|I Gbps x 4|Windows Server Datacenter 2012 R2 (x64) + Hyper-V ロール|
|ESTLAB-HOST17|128|Dell ™ PowerEdge ™ R820|Intel(R) Xeon(R) CPU E5-4620 0 (2.20 GHz)|4||Windows Server Datacenter 2012 R2 (x64) + Hyper-V ロール|
|ESTLAB-HOST24|256|Dell ™ PowerEdge ™ R820|Intel(R) Xeon(R) CPU E5-4620 0 (2.20 GHz)|2||Windows Server Datacenter 2012 R2 (x64) + Hyper-V ロール|
|VMM サーバー|2|||2|1 Gbps|Windows Server Database 2012 R2 (x64) + VMM 2012 R2|

### サーバー ワークロード

- テストのために、企業の顧客のシナリオでよく使用されるワークロードを選択しました。

- シミュレーションの表にまとめたワークロードの特性を利用して、[IOMeter](http://www.iometer.org) を使用しました。

- IOMeter のすべてのプロファイルは、ランダムにバイトを書き込むよう設定して、ワークロードに対して、最悪の場合の書き込みパターンをシミュレートしました。

|ワークロード|I/O サイズ (KB)|アクセスの割合|読み取りの割合|処理待ち I/O 数|I/O パターン|
|---|---|---|---|---|---|
|ファイル サーバー|48163264|60% 20% 5% 5% 10%|80% 80% 80% 80% 80%|88888|すべて 100% ランダム|
|SQL Server (ボリューム 1) SQL Server (ボリューム 2)|864|100% 100%|70% 0%|88|100% ランダム 100% シーケンシャル|
|Exchange|32|100%|67%|8|100% ランダム|
|ワークステーション/VDI|464|66% 34%|70% 95%|11|どちらも 100% ランダム|
|Web ファイル サーバー|4864|33% 34% 33%|95% 95% 95%|888|すべて 75% ランダム|

### 仮想マシンの構成

- プライマリ クラスターに 470 台の仮想マシン。

- すべての仮想マシンに VHDX ディスクを搭載。

- 仮想マシンでは、次の表に要約したワークロードを実行。すべて VMM テンプレートを使用して作成されました。

|ワークロード|VM の数|最小 RAM 容量 (GB)|最大 RAM 容量 (GB)|VM あたりの論理ディスク サイズ (GB)|最大 IOPS|
|---|---|---|---|---|---|
|SQL Server|51|1|4|167|10|
|Exchange Server|71|1|4|552|10|
|ファイル サーバー|50|1|2|552|22|
|VDI|149|0\.5|1|80|6|
|Web サーバー|149|0\.5|1|80|6|
|合計|470|||96\.83 TB|4108|

### Azure Site Recovery 設定

- Azure Site Recovery をオンプレミス間の保護用に構成しました。

- VMM サーバーには、Hyper-V クラスター サーバーとその仮想マシンを含むクラウドが 4 つ構成されています。

|プライマリ VMM クラウド|クラウド内の保護された仮想マシン|レプリケーションの頻度|追加の復旧ポイント|
|---|---|---|---|
|PrimaryCloudRpo15m|142|15 分|なし|
|PrimaryCloudRpo30s|47|30 秒|なし|
|PrimaryCloudRpo30sArp1|47|30 秒|1|
|PrimaryCloudRpo5m|235|5 分|なし|

### パフォーマンス メトリック

この表では、このデプロイにおいて測定されたパフォーマンス メトリックとカウンターの概要を示します。

|メトリック|カウンター|
|---|---|
|CPU|\\Processor(\_Total)\\% Processor Time|
|使用可能なメモリ|\\Memory\\Available MBytes|
|IOPS|\\PhysicalDisk(\_Total)\\Disk Transfers/sec|
|VM 読み取り (IOPS) 操作数/秒|\\Hyper-V Virtual Storage Device(<VHD>)\\Read Operations/Sec|
|VM 書き込み (IOPS) 操作数/秒|\\Hyper-V Virtual Storage Device(<VHD>)\\Write Operations/S|
|VM 読み取りスループット|\\Hyper-V Virtual Storage Device(<VHD>)\\Read Bytes/sec|
|VM 書き込みスループット|\\Hyper-V Virtual Storage Device(<VHD>)\\Write Bytes/sec|


## 次のステップ

ASR のデプロイを開始する際は、次の記事を参照してください。

- [オンプレミスの VMM サイトと Azure 間の保護の設定](site-recovery-vmm-to-azure.md)
- [Set up protection between an on-premises Hyper-V site and Azure (オンプレミスの Hyper-V サイトと Azure 間の保護の設定)](site-recovery-hyper-v-site-to-azure.md)
- [Set up protection between two on-premises VMM sites (2 つのオンプレミスの VMM サイト間の保護の設定)](site-recovery-vmm-to-vmm.md)
- [Set up protection between two on-premises VMM sites with SAN (SAN を使用した 2 つのオンプレミスの VMM サイト間の保護の設定)](site-recovery-vmm-san.md)
- [単一の VMM サーバーを使用した保護の設定](site-recovery-single-vmm.md)
 

<!----HONumber=Oct15_HO3-->