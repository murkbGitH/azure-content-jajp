<properties 
   pageTitle="バックアップの作成"
   description="StorSimple のバックアップ ポリシーを定義する方法について説明します。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/01/2015"
   ms.author="v-sharos" />

### バックアップを作成するには

1. デバイスの **[クイック スタート]** ページで、**[バックアップ ポリシーの追加]** をクリックします。バックアップ ポリシーの追加ウィザードが開きます。 

2. **[バックアップ ポリシーの定義]** ページで次の操作を行います。
  1. バックアップ ポリシーに 3 ～ 150 文字の名前を指定します。
  2. バックアップするボリュームを選択します。複数のボリュームを選択した場合、ボリュームはグループ化され、クラッシュ整合バックアップが作成されます。
  3. 矢印アイコン ![矢印アイコン](./media/storsimple-take-backup/HCS_ArrowIcon-include.png) をクリックします。 
  
    ![バックアップ ポリシーの追加](./media/storsimple-take-backup/HCS_AddBackupPolicyWizard1M-include.png)

3. **[スケジュールの定義]** ページで次の操作を行います。
  1. ドロップダウン リストからバックアップの種類を選択します。短時間で復元するには、**[ローカル スナップショット]** を選択します。データの回復性を求める場合は、**[クラウド スナップショット]** を選択します。
  2. バックアップの頻度を分、時間、日、または週で指定します。
  3. 保存期間を選択します。保存期間の選択肢はバックアップの頻度によって異なります。たとえば、日次のポリシーでは保存期間を日単位で指定できるのに対して、月次のポリシーでは保存期間は月単位で指定します。
  4. バックアップ ポリシーの開始日時を選択します。
  5. **[有効化]** チェック ボックスをオンにして、バックアップ ポリシーを有効にします。 
  6. チェック マーク アイコン ![チェック マーク アイコン](./media/storsimple-take-backup/HCS_CheckIcon-include.png) をクリックして、ポリシーを保存します。

    ![バックアップ ポリシーの追加](./media/storsimple-take-backup/HCS_AddBackupPolicyWizard2M-include.png)
 
     これで、スケジュールに従ってボリューム データをバックアップするバックアップ ポリシーが作成されました。

デバイスの構成が完了しました。



<!--HONumber=52-->