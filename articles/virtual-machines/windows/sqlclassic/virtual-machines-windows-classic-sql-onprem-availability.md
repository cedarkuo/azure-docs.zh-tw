---
title: 將內部部署 Always On 可用性群組延伸至 Azure | Microsoft Docs
description: 本教學課程使用隨傳統部署模型建立的資源，並說明如何在 SQL Server Management Studio (SSMS) 中使用 [加入複本精靈]，以在 Azure 中加入 Always On 可用性群組複本。
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 7ca7c423-8342-4175-a70b-d5101dfb7f23
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 4521c2c112c93e83144cfc84d600208817b2ccac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75978055"
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>將內部部署 Always On 可用性群組延伸至 Azure
Always On 可用性群組可透過新增次要複本，為資料庫群組提供高可用性。 如果發生故障，這些複本便可容錯移轉資料庫。 此外，它們還可用來卸載讀取工作負載或備份工作。

您可使用 SQL Server 佈建一或多個 Azure VM，並將它們以複本形式新增至內部部署可用性群組，藉此將內部部署可用性群組延伸至 Microsoft Azure。

本教學課程假設您具有下列項目：

* 有效的 Azure 訂用帳戶。 您可以 [註冊免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* 現有的 Always On 可用性群組內部部署。 如需可用性群組的詳細資訊，請參閱 [Always On 可用性群組](https://msdn.microsoft.com/library/hh510230.aspx)。
* 內部部署網路與 Azure 虛擬網路之間的連線 如需關於建立此虛擬網路的詳細資訊，請參閱[使用 Azure 入口網站 (傳統) 建立站對站連線](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)。

> [!IMPORTANT] 
> Azure 有兩種不同的部署模型可用於建立及使用資源： [Resource Manager 和傳統](../../../azure-resource-manager/management/deployment-models.md)。 本文涵蓋之內容包括使用傳統部署模型。 Microsoft 建議讓大部分的新部署使用 Resource Manager 模式。

## <a name="add-azure-replica-wizard"></a>加入 Azure 複本精靈
本章節將說明如何使用 [加入 Azure 複本精靈] **** 延伸您的 Always On 可用性群組解決方案使其包含 Azure 複本。

> [!IMPORTANT]
> [加入 Azure 複本精靈]**** 僅支援以傳統部署模型建立的虛擬機器。 新的 VM 部署應該使用較新的 Resource Manager 模型。 如果您使用具有 Resource Manager 的 Vm，則必須使用 Transact-sql 命令手動新增次要 Azure 複本（此處未顯示）。 此精靈並無法在 Resource Manager 的案例中運作。

1. 從 SQL Server Management Studio 中，展開 [ **Always On 高可用性** > **可用性群組** > **] [您的可用性群組的名稱]**。
2. 以滑鼠右鍵按一下 [可用性複本]****，接著按一下 [加入複本]****。
3. 根據預設，[將複本加入至可用性群組精靈] **** 會隨即顯示。 按 [下一步]  。  如果先前啟動此精靈時，您已在頁面下方選取 [不要再顯示此頁面]**** 選項，便不會顯示此畫面。
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. 您必須連線到所有現有的次要複本。 您可以按一下 [連接...]**** 位於每個複本旁，或可以按一下 [全部連接...]**** 。 驗證完成後，按 [下一步]**** 進展到下一個畫面。
5. [指定複本]**** 頁面的頂端會列出多個索引標籤：[複本]****、[端點]****、[備份偏好設定]**** 和 [接聽程式]****。 從 [複本]**** 索引標籤按一下 [新增 Azure 複本...]****  以啟動 [加入 Azure 複本精靈]。
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. 如果您先前已安裝 Azure 管理憑證，請從本機的 Windows 憑證存放區選取現有的 Azure 管理憑證。 如果您先前已使用過 Azure 訂用帳戶的識別碼，請選取或輸入該識別碼。 您可以按 [下載] 下載並安裝 Azure 管理憑證，然後使用 Azure 帳戶下載訂用帳戶清單。
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. 請在頁面中的每個欄位填入值，這些值將用來建立裝載複本的 Azure 虛擬機器 (VM)。
   
   | 設定 | 描述 |
   | --- | --- |
   | **影像** |選取所需的作業系統和 SQL Server 組合 |
   | **VM 大小** |選取最適合業務需求的 VM 大小 |
   | **VM 名稱** |指定新 VM 的唯一名稱。 名稱必須介於 3 到 15 個字元，可以包含英數字和連字號，且必須以英文字母開頭，並以英文字母或數字結尾。 |
   | **VM 使用者名稱** |指定將成為 VM 系統管理員帳戶的使用者名稱 |
   | **VM 系統管理員密碼** |指定新帳戶的密碼 |
   | **確認密碼** |確認新帳戶的密碼 |
   | **虛擬網路** |指定新 VM 應使用的 Azure 虛擬網路。 如需虛擬網路的詳細資訊，請參閱 [虛擬網路概觀](../../../virtual-network/virtual-networks-overview.md)。 |
   | **虛擬網路子網路** |指定新 VM 應使用的虛擬網路子網路 |
   | **Domain** |請確認預先填入的網域值是正確的 |
   | **網域使用者名稱** |指定位於本機叢集節點上之本機系統管理員群組的帳戶 |
   | **密碼** |指定網域使用者名稱的密碼 |
8. 按一下 [確定]**** 驗證部署設定。
9. 接著會顯示法律條款。 閱讀後，如果您同意這些條款，請按一下 [確定]****。
10. [指定複本]**** 頁面會再次顯示。 確認 [複本]****、[端點]**** 和 [備份偏好設定]****。 修改設定以符合您的業務需求。  如需這些索引標籤上所含參數的詳細資訊，請參閱 [指定複本頁面 (新增可用性群組精靈/加入複本精靈)](https://msdn.microsoft.com/library/hh213088.aspx)。請注意，您無法使用包含 Azure 複本之可用性群組的 [接聽程式] 索引標籤來建立接聽程式。 此外，如果接聽程式已在精靈啟動之前建立，您將會收到一則訊息指出在 Azure 中不支援該精靈。 在**建立可用性群組接聽程式**一節中，我們將介紹如何建立接聽程式。
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. 按 [下一步]  。
12. 在 [選取初始資料同步處理]**** 頁面上選取您要使用的資料同步處理方法，然後按 **[下一步]**。 在大部分的情況下，請選取 [完整資料同步處理] ****。 如需資料同步處理方法的詳細資訊，請參閱 [選取初始資料同步處理頁面 (Always On 可用性群組精靈)](https://msdn.microsoft.com/library/hh231021.aspx)。
13. 檢閱 [驗證] **** 頁面中的結果。 修正未解決的問題，並視需要重新執行驗證。 按 [下一步]  。
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. 在 [摘要]**** 頁面上檢閱設定，然後按一下 **[完成]**。
15. 佈建程序隨即開始。 精靈成功執行完後，按一下 [關閉]**** 結束精靈。

> [!NOTE]
> [加入 Azure 複本精靈] 會在下列位置建立記錄檔：Users\User Name\AppData\Local\SQL Server\AddReplicaWizard。 此記錄檔可用來疑難排解失敗的 Azure 複本部署。 如果精靈無法執行任何動作，則所有先前的作業皆會回復，包括刪除佈建的 VM。
> 
> 

## <a name="create-an-availability-group-listener"></a>建立可用性群組接聽程式
建立可用性群組後，您應該建立供用戶端連線到複本的接聽程式。 接聽程式會將連入連線導向主要或唯讀次要複本。 如需接聽程式的詳細資訊，請參閱 [在 Azure 中設定 Always On 可用性群組的 ILB 接聽程式](../classic/ps-sql-int-listener.md)。

## <a name="next-steps"></a>後續步驟
除了使用 [加入 Azure 複本精靈] **** 將您的 Always On 可用性群組延伸至 Azure 之外，您也可以將部分 SQL Server 工作負載完全移動至 Azure。 若要開始進行，請參閱 [在 Azure 上佈建 SQL Server 虛擬機器](../sql/virtual-machines-windows-portal-sql-server-provision.md)。

如需在 Azure VM 中執行 SQL Server 的其他相關主題，請參閱 [Azure 虛擬機器上的 SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)。

