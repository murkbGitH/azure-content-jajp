<properties
   pageTitle="Azure サブスクリプションの譲渡 | Microsoft Azure"
   description="Azure サブスクリプションを別のユーザーに譲渡する方法と、そのプロセスに関してよく寄せられる質問 (FAQ)"
   services="billing"
   documentationCenter=""
   authors="curtand"
   manager="msmStevenPo"
   editor=""/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="08/19/2015"
   ms.author="curtand;ruchic"/>

# Azure サブスクリプションの譲渡

次の点に該当しますか。

- Azure サブスクリプションの所有権を別のユーザーに譲渡する必要がある。
- Azure へのサインアップに使用したアカウントを変更する必要がある。 おそらく、Microsoft アカウントを使用していたが、代わりに職場または学校アカウントを使用する必要がある。
- Azure サブスクリプションをあるディレクトリから別のディレクトリに移動する必要がある。
- 異なるテナントにある Azure と Office 365 を統合する必要がある。

お使いのアカウントが米国のものである場合、従量課金サブスクリプションについては、Microsoft Azure アカウント センターで今すぐ簡単に実行できます。サブスクリプションを別のユーザーに譲渡する機能が追加されました。つまり、所有する任意の従量課金サブスクリプションでアカウント管理者を変更できるようになりました。

## Azure サブスクリプションを譲渡する方法

1.  <https://account.windowsazure.com/Subscriptions> でサインインします。

2.  譲渡するサブスクリプションを選択します。

3.  **[サブスクリプションの譲渡]** オプションをクリックします。

    ![Azure account subscriptions tab](./media/billing-subscription-transfer/image1.png)

4.  指示に従って、譲渡先を指定します。

    ![Transfer Subscription dialog box](./media/billing-subscription-transfer/image2.PNG)

5.  譲渡先には、承認用のリンクが記載された電子メールが自動的に送信されます。

    ![Subscription transfer email to recipient](./media/billing-subscription-transfer/image3.png)

6.  譲渡先のユーザーは、リンクをクリックして指示に従います (支払情報の入力など)。

    ![First subscription transfer web page](./media/billing-subscription-transfer/image4.PNG)

    ![Second subscription transfer web page](./media/billing-subscription-transfer/image5.PNG)

7. 成功です。 サブスクリプションが譲渡されました。

## よく寄せられる質問 (FAQ)

-   **サブスクリプションの譲渡により、サービスのダウンタイムは発生しますか。**

    サービスへの影響はありません。この譲渡により、現在のアカウント管理者にあるサブスクリプションが事実上取り消され、譲渡先のアカウントに新しいサブスクリプションが作成されます。ただし、基になる Azure サービスが新しいサブスクリプションに関連付けられます。サブスクリプション ID は変わりません。

