---
title: 教學課程：使用 Azure Active Directory 設定 iPass SmartConnect 來自動布建使用者 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory 以自動布建和取消布建使用者帳戶至 iPass SmartConnect。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 43d6de4a-b3a2-439b-95f2-8883fffded52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: fb3a5d03f390b88f9856f03bdc97a35b845874ff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77057510"
---
# <a name="tutorial-configure-ipass-smartconnect-for-automatic-user-provisioning"></a>教學課程：設定 iPass SmartConnect 來自動布建使用者

本教學課程的目的是要示範要在 iPass SmartConnect 和 Azure Active Directory （Azure AD）中執行的步驟，以設定 Azure AD 自動布建和取消布建使用者和/或群組至 iPass SmartConnect。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶。
* [IPass SmartConnect 租](https://www.ipass.com/buy-ipass/)使用者。
* IPass SmartConnect 中具有系統管理員許可權的使用者帳戶。

## <a name="assigning-users-to-ipass-smartconnect"></a>將使用者指派給 iPass SmartConnect

Azure Active Directory 使用稱為「*指派*」的概念，來判斷哪些使用者應接收所選應用程式的存取權。 在自動使用者布建的內容中，只有已指派給 Azure AD 中應用程式的使用者和/或群組會進行同步處理。

在設定並啟用自動使用者布建之前，您應該決定 Azure AD 中的哪些使用者和/或群組需要存取 iPass SmartConnect。 一旦決定後，您可以遵循此處的指示，將這些使用者和/或群組指派給 iPass SmartConnect：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-ipass-smartconnect"></a>將使用者指派給 iPass SmartConnect 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 iPass SmartConnect，以測試自動使用者布建設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 iPass SmartConnect 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特定角色（如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="setup-ipass-smartconnect-for-provisioning"></a>設定 iPass SmartConnect 以提供布建

將 iPass SmartConnect 設定為使用 Azure AD 自動布建使用者之前，您必須從 iPass SmartConnect 管理主控台取出設定資訊：

1. 若要取得對 iPass SmartConnect SCIM 端點進行驗證所需的持有人權杖，請參閱第一次設定 iPass SmartConnect，因為只會提供此值。 
2. 如果您沒有持有人權杖，請與[IPass SmartConnect 的支援小組](mailto:help@ipass.com)聯繫，以取得新的權杖。

## <a name="add-ipass-smartconnect-from-the-gallery"></a>從資源庫新增 iPass SmartConnect

若要使用 Azure AD 來設定 iPass SmartConnect 來自動布建使用者，您需要從 Azure AD 應用程式資源庫將 iPass SmartConnect 新增到受控 SaaS 應用程式清單中。

**若要從 Azure AD 應用程式庫新增 iPass SmartConnect，請執行下列步驟：**

1. 在**[Azure 入口網站](https://portal.azure.com)** 的左側導覽窗格中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [**企業應用程式**]，然後選取 [**所有應用程式**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取窗格頂端的 [**新增應用程式**] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入**IPass SmartConnect**，在結果面板中選取 [ **iPass SmartConnect** ]，然後按一下 [**新增**] 按鈕以新增應用程式。

    ![結果清單中的 iPass SmartConnect](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-ipass-smartconnect"></a>設定自動使用者布建至 iPass SmartConnect 

本節將引導您逐步設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派，在 iPass SmartConnect 中建立、更新和停用使用者和/或群組。

> [!TIP]
>  您也可以選擇啟用 BitaBIZ 的 SAML 型單一登入，請遵循[IPass SmartConnect 單一登入教學](ipasssmartconnect-tutorial.md)課程中提供的指示。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-ipass-smartconnect-in-azure-ad"></a>在 Azure AD 中設定 iPass SmartConnect 的自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [**企業應用程式**]，然後選取 [**所有應用程式**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [iPass SmartConnect] ****。

    ![應用程式清單中的 iPass SmartConnect 連結](common/all-applications.png)

3. 選取 [**布**建] 索引標籤。

    ![布建索引標籤](common/provisioning.png)

4. 將布建模式設定為 [**自動** **]** 。

    ![布建索引標籤](common/provisioning-automatic.png)

5. 在 [**管理員認證**] 區段下`https://openmobile.ipass.com/moservices/scim/v1` ，輸入 [**租使用者 URL**]。 在 [**秘密權杖**] 中，輸入先前抓取的持有人權杖。 按一下 [**測試連接**] 以確保 Azure AD 可以連接到 iPass SmartConnect。 如果連線失敗，請確定您的 iPass SmartConnect 帳戶具有系統管理員許可權，然後再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [檔案]  。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者至 iPass SmartConnect**]。

    ![iPass SmartConnect 使用者對應](media/ipass-smartconnect-provisioning-tutorial/usermapping.png)

9. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步處理到 iPass SmartConnect 的使用者屬性。 選取為 [比對] 屬性**的屬性會**用來比對 iPass SmartConnect 中的使用者帳戶，以進行更新作業。 選取 [儲存]**** 按鈕以認可所有變更。

    ![iPass SmartConnect 使用者對應](media/ipass-smartconnect-provisioning-tutorial/userattribute.png)


10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 若要啟用 iPass SmartConnect 的 Azure AD 布建服務，請在 [**設定**] 區段中，將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建到 iPass SmartConnect 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [**同步處理詳細資料**] 區段來監視進度，並遵循連結來布建活動報告，其中描述 iPass SmartConnect 上的 Azure AD 布建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* iPass SmartConnect 只接受在 iPass SmartConnect 管理主控台中註冊其網域的使用者名稱。  

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶布建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
