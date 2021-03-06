---
title: 教學課程 - 建立 Azure Red Hat OpenShift 4 叢集
description: 了解如何使用 Azure CLI 建立 Microsoft Azure Red Hat OpenShift 叢集
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: d9b02c11c055b4b072c5f8a1ff47e44001ec4580
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509715"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>教學課程：建立 Azure Red Hat OpenShift 4 叢集

在本教學課程 (三部分中的第一部分) 中，您將準備環境來建立執行 OpenShift 4 的 Azure Red Hat OpenShift 叢集，並建立叢集。 您將了解如何：
> [!div class="checklist"]
> * 設定必要條件，並建立必要的虛擬網路和子網路
> * 部署叢集

## <a name="before-you-begin"></a>開始之前

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 2.0.75 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

### <a name="install-the-az-aro-extension"></a>安裝 `az aro` 擴充功能
`az aro` 擴充功能可讓您使用 Azure CLI，直接從命令列建立、存取和刪除 Azure Red Hat OpenShift 叢集。

請執行下列命令來安裝 `az aro` 擴充功能。

```azurecli-interactive
az extension add -n aro --index https://az.aroapp.io/stable
```

如果您已安裝此擴充功能，您可以執行下列命令來進行更新。

```azurecli-interactive
az extension update -n aro --index https://az.aroapp.io/stable
```

### <a name="register-the-resource-provider"></a>註冊資源提供者

接下來，您必須在您的訂用帳戶中註冊 `Microsoft.RedHatOpenShift` 資源提供者。

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

確認已註冊此擴充功能。

```azurecli-interactive
az -v
```

  您應該會看到如下的輸出。

```output
...
Extensions:
aro                                1.0.0
...
```

### <a name="get-a-red-hat-pull-secret-optional"></a>取得 Red Hat 提取祕密 (選擇性)

Red Hat 提取祕密可讓您的叢集存取 Red Hat 容器登錄及其他內容。 此步驟為選用步驟，但建議執行。

瀏覽至 https://cloud.redhat.com/openshift/install/azure/aro-provisioned ，然後按一下 [下載提取祕密]  ，即可取得您的提取祕密。

您必須登入 Red Hat 帳戶，或使用您的公司電子郵件建立新的 Red Hat 帳戶，並接受條款及條件。

將儲存的 `pull-secret.txt` 檔案保存在安全的地方 (建立每個叢集時都會用到此檔案)。

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>建立包含兩個空白子網路的虛擬網路

接下來，您將建立包含兩個空白子網路的虛擬網路。

1. **設定下列變數。**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

1. **建立資源群組**

    Azure 資源群組是部署及管理 Azure 資源所在的邏輯群組。 建立資源群組時，系統會要求您指定位置。 此位置是儲存資源群組中繼資料的位置，如果您未在資源建立期間指定另一個區域，此位置也會是您在 Azure 中執行資源的位置。 使用 [az group create][az-group-create] 命令來建立資源群組。

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
    ```

    下列範例輸出顯示已成功建立的資源群組：

    ```json
    {
    "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
    "location": "eastus",
    "managedBy": null,
    "name": "aro-rg",
    "properties": {
        "provisioningState": "Succeeded"
    },
    "tags": null
    }
    ```

2. **建立虛擬網路。**

    執行 OpenShift 4 的 Azure Red Hat OpenShift 叢集需要具有兩個空白子網路的虛擬網路，分別用於主要節點和背景工作角色節點。

    在您稍早建立的相同資源群組中建立新的虛擬網路。

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    下列範例輸出顯示已成功建立的虛擬網路：

    ```json
    {
    "newVNet": {
        "addressSpace": {
        "addressPrefixes": [
            "10.0.0.0/22"
        ]
        },
        "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
        "location": "eastus",
        "name": "aro-vnet",
        "provisioningState": "Succeeded",
        "resourceGroup": "aro-rg",
        "type": "Microsoft.Network/virtualNetworks"
    }
    }
    ```

3. **為主要節點新增空白子網路。**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

4. **為背景工作節點新增空白子網路。**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **[停用主要子網上的子網路私人端點原則](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy)。** 這是能夠連線和管理叢集的必要條件。

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>建立叢集

執行下列命令來建立叢集： (選擇性) 您可以傳遞提取祕密，讓您的叢集能夠存取 Red Hat 容器登錄及其他內容。 瀏覽至 [Red Hat OpenShift 叢集管理員](https://cloud.redhat.com/openshift/install/azure/installer-provisioned)，然後按一下 [複製提取]  ，即可存取您的提取祕密。

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret '$(< pull-secret.txt)' # [OPTIONAL]
```
>[!NOTE]
> 一般大約需要 35 分鐘的時間來建立叢集。

>[!IMPORTANT]
> 如果您選擇指定自訂網域 (例如 **foo.example.com**)，OpenShift 主控台將會以類似 `https://console-openshift-console.apps.foo.example.com` 的 URL 來提供，而不是內建的網域：`https://console-openshift-console.apps.<random>.<location>.aroapp.io`。
>
> 根據預設，OpenShift 會針對 `*.apps.<random>.<location>.aroapp.io` 上建立的所有路由使用自我簽署憑證。  如果您在連線至叢集之後選擇使用自訂 DNS，則必須遵循 OpenShift 文件來[為您的輸入控制器設定自訂 CA](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html)，以及[為您的 API 伺服器自訂 CA](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html)。
>

## <a name="next-steps"></a>後續步驟

在教學課程的這個部分中，您已了解如何：
> [!div class="checklist"]
> * 設定必要條件，並建立必要的虛擬網路和子網路
> * 部署叢集

前進到下一個教學課程：
> [!div class="nextstepaction"]
> [連線至 Azure Red Hat OpenShift 叢集](tutorial-connect-cluster.md)
