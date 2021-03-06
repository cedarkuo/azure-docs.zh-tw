---
title: 對應資料流程中的接收轉換
description: 瞭解如何在對應的資料流程中設定接收轉換。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 4b10a4c98abd6bec4074bf35764a9cbb85d5b157
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605973"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>對應資料流程中的接收轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

轉換資料之後，您可以將資料接收到目的地資料集。 每個資料流程都需要至少一個接收轉換，但是您可以視需要寫入至多個接收器來完成轉換流程。 若要寫入其他接收，請透過新的分支和條件式分割來建立新的資料流程。

每個接收轉換只會與一個 Data Factory 資料集相關聯。 資料集會定義您想要寫入之資料的形狀和位置。

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>對應資料流程中支援的接收連接器

下列資料集目前可用於接收轉換：
    
* [Azure Blob 儲存體](connector-azure-blob-storage.md#mapping-data-flow-properties)（JSON、Avro、Text、Parquet）
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) （JSON、Avro、Text、Parquet）
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) （JSON、Avro、Text、Parquet）
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

這些連接器的特定設定位於 [**設定**] 索引標籤中。這些設定的相關資訊位於連接器檔中。 

Azure Data Factory 可以存取超過[90 的原生連接器](connector-overview.md)。 若要將資料從您的資料流程寫入其他來源，請使用複製活動，在資料流程完成後，從其中一個支援的臨時區域載入該資料。

## <a name="sink-settings"></a>接收設定

新增接收之後，請透過 [**接收**] 索引標籤進行設定。您可以在這裡挑選或建立接收所寫入的資料集。 以下影片解說文字分隔檔案類型的數個不同接收選項：

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tf7T]

![接收設定](media/data-flow/sink-settings.png "接收設定")

**架構漂移：** [架構漂移](concepts-data-flow-schema-drift.md)是 data factory 能夠以原生方式處理您資料流程中的彈性架構，而不需要明確地定義資料行變更。 啟用 [**允許架構漂移**]，在接收資料架構中定義的內容之上寫入其他資料行。

**驗證架構：** 如果選取 [驗證架構]，則如果在來源投射中找不到傳入來源架構的任何資料行，或資料類型不符，資料流程將會失敗。 使用此設定來強制來源資料符合您定義之投影的合約。 這在資料庫來源案例中非常有用，表示資料行名稱或類型已經變更。

## <a name="field-mapping"></a>欄位對應

類似于 [選取] 轉換，在接收器的 [**對應**] 索引標籤中，您可以決定要寫入的傳入資料行。 根據預設，所有輸入資料行（包括漂移資料行）都會對應。 這就是所謂的「**自動對應**」。

當您關閉自動對應時，您可以加入宣告固定的資料行對應或以規則為基礎的對應。 以規則為基礎的對應可讓您撰寫具有模式比對的運算式，而固定對應則會對應邏輯和實體資料行名稱。 如需以規則為基礎之對應的詳細資訊，請參閱[對應資料流程中的資料行模式](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink)。

## <a name="custom-sink-ordering"></a>自訂接收順序

根據預設，資料會以非決定性的順序寫入至多個接收。 執行引擎會在轉換邏輯完成時以平行方式寫入資料，而且接收順序可能會因每次執行而異。 若要指定和精確的接收順序，請在資料流程的 [一般] 索引標籤中啟用**自訂接收順序**。 啟用時，會依遞增順序順序寫入接收。

![自訂接收順序](media/data-flow/custom-sink-ordering.png "自訂接收順序")

## <a name="data-preview-in-sink"></a>接收中的資料預覽

在 debug 叢集上提取資料預覽時，不會有任何資料寫入至您的接收。 會傳回資料外觀的快照集，但不會將任何內容寫入目的地。 若要測試將資料寫入至您的接收，請從管線畫布執行管線偵錯工具。

## <a name="next-steps"></a>後續步驟
既然您已建立資料流程，請將「資料流程」[活動新增至您的管線](concepts-data-flow-overview.md)。
