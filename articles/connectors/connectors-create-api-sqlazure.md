---
title: 連接到 SQL Server 或 Azure SQL Database
description: 使用 Azure Logic Apps 將內部部署或雲端中 SQL 資料庫的工作自動化
services: logic-apps
ms.suite: integration
ms.reviewer: klam; logicappspm
ms.topic: conceptual
ms.date: 11/08/2019
tags: connectors
ms.openlocfilehash: 93b63d332f00c31a352c11e483fc3ce5cb45a922
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789185"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 將 SQL Server 或 Azure SQL Database 的工作流程自動化

本文說明如何使用 SQL Server 連接器，從邏輯應用程式內部存取 SQL 資料庫中的資料。 如此一來，您就可以藉由建立邏輯應用程式，自動執行管理 SQL 資料和資源的工作、進程或工作流程。 SQL Server 連接器適用于內部[部署 SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation)和[雲端式 Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)。

您可以建立會由 SQL 資料庫或其他系統 (例如，Dynamics CRM Online) 中的事件所觸發而執行的邏輯應用程式。 您的邏輯應用程式也可以取得、插入及刪除資料，以及執行 SQL 查詢和預存程式。 例如，您可以建立一個邏輯應用程式，以自動檢查 Dynamics CRM Online 中的新記錄、將專案新增至 SQL 資料庫中的任何新記錄，然後傳送有關已新增專案的電子郵件警示。

