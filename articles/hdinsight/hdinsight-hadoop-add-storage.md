---
title: 將其他 Azure 儲存體帳戶新增至 HDInsight
description: 瞭解如何將其他 Azure 儲存體帳戶新增至現有的 HDInsight 叢集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: d5dde8c45331cf8c443aba86c96ba12c8277472c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192479"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>將其他儲存體帳戶新增至 HDInsight

瞭解如何使用腳本動作，將其他 Azure 儲存體*帳戶*新增至 HDInsight。 本檔中的步驟會將儲存體*帳戶*新增至現有的 HDInsight 叢集。 本文適用于儲存體*帳戶*（非預設叢集儲存體帳戶），而不是[`Azure Data Lake Storage Gen1`](hdinsight-hadoop-use-data-lake-store.md)其他儲存體（例如和[`Azure Data Lake Storage Gen2`](hdinsight-hadoop-use-data-lake-storage-gen2.md)）。

> [!IMPORTANT]  
> 本檔中的資訊是關於在叢集建立之後，將其他儲存體帳戶新增至叢集。 如需在叢集建立期間新增儲存體帳戶的資訊，請參閱[使用 Apache Hadoop、Apache Spark、Apache Kafka 等在 HDInsight 中設定叢集](hdinsight-hadoop-provision-linux-clusters.md)。

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的 Hadoop 叢集。 請參閱[開始在 Linux 上使用 HDInsight](./hadoop/apache-hadoop-linux-tutorial-get-started.md)。
* 儲存體帳戶名稱和金鑰。 請參閱[管理儲存體帳戶存取金鑰](../storage/common/storage-account-keys-manage.md)。
* 如果使用 PowerShell，您將需要 AZ 模組。  請參閱[Azure PowerShell 的總覽](https://docs.microsoft.com/powershell/azure/overview)。

## <a name="how-it-works"></a>運作方式

在處理期間，腳本會執行下列動作：

* 如果儲存體帳戶已存在於叢集的 core-site.xml 設定中，腳本會結束，且不會執行任何進一步的動作。

* 確認儲存體帳戶存在並可使用金鑰來存取。

* 使用叢集認證加密金鑰。

* 將儲存體帳戶新增至 core-site.xml 檔案。

* 停止並重新啟動 Apache Oozie、Apache Hadoop YARN、Apache Hadoop MapReduce2，以及 Apache Hadoop HDFS 服務。 停止並啟動這些服務可讓它們使用新的儲存體帳戶。

> [!WARNING]  
> 不支援在與 HDInsight 叢集不同的位置中使用儲存體帳戶。

## <a name="add-storage-account"></a>新增儲存體帳戶

使用 [[腳本動作](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)] 以下列考慮套用變更：

|屬性 | 值 |
|---|---|
|Bash 指令碼 URI|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|節點類型|Head|
|參數|`ACCOUNTNAME``ACCOUNTKEY` `-p`|

* `ACCOUNTNAME`這是要新增至 HDInsight 叢集的儲存體帳戶名稱。
* `ACCOUNTKEY`是的存取金鑰`ACCOUNTNAME`。
* `-p` 是選擇性的。 如果指定，金鑰不會加密，而且會以純文字的形式儲存在 core-site.xml 中。

## <a name="verification"></a>驗證

在 Azure 入口網站中查看 HDInsight 叢集時，選取 [__屬性__] 底下的 [__儲存體帳戶__] 專案，並不會顯示透過此腳本動作新增的儲存體帳戶。 Azure PowerShell 和 Azure CLI 不會顯示其他儲存體帳戶。 因為此腳本只會修改叢集的`core-site.xml`設定，所以不會顯示儲存資訊。 使用 Azure 管理 Api 來抓取叢集資訊時，不會使用此資訊。

若要確認額外的儲存體，請使用下列其中一種方法：

### <a name="powershell"></a>PowerShell

腳本會傳回與指定叢集相關聯的儲存體帳戶名稱。 將`CLUSTERNAME`取代為實際的叢集名稱，然後執行腳本。

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Apache Ambari

1. 從網頁瀏覽器瀏覽至 `https://CLUSTERNAME.azurehdinsight.net`，其中 `CLUSTERNAME` 是叢集的名稱。

1. 流覽至**HDFS** > **Configs** > [自訂] [**Advanced** > **Custom]-site**。

1. 觀察開頭為`fs.azure.account.key`的索引鍵。 帳戶名稱會是金鑰的一部分，如下列範例影像所示：

   ![透過 Apache Ambari 進行驗證](./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png)

## <a name="remove-storage-account"></a>移除儲存體帳戶

1. 從網頁瀏覽器瀏覽至 `https://CLUSTERNAME.azurehdinsight.net`，其中 `CLUSTERNAME` 是叢集的名稱。

1. 流覽至**HDFS** > **Configs** > [自訂] [**Advanced** > **Custom]-site**。

1. 移除下列機碼：
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

移除這些金鑰並儲存設定之後，您必須逐一重新開機 Oozie、Yarn、MapReduce2、HDFS 和 Hive。

## <a name="known-issues"></a>已知問題

### <a name="storage-firewall"></a>儲存體防火牆

如果您選擇使用**所選網路**上的**防火牆和虛擬網路**限制來保護您的儲存體帳戶，請務必啟用 [**允許信任的 Microsoft 服務**] 例外狀況，讓 HDInsight 可以存取您的儲存體帳戶`.`

### <a name="unable-to-access-storage-after-changing-key"></a>無法在變更金鑰之後存取儲存體

如果您變更儲存體帳戶的金鑰，HDInsight 就無法再存取儲存體帳戶。 HDInsight 在叢集的 core-site.xml 中使用金鑰的快取複本。 此快取副本必須更新以符合新的金鑰。

再次執行腳本動作**並不會**更新金鑰，因為腳本會檢查是否已有儲存體帳戶的專案。 如果專案已存在，則不會進行任何變更。

若要解決此問題：  
1. 移除儲存體帳戶。
1. 新增儲存體帳戶。

> [!IMPORTANT]  
> 不支援輪替附加至叢集之主要儲存體帳戶的儲存體金鑰。

### <a name="poor-performance"></a>效能不佳

如果儲存體帳戶位於與 HDInsight 叢集不同的區域中，您可能會遇到效能不佳。 存取不同區域中的資料會將網路流量傳送到區域 Azure 資料中心以外。 而跨公用網際網路，這可能會導致延遲。

### <a name="additional-charges"></a>額外費用

如果儲存體帳戶位於與 HDInsight 叢集不同的區域中，您可能會發現 Azure 帳單上出現額外輸出費用。 當資料離開區域資料中心時，就會產生輸出費用。 即使流量的目的地是不同區域中的其他 Azure 資料中心，仍會產生此費用。

## <a name="next-steps"></a>後續步驟

您已瞭解如何將其他儲存體帳戶新增至現有的 HDInsight 叢集。 如需指令碼動作的詳細資訊，請參閱[使用指令碼動作自訂以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)
