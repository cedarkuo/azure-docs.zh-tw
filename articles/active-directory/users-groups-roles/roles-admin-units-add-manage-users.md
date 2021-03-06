---
title: 新增、移除和列出系統管理單位中的使用者（預覽）-Azure Active Directory |Microsoft Docs
description: 在 Azure Active Directory 的管理單位中管理使用者及其角色許可權
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c2c5c083115440e1e4da203f39f2b32734458c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684963"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>在 Azure Active Directory 的管理單位中新增及管理使用者

在 Azure Active Directory （Azure AD）中，您可以將使用者新增至管理單位（AU），以取得更細微的控制管理範圍。

如需準備將 PowerShell 和 Microsoft Graph 用於管理單位管理的步驟，請參閱[開始](roles-admin-units-manage.md#get-started)使用。

## <a name="add-users-to-an-au"></a>將使用者新增至 AU

### <a name="azure-portal"></a>Azure 入口網站

您可以透過兩種方式將使用者指派給管理單位。

1. 個別指派

    1. 您可以移至入口網站中的 [Azure AD]，並選取 [使用者]，然後選取要指派給管理單位的使用者。 接著，您可以在左面板中選取 [管理單位]。 您可以按一下 [指派至管理單位]，然後選取要指派使用者的管理單位，將使用者指派給一個或多個管理單位。

       ![選取 [新增]，然後輸入管理單位的名稱](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

    1. 您可以移至入口網站中的 Azure AD，然後在左窗格中選取 [管理單位]，然後選取要指派使用者的系統管理單位。 選取左窗格上的 [所有使用者]，然後選取 [新增成員]。 接著，您可以從右窗格中，選取要指派給管理單位的一或多個使用者。

        ![選取管理單位，然後選取 [新增成員]](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

1. 大量指派

    移至入口網站中的 Azure AD，然後選取 [管理單位]。 選取要新增使用者的系統管理單位。 按一下 [所有使用者] 來繼續，> 從 .csv 檔案新增成員]。 接著，您可以下載 CSV 範本並編輯檔案。 格式很簡單，而且需要在每一行新增單一 UPN。 檔案備妥之後，請將它儲存在適當的位置，然後在步驟3中將它上傳（如快照中所強調）。

    ![將使用者大量指派給管理單位](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrativeunitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId

在上述範例中，會使用 Cmdlet AzureADAdministrativeUnitMember 將使用者新增至管理單位。 要在其中新增使用者之管理單位的物件識別碼，以及要加入之使用者的物件識別碼會做為引數。 反白顯示的區段可能會視特定環境的需要而變更。

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref
    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/users/{id}"
    }

範例：

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/johndoe@fabidentity.com"
    }

## <a name="list-administrative-units-for-a-user"></a>列出使用者的系統管理單位

### <a name="azure-portal"></a>Azure 入口網站

在 Azure 入口網站您可以前往 Azure AD > 使用者] 來開啟使用者的設定檔。 按一下 [使用者] 以開啟使用者的設定檔。

![在 Azure Active Directory 中開啟使用者的設定檔](./media/roles-admin-units-add-manage-users/user-profile-admin-units.png)

選取左面板上的 [**管理單位**]，以查看已指派使用者的系統管理單位清單。

![列出使用者的系統管理單位](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $userObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/users//memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-single-user-from-an-au"></a>從 AU 移除單一使用者

### <a name="azure-portal"></a>Azure 入口網站

有兩種方式可將使用者從管理單位移除。 在 [Azure 入口網站您可以前往 [ **Azure AD** > **使用者**] 來開啟使用者的設定檔。 選取使用者以開啟使用者的設定檔。 選取您想要移除使用者的系統管理單位，然後選取 [**從管理單位移除**]。

![從使用者設定檔將使用者從管理單位移除](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

您也可以選取您想要移除使用者的系統管理單位，以**Azure AD** > **管理單位**移除使用者。 選取使用者，然後選取 [**移除成員**]。
  
![移除管理單位層級的使用者](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberUserObjId

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<user-id>/$ref

## <a name="bulk-remove-more-than-one-user"></a>大量移除一個以上的使用者

您可以移至 Azure AD > 管理單位，並選取您想要移除使用者的系統管理單位。 按一下 [大量移除成員]。 下載 CSV 範本，以提供要移除的使用者清單。

以相關的使用者專案編輯已下載的 CSV 範本。 請勿移除範本的前兩個數據列。 在每個資料列中加入一個使用者 UPN。

![編輯 CSV 檔案以從管理單位移除大量使用者](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

在檔案中儲存專案之後，請上傳檔案，然後選取 [**提交**]。

![提交大量上傳檔案](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>後續步驟

- [將角色指派給管理單位](roles-admin-units-assign-roles.md)
- [將群組新增至管理單位](roles-admin-units-add-manage-groups.md)
