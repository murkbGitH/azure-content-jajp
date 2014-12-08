<properties urlDisplayName="Upload a SUSE Linux VHD" pageTitle="Azure 上での SUSE Linux VHD の作成とアップロード" metaKeywords="Azure VHD, uploading Linux VHD, SUSE, SLES, openSUSE" description="SUSE Linux オペレーティング システムを格納した Azure 仮想ハード ディスク (VHD) を作成してアップロードする方法について説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="SUSE Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="06/05/2014" ms.author="kathydav, szarkos" />

# Azure 用の SLES または openSUSE 仮想マシンの準備

-   [Azure 用の SLES 11 SP3 仮想マシンの準備][Azure 用の SLES 11 SP3 仮想マシンの準備]
-   [Azure 用の openSUSE 13.1 以上の仮想マシンの準備][Azure 用の openSUSE 13.1 以上の仮想マシンの準備]

## 前提条件

この記事では、既に SUSE または openSUSE Linux オペレーティング システムを仮想ハード ディスクにインストールしていることを前提にしています。.vhd ファイルを作成するツールは、たとえば、Hyper-V などの仮想化ソリューションなど複数あります。その手順については、「[Hyper-V の役割のインストールと仮想マシンの構成][Hyper-V の役割のインストールと仮想マシンの構成]」を参照してください。

**SLES/openSUSE のインストールに関する一般的な注記**

-   [SUSE Studio][SUSE Studio] では、Azure および Hyper-V 用の SLES/openSUSE イメージを簡単に作成、管理できます。これは、独自の SLES イメージと openSUSE イメージをカスタマイズするアプローチとして推奨されます。SUSE Studio ギャラリーにある次の公式イメージをダウンロードまたは SUSE Studio アカウントに複製できます。

-   [SUSE Studio ギャラリーの Azure 向け SLES 11 SP3][SUSE Studio ギャラリーの Azure 向け SLES 11 SP3]
-   [SUSE Studio ギャラリーの Azure 向け openSUSE 13.1][SUSE Studio ギャラリーの Azure 向け openSUSE 13.1]

-   新しい VHDX 形式は、Azure ではサポートされていません。Hyper-V マネージャーまたは convert-vhd コマンドレットを使用して、ディスクを VHD 形式に変換できます。

-   Linux システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することをお勧めします。これにより、特に OS ディスクをトラブルシューティングのために別の VM に接続する必要がある場合に、LVM 名と複製された VM の競合が回避されます。必要な場合は、LVM または [RAID][RAID] をデータ ディスク上で使用できます。

-   OS ディスクにスワップ パーティションを構成しないでください。Linux エージェントは、一時的なリソース ディスク上にスワップ ファイルを作成するよう構成できます。このことに関する詳細については、次の手順を参照してください。

-   すべての VHD のサイズは 1 MB の倍数であることが必要です。

## <span id="sles11"></span> </a>SUSE Linux Enterprise Server 11 SP3 を準備する

1.  Hyper-V マネージャーの中央のウィンドウで仮想マシンを選択します。

2.  **[接続]** をクリックすると、仮想マシンのウィンドウが開きます。

3.  最新のカーネルおよび Azure Linux エージェントが格納されているリポジトリを追加します。コマンド `zypper lr` を実行します。たとえば、SLES 11 SP3 の出力は次のようになります。

        # | Alias                        | Name               | Enabled | Refresh
        --+------------------------------+--------------------+---------+--------
        1 | susecloud:SLES11-SP1-Pool    | SLES11-SP1-Pool    | No      | Yes
        2 | susecloud:SLES11-SP1-Updates | SLES11-SP1-Updates | No      | Yes
        3 | susecloud:SLES11-SP2-Core    | SLES11-SP2-Core    | No      | Yes
        4 | susecloud:SLES11-SP2-Updates | SLES11-SP2-Updates | No      | Yes
        5 | susecloud:SLES11-SP3-Pool    | SLES11-SP3-Pool    | Yes     | Yes
        6 | susecloud:SLES11-SP3-Updates | SLES11-SP3-Updates | Yes     | Yes

    このコマンドでは、次のようなエラー メッセージを返すことがあります。

        "No repositories defined. Use the 'zypper addrepo' command to add one or more repositories."

    その場合は、次のコマンドを実行してこれらのリポジトリを追加します。

        # sudo zypper ar -f http://azure-update.susecloud.net/repo/$RCE/SLES11-SP3-Pool/sle-11-x86_64 SLES11-SP3-Pool 
        # sudo zypper ar -f http://azure-update.susecloud.net/repo/$RCE/SLES11-SP3-Updates/sle-11-x86_64 SLES11-SP3-Updates

    更新したリポジトリのいずれかが有効になっていない場合は、次のコマンドを使用して有効にします。

        # sudo zypper mr -e [REPOSITORY NUMBER]

