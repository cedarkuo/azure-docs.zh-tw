---
title: 快速入門：使用 Azure DevOps Starter 建立適用于 Ruby on Rails 的 CI/CD 管線
description: Azure DevOps Starter 可以讓您輕鬆地開始使用 Azure。 您可以透過幾個簡單的步驟，在 Azure 服務上啟動 Ruby Web 應用程式。
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: cde959d8e075b55cb6cbb37479ca49cdd8a8c0c1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233732"
---
# <a name="create-a-cicd-pipeline-for-ruby-on-rails-by-using-azure-devops-starter"></a>使用 Azure DevOps Starter 建立適用于 Ruby on Rails 的 CI/CD 管線

使用 Azure DevOps Starter，為您的 Ruby on Rails 應用程式設定持續整合（CI）和持續傳遞（CD）。 DevOps Starter 可簡化 Azure DevOps 組建和發行管線的初始設定。

如果您沒有 Azure 訂用帳戶，可以透過 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) 取得一個免費的訂用帳戶。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

Azure DevOps Starter 會在 Azure Repos 中建立 CI/CD 管線。 您可以建立新的 Azure DevOps 組織或使用現有組織。 DevOps Starter 也會在您選擇的 Azure 訂用帳戶中建立 Azure 資源。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在搜尋方塊中，輸入**DevOps Starter**，然後選取。 按一下 [**新增] 以建立**新的 [新增]。

    ![DevOps 入門儀表板](_img/azure-devops-starter-aks/search-devops-starter.png) 

## <a name="select-a-sample-app-and-azure-service"></a>選取應用程式範例和 Azure 服務

1. 選取 **Ruby** 應用程式範例。

1. 選取 [Ruby on Rails]  應用程式架構。 完成時，選取 [下一步]  。

1. **Linux 上的 Web 應用程式**是預設的部署目標。  您可以選擇性地選取 [用於容器的 Web App]  。 您先前選擇的應用程式架構，會指出這裡可用的 Azure 服務部署目標類型。 
    
1. 選取您選擇的目標服務，然後選取 [下一步]  。

## <a name="configure-azure-devops-and-an-azure-subscription"></a>設定 Azure DevOps 與 Azure 訂用帳戶 

1. 免費建立新的 Azure DevOps 組織或選擇現有組織。 

1. 為您的 Azure DevOps 專案輸入名稱。 

1. 選取 Azure 訂用帳戶和位置、輸入應用程式名稱，然後選取 [完成]  。  
    幾分鐘後，[DevOps 入門儀表板] 就會顯示在 Azure 入口網站中。 系統會在您 Azure DevOps 組織中的存放庫中設定範例應用程式、執行建置，然後將您的應用程式部署到 Azure。 
    
    儀表板可顯示您的程式碼存放庫、您的 CI/CD 管線，和您在 Azure 中的應用程式。 從右側選取 [瀏覽]  以檢視執行中應用程式。

    ![儀表板檢視](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-your-code-changes-and-execute-the-cicd"></a>認可程式碼變更並執行 CI/CD

Azure DevOps Starter 會在 Azure Pipelines 或 GitHub 中建立 Git 存放庫。 若要檢視存放庫並變更您應用程式的程式碼，請執行下列作業：

1. 在 DevOps 入門儀表板的左側，選取您主要分支的連結。 此連結會開啟新建立 Git 存放庫的檢視。

1. 若要檢視存放庫的複製 URL，請選取右上方的 [複製]  。 您可以在最愛的 IDE 中複製 Git 存放庫。 在接下來的幾個步驟中，您可以使用網頁瀏覽器，直接進行和認可主要分支的程式碼變更。

1. 從左側移至 app/views/pages/home.html.erb  檔案，然後選取 [編輯]  。

1. 對檔案進行變更。 例如，修改其中一個 div 標籤內的部分文字。

1. 選取 [認可]  ，然後儲存您的變更。

1. 在您的瀏覽器中，移至 DevOps 入門儀表板。 建置應該仍在進行中。 您所做的變更會透過 CI/CD 管線自動建置及部署。

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>檢查 Azure Pipelines 的 CI/CD 管線

Azure DevOps Starter 會自動在您的 Azure DevOps 組織中設定完整的 CI/CD 管線。 瀏覽管線，並視需要進行自訂。 若要讓您自己熟悉 Azure DevOps 建置和發行管線，請執行下列作業：

1. 移至 DevOps 入門儀表板。

1. 從頂端選取 [建置管線]  。 瀏覽器索引標籤會顯示新專案的建置管線。

1. 指向 [**狀態**] 欄位，然後選取省略號（...）。功能表會顯示數個選項，例如將新的組建排入佇列、暫停組建，以及編輯組建管線。

1. 選取 [編輯]  。

1. 在此窗格中，您可以檢查建置管線的各種工作。 建置會執行各種工作，例如從 Git 存放庫擷取來源、還原相依性，以及發佈用來進行部署的輸出。

1. 在建置管線的頂端，選取建置管線名稱。

1. 將組建管線的名稱變更為更具描述性的名稱，選取 [**儲存 & 佇列**]，然後選取 [**儲存**]。

1. 在建置管線名稱下，選取 [記錄]****。 此窗格會顯示近期建置變更的稽核線索。 Azure DevOps 會追蹤對建置管線進行的任何變更，且可讓您比較版本。

1. 選取 [**觸發**程式]。  DevOps Starter 會自動建立 CI 觸發程式，且每次對存放庫的認可都會啟動新的組建。 您可以選擇性地選擇要在 CI 程序中包含還是排除分支。

1. 選取 [保留期]****。 根據案例，您可以指定原則來保留或移除特定數目的組建。

1. 選取 [建置及發行]****，然後選取 [版本]****。  DevOps Starter 會建立發行管線來管理 Azure 的部署。

1. 選取發行管線旁邊的省略符號 (...)，然後選取 [編輯]****。 發行管線中包含 [管線]**，它會定義發行程序。

1. 在 [成品]**** 下，選取 [置放]****。 您先前檢查的建置管線會產生用於成品的輸出。 

1. 在 [置放]**** 圖示的右側，選取 [持續部署觸發程序]****。 這個發行管線已啟用 CD 觸發程序，每次有新的建置成品可用時，它就會執行部署。 您可以選擇性地停用觸發程序，因此需要手動執行部署。 

1. 從左側選取 [工作]****。 工作是您部署程序所執行的活動。 在此範例中，會建立一個工作來部署到 Azure App Service。

1. 從右側選取 [檢視版本]****，以顯示版本的歷程記錄。

1. 選取發行旁邊的省略符號 (...)，然後選取 [開啟]****。 您可以瀏覽數個功能表，例如版本摘要、相關聯的工作項目及測試。

1. 選取 [認可]****。 此檢視會顯示與此部署相關聯的程式碼認可。 

1. 選取 [**記錄**]。 記錄包含關於部署程序的實用資訊。 您可以在部署期間和部署之後加以檢視。

## <a name="clean-up-resources"></a>清除資源

如果不再需要，您可以刪除在此快速入門中建立的 Azure App Service 執行個體和相關資源。 若要這麼做，請使用 DevOps 入門儀表板上的 [**刪除**] 功能。

## <a name="next-steps"></a>後續步驟

若要深入了解如何修改建置與發行管線以符合小組的需求，請參閱：

> [!div class="nextstepaction"]
> [定義多階段的持續部署 (CD) 管線](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
