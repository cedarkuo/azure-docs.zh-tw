---
title: 針對 Azure HDInsight 中的 Apache Oozie 進行疑難排解
description: 針對 Azure HDInsight 中的某些 Apache Oozie 錯誤進行疑難排解。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: 18831832f82cdbc8cec69e368f006f7acd4836c1
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205257"
---
# <a name="troubleshoot-apache-oozie-in-azure-hdinsight"></a>針對 Azure HDInsight 中的 Apache Oozie 進行疑難排解

您可以使用 Apache Oozie UI 來查看 Oozie 記錄。 Oozie UI 也包含工作流程所啟動之 MapReduce 工作的 JobTracker 記錄連結。 疑難排解的模式應該是：

1. 在 Oozie Web UI 中檢視作業。

2. 如果發生錯誤或特定動作失敗，請選取動作，以查看**錯誤訊息**欄位是否提供失敗的詳細資訊。

3. 如果有提供，請使用動作的 URL 以檢視動作的更多詳細資料，例如 JobTracker 記錄。

以下是您可能會遇到的特定錯誤，以及解決方法。

## <a name="ja009-cant-initialize-cluster"></a>JA009：無法初始化叢集

### <a name="issue"></a>問題

作業狀態會變更為 **SUSPENDED**。 作業的詳細資料會將 `RunHiveScript` 狀態顯示為 **START_MANUAL**。 選取該動作會顯示下列錯誤訊息：

    JA009: Cannot initialize Cluster. Please check your configuration for map

### <a name="cause"></a>原因

**job.xml** 檔案中使用的 Azure Blob 儲存體位址未包含儲存體容器或儲存體帳戶名稱。 Blob 儲存體位址的格式必須是 `wasbs://containername@storageaccountname.blob.core.windows.net`。

### <a name="resolution"></a>解決方案

變更作業所使用的 Blob 儲存體位址。

---

## <a name="ja002-oozie-isnt-allowed-to-impersonate-ltusergt"></a>JA002：不允許 Oozie 模擬&lt;使用者&gt;

### <a name="issue"></a>問題

作業狀態會變更為 **SUSPENDED**。 作業的詳細資料會將 `RunHiveScript` 狀態顯示為 **START_MANUAL**。 如果您選取該動作，它將會顯示下列錯誤訊息：

    JA002: User: oozie is not allowed to impersonate <USER>

### <a name="cause"></a>原因

目前的權限設定不允許 Oozie 模擬指定的使用者帳戶。

### <a name="resolution"></a>解決方案

Oozie 可以模擬群組中的**`users`** 使用者。 使用 `groups USERNAME` 查看使用者帳戶所屬的群組。 如果使用者不是**`users`** 群組的成員，請使用下列命令將使用者新增至群組：

    sudo adduser USERNAME users

> [!NOTE]  
> 這可能需要幾分鐘，HDInsight 才能辨識出使用者已新增至該群組。

---

## <a name="launcher-error-sqoop"></a>啟動器錯誤 (Sqoop)

### <a name="issue"></a>問題

作業狀態會變更為 **KILLED**。 作業的詳細資料會將 `RunSqoopExport` 狀態顯示為 **ERROR**。 如果您選取該動作，它將會顯示下列錯誤訊息：

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

### <a name="cause"></a>原因

Sqoop 無法載入存取資料庫時所需的資料庫驅動程式。

### <a name="resolution"></a>解決方案

當您從 Oozie 作業使用 Sqoop 時，您必須將資料庫驅動程式與作業所使用的其他資源 (例如 workflow.xml) 包含在一起。 此外，請從 workflow.xml 的 `<sqoop>...</sqoop>` 區段，參考含有資料庫驅動程式的封存。

例如，針對[使用 Hadoop Oozie 工作流程](hdinsight-use-oozie-linux-mac.md)的作業範例，您會使用下列步驟：

1. `mssql-jdbc-7.0.0.jre8.jar`將檔案複製到 **/tutorials/useoozie**目錄：

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. 修改 `workflow.xml`，在 `</sqoop>` 上方新的一行上新增下列 XML：

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 連接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帳戶，以改善客戶體驗。 將 Azure 社區連接到正確的資源：解答、支援和專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
