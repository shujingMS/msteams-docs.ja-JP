---
title: アダプティブ カードのユニバーサル アクションの概要
description: アダプティブ カードのユニバーサル アクションの概要を簡単に説明します。
ms.topic: overview
localization_priority: Normal
ms.openlocfilehash: f0adf3d1a01262ff9cbdf14128c9ffe088ae8072
ms.sourcegitcommit: 1256639fa424e3833b44207ce847a245824d48e6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2021
ms.locfileid: "52088914"
---
# <a name="universal-actions-for-adaptive-cards"></a>アダプティブ カードのユニバーサル アクション

アダプティブ カードのユニバーサル アクションは、アダプティブ カードのレイアウトとレンダリングがユニバーサルであるにもかかわらず、アクション処理が行えなくても、開発者からのフィードバックから進化しました。 開発者が同じカードを別の場所に送信する場合でも、異なる方法でアクションを処理する必要があります。

アダプティブ カードのユニバーサル アクションは、アクションを処理する一般的なバックエンドとしてボットを提供し、Teams や Outlook などのアプリ間で動作する新しいアクションの種類 `Action.Execute` を導入します。

このドキュメントでは、ユニバーサル アクション モデルを使用して、プラットフォームやアプリケーション間でアダプティブ カードを操作するユーザー エクスペリエンスを強化する方法を理解するのに役立ちます。

> [!NOTE]
> アダプティブ カードのユニバーサル アクションのサポートは、ボットから送信されたカードでのみ使用できます。 新規作成ボックスとリンク解除カードを介して送信されるカードのサポートは、近日公開予定です。

## <a name="enhance-user-experiences-with-universal-actions-for-adaptive-cards"></a>アダプティブ カードのユニバーサル アクションを使用してユーザー エクスペリエンスを強化する

アダプティブ カードのユニバーサル アクションは、次のシナリオを有効にすることでユーザー エクスペリエンスを強化します。

