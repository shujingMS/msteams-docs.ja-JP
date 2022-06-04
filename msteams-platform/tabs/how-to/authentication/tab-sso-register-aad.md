---
title: Azure AD にタブ アプリを登録する
description: Azure AD へのタブ アプリの登録について説明します
ms.topic: how-to
ms.localizationpriority: medium
keywords: teams 認証タブ Microsoft Azure Active Directory (Azure AD) アクセス トークン SSO テナント スコープ
ms.openlocfilehash: e508e80f4e2c881e848f628a12392e6ced5e6f4b
ms.sourcegitcommit: e16b51a49756e0fe4eaf239898e28d3021f552da
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2022
ms.locfileid: "65888201"
---
# <a name="register-your-app-in-azure-ad"></a>Azure AD にアプリを登録する

Azure AD では、アプリ ユーザーの Teams ID に基づいてタブ アプリにアクセスできます。 Teams にサインインしたアプリ ユーザーにタブ アプリへのアクセス権を付与できるように、タブ アプリを Azure AD に登録する必要があります。

## <a name="enabling-sso-on-azure-ad"></a>Azure AD での SSO の有効化

Azure AD にタブ アプリを登録し、SSO を有効にするには、アプリの構成 (アプリ ID の生成、API スコープの定義、信頼されたアプリケーションのクライアント ID の事前承認など) が必要です。

:::image type="content" source="../../../assets/images/authentication/teams-sso-tabs/register-azure-ad.png" alt-text="Teams クライアント アプリにアクセス トークンを送信するように Azure AD を構成する" border="false":::

Azure AD で新しいアプリ登録を作成し、スコープ (アクセス許可) を使用してその (Web) API を公開します。 Azure AD で公開されている API とアプリの間の信頼関係を構成します。 これにより、Teams クライアントは、アプリケーションとログイン ユーザーに代わってアクセス トークンを取得できます。 事前承認する信頼できるモバイル、デスクトップ、および Web アプリケーションのクライアント ID を追加できます。

また、タブ アプリをターゲットにするプラットフォームまたはデバイスでアプリ ユーザーを認証するなど、追加の詳細を構成する必要がある場合もあります。

ユーザー レベルの Graph API アクセス許可 (電子メール、プロファイル、offline_access、OpenId) がサポートされています。 追加の Graph スコープ (たとえば`User.Read``Mail.Read`、Graph スコープなど) へのアクセスが必要な場合は、「[Graph アクセス許可を持つアクセス トークンを取得する](tab-sso-graph-api.md)」を参照してください。

Azure AD 構成では、Teams でタブ アプリの SSO が有効になります。 アプリ ユーザーを検証するためのアクセス トークンで応答します。

> [!NOTE]
> Microsoft Teams Toolkit は、SSO プロジェクトに Azure AD アプリケーションを登録します。

### <a name="before-you-register-with-azure-ad"></a>Azure AD に登録する前に

事前に Azure AD にアプリを登録するための構成について学習しておくと便利です。 アプリを登録する前に、次の詳細を構成する準備が整っていることを確認します。

- **単一テナントまたはマルチテナント オプション**: アプリケーションは、登録されている Microsoft 365 テナントでのみ使用されますか、または多くの Microsoft 365 テナントで使用されますか? 1 つのエンタープライズ用に作成されたアプリケーションは、通常はシングルテナントです。独立系ソフトウェア ベンダーによって作成され、多くの顧客が使用するアプリケーションは、各顧客のテナントがアプリケーションにアクセスできるようにマルチテナントである必要があります。
- **アプリケーション ID URI**: これは、スコープを介してアプリのアクセスのために公開する Web API を識別するグローバルに一意の URI です。 識別子 URI とも呼ばれます。 アプリケーション ID URI には、アプリ ID と、アプリがホストされているサブドメインが含まれます。 アプリケーションのドメイン名と、Azure AD アプリケーションに登録するドメイン名は同じである必要があります。 現在、アプリごとに複数のドメインはサポートされていません。
- **スコープ**: これは、承認されたアプリ ユーザーまたはアプリが API によって公開されているリソースにアクセスするために付与できるアクセス許可です。

