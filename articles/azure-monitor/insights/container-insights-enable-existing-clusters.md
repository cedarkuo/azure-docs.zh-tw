---
title: 監視已部署的 Azure Kubernetes Service （AKS）叢集 |Microsoft Docs
description: 瞭解如何針對已部署在訂用帳戶中的容器，使用 Azure 監視器來啟用 Azure Kubernetes Service （AKS）叢集的監視。
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 8589ea71b5c7affadc61d5e4543f734a660ab543
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79275447"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>啟用已部署 Azure Kubernetes Service （AKS）叢集的監視

本文說明如何設定容器的 Azure 監視器，以監視已部署在您訂用帳戶中[Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/)上的受管理 Kubernetes 叢集。

您可以使用其中一種支援的方法，啟用已部署之 AKS 叢集的監視：

* Azure CLI
* Terraform
* [從 Azure 監視器](#enable-from-azure-monitor-in-the-portal)或[直接從 AZURE 入口網站中的 AKS](#enable-directly-from-aks-cluster-in-the-portal)叢集
* 使用 Azure PowerShell Cmdlet `New-AzResourceGroupDeployment`或 Azure CLI[提供的 Azure Resource Manager 範本](#enable-using-an-azure-resource-manager-template)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="enable-using-azure-cli"></a>啟用使用 Azure CLI

下列步驟會使用 Azure CLI 來啟用對 AKS 叢集的監視。 在此範例中，您不需要預先建立或指定現有的工作區。 此命令透過在 AKS 叢集預設資源群組中建立預設工作區 (若該區域中沒有預設工作區) 來為您簡化程序。  所建立的預設工作區格式類似 *DefaultWorkspace-\<GUID>-\<Region>*。  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

輸出看起來會像下面這樣：

```output
provisioningState       : Succeeded
```

### <a name="integrate-with-an-existing-workspace"></a>與現有的工作區整合

如果您想要與現有的工作區整合，請執行下列步驟，先找出`--workspace-resource-id`參數所需之 Log Analytics 工作區的完整資源識別碼，然後執行命令，針對指定的工作區啟用監視附加元件。  

1. 使用下列命令，列出您有權存取的所有訂用帳戶：

    ```azurecli
    az account list --all -o table
    ```

    輸出看起來會像下面這樣：

    ```output
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    複製**SubscriptionId**的值。

2. 使用下列命令，切換至裝載 Log Analytics 工作區的訂用帳戶：

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. 下列範例會以預設的 JSON 格式顯示訂用帳戶中的工作區清單。

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    在輸出中，尋找工作區名稱，然後將該 Log Analytics 工作區的完整資源識別碼複製到欄位**識別碼**之下。

4. 執行下列命令來啟用監視附加元件，並取代`--workspace-resource-id`參數的值。 字串值必須在雙引號內：

    ```azurecli
    az aks enable-addons -a monitoring -n ExistingManagedCluster -g ExistingManagedClusterRG --workspace-resource-id "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>"
    ```

    輸出看起來會像下面這樣：

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-using-terraform"></a>啟用使用 Terraform

1. 將 **oms_agent** 附加元件設定檔新增至現有的 [azurerm_kubernetes_cluster 資源](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile)

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. 按照 Terraform 文件中的步驟新增 [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html)。

## <a name="enable-from-azure-monitor-in-the-portal"></a>從入口網站中的 Azure 監視器啟用

若要從 Azure 監視器在 Azure 入口網站中啟用 AKS 叢集的監視，請執行下列步驟：

1. 在 [Azure 入口網站中，選取 [**監視**]。

2. 從清單中選取 [容器]****。

3. 在 [監視器 - 容器]**** 頁面上，選取 [不受監視的叢集]****。

4. 從不受監視的叢集清單，在清單中尋找容器，然後按一下 [啟用]****。   

5. 在 [適用於容器的 Azure 監視器上線]**** 頁面上，如果相同訂用帳戶中有現有 Log Analytics 工作區可作為叢集，請從下拉式清單中加以選取。  
    清單會預先選取訂用帳戶中已部署 AKS 容器的預設工作區和位置。

    ![啟用 AKS 容器深入解析監視](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >如果您想要建立新的 Log Analytics 工作區以儲存來自叢集的監視資料，請遵循[建立 Log Analytics 工作區](../../azure-monitor/learn/quick-create-workspace.md)中的指示。 請務必在和部署 AKS 容器相同的訂用帳戶中建立工作區。

啟用監視之後，可能需要約 15 分鐘的時間才能檢視叢集的健康情況計量。

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>在入口網站中直接從 AKS 叢集啟用

若要直接從 Azure 入口網站中的一個 AKS 叢集啟用監視，請執行下列動作：

1. 在 [Azure 入口網站中，選取 [**所有服務**]。

2. 在資源清單中，開始輸入**容器**。  清單會根據您輸入的文字進行篩選。

3. 選取 [Kubernetes 服務]****。  

    ![[Kubernetes 服務] 連結](./media/container-insights-onboard/portal-search-containers-01.png)

4. 在容器清單中，選取容器。

5. 在容器概觀頁面上，選取 [監視容器]****。  

6. 在 [適用於容器的 Azure 監視器上線]**** 頁面上，如果相同訂用帳戶中有現有 Log Analytics 工作區可作為叢集，請在下拉式清單中選取。  
    清單會預先選取訂用帳戶中已部署 AKS 容器的預設工作區和位置。

    ![啟用 AKS 容器健康情況監視](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >如果您想要建立新的 Log Analytics 工作區以儲存來自叢集的監視資料，請遵循[建立 Log Analytics 工作區](../../azure-monitor/learn/quick-create-workspace.md)中的指示。 請務必在和部署 AKS 容器相同的訂用帳戶中建立工作區。

在啟用監視之後，可能需要約 15 分鐘的時間才能檢視叢集的作業資料。

## <a name="enable-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本啟用

此方法包含兩個 JSON 範本。 一個範本會指定啟用監視的設定，另一個範本則包含可設定以指定下列各項的參數值：

* AKS 容器資源識別碼。
* 叢集部署所在的資源群組。

>[!NOTE]
>範本必須部署在叢集所在的資源群組。
>

您必須先建立 Log Analytics 工作區，才能使用 Azure PowerShell 或 CLI 啟用監視。 若要建立工作區，您可以透過 [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)、透過 [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)，或是在 [Azure 入口網站](../../azure-monitor/learn/quick-create-workspace.md)中設定它。

若您不熟悉使用範本來部署資源的概念，請參閱：

* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../../azure-resource-manager/templates/deploy-powershell.md)

* [使用 Resource Manager 範本與 Azure CLI 部署資源](../../azure-resource-manager/templates/deploy-cli.md)

如果您選擇使用 Azure CLI，必須先在本機安裝並使用 CLI。 您必須執行 Azure CLI 版2.0.59 或更新版本。 若要知道您使用的版本，請執行 `az --version`。 如果您需要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="create-and-execute-a-template"></a>建立並執行範本

1. 複製以下 JSON 語法並貼到您的檔案中：

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "type": "string",
          "metadata": {
            "description": "AKS Cluster Resource ID"
          }
        },
        "aksResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the AKS resource e.g. \"East US\""
          }
        },
        "aksResourceTagValues": {
          "type": "object",
          "metadata": {
            "description": "Existing all tags on AKS Cluster Resource"
          }
        },
        "workspaceResourceId": {
          "type": "string",
          "metadata": {
            "description": "Azure Monitor Log Analytics Resource ID"
          }
        }
      },
      "resources": [
        {
          "name": "[split(parameters('aksResourceId'),'/')[8]]",
          "type": "Microsoft.ContainerService/managedClusters",
          "location": "[parameters('aksResourceLocation')]",
          "tags": "[parameters('aksResourceTagValues')]",
          "apiVersion": "2018-03-31",
          "properties": {
            "mode": "Incremental",
            "id": "[parameters('aksResourceId')]",
            "addonProfiles": {
              "omsagent": {
                "enabled": true,
                "config": {
                  "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
                }
              }
            }
          }
        }
      ]
    }
    ```

2. 將此檔案儲存為本機資料夾的 **existingClusterOnboarding.json**。

3. 將下列 JSON 語法貼到您的檔案中：

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterLocation>"
        },
        "workspaceResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. 使用 AKS 叢集的**AKS [總覽**] 頁面上的值，編輯**編輯 aksresourceid**和**aksResourceLocation**的值。 **workspaceResourceId** 值是您 Log Analytics 工作區的完整資源識別碼，其中包含工作區名稱。

    編輯**aksResourceTagValues**的值，以符合針對 AKS 叢集指定的現有標記值。

5. 將此檔案儲存為本機資料夾的 **existingClusterParam.json**。

6. 您已準備好部署此範本。

   * 若要使用 Azure PowerShell 進行部署，請在包含範本的資料夾中使用下列命令：

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```

       可能需要幾分鐘的時間才能完成設定變更。 完成之後，將會顯示如下訊息並包含結果：

       ```output
       provisioningState       : Succeeded
       ```

   * 若要使用 Azure CLI 進行部署，請執行下列命令：

       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       可能需要幾分鐘的時間才能完成設定變更。 完成之後，將會顯示如下訊息並包含結果：

       ```output
       provisioningState       : Succeeded
       ```

       啟用監視之後，可能需要約 15 分鐘的時間才能檢視叢集的健康情況計量。

## <a name="verify-agent-and-solution-deployment"></a>驗證代理程式和解決方案部署

使用代理程式 *06072018* 版或更新版本時，您可以確認代理程式和方案皆已成功部署。 使用舊版的代理程式時，您只能確認代理程式部署。

### <a name="agent-version-06072018-or-later"></a>代理程式 06072018 版或更新版本

執行下列命令以確認代理程式已成功部署。

```
kubectl get ds omsagent --namespace=kube-system
```

輸出應該像下面這樣，這表示它已正確部署：

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

若要確認解決方案的部署，請執行下列命令：

```
kubectl get deployment omsagent-rs -n=kube-system
```

輸出應該像下面這樣，這表示它已正確部署：

```output
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>早於 06072018 的代理程式版本

若要確認已正確部署 06072018** 版以前發行的 Log Analytics 代理程式，請執行下列命令：  

```
kubectl get ds omsagent --namespace=kube-system
```

輸出應該像下面這樣，這表示它已正確部署：  

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>使用 CLI 來檢視設定

使用 `aks show` 命令來取得詳細資料，例如解決方案是否已啟用、Log Analytics 工作區 resourceID 為何，以及有關叢集的詳細資料。  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

在幾分鐘之後，此命令就會完成，並以 JSON 格式傳回叢集的相關資訊。  此命令的結果應該會顯示監視附加元件設定檔，而且應該會類似下列命令輸出：

```output
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>後續步驟

* 如果您在試著將解決方案上線時遇到問題，請檢閱[疑難排解指南](container-insights-troubleshoot.md)

* 啟用監視以收集 AKS 叢集的健康情況和資源使用率，以及在其上執行的工作負載，瞭解[如何使用](container-insights-analyze.md)容器的 Azure 監視器。
