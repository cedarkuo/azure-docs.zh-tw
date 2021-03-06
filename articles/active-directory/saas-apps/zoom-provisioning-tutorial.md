---
title: 教學課程：使用 Azure Active Directory 設定 Zoom 進行自動使用者布建 |Microsoft Docs
description: 瞭解如何自動布建和取消布建使用者帳戶，從 Azure AD 到縮放。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: Zhchia
ms.openlocfilehash: 94c261da0c935cb7a41dde768069099b4e5ed251
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80384070"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>教學課程：設定自動使用者布建的 Zoom

本教學課程說明您需要在 Zoom 和 Azure Active Directory （Azure AD）中執行的步驟，以設定自動使用者布建。 設定之後，Azure AD 會自動布建及取消布建使用者和群組，以使用 Azure AD 布建服務進行[縮放](https://zoom.us/pricing/)。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 以 Zoom 建立使用者
> * 當使用者不再需要存取權時，移除 Zoom 中的使用者
> * 讓使用者屬性在 Azure AD 和 Zoom 之間保持同步
> * [單一登入](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-tutorial)以進行縮放（建議）

## <a name="prerequisites"></a>先決條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)使用者。
* Azure AD 中的使用者帳戶，具有設定布建的[許可權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)（例如，應用程式系統管理員、雲端應用程式系統管理員、應用程式擁有者或全域管理員）。 
* [Zoom 租](https://zoom.us/pricing)使用者。
* 以 [系統管理員] 許可權縮放的使用者帳戶。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃您的布建部署
1. 深入瞭解布建[服務的運作方式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 判斷誰會在布建[範圍](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)內。
3. 判斷要[在 Azure AD 和 Zoom 之間對應](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的資料。 

## <a name="step-2-configure-zoom-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定 Zoom 以支援以 Azure AD 布建

1. 登入您的[Zoom 管理主控台](https://zoom.us/signin)。 在左側流覽窗格中，流覽至 [**適用于開發人員的 Advanced > Zoom** ]。

    ![縮放整合](media/zoom-provisioning-tutorial/zoom01.png)

2. 流覽至頁面右上角的 [**管理**]。 

    ![縮放安裝](media/zoom-provisioning-tutorial/zoom02.png)

3. 流覽至您建立的 Azure AD 應用程式。 
    
    ![縮放應用程式](media/zoom-provisioning-tutorial/zoom03.png)

4. 在左側流覽窗格中選取 [**應用程式認證**]。

    ![縮放應用程式](media/zoom-provisioning-tutorial/zoom04.png)

5. 複製並儲存**JWT 權杖**。 此值將會在 [Azure 入口網站的 Zoom 應用程式之 [布建] 索引標籤的 [**密碼權杖**] 欄位中輸入。 如果您需要新的非過期權杖，您將需要重新設定會自動產生新權杖的到期時間。 

    ![縮放安裝](media/zoom-provisioning-tutorial/zoom05.png)

## <a name="step-3-add-zoom-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫新增 Zoom

從 Azure AD 應用程式庫新增 Zoom，開始管理布建到縮放。 如果您先前已設定 [縮放為 SSO]，您可以使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 在[這裡](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)深入瞭解如何從資源庫新增應用程式。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在布建範圍內的人員 

Azure AD 布建服務可讓您根據指派給應用程式的人員，或根據使用者/群組的屬性，來界定將布建的物件。 如果您選擇根據指派將布建到您的應用程式的範圍，您可以使用下列[步驟](../manage-apps/assign-user-or-group-access-portal.md)，將使用者和群組指派給應用程式。 如果您選擇將僅根據使用者或群組的屬性布建的使用者範圍，您可以使用範圍篩選器，如[這裡](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述。 

* 將使用者和群組指派給 Zoom 時，您必須選取 [**預設存取**] 以外的角色。 具有預設存取角色的使用者會從布建中排除，且在布建記錄中會被標示為不有效率。 如果應用程式上唯一可用的角色是預設存取角色，您可以[更新應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)來新增其他角色。 

* 從小規模開始。 在推出給所有人之前，先使用一小組的使用者和群組進行測試。 當布建的範圍設定為 [已指派的使用者和群組] 時，您可以藉由將一或兩個使用者或群組指派給應用程式來控制此項。 當 [範圍] 設定為 [所有使用者和群組] 時，您可以指定以[屬性為基礎的範圍篩選器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-zoom"></a>步驟 5。 設定自動使用者布建以縮放 

本節將引導您逐步設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派，在 TestApp 中建立、更新和停用使用者和/或群組。

### <a name="to-configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>若要設定自動使用者布建以進行縮放 Azure AD：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [**企業應用程式**]，然後選取 [**所有應用程式**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Zoom]****。

    ![應用程式清單中的 Zoom 連結](common/all-applications.png)

3. 選取 [**布**建] 索引標籤。

    ![布建索引標籤](common/provisioning.png)

4. 將布建模式設定為 [**自動** **]** 。

    ![布建索引標籤](common/provisioning-automatic.png)

5. 在 [**管理員認證**] 區段下`https://api.zoom.us/scim` ，輸入**租使用者 URL**。 輸入稍早在**秘密權杖**中所取得的**JWT 權杖**值。 按一下 [**測試連接**] 以確保 Azure AD 可以連接到 Zoom。 如果連線失敗，請確定您的縮放帳戶具有系統管理員許可權，然後再試一次。

    ![縮放布建](./media/zoom-provisioning-tutorial/provisioning.png)

6. 在 [**通知電子郵件**] 欄位中，輸入應收到布建錯誤通知之個人或群組的電子郵件地址，然後選取 [**發生失敗時傳送電子郵件通知**] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]  。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者以縮放**]。

9. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步處理到 Zoom 的使用者屬性。 選取為 [比對] 屬性**的屬性會**用來比對 [縮放] 中的使用者帳戶以進行更新作業。 如果您選擇變更相符的[目標屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，則必須確定 Zoom API 支援根據該屬性來篩選使用者。 選取 [儲存]**** 按鈕以認可所有變更。

   |屬性|類型|
   |---|---|
   |userName|字串|
   |作用中|布林值|
   |name.givenName|字串|
   |name.familyName|字串|
   |電子郵件 [type eq "work"]|字串|
   |urn： ietf： params： scim：架構：擴充功能： enterprise：2.0： User：部門|字串|

10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 若要啟用 Zoom 的 Azure AD 布建服務，請在 [**設定**] 區段中將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會啟動 [**設定**] 區段的 [**範圍**] 中定義的所有使用者和群組的初始同步處理迴圈。 初始週期比後續迴圈花費更多時間執行，只要 Azure AD 布建服務正在執行，這大約每40分鐘就會發生一次。 

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
設定布建之後，請使用下列資源來監視您的部署：

1. 使用布建[記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)來判斷哪些使用者已成功布建或失敗
2. 檢查[進度](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)列以查看布建週期的狀態，以及關閉其完成的方式
3. 如果布建設定似乎處於狀況不良的狀態，應用程式將會進入隔離。 [在這裡](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)深入瞭解隔離狀態。  

## <a name="connector-limitations"></a>連接器限制
* [縮放] 僅允許目前最多9999個基本使用者。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶布建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
