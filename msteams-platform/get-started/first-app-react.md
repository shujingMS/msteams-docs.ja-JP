---
title: 使用を開始する - React を使用した初めての Teams アプリのビルド
author: adrianhall
description: "\"こんにちは!\" を表示する Microsoft Teams アプリを迅速に作成します。 Microsoft Teams ツールキットおよび React を使用したメッセージ。"
ms.author: adhal
ms.date: 05/18/2021
ms.topic: quickstart
ms.openlocfilehash: 4560e332834fec7b681a6b2babf3e881b5e472f7
ms.sourcegitcommit: 2c8b35899dd845acd66f1f927e40d99523c29a91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "52698138"
---
# <a name="build-and-run-your-first-microsoft-teams-app-with-react"></a>React を使用した最初の Microsoft Teams アプリのビルドと実行

このチュートリアルでは、React で新しい Microsoft Teams アプリを作成し、Microsoft Graph から情報を引き出すシンプルな個人用アプリを実装します。 (*個人用アプリ* には、個人ユーザーを対象にした一連のタブが含まれます。) チュートリアルでは、Teams アプリの構造、アプリをローカルで実行する方法、さらにはアプリを Azure に展開する方法について学びます。

ビルドされたアプリは、現在のユーザーの基本的なユーザー情報を表示します。  許可が付与された場合、アプリは現在のユーザーとして Microsoft Graph に接続し、完全なプロフィールを取得します。

## <a name="before-you-begin"></a>始める前に

[前提条件](prerequisites.md)をインストールして、開発環境が整っていることを確認する

> [!div class="nextstepaction"]
> [前提条件のインストール](prerequisites.md)

## <a name="create-your-project"></a>プロジェクトを作成する

Teams ツールキットを使用して、最初のプロジェクトを作成します。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/vscode)

1. Visual Studio Code を開きます。
1. サイド バーの Teams アイコンを選択して、Teams ツールキットを開きます。

    :::image type="content" source="../assets/images/teams-toolkit-v2/sidebar-icon.png" alt-text="Visual Studio Code サイド バーの Teams アイコン":::。

1. **[新しいプロジェクトの作成]** を選択します。

   :::image type="content" source="../assets/images/teams-toolkit-v2/create-project.png" alt-text="Teams ツールキットのサイド バーにある [新しいプロジェクトの作成] リンクの位置":::。

1. **[新しい Teams アプリを作成]** を選択します。

   :::image type="content" source="../assets/images/teams-toolkit-v2/create-new-project-intro.png" alt-text="[新しいプロジェクトの作成] のウィザードの開始":::。

1. **[機能の選択]** 手順では、**[タブ]** 機能がすでに選択されています。  **[OK]** を押します。

   :::image type="content" source="../assets/images/teams-toolkit-v2/create-project-capabilities.png" alt-text="新しいアプリに機能を追加する方法を示すスクリーンショット":::。

1. **[フロントエンド ホストの種類]** 手順で、**[Azure]** を選択します。

   :::image type="content" source="../assets/images/teams-toolkit-v2/create-project-hosting.png" alt-text="新規アプリのホスティングを選択する方法を示すスクリーンショット":::。

1. **クラウド リソース** 手順で **[OK]** を押します。  このチュートリアルでは、追加のクラウド リソースは必要ありません。

   :::image type="content" source="../assets/images/teams-toolkit-v2/create-project-cloud-resources.png" alt-text="新しいアプリにクラウド リソースを追加する方法を示すスクリーンショット":::。

1. **プログラミング言語** の手順で、**[JavaScript]** を選択します。

    :::image type="content" source="../assets/images/teams-toolkit-v2/create-project-programming-languages.png" alt-text="プログラミング言語を選択する方法のスクリーンショット":::

1. ワークスペース フォルダーを選択します。  ワークスペース フォルダー内に、作成中のプロジェクト向けのフォルダーが作成されます。

1. `helloworld` のように、アプリに適した名前を入力します。  アプリの名前は、英数字のみで構成されている必要があります。  **Enter** キーを押して続行します。

数秒後に Teams アプリが作成されます。

# <a name="command-line"></a>[コマンド ライン](#tab/cli)

`teamsfx` CLI を使用して、最初のプロジェクトを作成します。  プロジェクト フォルダーを作成するフォルダーから開始します。

``` bash
teamsfx new
```

CLI では、プロジェクトを作成するためのいくつかの質問を行います。  各質問には、回答方法 (矢印キーで選択肢を選択するなど) が記載されています。  質問に答えた後、**Enter** キーを押して選択を確認します。

