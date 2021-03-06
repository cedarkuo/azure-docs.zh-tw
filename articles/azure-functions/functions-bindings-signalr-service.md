---
title: Azure Functions SignalR Service 繫結
description: 了解如何搭配使用 SignalR Service 繫結與 Azure Functions。
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 863620ce6f0af33b05ef290ae95ccdc99a53a54d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77523031"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>適用於 Azure Functions 的 SignalR Service 繫結

這一組文章說明如何使用 Azure Functions 中的 SignalR Service 系結，對連線至[Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/)的用戶端驗證和傳送即時訊息。 Azure Functions 支援 SignalR Service 的輸入和輸出繫結。

| 動作 | 類型 |
|---------|---------|
| 傳回服務端點 URL 和存取權杖 | [輸入系結](./functions-bindings-signalr-service-input.md) |
| 傳送 SignalR Service 訊息 |[輸出系結](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>新增至函數應用程式

### <a name="functions-2x-and-higher"></a>函數2.x 和更新版本

使用觸發程式和系結時，您需要參考適當的套件。 NuGet 套件適用于 .NET 類別庫，而延伸模組配套則用於所有其他應用程式類型。

| Language                                        | 加入者 .。。                                   | 備註 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安裝[NuGet 套件]3.x 版 | |
| C # 腳本，JAVA，JavaScript，Python，PowerShell | 註冊[延伸]模組配套          | 建議使用[Azure Tools 擴充]功能搭配 Visual Studio Code。 |
| C # 腳本（僅限線上 Azure 入口網站）         | 新增系結                            | 若要更新現有的系結延伸模組而不需要重新發佈函式應用程式，請參閱[更新您的擴充]功能。 |

[Nuget 套件]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[延伸模組配套]: ./functions-bindings-register.md#extension-bundles
[更新您的擴充功能]: ./install-update-binding-extensions-manual.md
[Azure Tools 擴充功能]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

如需如何設定及使用 SignalR Service 和 Azure Functions 的詳細資訊，請參閱[Azure SignalR Service 的 Azure Functions 開發和](../azure-signalr/signalr-concept-serverless-development-config.md)設定。

### <a name="annotations-library-java-only"></a>注釋程式庫（僅限 JAVA）

若要使用 JAVA 函式中的 SignalR Service 批註，您必須將相依性新增至您的*pom*檔案中的*SignalR*成品（1.0 版或更高版本）。

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>後續步驟

- [傳回服務端點 URL 和存取權杖（輸入系結）](./functions-bindings-signalr-service-input.md)
- [傳送 SignalR Service 訊息（輸出系結）](./functions-bindings-signalr-service-output.md) 
