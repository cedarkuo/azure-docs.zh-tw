---
title: Azure 轉送的安全性控制項
description: 這篇文章提供評估 Azure 轉送的內建安全性控制檢查清單。
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: f8165d994e998af4f15cd6aa2fd08b75191b8b64
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211457"
---
# <a name="security-controls-for-azure-relay"></a>Azure 轉送的安全性控制項

本文記載內建在 Azure 轉送中的安全性控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 附註 | 文件 |
|---|---|--|--|
| 服務端點支援| 否 |  |   |
| 網路隔離和防火牆支援| 否 |  |   |
| 強制通道支援| 不適用 | 轉送是 TLS 通道  |   |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 附註| 文件 |
|---|---|--|--|
| Azure 監視支援（Log analytics、App insights 等）| 是 | |   |
| 控制和管理平面記錄和審核| 是 | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)。 |   |
| 資料平面記錄和審核| 是 | 連接成功/失敗及已記錄的錯誤。  |   |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 附註| 文件 |
|---|---|--|--|
| 驗證| 是 | 透過 SAS。 | [Azure 轉送驗證和授權](relay-authentication-and-authorization.md) |
| 授權|  是 | 透過 SAS。 | [Azure 轉送驗證和授權](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 附註 | 文件 |
|---|---|--|--|
| 待用的伺服器端加密： Microsoft 管理的金鑰 |  不適用 | 轉送是 web 通訊端，不會保存資料。 |   |
| 待用的伺服器端加密：客戶管理的金鑰（BYOK） | 否 | 僅使用 Microsoft TLS 憑證。  |   |
| 資料行層級加密（Azure 資料服務）| 不適用 | |   |
| 傳輸中的加密（例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密）| 是 | 服務需要 TLS。 |   |
| API 呼叫加密| 是 | IP-HTTPS. |


## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 附註| 文件 |
|---|---|--|--|
| 設定管理支援（設定的版本設定等）| 是 | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)。|   |

## <a name="next-steps"></a>後續步驟

- 深入瞭解[跨 Azure 服務的內建安全性控制](../security/fundamentals/security-controls.md)。