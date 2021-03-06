---
title: 在自動調整布建的輸送量中建立 Azure Cosmos 容器和資料庫。
description: 瞭解優點、使用案例，以及如何以自動調整布建的輸送量布建 Azure Cosmos 資料庫和容器。
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 81a13dcb7955a7d46f485416bf9b7e4e7be4d9ac
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791709"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-provisioned-throughput"></a>建立具有自動調整布建輸送量的 Azure Cosmos 容器和資料庫

Azure Cosmos DB 可讓您使用標準（手動）布建的輸送量或自動調整布建的輸送量來設定您的容器。 本文說明自動調整的優點和使用案例。

> [!NOTE]
> 您只能[為新的資料庫和容器啟用自動](#create-db-container-autoscale)調整。 不適用現有的容器和資料庫。

除了標準的輸送量布建外，您現在還可以設定 Azure Cosmos 容器，並提供自動調整的輸送量。 在自動調整布建的輸送量中設定的容器和資料庫 **，會自動並根據您的應用程式需求立即擴充布建的輸送量，而不會影響全域工作負載的可用性、延遲、輸送量或效能。**

在自動調整中設定容器和資料庫時，您必須指定不超過`Tmax`的最大輸送量。 然後，容器可以擴充其輸送量， `0.1*Tmax < T < Tmax`以便進行調整。 換句話說，容器和資料庫會根據工作負載需求立即進行調整，從最低輸送量值的10%，到您設定的最大輸送量值。 設定自動調整之後，您可以在任何時間點`Tmax`變更資料庫或容器的最大輸送量（）設定。 使用自動調整選項時，每個容器或資料庫的 400 RU/秒最小輸送量已不再適用。

針對容器或資料庫上指定的最大輸送量，系統允許在計算的儲存空間限制內運作。 如果超過儲存空間限制，則會自動將最大輸送量調整為較高的值。 搭配自動調整使用資料庫層級輸送量時，資料庫內允許的容器數目會計算為： `0.001*TMax`。 例如，如果您布建20000自動調整 RU/秒，則資料庫可以有20個容器。

## <a name="benefits-of-autoscale-provisioned-throughput"></a><a id="autoscale-benefits"></a>自動調整布建輸送量的優點

使用自動調整設定的 Azure Cosmos 容器具有下列優點：

* **簡單：** 具有自動調整的容器會移除為各種容器手動管理布建輸送量（ru）和容量的複雜性。

* **可調整：** 具有自動調整的容器會在需要時順暢地相應增加布建的輸送量容量。 用戶端連線、應用程式不會中斷，而且它們不會影響任何現有的 Sla。

* 符合**成本效益：** 當您使用以自動調整設定的容器時，您只需以每小時為基礎，為您的工作負載所需的資源付費。

* **高可用性：** 具有自動調整功能的容器會使用相同的全域分散式、容錯、高可用性後端，以確保資料持久性和高可用性。

## <a name="use-cases-of-autoscale-provisioned-throughput"></a><a id="autoscale-usecases"></a>自動調整布建輸送量的使用案例

以自動調整設定的 Azure Cosmos 容器使用案例包括：

* **變數工作負載：** 當您執行的應用程式使用了1小時的尖峰使用量到數小時，或每年有幾次時。 範例包括人力資源、預算和營運報表的應用程式。 在這類情況下，可以使用以自動調整設定的容器，而且您不再需要手動布建尖峰或平均容量。

* 無法**預測的工作負載：** 當您執行的工作負載在一整天都有資料庫使用量，同時也在難以預測的活動尖峰時執行。 其中一個範例包含一個流量網站，當氣象預報變更時，會看到活動的激增。 設定了自動調整規模的容器，可調整容量以符合應用程式尖峰負載的需求，並在活動激增時相應減少。

* **新的應用程式：** 如果您要部署新的應用程式，但不確定您需要多少布建的輸送量（也就是您所需的 ru 數目）。 使用自動調整規模設定的容器，您可以自動擴充至應用程式的容量需求和需求。

* 不**常使用的應用程式：** 如果您的應用程式只使用幾個小時，每天或每週或每個月數次，例如低容量應用程式/web/blog 網站。

* **開發和測試資料庫：** 如果您的開發人員在工作時間使用容器，但在夜間或週末不需要它們。 使用自動調整設定的容器時，它們會相應減少為不使用時的最小值。

* 已**排程的生產工作負載/查詢：** 當您在單一容器上有一系列已排程的要求/作業/查詢，而且如果您想要以絕對低輸送量執行，您現在可以輕鬆地執行這項工作。 當排程的查詢/要求提交至設定了自動調整的容器時，它會視需要自動相應增加並執行作業。

先前問題的解決方案不僅需要大量的執行時間，還會在設定或您的程式碼中引進複雜性，而且經常需要手動介入來處理它們。 自動調整可讓上述案例現成可用，因此您不再需要擔心這些問題。

## <a name="comparison--standard-manual-vs-autoscale-provisioned-throughput"></a>比較–標準（手動）與自動調整布建輸送量

|  | 設定了標準布建輸送量的容器  | 設定了自動調整布建輸送量的容器 |
|---------|---------|---------|
| **佈建的輸送量** | 手動布建。 | 根據工作負載使用模式自動和立即調整。 |
| **要求/作業的速率限制（429）**  | 如果耗用量超過布建的容量，則可能會發生。 | 如果耗用的輸送量是在您選擇 [自動調整] 的最大輸送量內，則不會發生。   |
| **容量規劃** |  您必須進行初始容量規劃，並布建所需的輸送量。 |    您不必擔心容量規劃。 系統會自動負責容量規劃和容量管理。 |
| **定價** | 每小時手動布建 RU/秒。 | 針對單一寫入區域帳戶，您需支付每小時使用的輸送量，方法是使用 [每小時自動調整 RU/秒] 速率。 <br/><br/>針對具有多個寫入區域的帳戶，自動調整不會額外收費。 您需支付每小時使用的輸送量，並使用相同的多宿主 RU/秒費率。 |
| **最適合工作負載類型** |  可預測且穩定的工作負載|   無法預測和可變的工作負載  |

## <a name="create-a-database-or-a-container-with-autoscale"></a><a id="create-db-container-autoscale"></a>建立具有自動調整的資料庫或容器

透過 Azure 入口網站建立新的資料庫或容器時，您可以設定自動調整。 使用下列步驟來建立新的資料庫或容器、啟用自動調整，並指定最大輸送量（RU/秒）。

1. 登入[Azure 入口網站](https://portal.azure.com)或 [ [Azure Cosmos DB explorer]。](https://cosmos.azure.com/)

1. 流覽至您的 Azure Cosmos DB 帳戶，然後開啟 [**資料總管**] 索引標籤。

1. 選取 [**新增容器]。** 輸入您的資料庫、容器和分割區索引鍵的名稱。 在 [**輸送量**] 底下，選取 [**自動**調整] 選項，然後選擇使用 [自動調整] 選項時，資料庫或容器不能超過的最大輸送量（RU/秒）。

   ![建立容器及設定自動調整輸送量](./media/provision-throughput-autoscale/create-container-autoscale-mode.png)

1. 選取 [確定]  。

您可以選取 [布建**資料庫輸送量**] 選項，以建立具有自動調整的共用輸送量資料庫。

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a>自動調整的輸送量和儲存體限制

下表顯示自動調整中不同選項的 [整個] 和 [儲存體限制] 上限：

|最大輸送量限制  |最大儲存體限制  |
|---------|---------|
|4000 RU/秒  |   50 GB    |
|20000 RU/秒  |  200 GB  |
|100000 RU/秒    |  1 TB   |
|500000 RU/秒    |  5 TB  |

## <a name="next-steps"></a>後續步驟

* 請參閱[自動調整常見問題](autoscale-faq.md)。
* 深入瞭解[邏輯](partition-data.md)分割區。
* 了解如何[在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。
* 了解如何[在 Azure Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)。
