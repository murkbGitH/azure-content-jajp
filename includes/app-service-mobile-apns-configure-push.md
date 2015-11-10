
1.  お使いの Mac で、**Keychain Access** を起動します。**[分類]** の **[自分の証明書]** を開きます。エクスポートする SSL 証明書 (事前にダウンロードしたもの) を探して、その内容を開示します。秘密鍵を選択せずに証明書のみを選択し、それを[書き出し](https://support.apple.com/kb/PH20122?locale=en_US)ます。

2. Azure ポータルで、**[すべて参照]**、**[Mobile Apps]**、バックエンド、**[設定]**、**[モバイル アプリ]**、**[プッシュ]**、**[必要な設定の構成]**、**[+ 通知ハブ]** の順にクリックし、通知ハブの名前と名前空間を指定してから、**[OK]** をクリックします。

  ![][1]

3. **[通知ハブの作成]** ブレードで、**[作成]** をクリックします。
     
    次の手順に進む前に、**[通知]** をクリックし、通知ハブのセットアップが完了していることを確認します。 
4. Azure ポータルで、**[すべて参照]**、**[Mobile Apps]**、バックエンド、**[設定]**、**[モバイル アプリ]**、**[プッシュ]**、**[Apple プッシュ通知サービス]**、**[証明書のアップロード]** の順にクリックします。(生成したクライアント SSL 証明書が開発用か配布用かに応じて) 適切な **[モード]** を選択し、.p12 ファイルをアップロードします。 これで、iOS のプッシュ通知と連携するようにサービスが構成されました。

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png

<!---HONumber=Nov15_HO1-->