---
title: 搜尋自訂檢視 - Bing 自訂搜尋
titleSuffix: Azure Cognitive Services
description: 設定自訂搜尋體驗之後，您可以在 Bing 自訂搜尋入口網站中測試。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: aahi
ms.openlocfilehash: f00ffee47e3eb6366d632d8b6ee9beb01f048442
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "76983107"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>從入口網站呼叫 Bing 自訂搜尋執行個體

設定自訂搜尋體驗之後，您可以在 Bing 自訂搜尋[入口網站](https://customsearch.ai)中測試。 

![Bing 自訂搜尋入口網站的螢幕擷取畫面](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>建立搜尋查詢 

登入 Bing 自訂搜尋[入口網站](https://customsearch.ai)後，請選取您的搜尋實例，然後按一下 [生產] 索引**卷**標。在 [**端點**] 底下，選取 API 端點（例如 Web api）。 您的訂用帳戶可決定顯示的端點。

若要建立搜尋查詢，請輸入您端點的參數值。 請注意，入口網站中顯示的參數可能會隨著您所選的端點而改變。 如需詳細資訊，請參閱[自訂搜尋 API 參考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)。 若要變更搜尋實例使用的訂用帳戶，請新增適當的訂用帳戶金鑰，並更新適當的市場和/或語言參數。

一些重要參數如下：


|參數  |描述  |
|---------|---------|
|查詢     | 要搜尋的搜尋字詞。 僅適用於網頁、影像、影片與自動建議端點 |
|自訂組態識別碼 | 所選取自訂搜尋執行個體的組態識別碼。 這是唯讀欄位。 |
|Market     | 結果所源自的市場。 僅適用於網頁、影像、影片與裝載 UI 端點。        |
|訂用帳戶金鑰 | 要用來測試的訂用帳戶金鑰。 您可以從下拉式清單中選取金鑰，或手動輸入一個。          |

按一下 [其他參數]**** 會顯示下列參數：  

|參數  |描述  |
|---------|---------|
|安全搜尋     | 用來篩選成人內容網頁的篩選條件。 僅適用於網頁、影像、影片與裝載 UI 端點。 請注意，Bing 自訂影片搜尋只支援兩`moderate`個`strict`值：和。        |
|使用者介面語言    | 用於使用者介面字串的語言。 例如，若您在裝載 UI 中啟用影像與影片， [影像]**** 與 [影片]**** 索引標籤會使用指定的語言。        |
|計數     | 回應中傳回的搜尋結果數目。 僅適用於網頁、影像與影片端點。         |
|Offset    | 傳回結果之前要跳過的搜尋結果數目。 僅適用於網頁、影像與影片端點。        |
    
指定所有必要選項之後，請按一下 [呼叫]**** 在右窗格中檢視 JSON 回應。 如果您選取裝載 UI 端點，可以從底部窗格測試搜尋體驗。

## <a name="change-your-bing-custom-search-subscription"></a>變更您的 Bing 自訂搜尋訂用帳戶

您可以變更與 Bing 自訂搜尋實例相關聯的訂用帳戶，而不需要建立新的實例。 若要將 API 呼叫傳送至新的訂用帳戶並向其收費，請在 Azure 入口網站中建立新的 Bing 自訂搜尋資源。 在您的 API 要求中使用新的訂用帳戶金鑰，以及實例的自訂設定識別碼。

## <a name="next-steps"></a>後續步驟

- [使用 C# 呼叫您的自訂檢視](./call-endpoint-csharp.md)
- [使用 Java 呼叫您的自訂檢視](./call-endpoint-java.md)
- [使用 NodeJs 呼叫您的自訂檢視](./call-endpoint-nodejs.md)
- [使用 Python 呼叫您的自訂檢視](./call-endpoint-python.md)

- [使用 C# SDK 呼叫您的自訂檢視](./sdk-csharp-quick-start.md)