> [!NOTE]
>
> - **LOB アプリケーション**: 組織は、Microsoft Store を通じて LOB アプリケーションを利用できるようにします。 これらのアプリは、組織に合わせてカスタムです。 これらは、組織内または企業内で内部または固有です。
> - **顧客所有のアプリ**: SSO は、Azure AD B2C テナント内の顧客所有アプリでもサポートされています。

SSO を有効にするために Azure AD でアプリを作成して構成するには:

- [Azure AD アプリを登録して構成します。](#create-an-app-registration-in-azure-ad)
- [アクセス トークンのスコープを構成します。](#configure-scope-for-access-token)
- [アクセス トークンのバージョンを構成します。](#configure-access-token-version)

## <a name="create-an-app-registration-in-azure-ad"></a>Azure AD でアプリ登録を作成する

Azure AD に新しいアプリを登録し、テナントとアプリのプラットフォームを構成します。 Teams アプリ マニフェスト ファイルで後で更新される新しいアプリ ID を生成します。

### <a name="to-register-a-new-app-in-azure-ad"></a>Azure AD に新しいアプリを登録するには

1. Web ブラウザーで [Azure portal](https://ms.portal.azure.com/) を開きます。
   Microsoft Azure AD Portal ページが開きます。

2. **[アプリの登録**] アイコンを選択します。

   :::image type="content" source="../../../assets/images/authentication/teams-sso-tabs/azure-portal.png" alt-text="Azure AD Portal ページ。" border="true":::

   **[アプリの登録]** ページが表示されます。

3. [ **+ 新しい登録** ] アイコンを選択します。

    :::image type="content" source="../../../assets/images/authentication/teams-sso-tabs/app-registrations.png" alt-text="Azure AD Portal の新しい登録ページ。" border="true":::

    **[アプリケーション登録]** ページが表示されます。

4. アプリ ユーザーに表示するアプリの名前を入力します。 この名前は、必要に応じて後の段階で変更できます。

    :::image type="content" source="../../../assets/images/authentication/teams-sso-tabs/register-app.png" alt-text="Azure AD Portal の [アプリの登録] ページ。" border="true":::

5. アプリにアクセスできるユーザー アカウントの種類を選択します。 シングル テナントまたはマルチテナント オプション、またはプライベート Microsoft アカウントから選択できます。

    <details>
    <summary><b>サポートされているアカウントの種類のオプション</b></summary>

    | オプション | これを選択します... |
    | --- | --- |
    | この組織ディレクトリ内のアカウントのみ (Microsoft のみ - シングル テナント) | テナント内のユーザー (またはゲスト) のみが使用するアプリケーションをビルドします。 <br> LOB アプリケーションと呼ばれることがよくあります。このアプリは、Microsoft ID プラットフォームのシングルテナント アプリケーションです。 |
    | 任意の組織ディレクトリ内のアカウント (任意の Azure AD ディレクトリ - マルチテナント) | 任意の Azure AD テナントのユーザーがアプリケーションを使用できるようにします。 このオプションは、たとえば SaaS アプリケーションを構築していて、複数の組織で使用できるようにする場合に適しています。 <br> この種類のアプリは、Microsoft ID プラットフォームのマルチテナント アプリケーションと呼ばれます。|
    | 任意の組織ディレクトリ (任意の Azure AD ディレクトリ - マルチテナント) および個人用 Microsoft アカウントのアカウント | 最も幅広い顧客のセットをターゲットにします。 <br> このオプションを選択すると、個人の Microsoft アカウントを持つアプリ ユーザーもサポートできるマルチテナント アプリケーションを登録します。 |
    | 個人用 Microsoft アカウントのみ | 個人の Microsoft アカウントを持つユーザー専用のアプリケーションをビルドします。 |

    </details>

    > [!NOTE]
    > タブ アプリの SSO を有効にするための **リダイレクト URI** を入力する必要はありません。

7. **[登録]** を選択します。
    アプリが作成されたことを示すメッセージがブラウザーに表示されます。

    :::image type="content" source="../../../assets/images/authentication/teams-sso-tabs/app-created-msg.png" alt-text="Azure AD Portal にアプリを登録します。" border="true":::

    アプリ ID とその他の構成を含むページが表示されます。

    :::image type="content" source="../../../assets/images/authentication/teams-sso-tabs/tab-app-created.png" alt-text="アプリの登録が成功しました。" border="true":::

8. **アプリケーション (クライアント)** ID からアプリ ID をメモして保存します。 後で Teams アプリ マニフェストを更新するために必要になります。

    アプリは Azure AD に登録されています。 これで、タブ アプリのアプリ ID が作成されます。

## <a name="configure-scope-for-access-token"></a>アクセス トークンのスコープを構成する

新しいアプリ登録を作成したら、Teams クライアントにアクセス トークンを送信し、信頼されたクライアント アプリケーションを承認して SSO を有効にするためのスコープ (アクセス許可) オプションを構成します。

スコープを構成し、信頼されたクライアント アプリケーションを承認するには、次のものが必要です。

- [API を公開するには](#to-expose-an-api):アプリのスコープ (アクセス許可) オプションを構成します。 Web API を公開し、アプリケーション ID URI を構成します。
- [API スコープを構成するには:API](#to-configure-api-scope) のスコープと、スコープに同意できるユーザーを定義します。 管理者のみが、より高い特権を持つアクセス許可に対する同意を提供できます。
- [承認されたクライアント アプリケーションを構成するには](#to-configure-authorized-client-application):事前承認するアプリケーションの承認されたクライアント ID を作成します。 これにより、アプリ ユーザーは、追加の同意を必要とせずに、構成したアプリ スコープ (アクセス許可) にアクセスできます。 アプリ ユーザーが同意を拒否する機会がないため、信頼できるクライアント アプリケーションのみを事前に承認します。

### <a name="to-expose-an-api"></a>API を公開するには

1. 左側のウィンドウで [**API の公開** の **管理** > ] を選択します。

    :::image type="content" source="../../../assets/images/authentication/teams-sso-tabs/expose-api-menu.png" alt-text="API メニュー オプションを公開します。" border="true":::

    [ **API の公開]** ページが表示されます。

1. **[Set]\(設定\)** を選択して、アプリケーション ID URI を次の`api://{AppID}`形式で生成します。

    :::image type="content" source="../../../assets/images/authentication/teams-sso-tabs/expose-an-api.png" alt-text="アプリ ID URI を設定する" border="true":::

    アプリケーション ID URI を設定するためのセクションが表示されます。

1. ここで説明する形式でアプリケーション ID URI を入力します。

    :::image type="content" source="../../../assets/images/authentication/teams-sso-tabs/set-app-id-uri.png" alt-text="アプリケーション ID URI" border="true":::

    - **アプリケーション ID URI** には、アプリ ID (GUID) が形式`api://{AppID}`で事前に入力されています。
    - アプリケーション ID URI の形式は次のようになります `api://fully-qualified-domain-name.com/{AppID}`。
    - 間と `fully-qualified-domain-name.com` `{AppID}` (`api://`つまり GUID) を挿入します。 たとえば、api://example.com/{AppID}。

    どこ
    - `fully-qualified-domain-name.com` は、タブ アプリが提供される人間が判読できるドメイン名です。 アプリケーションのドメイン名と、Azure AD アプリケーションに登録するドメイン名は同じである必要があります。

      ngrok などのトンネリング サービスを使用している場合は、ngrok サブドメインが変更されるたびにこの値を更新する必要があります。
    - `AppID` は、アプリを登録したときに生成されたアプリ ID (GUID) です。 概要 **セクションで** 表示できます。

    > [!IMPORTANT]
    >
    > - **複数の機能を備えたアプリのアプリケーション ID URI**: ボット、メッセージング拡張機能、タブを使用してアプリを構築する場合は、BotID がボット アプリ ID であるアプリケーション ID URI を `api://fully-qualified-domain-name.com/BotId-{YourClientId}`入力します。
    >
    > - **ドメイン名の形式: ドメイン名** には小文字を使用します。 大文字を使用しないでください。
    >
    >   たとえば、リソース名 "demoapplication" を使用してアプリ サービスまたは Web アプリを作成するには、次のようにします。
    >
    >   | 使用される基本リソース名が | URL は次のようになります... | 形式はサポートされています... |
    >   | --- | --- | --- |
    >   | *demoapplication* | **<https://demoapplication.example.net>** | すべてのプラットフォーム。|
    >   | *DemoApplication* | **<https://DemoApplication.example.net>** | デスクトップ、Web、および iOS のみ。 Android ではサポートされていません。 |
    >
    >    小文字のオプション *の適用を* 基本リソース名として使用します。

1. **[保存]** を選択します。

    アプリケーション ID URI が更新されたことを示すメッセージがブラウザーに表示されます。

    :::image type="content" source="../../../assets/images/authentication/teams-sso-tabs/app-id-uri-msg.png" alt-text="アプリケーション ID URI メッセージ" border="true":::

    アプリケーション ID URI がページに表示されます。

    :::image type="content" source="../../../assets/images/authentication/teams-sso-tabs/app-id-uri-added.png" alt-text="アプリケーション ID URI が更新されました" border="true":::

1. アプリケーション ID URI をメモして保存します。 後で Teams アプリ マニフェストを更新するために必要になります。

### <a name="to-configure-api-scope"></a>API スコープを構成するには

1. **[この API で定義された** スコープ] セクションで [+ スコープの **追加]** を選択します。

    :::image type="content" source="../../../assets/images/authentication/teams-sso-tabs/select-scope.png" alt-text="スコープを選択する" border="true":::

    [ **スコープの追加]** ページが表示されます。

1. スコープを構成するための詳細を入力します。

    :::image type="content" source="../../../assets/images/authentication/teams-sso-tabs/add-scope.png" alt-text="スコープの詳細を追加する" border="true":::

    1. スコープ名を入力します。 これは必須フィールドです。
    2. このスコープに同意できるユーザーを選択します。 既定のオプションは **管理者のみです**。
    3. **管理者の同意表示名** を入力します。 これは必須フィールドです。
    4. 管理者の同意の説明を入力します。 これは必須フィールドです。
    5. **[ユーザーの同意] 表示名** を入力します。
    6. ユーザー同意の説明の説明を入力します。
    7. 状態の **[有効]** オプションを選択します。
    8. **[スコープの追加]** を選択します。

    スコープが追加されたことを示すメッセージがブラウザーに表示されます。

    :::image type="content" source="../../../assets/images/authentication/teams-sso-tabs/scope-added-msg.png" alt-text="スコープが追加されたメッセージ" border="true":::

    定義した新しいスコープがページに表示されます。

    :::image type="content" source="../../../assets/images/authentication/teams-sso-tabs/scope-added.png" alt-text="追加および表示されるスコープ" border="true":::

### <a name="to-configure-authorized-client-application"></a>承認されたクライアント アプリケーションを構成するには

1. **[Api の公開**] ページを **[承認済みクライアント アプリケーション**] セクションに移動し、[**+ クライアント アプリケーションの追加]** を選択します。

    :::image type="content" source="../../../assets/images/authentication/teams-sso-tabs/auth-client-apps.png" alt-text="承認されたクライアント アプリケーション" border="true":::

    [ **クライアント アプリケーションの追加]** ページが表示されます。

1. アプリの Web アプリケーションに対して承認するアプリケーションの Teams クライアントの適切なクライアント ID を入力します。

    :::image type="content" source="../../../assets/images/authentication/teams-sso-tabs/add-client-app.png" alt-text="クライアント アプリケーションを追加する" border="true":::

    > [!NOTE]
    >
    > - Teams モバイル、デスクトップ、および Web アプリケーションのクライアント ID は、追加する必要がある実際の ID です。
    > - Teams タブ アプリの場合は、Teams でモバイル クライアント アプリケーションまたはデスクトップ クライアント アプリケーションを使用できないため、Web または SPA が必要です。

    1. 次のいずれかのクライアント ID を選択します。

       | クライアント ID を使用する | 承認する場合... |
       | --- | --- |
       | 1fec8e78-bce4-4aaf-ab1b-5451cc387264 | Teams のモバイルまたはデスクトップ アプリケーション |
       | 5e3ce6c0-2b1f-4285-8d4b-75ee78787346 | Teams Web アプリケーション |

    1. 公開した Web API にスコープを追加するには、 **承認されたスコープ** でアプリ用に作成したアプリケーション ID URI を選択します。

    1. **[アプリケーションの追加]** を選択します。

    承認されたクライアント アプリが追加されたことを示すメッセージがブラウザーに表示されます。

    :::image type="content" source="../../../assets/images/authentication/teams-sso-tabs/update-app-auth-msg.png" alt-text="クライアント アプリケーションがメッセージを追加しました" border="true":::

    クライアント ID がページに表示されます。

    :::image type="content" source="../../../assets/images/authentication/teams-sso-tabs/client-app-added.png" alt-text="クライアント アプリの追加と表示" border="true":::

> [!NOTE]
> 複数のクライアント アプリケーションを承認できます。 承認された別のクライアント アプリケーションを構成するには、この手順の手順を繰り返します。

## <a name="configure-access-token-version"></a>アクセス トークンのバージョンを構成する

アプリで許容されるアクセス トークンのバージョンを定義する必要があります。 この構成は、Azure AD アプリケーション マニフェストで行われます。

### <a name="to-define-the-access-token-version"></a>アクセス トークンのバージョンを定義するには

1. 左側のウィンドウで [**マニフェスト** の **管理** > ] を選択します。

   :::image type="content" source="../../../assets/images/authentication/teams-sso-tabs/azure-portal-manifest.png" alt-text="Azure AD portal マニフェスト" border="true":::

    Azure AD アプリケーション マニフェストが表示されます。

1. プロパティの値`accessTokenAcceptedVersion`として **2** を入力します。

    :::image type="content" source="../../../assets/images/authentication/teams-sso-tabs/azure-manifest-value.png" alt-text="承認済みアクセス トークンバージョンの値" border="true":::

1. **[保存]** を選びます。

    マニフェストが正常に更新されたことを示すメッセージがブラウザーに表示されます。

    :::image type="content" source="../../../assets/images/authentication/teams-sso-tabs/update-aad-manifest-msg.png" alt-text="マニフェスト更新メッセージ":::

おめでとうございます! タブ アプリの SSO を有効にするために必要な Azure AD のアプリ構成が完了しました。

## <a name="next-step"></a>次の手順

> [!div class="nextstepaction"]
> [SSO を有効にするコードを構成する](tab-sso-code.md)

## <a name="see-also"></a>関連項目

- [Azure Active Directory のテナント](/azure/active-directory/develop/single-and-multi-tenant-apps)
- [Microsoft Graph のアクセス許可とスコープを使用してタブ アプリを拡張する](tab-sso-graph-api.md)
- [クイック スタート - Microsoft ID プラットフォームにアプリケーションを登録する](/azure/active-directory/develop/quickstart-register-app)
- [クイック スタート: Web API を公開するようにアプリケーションを構成する](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
- [OAuth 2.0 承認コード フロー](/azure/active-directory/develop/v2-oauth2-auth-code-flow)