﻿
<properties 
    pageTitle="RemoteApp イメージの要件"
    description="RemoteApp で使用するイメージを作成するための要件について説明します。" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/19/2015" 
    ms.author="elizapo" />



# RemoteApp イメージの要件
RemoteApp では Windows Server 2012 R2 イメージを使用して、ユーザーと共有するすべてのプログラムをホストします。RemoteApp のカスタム イメージを作成するには、既存のイメージを使用することも、[新しいイメージを作成](remoteapp-create-custom-image.md)することもできます。Azure RemoteApp で使用するためにアップロードできるイメージの要件は次のとおりです。


- カスタム アプリケーションは、イメージ上にローカルにデータを格納しません。これらのイメージはステートレスで、アプリケーションのみを含む必要があります。
- イメージには、失われる可能性があるデータは含まれません。
- イメージのサイズは MB の倍数にする必要があります。正確な倍数ではないイメージをアップロードしようとすると、アップロードは失敗します。
- イメージのサイズは 127 GB 未満でなければなりません。 
- VHD ファイルになければなりません (VHDX ファイルは現在サポートされていません)。
- VHD は、第 2 世代仮想マシンであってはなりません。
- VHD は固定サイズにすることも、動的に拡大する容量可変にすることも可能です。固定サイズの VHD より Azure へのアップロードの所要時間が短いことから、容量可変の VHD が推奨されます。
- ディスクはマスター ブート レコード (MBR) パーティション分割のスタイルを使用して初期化しなければなりません。GUID パーティション テーブル (GPT) パーティション分割のスタイルはサポートされていません。 
- VHD には Windows Server 2012 R2 の単一インストールが含まれていなければなりません。複数のボリュームを含むことはできますが、Windows がインストールされるのは 1 ボリュームのみです。 
- リモート デスクトップ セッション ホスト (RDSH) ロールとデスクトップ エクスペリエンスの機能がインストール済みでなければなりません。
- リモート デスクトップ接続ブローカー ロールはインストール *しないでください*。
- 暗号化ファイル システム (EFS) は無効にする必要があります。
- イメージはパラメーター **/oobe /generalize /shutdown** を使用して SYSPREP を実施済みでなければなりません (**/mode:vm** パラメーターは使用しないでください)。
- スナップショット チェーンからの VHD のアップロードはサポートされていません。


<!--HONumber=52-->