---
title: Teams で SSO を使用したタブの認証のトラブルシューティング
description: Teams での SSO 認証のトラブルシューティングとタブでの SSO 認証の使用方法
ms.topic: how-to
ms.localizationpriority: medium
keywords: teams 認証タブ Microsoft Azure Active Directory (Azure AD) SSO エラーに関する質問
ms.openlocfilehash: 474f1050642124d2fa34e51417dcd14c9937f033
ms.sourcegitcommit: e16b51a49756e0fe4eaf239898e28d3021f552da
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2022
ms.locfileid: "65888124"
---
# <a name="troubleshooting-sso-authentication-in-teams"></a>Teams での SSO 認証のトラブルシューティング

SSO に関する問題と質問の一覧と、それらを修正する方法を次に示します。
<br>

## <a name="support-for-microsoft-graph"></a>Microsoft Graph のサポート

<br>
<details>
<summary>1. Postman では Graph API は機能しますか?</summary>
<br>
Microsoft Graph Postman コレクションは、Microsoft Graph API で使用できます。

詳細については、「[Microsoft Graph API で Postman を使用する](/graph/use-postman)」をご覧ください。
</details>
<br>
<details>
<summary>2. Graph API は Microsoft Graph エクスプローラーで機能しますか?</summary>
<br>
はい。Graph API は Microsoft Graph エクスプローラーで動作します。

詳細については、「 [Graph エクスプローラー](https://developer.microsoft.com/graph/graph-explorer)」を参照してください。

</details>
<br>

## <a name="error-messages-and-how-to-handle-them"></a>エラー メッセージとその処理方法

<br>
<details>
<summary>1. エラー: 同意がありません。</summary>
<br>
Azure AD は、Microsoft Graph リソースへのアクセス要求を受信すると、ユーザー (またはテナント管理者) がこのリソースに対する同意を与えたかどうかを確認します。 ユーザーまたは管理者からの同意の記録がない場合、Azure AD は Web サービスにエラー メッセージを送信します。

コードでは、エラーを処理する方法 (たとえば、403 Forbidden 応答の本文) にクライアントに指示する必要があります。

- 管理者のみが同意できる Microsoft Graph スコープがタブ アプリに必要な場合は、コードでエラーをスローする必要があります。
- 唯一必要なスコープに対して同意できるのがユーザーである場合は、コードはユーザー認証の代替システムにフォールバックする必要があります。

</details>
<br>
<details>
<summary>2. エラー: スコープ (アクセス許可) が見つかりません。</summary>
<br>
このエラーは、開発中にのみ発生します。

このエラーを処理するには、サーバー側のコードからクライアントに 403 Forbidden 応答を送信する必要があります。 エラーをコンソールに記録するか、ログに記録する必要があります。
</details>
<br>
<details>
<summary>3. エラー: Microsoft Graph のアクセス トークンの対象ユーザーが無効です。</summary>
<br>
サーバー側のコードは、クライアントに 403 Forbidden 応答を送信して、ユーザーにメッセージを表示する必要があります。 また、エラーをコンソールに記録するか、ログに記録することをお勧めします。
</details>
<br>
<details>
<summary>4. エラー: ホスト名は、既に所有されているドメインに基づいていてはなりません。</summary>
<br>
このエラーは、次の 2 つのシナリオのいずれかで発生します。

1. カスタム ドメインは Azure AD に追加されません。 カスタム ドメインを Azure AD に追加して登録するには、 [Azure AD にカスタム ドメイン名を追加する手順に](/azure/active-directory/fundamentals/add-custom-domain) 従い、手順に従って [アクセス トークンのスコープ](tab-sso-register-aad.md#configure-scope-for-access-token) をもう一度構成します。
1. Microsoft 365 テナントで管理者資格情報を使用してサインインしていません。 管理者として Microsoft 365 にサインインします。

</details>
<br>
<details>
<summary>5. エラー: 返されたアクセス トークンでユーザー プリンシパル名 (UPN) が受信されません。</summary>
<br>
AZURE AD では、オプションの要求として UPN を追加できます。

詳細については、「 [省略可能な要求をアプリに提供し](/azure/active-directory/develop/active-directory-optional-claims) 、 [トークンにアクセス](/azure/active-directory/develop/access-tokens)する」を参照してください。
</details>
<br>
<details>
<summary>6. エラー: Teams SDK エラー: resourceDisabled.</summary>
<br>
このエラーを回避するには、Azure AD アプリの登録と Teams クライアントでアプリケーション ID URI が正しく構成されていることを確認します。

アプリケーション ID URI の詳細については、「 [API を公開するには](tab-sso-register-aad.md#to-expose-an-api)」を参照してください。

</details>
<br>

<details>
<summary>7. エラー: タブ アプリを実行するときの一般的なエラー。</summary>
<br>
Azure AD で行われた 1 つ以上のアプリ構成が正しくない場合、一般的なエラーが表示されることがあります。 このエラーを解決するには、コードで構成されたアプリの詳細と Teams マニフェストが Azure AD の値と一致するかどうかを確認します。

次の図は、Azure AD で構成されたアプリの詳細の例を示しています。

:::image type="content" source="../../../assets/images/authentication/teams-sso-tabs/azure-app-details.png" alt-text="Azure AD のアプリ構成値" border="false":::

Azure AD、クライアント側コード、Teams アプリ マニフェストの間で、次の値が一致することを確認します。

- **アプリ ID**: Azure AD で生成したアプリ ID は、コードと Teams マニフェスト ファイルで同じである必要があります。 Teams マニフェストのアプリ ID が Azure AD の **アプリケーション (クライアント) ID と** 一致するかどうかを確認します。

- **アプリ シークレット**: アプリのバックエンドで構成されたアプリ シークレットは、Azure AD の **クライアント資格情報** と一致する必要があります。
    クライアント シークレットの有効期限が切れているかどうかも確認する必要があります。

- **アプリケーション ID URI**: コードと Teams アプリ マニフェスト ファイル内のアプリ ID URI は、Azure AD の **アプリケーション ID URI と** 一致する必要があります。

- **アプリのアクセス許可**: スコープで定義したアクセス許可がアプリの要件に従っているかどうかを確認します。 その場合は、アクセス トークンでユーザーに付与されたかどうかを確認します。

- **管理者の同意**: いずれかのスコープで管理者の同意が必要な場合は、特定のスコープに対してユーザーに同意が付与されているかどうかを確認します。

さらに、タブ アプリに送信されたアクセス トークンを調べて、次の値が正しいかどうかを確認します。

- **対象ユーザー (aud)**: トークン内のアプリ ID が Azure AD で指定されているように正しいかどうかを確認します。
- **テナント ID(tid)**: トークンに記載されているテナントが正しいかどうかを確認します。
- **ユーザー ID (preferred_username)**: ユーザー ID が、現在のユーザーがアクセスするスコープのアクセス トークン要求のユーザー名と一致するかどうかを確認します。
- **スコープ (scp)**: アクセス トークンが要求されるスコープが正しく、Azure AD で定義されているかどうかを確認します。
- **Azure AD バージョン 1.0 または 2.0 (ver)**: Azure AD のバージョンが正しいかどうかを確認します。

[JWT](https://jwt.ms) を使用してトークンを検査できます。

</details>