4.  カーネルを最新のバージョンに更新します。

        # sudo zypper up kernel-default

    または、次のように、すべての最新のパッチでシステムを更新します。

        # sudo zypper update

5.  Azure Linux エージェントをインストールします。

        # sudo zypper install WALinuxAgent

6.  GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。これを行うには、テキスト エディターで "/boot/grub/menu.lst" を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。

7.  "/etc/sysconfig/network/dhcp" ファイルを編集して、次のように `DHCLIENT_SET_HOSTNAME` パラメーターを変更することをお勧めします。

        DHCLIENT_SET_HOSTNAME="no"

8.  "/etc/sudoers" で、次の行をコメント アウトするか削除する必要があります (ある場合)。

        Defaults targetpw   # ask for the password of the target user i.e. root
        ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.  SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。通常これが既定です。

10. OS ディスクにスワップ領域を作成しないでください。

    Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。ローカル リソース ディスクは*一時*ディスクであるため、仮想マシンのプロビジョニングが解除されると空になることに注意してください。Azure Linux エージェントのインストール後に (前の手順を参照)、/etc/waagent.conf にある次のパラメーターを適切に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

12. Hyper-V マネージャーで **[アクション] -\> [シャットダウン]** をクリックします。これで、Linux VHD を Azure にアップロードする準備が整いました。

------------------------------------------------------------------------

## <span id="osuse"></span> </a>openSUSE 13.1 以上の準備

1.  Hyper-V マネージャーの中央のウィンドウで仮想マシンを選択します。

2.  **[接続]** をクリックすると、仮想マシンのウィンドウが開きます。

3.  シェルでコマンド "`zypper lr`" を実行します。このコマンドによって次のような出力が返されます (バージョン番号は異なる場合があります)。

        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes

    この場合、リポジトリは予期したとおりに構成されているため、調整は不要です。

    コマンドによって "No repositories defined..." が返された場合は、次のコマンドを実行してこれらのリポジトリを追加します。

        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1 
        # sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates

    '`zypper lr`' コマンドをもう一度実行してリポジトリが追加されたことを確認できます。更新したリポジトリのいずれかが有効になっていない場合は、次のコマンドを使用して有効にします。

        # sudo zypper mr -e [NUMBER OF REPOSITORY]

4.  カーネルを最新のバージョンに更新します。

        # sudo zypper up kernel-default

    または、次のように、すべての最新のパッチでシステムを更新します。

        # sudo zypper update

5.  Azure Linux エージェントをインストールします。

        # sudo zypper install WALinuxAgent

6.  GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。これを行うには、テキスト エディターで "/boot/grub/menu.lst" を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。また、カーネルのブート行に次のパラメーターがある場合は削除します。

        libata.atapi_enabled=0 reserve=0x1f0,0x8

7.  "/etc/sysconfig/network/dhcp" ファイルを編集して、次のように `DHCLIENT_SET_HOSTNAME` パラメーターを変更することをお勧めします。

        DHCLIENT_SET_HOSTNAME="no"

8.  **重要:**"/etc/sudoers" で、次の行をコメント アウトするか削除する必要があります (ある場合)。

        Defaults targetpw   # ask for the password of the target user i.e. root
        ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.  SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。通常これが既定です。

10. OS ディスクにスワップ領域を作成しないでください。

    Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。ローカル リソース ディスクは*一時*ディスクであるため、仮想マシンのプロビジョニングが解除されると空になることに注意してください。Azure Linux エージェントのインストール後に (前の手順を参照)、/etc/waagent.conf にある次のパラメーターを適切に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

12. 起動時に Azure Linux エージェントが実行されるようにします。

        # sudo systemctl enable waagent.service

13. Hyper-V マネージャーで **[アクション] -\> [シャットダウン]** をクリックします。これで、Linux VHD を Azure にアップロードする準備が整いました。

  [Azure 用の SLES 11 SP3 仮想マシンの準備]: #sles11
  [Azure 用の openSUSE 13.1 以上の仮想マシンの準備]: #osuse
  [Hyper-V の役割のインストールと仮想マシンの構成]: http://technet.microsoft.com/library/hh846766.aspx
  [SUSE Studio]: http://www.susestudio.com
  [SUSE Studio ギャラリーの Azure 向け SLES 11 SP3]: http://susestudio.com/a/02kbT4/sles-11-sp3-for-windows-azure
  [SUSE Studio ギャラリーの Azure 向け openSUSE 13.1]: https://susestudio.com/a/02kbT4/opensuse-13-1-for-windows-azure
  [RAID]: ../virtual-machines-linux-configure-raid