1. **[新しい Teams アプリを作成]** を選択します。
1. **[タブ]** 機能を選択します。
1. **Azure** フロントエンド ホスティングを選択します。
1. クラウド リソースは選択しないでください。
1. プログラミング言語として **[JavaScript]** を選択します。
1. **Enter** キーを押して、既定のワークスペース フォルダーを選択します。
1. `helloworld` のように、アプリに適した名前を入力します。  アプリの名前は、英数字のみで構成されている必要があります。

すべての質問に答えると、プロジェクトが作成されます。

---

## <a name="take-a-tour-of-the-source-code"></a>ソース コードのツアーを開始する

このセクションをスキップしたい場合は、[アプリをローカルで実行する](#run-your-app-locally)ことができます。

Teams ツールキットでプロジェクトを構成すると、Teams 向けの基本的な個人用タブを構築するためのコンポーネントがあります。 プロジェクトのディレクトリとファイルには、Visual Studio Code の [エクスプローラー] 領域に表示されます。

:::image type="content" source="../assets/images/teams-toolkit-v2/react-app-project.png" alt-text="Visual Studio Code で個人用アプリ向けのアプリのプロジェクト ファイルを表示したスクリーンショット。":::

ツールキットは、セットアップ時に追加した機能に基づいて、プロジェクト ディレクトリにスキャフォールディングを自動的に作成します。 Teams ツールキットは、`.fx` ディレクトリにアプリの状態を保持します。  このディレクトリの他の項目の間では、以下のようになります。

- アプリ アイコンは PNG ファイルとして `color.png` と `outline.png` に格納されます。
- Developer Portal for Teams に公開するアプリのマニフェストは `manifest.source.json` に格納されています。
- プロジェクト作成時に選択した設定が `settings.json` に保存されます。

セットアップ時にタブ機能を選択したため、Teams ツールキットは基本的なタブに必要なコードをすべて `tabs` ディレクトリに格納します。 このディレクトリの中には、いくつかの重要なファイルがあります。

- `tabs/src/index.jsx` はフロントエンド アプリの入力ポイントで、メインの `App` コンポーネントは `ReactDOM.render()` でレンダリングされます。
- `tabs/src/components/App.jsx` は、アプリの URL ルーティングを処理します。 [Microsoft Teams JavaScript client SDK](../tabs/how-to/using-teams-client-sdk.md) を呼び出して、アプリと Teams の間の通信を確立します。
- `tabs/src/components/Tab.jsx` は、アプリの UI を実装するコードを含みます。
- `tabs/src/components/TabConfig.jsx` は、アプリを構成する UI を実装するコードを含みます。

Teams ランタイムでは、プライバシー通知、利用規約、構成タブなど、いくつかのタブが必要となります。  プライバシーに関する通知と利用規約のコードは、同じディレクトリ内にあります。

クラウド機能を追加すると、プロジェクトにディレクトリが追加されます。  最も重要なのは、`api` ディレクトリに、ユーザーが書き込んだ Azure Functions のコードが格納されていることです。

## <a name="run-your-app-locally"></a>アプリをローカルで実行する

Teams ツールキットでは、アプリをローカルで実行することができます。  これは、Teams が予想する正しいインフラを提供するために必要ないくつかのパーツで構成されています。

- Azure Active Directory を使用してアプリケーションを登録しました。  このアプリケーションには、アプリケーションが読み込まれる場所や、アクセスするバックエンド リソースに関連するアクセス許可があります。
- 認証タスクを支援する Web API がホストされ、アプリと Azure Active Directory の間のプロキシとして機能します。  これは、Azure Functions Core Tools によって実行されます。  これは、URL `https://localhost:5000` でアクセスできます。
- アプリのフロントエンドを構成する HTML、CSS、JavaScript のリソースは、ローカル サービスでホストされています。 これは、`https://localhost:3000` でアクセスできます。
- アプリのマニフェストは、Developer Portal for Teams で生成され存在します。  Teams はアプリ マニフェストを使用して、接続しているクライアントにアプリをロードする場所を伝達します。

この作業が完了すると、Teams クライアント内でアプリを読み込むことができます。  Teams の Web クライアントを使用することで、標準的な Web 開発環境で HTML、CSS、JavaScript のコードを確認することができます。

[!INCLUDE [Adjust your browser launch settings](~/includes/get-started/browser-private-launch.md)]

### <a name="build-and-run-your-app-locally-in-visual-studio-code"></a>Visual Studio Code でアプリをローカルにビルドして実行する

アプリをローカルに構築して実行するには、以下のようにします。

1. Visual Studio Code で、**F5** を押して、アプリケーションをデバッグ モードで実行します。

   > アプリを初めて実行すると、すべての依存関係がダウンロードされ、アプリがビルドされます。  ビルドが完了すると、自動的にブラウザー ウィンドウが開きます。  この作業には 3 ～ 5 分かかります。

   ツールキットでは、必要に応じてローカル証明書をインストールするようメッセージが表示されます。 この証明書により、Teams は `https://localhost` からアプリケーションを読み込むことができます。 以下のダイアログが表示されたら、「はい」を選択します。

   :::image type="content" source="../assets/images/teams-toolkit-v2/ssl-prompt.png" alt-text="Teams がアプリケーションを localhost からロードできるようにするための SSL 証明書のインストールを求めるメッセージが表示される方法を示すスクリーンショット":::。

1. アプリケーションを実行するために Web ブラウザーが起動します。 Microsoft Teams を開くようメッセージが表示されたら、「キャンセル」を選択してブラウザーに残ります。 メッセージが表示されたら、**[代わりに Web アプリケーションを使用する]** を選択します。

   :::image type="content" source="../assets/images/teams-toolkit-v2/launch-web-browser-and-pick-webapp.png" alt-text="起動時に Web 版のチームを選択する方法を示すスクリーンショット":::

1. サインインするように求めるメッセージが表示されることがあります。  その場合は、M365 アカウントを使用してサインインします。
1. Teams へのアプリのインストールを促すメッセージが表示された場合は、**[追加]** を押してください。

これでご使用のアプリが表示されます。

:::image type="content" source="../assets/images/teams-toolkit-v2/react-finished-app.png" alt-text="完了したアプリのスクリーンショット":::

他の Web アプリケーションと同様に、通常のデバッグ作業を行うことができます (ブレークポイントの設定など)。 このアプリはホット リロードをサポートしています。  プロジェクト内のファイルを変更すると、ページが再読み込みされます。

<!-- markdownlint-disable MD033 -->
<details>
<summary>デバッガーでアプリをローカルに実行した場合に発生することを説明します。</summary>

F5 を押すと、以下のように Teams ツールキットが表示されます。

1. Azure Active Directory を使用してアプリケーションを登録しました。
1. Teams でアプリを *サイドロード* しました。
1. [Azure Function Core Tools](/azure/azure-functions/functions-run-local?#start) を使用して、アプリケーション バックエンドのローカルでの実行を開始しました。
1. アプリケーションのフロント エンドがローカルでホストされるようになりました。
1. Microsoft Teams を Web ブラウザーで起動し、`https://localhost:3000/tab` でアプリケーションをサイドロードするよう Teams に指示するコマンドを実行します (URL はアプリケーション マニフェスト内に登録されています)。

</details>

<!-- markdownlint-disable MD033 -->
<details>
<summary>アプリをローカルで実行する場合の一般的な問題のトラブルシューティングを行う方法について説明します。</summary>

Teams でアプリを正常に実行するには、アプリのサイドロードを許可する Microsoft 365 Teams アカウントが必要です。 アカウント開設の詳細については、「[前提条件](prerequisites.md#enable-sideloading)」を参照してください。

</details>

[!INCLUDE [Provision and Deploy your app on Azure](~/includes/get-started/azure-provisioning-instructions.md)]

<!-- markdownlint-disable MD033 -->
<details>
<summary>アプリを Azure に展開した場合に発生することを説明します</summary>

展開前は、このアプリケーションは以下のようにローカルで動作しています。

1. バックエンドは、_Azure Functions Core Tools_ を使用して実行します。
1. アプリケーションの HTTP エンドポイントは、Microsoft Teams がアプリケーションを読み込む場所でローカルに実行されます。

展開では、アクティブな Azure サブスクリプションにリソースをプロビジョニングし、アプリケーションのバックエンドとフロントエンドのコードを Azure に展開 (アップロード) します。

1. バックエンドには (構成済みの場合) 、Azure App Service や Azure Storage など、さまざまな Azure のサービスが使用されています。
1. フロントエンド アプリケーションは、静的な Web ホスティング用に構成された Azure Storage アカウントに展開されます。

</details>

## <a name="next-steps"></a>次の手順

Teams アプリを作成する他の方法についてご紹介します。

- [Blazor を使用して Teams アプリを作成する](first-app-blazor.md)
- [SharePoint Web パーツとして Teams アプリを作成する](first-app-spfx.md) (Azure は必要なし)
- [会話ボット アプリを作成する](first-app-bot.md)
- [メッセージング拡張機能を作成する](first-message-extension.md)