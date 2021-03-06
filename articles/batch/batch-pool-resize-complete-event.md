---
title: Azure Batch 集區調整大小完成事件
description: Batch 集區調整大小完成事件的參考。 查看已增加大小且已成功完成的集區範例。
ms.topic: article
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: 4268c9d840aa9dfadd785d74811e9d12ac32ec31
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115885"
---
# <a name="pool-resize-complete-event"></a>集區調整大小完成事件

 集區調整大小完成或失敗時，就會發出此事件。

 下列範例顯示大小增加且成功完成的集區其集區調整大小完成事件內文。

```
{
    "id": "myPool",
    "nodeDeallocationOption": "invalid",
        "currentDedicatedNodes": 10,
        "targetDedicatedNodes": 10,
    "currentLowPriorityNodes": 5,
        "targetLowPriorityNodes": 5,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "resultCode": "Success",
    "resultMessage": "The operation succeeded"
}
```

|元素|類型|注意|
|-------------|----------|-----------|
|`id`|字串|集區的識別碼。|
|`nodeDeallocationOption`|字串|指定當集區大小一直減少時，會自集區中移除節點。<br /><br /> 可能的值包括：<br /><br /> **requeue** – 終止執行中工作並重新排入佇列。 當作業啟用時，工作將再次執行。 一旦工作終止，隨即移除節點。<br /><br /> **terminate** – 終止執行中工作。 工作將不會再次執行。 一旦工作終止，隨即移除節點。<br /><br /> **taskcompletion** – 允許目前執行中工作完成。 等待時不排程任何新的工作。 所有工作完成時，即移除節點。<br /><br /> **Retaineddata** -允許目前執行中的工作完成，然後等待所有工作資料保留期到期。 等待時不排程任何新的工作。 當所有工作保留期到期時即移除節點。<br /><br /> 預設值為 requeue。<br /><br /> 如果集區大小增加，則值會設定為 [無效]****。|
|`currentDedicatedNodes`|Int32|目前指派給集區的專用計算節點數目。|
|`targetDedicatedNodes`|Int32|針對集區要求的專用計算節點數目。|
|`currentLowPriorityNodes`|Int32|目前指派給集區的低優先順序計算節點數目。|
|`targetLowPriorityNodes`|Int32|針對集區要求的低優先順序計算節點數目。|
|`enableAutoScale`|Bool|指定集區大小是否隨著時間自動調整。|
|`isAutoPool`|Bool|指定是否已透過作業的 AutoPool 機制建立集區。|
|`startTime`|Datetime|集區調整大小開始時間。|
|`endTime`|Datetime|集區調整大小完成時間。|
|`resultCode`|字串|調整大小的結果。|
|`resultMessage`|字串| 有關結果的詳細訊息。<br /><br /> 如果調整大小已成功完成，表示作業已成功。|
