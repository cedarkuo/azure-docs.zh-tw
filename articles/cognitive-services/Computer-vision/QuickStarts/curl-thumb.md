---
title: 快速入門：產生縮圖 - REST、cURL
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將搭配使用電腦視覺 API 與 cURL，從影像產生縮圖。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d60f1101fc858e9dcaa22162be97417544e13693
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405051"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>快速入門：使用電腦視覺 REST API 和 cURL 產生縮圖

在此快速入門中，您會使用電腦視覺 REST API 從影像產生縮圖。 您可指定所需高度和寬度，其可與輸入影像的外觀比例不同。 「電腦視覺」會使用智慧型裁剪，以智慧方式識別關注區域，並產生該區域周圍的裁剪座標。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services)。

## <a name="prerequisites"></a>Prerequisites

- 您必須擁有 [cURL](https://curl.haxx.se/windows)。
- 您必須有電腦視覺的訂用帳戶金鑰。 您可以從[試用認知服務](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)取得免費試用的金鑰。 或者，依照[建立認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的指示訂閱電腦視覺並取得金鑰。

## <a name="create-and-run-the-sample-command"></a>建立和執行範例命令

若要建立並執行範例，請執行下列步驟：

1. 將下列命令複製到文字編輯器。
1. 視需要在命令中進行下列變更：
    1. 將 `<subscriptionKey>` 的值取代為您的訂用帳戶金鑰。
    1. 將 `<thumbnailFile>` 的值取代為儲存縮圖之檔案的路徑和名稱。
    1. 將要求 URL 的第一個部分 (`westcentralus`) 取代為您端點 URL 中的文字。
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. (選擇性) 將要求本文 (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) 中的影像 URL 變更為要產生縮圖之不同影像的 URL。
1. 開啟 [命令提示字元] 視窗。
1. 將文字編輯器中的命令貼到命令提示字元視窗中，然後執行該命令。

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.1/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>檢查回應

成功的回應會將縮圖影像寫入至 `<thumbnailFile>` 中指定的檔案。 如果要求失敗，回應會包含錯誤碼和訊息以協助判斷問題所在。 如果要求看似成功，但所建立的縮圖卻不是有效的影像檔案，則可能表示您的訂用帳戶金鑰無效。

## <a name="next-steps"></a>後續步驟

探索如何分析影像、偵測名人與地標、建立縮圖，以及擷取印刷與手寫文字的電腦視覺 API。 若要快速地試驗電腦視覺 API，請嘗試 [Open API 測試主控台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)。

> [!div class="nextstepaction"]
> [探索電腦視覺 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
