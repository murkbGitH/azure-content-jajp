<properties 
    pageTitle="チュートリアル: Azure Active Directory と ArcGIS の統合 | Microsoft Azure" 
    description="Azure Active Directory で ArcGIS を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#チュートリアル: Azure Active Directory と ArcGIS の統合

このチュートリアルでは、Azure と ArcGIS の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   ArcGIS でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、ArcGIS に割り当てた Azure AD ユーザーは、ArcGIS 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  ArcGIS のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-arcgis-tutorial/IC784735.png "シナリオ")
##ArcGIS のアプリケーション統合の有効化

このセクションでは、ArcGIS のアプリケーション統合を有効にする方法について説明します。

###ArcGIS のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-arcgis-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-arcgis-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-arcgis-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-arcgis-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**ArcGIS**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-arcgis-tutorial/IC784736.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[ArcGIS]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![ArcGIS](./media/active-directory-saas-arcgis-tutorial/IC784737.png "ArcGIS")
##シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで ArcGIS に対する認証を行えるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **ArcGIS** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-arcgis-tutorial/IC784738.png "シングル サインオンの構成")

2.  **[ユーザーの ArcGIS へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-arcgis-tutorial/IC784739.png "シングル サインオンの構成")

3.  **[アプリケーション URL の構成]** ページで、**[ArcGIS サインイン URL]** ボックスに、ユーザーがサインインに使用する URLを "**https://company.maps.arcgis.com*" のパターンで入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-arcgis-tutorial/IC784740.png "アプリケーション URL の構成")

4.  **[ArcGIS でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、メタデータ ファイルをコンピューターのローカルに保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-arcgis-tutorial/IC784741.png "シングル サインオンの構成")

5.  別の Web ブラウザーのウィンドウで、管理者として ArcGIS 企業サイトにログインします。

6.  **[Edit Settings]** をクリックします。

    ![Edit Settings](./media/active-directory-saas-arcgis-tutorial/IC784742.png "Edit Settings")

7.  **[セキュリティ]** をクリックします。

    ![Security](./media/active-directory-saas-arcgis-tutorial/IC784743.png "Security")

8.  **[Enterprise Logins]** で、**[Set Identity Provider]** をクリックします。

    ![Enterprise Logins](./media/active-directory-saas-arcgis-tutorial/IC784744.png "Enterprise Logins")

9.  **[Set Identity Provider]** 構成ページで、次の手順を実行します。

    ![Set Identity Provider](./media/active-directory-saas-arcgis-tutorial/IC784745.png "Set Identity Provider")

    1.  [Name] テキスト ボックスに、組織の名前を入力します。
    2.  **[Metadata for the Enterprise Identity Provider will be supplied using]** で、**[A File]** を選択します。
    3.  **[Choose File]** をクリックして、ダウンロードしたメタデータ ファイルをアップロードします。
    4.  **[Set Identity Provider]** をクリックします。

10. Azure AD ポータルで、シングル サインオンの構成確認を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-arcgis-tutorial/IC784746.png "シングル サインオンの構成")
##ユーザー プロビジョニングの構成

Azure AD ユーザーが ArcGIS にログインできるようにするには、そのユーザーを ArcGIS にプロビジョニングする必要があります。  
ArcGIS の場合、プロビジョニングは手動で行います。

###ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  **ArcGIS** テナントにログインします。

2.  **[Invite Members]** をクリックします。

    ![Invite Members](./media/active-directory-saas-arcgis-tutorial/IC784747.png "Invite Members")

3.  **[Add members automatically without sending an email]** を選択し、**[Next]** をクリックします。

    ![Add Members Automatically](./media/active-directory-saas-arcgis-tutorial/IC784748.png "Add Members Automatically")

4.  **[Members]** ダイアログ ページで、次の手順を実行します。

    ![Add and review](./media/active-directory-saas-arcgis-tutorial/IC784749.png "Add and review")

    1.  プロビジョニングする有効な AAD アカウントの **[First Name]**、**[Last Name]**、**[Email]** を入力します。
    2.  **[Add And Review]** をクリックします。

5.  入力したデータを確認してから、**[Add Members]** をクリックします。

    ![Add member](./media/active-directory-saas-arcgis-tutorial/IC784750.png "Add member")

>[AZURE.NOTE]他の ArcGIS ユーザー アカウントの作成ツールまたは ArcGIS から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを ArcGIS に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **ArcGIS** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-arcgis-tutorial/IC784751.png "ユーザーの割り当て")

3.  テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。

    ![あり](./media/active-directory-saas-arcgis-tutorial/IC767830.png "あり")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=Nov15_HO1-->