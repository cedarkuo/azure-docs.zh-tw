---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: bf8b98db98faf2aef3e634518da4914d46142739
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83635834"
---
|名稱 |描述 |效果 |版本 |GitHub |
|---|---|---|---|---|
|[應該為虛擬機器啟用 Azure 備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |此原則有助於在所有虛擬機器都啟用 Azure 備份服務時進行稽核。 Azure 備份是符合成本效益的一鍵備份解決方案，能夠簡化資料復原，且較其他雲端備份服務易於啟用。 |AuditIfNotExists, Disabled |1.0.0 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[設定將某位置的 VM 備份到相同位置中現有的中央保存庫](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |此原則會將指定位置中 VM 的 Azure 備份保護設定到相同位置中的現有中央保存庫。 此原則僅適用於尚未設定備份的 VM。 建議將此原則指派給不超過 200 個 VM。 如果將此原則指派給超過 200 個 VM，可能會導致備份在定義的排程過後數小時才觸發。 此原則將會增強，以支援更多的 VM 映像。 |deployIfNotExists、auditIfNotExists、disabled |1.0.0 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
|[為復原服務保存庫將診斷設定部署到 Log Analytics 工作區，以納入資源專屬類別。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc717fb0c-d118-4c43-ab3d-ece30ac81fb3) |為復原服務保存庫將診斷設定部署到 Log Analytics 工作區，以串流至資源專屬類別。 如果沒有啟用任何資源專屬類別，則會建立新的診斷設定。 |deployIfNotExists |1.0.0-preview |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/EnableRecoveryServiceVaultDiagnosticSetting_Backup_DeployIfNotExist.json) |