如果您還不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 如需連接器特定的技術資訊、限制和已知問題，請參閱[SQL Server 連接器參考頁面](https://docs.microsoft.com/connectors/sql/)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [SQL Server 資料庫](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database)或[Azure SQL database](../sql-database/sql-database-get-started-portal.md)

  資料表內必須有資料，邏輯應用程式才能在呼叫作業時傳回結果。 如果您建立的是 Azure SQL Database，則可以使用隨附的資料庫範例。

* 伺服器名稱名稱、資料庫名稱、使用者名稱和密碼。 需要有這些認證，才能授權邏輯存取 SQL 伺服器。

  * 如果是 SQL Server，可在連接字串中找到這些詳細資料：

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * 如果是 Azure SQL Database，可在連接字串中找到這些詳細資料，也可在 Azure 入口網站的 SQL Database 屬性下找到：

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* 內部[部署資料閘道](../logic-apps/logic-apps-gateway-install.md)安裝在本機電腦上，以及在這些案例[的 Azure 入口網站中建立的 Azure 資料閘道資源](../logic-apps/logic-apps-gateway-connection.md)：

  * 您的邏輯應用程式不會在[整合服務環境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中執行。

  * 您的邏輯應用程式會在整合服務環境中*執行*，但您必須將 Windows 驗證用於您的 SQL Server 連接。 針對此案例，請使用 SQL Server 連接器的非 ISE 版本以及資料閘道，因為 ISE 版本不支援 Windows 驗證。

* 需要存取 SQL 資料庫的邏輯應用程式。 若要使用 SQL 觸發程序啟動邏輯應用程式，您需要[空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>新增 SQL 觸發程式

在 Azure Logic Apps 中，每個邏輯應用程式都必須使用[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)啟動，而該觸發程序會在特定事件發生或符合特定條件時引發。 每次引發觸發程式時，Logic Apps 引擎都會建立邏輯應用程式實例，並開始執行邏輯應用程式的工作流程。

1. 在 Azure 入口網站或 Visual Studio 中，建立空白的邏輯應用程式，以開啟 Logic Apps 設計工具。 這個範例會使用 Azure 入口網站。

1. 在設計工具的 [搜尋] 方塊中，輸入「sql server」作為篩選準則。 從觸發程序清單中，選取您想要的 SQL 觸發程序。

   這個範例會使用 [**建立專案時**] 觸發程式。

   ![選取 [建立專案時] 觸發程式](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. 如果系統提示您建立連接，請[立即建立您的 SQL 連接](#create-connection)。 如果您的連接存在，請選取**資料表名稱**。

   ![選取您想要的資料表](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. 設定 [間隔]**** 和 [頻率]**** 屬性，以指定邏輯應用程式檢查資料表的頻率。

   此觸發程式只會從選取的資料表傳回一個資料列，其他則不會。 若要執行其他工作，請新增其他動作來執行您想要的工作。 例如，若要查看此資料列中的資料，您可以加入其他動作，以建立包含傳回資料列中之欄位的檔案，然後傳送電子郵件警示。 若要深入瞭解此連接器的其他可用動作，請參閱[連接器的參考頁面](https://docs.microsoft.com/connectors/sql/)。

1. 當您完成時，請在設計工具工具列上選取 [**儲存**]。

   此步驟會自動在 Azure 中即時啟用並發佈邏輯應用程式。

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>新增 SQL 動作

在 Azure Logic Apps 中，[動作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是工作流程中跟隨在觸發程序或另一個動作之後的步驟。 在此範例中，邏輯應用程式會使用[循環觸發程序](../connectors/connectors-native-recurrence.md)來啟動，並且會呼叫動作以從 SQL 資料庫取得資料列。

1. 在 Azure 入口網站或 Visual Studio 的邏輯應用程式設計工具中，開啟邏輯應用程式。 這個範例會使用 Azure 入口網站。

1. 在您要新增 SQL 動作的觸發程式或動作底下，選取 [**新增步驟**]。

   ![將新步驟新增至您的邏輯應用程式](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   若要在現有步驟之間新增動作，請將滑鼠放在連接箭頭上。 選取顯示的加號（**+**），然後選取 [**新增動作**]。

1. 在 [**選擇動作**] 下的 [搜尋] 方塊中，輸入「sql server」作為篩選準則。 從 [動作] 清單中，選取您想要的 SQL 動作。

   這個範例使用 [**取得資料列**] 動作，其會取得單一記錄。

   ![尋找並選取 SQL [取得資料列] 動作](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   此動作只會從選取的資料表中傳回一個資料列，其他則不會。 若要查看此資料列中的資料，您可以新增其他動作，以建立包含所傳回列之欄位的檔案，並將該檔案儲存在雲端儲存體帳戶中。 若要深入瞭解此連接器的其他可用動作，請參閱[連接器的參考頁面](https://docs.microsoft.com/connectors/sql/)。

1. 如果系統提示您建立連接，請[立即建立您的 SQL 連接](#create-connection)。 如果您的連接存在，請選取**資料表名稱**，然後輸入您想要的記錄的資料**列識別碼**。

   ![輸入資料表名稱和資料列識別碼](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. 當您完成時，請在設計工具工具列上選取 [**儲存**]。

   此步驟會自動在 Azure 中即時啟用並發佈邏輯應用程式。

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>連接到您的資料庫

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>處理大量資料

有時候，您必須使用結果集，如此一來，連接器不會同時傳回所有結果，或者您想要更有效地控制結果集的大小和結構。 以下是一些您可以處理這類大型結果集的方法：

* 若要協助您以較小的集合來管理結果，請開啟*分頁*功能。 如需詳細資訊，請參閱[使用分頁取得大量資料、記錄和專案](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md)。

* 建立預存程式，以您想要的方式來組織結果。

  取得或插入多個資料列時，您的邏輯應用程式可以在這些[限制](../logic-apps/logic-apps-limits-and-config.md)內使用[*until 迴圈*](../logic-apps/logic-apps-control-flow-loops.md#until-loop)，逐一查看這些資料列。 不過，當您的邏輯應用程式必須處理的記錄集很大（例如，數千或數百萬個數據列）時，您會想要將對資料庫的呼叫所產生的成本降至最低。

  若要以您想要的方式組織結果，您可以建立在 SQL 實例中執行的[*預存*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine)程式，並使用**SELECT-ORDER BY**語句。 此解決方案可讓您對結果的大小和結構擁有更好的控制能力。 邏輯應用程式會使用 SQL Server 連接器的**執行預存程序**動作來呼叫預存程序。

  如需更多解決方案的詳細資訊，請參閱下列文章：

  * [可供使用 Logic Apps 傳輸大量資料的 SQL 分頁](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT - ORDER BY 子句](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>連接器特定的詳細資料

如需有關此連接器的觸發程式、動作和限制的技術資訊，請參閱[連接器的參考頁面](https://docs.microsoft.com/connectors/sql/)。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[Azure Logic Apps 的其他連接器](../connectors/apis-list.md)
