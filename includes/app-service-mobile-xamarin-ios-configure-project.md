###Xamarin Studio での操作

1. Xamarin.Studio で、**Info.plist** を開き、前に作成した ID で **[Bundle Identifier]** を更新します。

    ![][88]

2. **[Background Modes]** まで下へスクロールし、**[Enable Background Modes]** と **[Remote notifications]** の各チェック ボックスをオンにします。

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)

3. ソリューション パネルでプロジェクトをダブルクリックし、**[Project Options]** を開きます。

4.  **[Build]** で **[iOS Bundle Signing]** を選択し、対応する **ID** とこのプロジェクトに対して設定した**プロビジョニング プロファイル**を選択します。

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

    これで、プロジェクトではコード署名のために新しいプロファイルを使用するようになります。公式の Xamarin デバイス プロビジョニングのドキュメントについては、[Xamarin デバイス プロビジョニング]に関するページを参照してください。

### Visual Studio で使用する

1. Visual Studio で、プロジェクトを右クリックし、**[プロパティ]** をクリックします。

3. プロパティ ページで、**[iOS Application]** タブをクリックし、前に作成した ID で **[Identifier]** を更新します。

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)

4. **[iOS Bundle Signing]** タブで、対応する **[Identity]** と、このプロジェクトに対して設定した **[Provisioning profile]** を選択します。

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    これで、プロジェクトではコード署名のために新しいプロファイルを使用するようになります。公式の Xamarin デバイス プロビジョニングのドキュメントについては、[Xamarin デバイス プロビジョニング]に関するページを参照してください。

[88]:./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png

[Xamarin デバイス プロビジョニング]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/

<!---HONumber=Oct15_HO3-->