-   **サブスクリプションの所有権を別の組織から引き継ぐ場合、その組織のユーザーが引き続きこのリソースにアクセスすることはできますか。**

    サブスクリプションが別のテナントに譲渡されると、以前のテナントに関連付けられているユーザーはそのサブスクリプションにアクセスできなくなります。現在サービス管理者または共同管理者ではないユーザーでも、他のセキュリティ メカニズムを介してまだサブスクリプションにアクセスできることがあります。このメカニズムには次のものが含まれます。- サブスクリプションのリソースに対する管理者権限をユーザーに付与する管理証明書。詳細については、「[Azure の管理証明書の作成とアップロード](https://msdn.microsoft.com/library/azure/gg551722.aspx)」を参照してください。- Storage などのサービスのアクセス キー。詳細については、「[ストレージ アクセス キーの表示、コピーおよび再生成](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)」を参照してください。- Azure Virtual Machines などのサービスのリモート アクセス資格情報。

    これですべてではありません。譲渡先では、リソースへのアクセスの制限を必要としている場合、サービスに関連付けられているすべてのシークレットの更新を検討する必要があります。ほとんどのリソースを次の手順で更新できます。

    1.   Azure ポータル [**https://portal.azure.com*](https://portal.azure.com) にアクセスします。

    2.    [すべてを参照]、[すべてのリソース] の順にクリックします。

    3.    リソースを選択します。リソースのブレードが開きます。

    4.    リソースのブレードで **[設定]** をクリックします。ここで、既存のシークレットを表示して更新できます。


-   **請求サイクルの途中でサブスクリプションを譲渡した場合、譲渡先が請求サイクル全体の料金を支払うことになりますか。**

    譲渡元は、譲渡が完了した時点までに報告されたすべての使用量に対して支払う責任があります。譲渡先は、譲渡した時点以降に報告された使用量に対して責任を負います。譲渡する前に発生したにもかかわらず、譲渡後に報告される使用量もあります。これは、譲渡先の請求書に含まれます。

-   **譲渡先は、使用履歴と請求履歴にアクセスできますか。**

    現時点では、譲渡先に示される情報は、最後の請求書の金額 (最初の請求書が生成される前にサブスクリプションが譲渡された場合は現在の残高) のみです。使用履歴と請求履歴の他の部分は、サブスクリプションと共に譲渡されることはありません。

-   **譲渡時にプランを変更できますか。**

    プランはそのままにしておく必要があります。プランを変更するには、[サポートにお問い合わせ](http://go.microsoft.com/fwlink/?LinkID=619338)ください。

-   **他の国のユーザー アカウントにサブスクリプションを譲渡できますか。**

    いいえ、現時点ではサポートされていません。譲渡先のユーザー アカウントは、同じ国に属している必要があります。

-   **譲渡先で別の支払いメカニズムを使用できますか。**

    はい。実際には、このメカニズムを使用して、サブスクリプションの支払方法を請求書からクレジット カードに変更することができます。所有する別のアカウントに譲渡して、サブスクリプションの受け取り時にクレジット カードを入力するだけです。これには制限があり、この場合、サブスクリプションの請求履歴は 2 つのアカウントに分かれます。ただし、[サポートに問い合わせ](http://go.microsoft.com/fwlink/?LinkID=619338)なくても実行できるという利点があります。

## サブスクリプションの所有権を受け取った後の次のステップ

1. アカウント管理者になったら、サービス管理者と共同管理者を見直して更新します。管理者の管理は、[Microsoft Azure 管理ポータル](https://manage.windowsazure.com)で [設定] に移動して実行します。[詳細情報](http://go.microsoft.com/fwlink/?LinkID=533293) 
2. サブスクリプションとサービスに対して、ロール ベースのアクセス制御 (RBAC) を使用することもできます。[Azure プレビュー ポータル](https://portal.azure.com)に関するページおよび「[Microsoft Azure ポータルでのロールベースのアクセス制御](http://go.microsoft.com/fwlink/?LinkID=544802)」を参照してください。
3. このサブスクリプションのサービスに関連付けられている資格情報を更新します。チェックの内容は次のとおりです 
    -   サブスクリプションのリソースに対する管理者権限をユーザーに付与する管理証明書。詳細については、「[Azure の管理証明書の作成とアップロード](https://msdn.microsoft.com/library/azure/gg551722.aspx)」をご覧ください。
    -	Storage などのサービス用のアクセス キー。詳細については、「[ストレージのアクセス キーを表示、コピー、および再生成する](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)」を参照してください。
    -	Azure Virtual Machines などのサービス用のリモート アクセス資格情報
4. このサブスクリプション用の課金アラートを、[Azure アカウント センター](https://account.windowsazure.com/Subscriptions)で更新します。[詳細情報](http://go.microsoft.com/fwlink/?LinkID=533292)
5. 	パートナーがいる場合は、このサブスクリプションのパートナー ID を更新することを検討します。この操作は [Azure アカウント センター](https://account.windowsazure.com/Subscriptions)で実行できます。

<!---HONumber=Sept15_HO2-->