* [ユニバーサル アクション](#universal-actions)
* [ユーザー固有のビュー](#user-specific-views)
* [シーケンシャル ワークフローのサポート](#sequential-workflow-support)
* [最新のビュー](#up-to-date-views)

### <a name="universal-actions"></a>ユニバーサル アクション

アダプティブ カードのユニバーサル アクションの前に、次のように異なるホストが異なるアクション モデルを提供しました。

* Teamsボットを使用する場合、実際の通信モデルを基になるチャネル `Action.Submit` に延期するアプローチ。
* Outlookアダプティブ カード `Action.Http` ペイロードで明示的に指定されたバックエンド サービスとの通信に使用されます。

次の図は、現在の一貫性のないアクション モデルを示しています。

:::image type="content" source="~/assets/images/adaptive-cards/current-teams-outlook-action-model.png" alt-text="一貫性のないアクション モデル":::

アダプティブ カードのユニバーサル アクションを使用すると、さまざまな `Action.Execute` プラットフォーム間でのアクション処理に使用できます。 `Action.Execute`を含むハブ間Teams動作Outlook。 さらに、アダプティブ カードは、トリガーされた呼び出し要求に対する `Action.Execute` 応答として返されます。

次の図は、新しいユニバーサル アクション モデルを示しています。

:::image type="content" source="~/assets/images/adaptive-cards/universal-action-model.png" alt-text="アダプティブ カードの新しいユニバーサル アクション":::

これで、同じカードを両方、Teams、Outlookに送信し、基になるボットを使用して互いに同期することができます。 いずれかのプラットフォームで実行されるアクションは、このビルドで一度だけ他のプラットフォームに反映され、任意の場所 *(アダプティブ* カード用ユニバーサル アクション) モデルを展開します。

次の図は、アダプティブ カードのユニバーサル アクションを表TeamsとOutlook。

:::image type="content" source="~/assets/images/adaptive-cards/universal-bots-teams-outlook.png" alt-text="同じカードをTeamsとOutlook":::

### <a name="user-specific-views"></a>ユーザー固有のビュー

現在、チャットまたはチャネルTeamsユーザーは、アダプティブ カードでまったく同じビューアクションとボタン アクションを表示します。 ただし、特定のシナリオでは、特定のユーザーが異なる方法で行動し、同じチャットまたはチャネル内の異なる情報にアクセスする必要があります。

たとえば、チャットまたはチャネルでインシデント レポート カードを送信する場合、インシデントが割り当てられているユーザーだけが [解決] ボタンを表示 **する必要** があります。 一方、インシデント作成者には [編集]ボタンが表示され、他のすべてのユーザーはインシデントの詳細のみを表示できる必要があります。 これは、プロパティによって有効になっているユーザー固有のビューによって可能 `refresh` になります。

次の図は、チャット内の異なるユーザーが要件に基づいて異なるアクションを表示するチケット メッセージング拡張機能 (ME) の例を示しています。

:::image type="content" source="~/assets/images/adaptive-cards/universal-bots-incident-management.png" alt-text="ユーザー固有のビュー":::

詳細については、「ユーザー固有の [ビューのサンプル」を参照してください](User-Specific-Views.md)。

### <a name="sequential-workflow-support"></a>シーケンシャル ワークフローのサポート

シーケンシャル ワークフローのサポートにより、ユーザーは別のカードを個別に送信することなく、一連のワークフローを進めできます。 これは、アクションに応答してアダプティブ カード `Action.Execute` を返す機能によって可能になります。 また、チャットまたはチャネル内のすべてのユーザーは、チャット内の他のユーザーのカードを変更せずにワークフローを進めできます。

次の図は、食品の注文ボットの例を示しています。 <br/>

<img src="~/assets/images/bots/sequentialWorkflow.gif" alt="Sequential Workflow" width="400"/>

次の図は、チャットまたはチャネル内のユーザーごとにさまざまな状態を示しています。

:::image type="content" source="~/assets/images/adaptive-cards/universal-bots-catering-bot.png" alt-text="ケータリング ボットの状態":::

詳細については、「シーケンシャル ワークフロー [のサンプル」を参照してください](Sequential-Workflows.md)。

### <a name="up-to-date-views"></a>最新のビュー

自動的に更新されるアダプティブ カードを作成できます。 たとえば、ユーザーが送信した承認要求を指定できます。 承認後、カードは要求の承認時間と要求を承認したユーザーに関する詳細を自動的に表示する必要があります。 更新モデルを使用すると、このような最新のビューを提供できます。 次の図は、複数ステップの承認フローと、さまざまなユーザーのビューの表示方法を示しています。

:::image type="content" source="~/assets/images/adaptive-cards/universal-bots-up-to-date-views.png" alt-text="最新のユーザー固有のビュー":::

詳細については、「最新のビュー [のサンプル」を参照してください](Up-To-Date-Views.md)。

これで、アダプティブ カードを新しいユニバーサル アクション モデルで変換して、独自の強化されたユーザー エクスペリエンスを提供する方法を理解できます。

## <a name="adaptive-cards-and-the-new-universal-actions-model"></a>アダプティブ カードと新しいユニバーサル アクション モデル

アダプティブ カードは、テキストやグラフィックスなどのコンテンツと、ユーザーが実行できるアクションの組み合わせです。 詳細については、「アダプティブ カード」 [を参照してください](http://adaptivecards.io/)。 アダプティブ カード用の新しいユニバーサル アクションを使用すると、プラットフォームやアプリケーション間でアダプティブ カードアクションを一般的に処理できます。 詳細については、「ユニバーサル アクション [モデル」を参照してください](https://docs.microsoft.com/adaptive-cards/authoring-cards/universal-action-model)。

[アダプティブ カードのユニバーサル アクションの操作](Work-with-universal-actions-for-adaptive-cards.md) ドキュメントでは、ソリューションにユニバーサル アクション for アダプティブ カードの機能を使用する手順について説明します。

## <a name="see-also"></a>関連項目

* [ボットについて](~/bots/what-are-bots.md)
* [アダプティブ カードの概要](~/task-modules-and-cards/what-are-cards.md)
* [アダプティブ カード @ Microsoft ビルド 2020](https://youtu.be/hEBhwB72Qn4?t=1393)
* [アダプティブ カード @ Ignite 2020](https://techcommunity.microsoft.com/t5/video-hub/elevate-user-experiences-with-teams-and-adaptive-cards/m-p/1689460)

## <a name="next-step"></a>次の手順

> [!div class="nextstepaction"]
> [アダプティブ カードのユニバーサル アクションを操作する](Work-with-universal-actions-for-adaptive-cards.md)