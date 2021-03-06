---
title: 最佳作法
description: 瞭解開發 Azure Batch 解決方案的最佳作法和實用秘訣。
ms.date: 04/03/2020
ms.topic: article
ms.openlocfilehash: 43a0020953ea44593cf38298a78547194751fc72
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82117500"
---
# <a name="azure-batch-best-practices"></a>Azure Batch 最佳做法

本文討論如何有效且有效率地使用 Azure Batch 服務的最佳作法集合。 這些最佳作法衍生自我們的 Batch 體驗和 Batch 客戶的體驗。 請務必瞭解這篇文章，以避免開發和使用 Batch 時的設計錯誤、潛在的效能問題和反模式。

在本文中，您將了解：

> [!div class="checklist"]
> - 最佳做法
> - 為何應該使用最佳作法
> - 如果您無法遵循最佳做法，可能會發生什麼事
> - 如何遵循最佳做法

## <a name="pools"></a>集區

Batch 集區是在 Batch 服務上執行作業的計算資源。 下列各節提供使用 Batch 集區時所要遵循之最佳作法的指引。

### <a name="pool-configuration-and-naming"></a>集區設定和命名

- **集區配置模式**建立 Batch 帳戶時，您可以選擇兩個集區配置模式： **Batch 服務**或**使用者訂**用帳戶。 在大部分情況下，您應該使用預設的 Batch 服務模式，其中的集區會在批次管理的訂用帳戶中配置於幕後。 在其他使用者訂用帳戶模式中，在建立集區時，會直接在您的訂用帳戶中建立 Batch VM 和其他資源。 使用者訂用帳戶主要是用來啟用重要但較小的案例子集。 您可以在[使用者訂用帳戶模式的其他](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode)設定中，閱讀更多使用者訂用帳戶模式的相關資訊。

- **判斷作業與集區的對應時，請考慮作業和工作執行時間。**
    如果您的作業主要是由短期執行的工作所組成，而且預期的總工作計數很小，所以作業的整體預期執行時間不會太長，請不要為每個工作配置新的集區。 節點的配置時間將會降低作業的執行時間。

- **集區應具有一個以上的計算節點。**
    個別節點不保證一律可供使用。 雖然不常見，硬體故障、作業系統更新和其他問題的主機可能會導致個別節點離線。 如果您的 Batch 工作負載需要具決定性且保證的進度，您應該配置具有多個節點的集區。

- **請勿重複使用資源名稱。**
    Batch 資源（作業、集區等）通常會隨時間而不同。 例如，您可以在星期一建立集區、在星期二將其刪除，然後在星期四建立另一個集區。 您所建立的每個新資源，都應該指定您之前未曾用過的唯一名稱。 這可以藉由使用 GUID （做為整個資源名稱或其一部分）來完成，或在資源名稱中內嵌資源的建立時間。 Batch 支援[DisplayName](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.jobspecification.displayname?view=azure-dotnet)，可以用來為資源提供人類看得懂的名稱，即使實際的資源識別碼不是人易記的東西。 使用唯一的名稱，可讓您更輕鬆地區分哪一項特定資源在記錄檔和計量中做了什麼。 如果您必須提出資源的支援案例，也會移除不明確的情況。

- **集區維護和失敗期間的持續性。**
    您的作業最好是以動態方式使用集區。 如果您的作業針對所有專案使用相同的集區，則當集區發生問題時，您的作業可能不會執行。 對於時間緊迫的工作負載而言，這一點特別重要。 若要修正此問題，請在排程每個工作時動態選取或建立集區，或有方法可以覆寫集區名稱，以便略過狀況不良的集區。

- **集區維護和失敗期間的商務持續性**有許多可能的原因會使集區無法成長到您想要的所需大小，例如內部錯誤、容量限制等。基於這個理由，您應該準備好將工作的目標重定至不同集區（可能有不同的 VM 大小）（如有需要，Batch 會透過[UpdateJob](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update?view=azure-dotnet)支援此功能）。 請避免在預期永遠不會刪除且永遠不會變更的情況下使用靜態集區識別碼。

### <a name="pool-lifetime-and-billing"></a>集區存留期和計費

集區存留期可能會根據配置的方法和套用至集區設定的選項而有所不同。 集區在任何時間點都可以有任意存留期和不同數目的計算節點。 您必須負責明確地管理集區中的計算節點，或透過服務所提供的功能（自動調整或 autopool）。

- **將集區保持在最新。**
    您應該每隔幾個月將集區大小調整為零，以確保您取得最新的節點代理程式更新和 bug 修正。 您的集區不會收到節點代理程式更新，除非重新建立，或調整大小為0個計算節點。 在您重新建立集區或調整其大小之前，建議您下載任何節點代理程式記錄以進行調試，如[節點](#nodes)一節中所述。

- **集區重新建立**在類似的注意事項中，不建議您每天刪除並重新建立集區。 相反地，請建立新的集區，並更新現有的作業以指向新的集區。 當所有工作都已移至新集區之後，請刪除舊的集區。

- **集區效率和計費**Batch 本身不會產生額外的費用，但所使用的計算資源會產生費用。 系統會針對集區中的每個計算節點計費，而不論其所在的狀態為何。 這包括執行節點所需的任何費用，例如儲存體和網路成本。 若要深入瞭解最佳做法，請參閱[Azure Batch 的成本分析和預算](budget.md)。

### <a name="pool-allocation-failures"></a>集區配置失敗

集區配置失敗可能會在第一次配置或後續調整大小的任何時間點發生。 這可能是因為在某個區域發生暫時容量耗盡，或批次所依賴的其他 Azure 服務失敗。 您的核心配額不是保證，而是限制。

### <a name="unplanned-downtime"></a>非計劃性停機

Batch 集區可以在 Azure 中遇到停機事件。 在規劃和開發 Batch 的案例或工作流程時，請務必記住這一點。

如果節點失敗，Batch 會自動嘗試代表您復原這些計算節點。 這可能會在復原的節點上觸發重新排程任何正在執行的工作。 請參閱[重試設計](#designing-for-retries-and-re-execution)以深入瞭解中斷的工作。

- **Azure 區域**相依性如果您有時間緊迫或生產工作負載，建議您不要依賴單一 Azure 區域。 雖然很罕見，但還是有可能會影響整個區域的問題。 例如，如果您的處理需要在特定時間啟動，請考慮在*開始時間之前*，在主要區域中相應增加集區。 如果該集區調整失敗，您可以切換回相應增加備份區域（或區域）中的集區。 在不同區域中跨多個帳戶的集區會在另一個集區發生問題時，提供準備好且容易存取的備份。 如需詳細資訊，請參閱[設計應用程式的高可用性](high-availability-disaster-recovery.md)。

## <a name="jobs"></a>工作

「作業」是一種容器，其設計目的是要包含數百個、數千個或甚至數百萬個工作。

- **在作業中放入許多工**使用作業執行單一工作沒有效率。 例如，使用包含1000工作的單一作業會更有效率，而不是建立每個包含10個工作的100項作業。 執行1000作業時，每個工作都有單一工作，這會是最不有效率、最慢且昂貴的方法。

    請勿設計同時需要數千個使用中作業的 Batch 解決方案。 工作不會有任何配額，因此，盡可能只在較少的作業下執行最多工作，可有效率地使用您的[作業和作業排程配額](batch-quota-limit.md#resource-quotas)。

- **作業存留期**批次作業在從系統中刪除之前，會有無限的存留期。 作業的狀態會指定它是否可以接受更多工具來進行排程。 除非明確終止，否則作業不會自動移至已完成狀態。 這可以透過[onAllTasksComplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete?view=azure-dotnet)屬性或[maxWallClockTime](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)自動觸發。

預設的作用中[作業和作業排程配額](batch-quota-limit.md#resource-quotas)。 處於 [已完成] 狀態的作業和作業排程不會計入此配額。

## <a name="tasks"></a>工作

工作是組成作業的個別工作單位。 工作是由使用者提交，並由批次排程到計算節點。 建立和執行工作時，有幾個設計考慮。 下列各節說明常見的案例，以及如何設計您的工作來處理問題並有效率地執行。

- **將工作資料儲存為工作的一部分。**
    計算節點的本質是暫時的。 Batch 中有許多功能，例如 autopool 和自動調整，可讓節點輕鬆地消失。 當節點離開集區時（由於調整大小或集區刪除），也會一併刪除這些節點上的所有檔案。 因此，建議您在工作完成之前，將其輸出從其執行所在的節點移出至長期存放區，同樣地，如果工作失敗，則會將所需的記錄檔移到將失敗診斷到永久性存放區。 Batch 具有整合的支援 Azure 儲存體透過[OutputFiles](batch-task-output-files.md)上傳資料，以及各種共用檔案系統，您也可以在工作中自行執行上傳作業。

### <a name="task-lifetime"></a>工作存留期

- **當工作完成時將其刪除。**
    當不再需要工作時刪除工作，或設定[retentionTime](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime?view=azure-dotnet)工作條件約束。 `retentionTime`如果設定了，Batch 會在`retentionTime`過期時，自動清除工作所使用的磁碟空間。

    刪除工作完成兩項作業。 它可確保您在作業中沒有工作的組建，讓查詢/尋找您感興趣的工作（因為您必須篩選完成的工作）。 它也會清除節點上對應的工作資料（已提供`retentionTime`的尚未叫用）。 這可確保您的節點不會填滿工作資料並耗盡磁碟空間。

### <a name="task-submission"></a>工作提交

- **在集合中提交大量的工作。**
    可以個別或在集合中提交工作。 在大量提交工作時，一次提交最多100個[集合](https://docs.microsoft.com/rest/api/batchservice/task/addcollection)中的工作，以減少額外負荷和提交時間。

### <a name="task-execution"></a>工作執行

- **選擇每個節點的最大工作**Batch 支援節點上的傳承工作（執行的工作數目比節點具有核心的還多）。 您必須確保您的工作「符合」集區中的節點。 例如，如果您嘗試排程八個工作，每個都耗用25% 的 CPU 使用量到一個節點（在集區中`maxTasksPerNode = 8`），您可能會有降級的體驗。

### <a name="designing-for-retries-and-re-execution"></a>重試和重新執行的設計

Batch 可以自動重試工作。 重試的類型有兩種：使用者控制和內部。 使用者控制的重試是由工作的[maxTaskRetryCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount?view=azure-dotnet)所指定。 當工作中指定的程式以非零的結束代碼結束時，會將工作重試到的值`maxTaskRetryCount`。

雖然很罕見，但由於計算節點發生失敗，因此工作可能會在內部重試，例如無法在工作執行時更新內部狀態或節點上的失敗。 此工作將會在相同的計算節點上重試（如果可能的話），在放棄工作之前，最多可達內部限制，並延遲工作以 Batch 重新排程，可能是在不同的計算節點上。

- **建立**長期工作工作應設計為可承受失敗並配合重試。 這對於長時間執行的工作而言特別重要。 若要這樣做，請確定工作會產生相同的單一結果，即使它們執行一次以上也一樣。 達成此目標的其中一種方式，就是讓您的工作「進行搜尋」。 另一種方式是確定您的工作具有等冪性（不論執行多少次，工作都會有相同的結果）。

    常見的範例是將檔案複製到計算節點的工作。 簡單的方法是在每次執行時複製所有指定的檔案，這樣會沒有效率，而且不會因為無法承受的失敗而建立。 請改為建立工作，以確保檔案位於計算節點上;不會重新複製已存在之檔案的工作。 如此一來，工作就會從中斷的地方繼續進行。

- **低優先順序節點**在專用或低優先順序的節點上執行您的工作時，沒有任何設計上的差異。 工作在低優先順序節點上執行時是否已被佔用，或因為專用節點失敗而中斷，這兩種情況都是藉由設計工作來承受故障而降低。

- 工作**執行時間**避免執行短時間的工作。 只執行一到兩秒的工作並不理想。 您應該嘗試在個別工作中執行大量的工作（最多10秒，直到數小時或數天）。 如果每個工作執行一分鐘（或更多），則整體計算時間的一小部分的排程負荷會很小。

## <a name="nodes"></a>節點

- **啟動工作應為等冪**類似于其他工作，節點啟動工作應該具有等冪性，因為它會在每次節點啟動時重新執行。 等冪工作只是在執行多次時，會產生一致結果的一個。

- **透過作業系統服務介面管理長時間執行的服務。**
    有時候，需要在節點中與 Batch 代理程式一起執行另一個代理程式，例如，從節點收集資料並加以報告。 建議您將這些代理程式部署為作業系統服務，例如 Windows 服務或 Linux `systemd`服務。

    執行這些服務時，它們不得對節點上批次管理目錄中的任何檔案採取檔案鎖定，因為否則 Batch 將無法刪除這些目錄，因為檔案鎖定。 例如，如果在啟動工作中安裝 Windows 服務，而不是直接從 [啟動工作] 工作目錄啟動服務，請將檔案複製到其他位置（如果檔案存在，只略過該複本）。 從該位置安裝服務。 當 Batch 重新執行啟動工作時，它會刪除 [啟動工作] 工作目錄，並再次建立。 這是因為服務在另一個目錄上有檔案鎖定，而不是啟動工作作業目錄。

- **避免在 Windows 中建立目錄接合**目錄聯接（有時稱為目錄永久連結）在工作和作業清除期間很難處理。 使用符號連結（軟連結），而不是永久連結。

- **如果發生問題，請收集 Batch 代理程式記錄**檔如果您注意到某個節點的行為，或節點上執行的工作發生問題，建議您在解除配置有問題的節點之前，先收集 Batch 代理程式記錄。 您可以使用上傳 Batch 服務記錄 API 來收集 Batch 代理程式記錄。 這些記錄可以做為 Microsoft 支援票證的一部分提供，並可協助解決問題的疑難排解和解決方法。

## <a name="security"></a>安全性

### <a name="security-isolation"></a>安全性隔離：

基於隔離的目的，如果您的案例需要隔離作業，則您應該將這些作業放在不同的集區中來隔離。 集區是 Batch 中的安全性隔離界限，而且根據預設，不會顯示或無法彼此通訊的兩個集區。 避免使用個別的 Batch 帳戶做為隔離的方法。

## <a name="moving"></a>變化

### <a name="move-batch-account-across-regions"></a>跨區域移動 Batch 帳戶

在許多情況下，您會想要將現有的 Batch 帳戶從一個區域移至另一個區域。 例如，您可能會想要移到另一個區域，做為嚴重損壞修復計畫的一部分。

Azure Batch 帳戶無法從一個區域移至另一個區域。 不過，您可以使用 Azure Resource Manager 範本來匯出 Batch 帳戶的現有設定。  接著，您可以將 Batch 帳戶匯出至範本、修改參數以符合目的地區域，然後將範本部署到新的區域，藉此將資源放在另一個區域中。 將範本上傳至新的區域之後，您必須重新建立憑證、作業排程和應用程式套件。 若要認可變更並完成 Batch 帳戶的移動，請記得刪除原始的 Batch 帳戶或資源群組。

如需 Resource Manager 和範本的詳細資訊，請參閱[快速入門：使用 Azure 入口網站來建立和部署 Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。

## <a name="connectivity-to-the-batch-service"></a>Batch 服務的連線能力

### <a name="network-security-groups-nsgs-and-user-defined-routes-udrs"></a>網路安全性群組（Nsg）和使用者定義的路由（Udr）

[在虛擬網路中](batch-virtual-network.md)布建 Batch 集區時，請確定您已仔細遵循有關使用`BatchNodeManagement`服務標籤、埠、通訊協定和規則方向的指導方針。
強烈建議使用服務標籤，而不是基礎 Batch 服務 IP 位址，因為它們會隨著時間而改變。 當 Batch 服務更新經過一段時間所使用的 IP 位址時，直接使用 Batch 服務 IP 位址會向您的 Batch 集區提供不穩定、中斷或中斷的資訊清單。 如果您目前在 NSG 規則中使用 Batch 服務 IP 位址，建議您切換為使用服務標籤。

針對使用者定義的路由，請確定您已準備好在路由表中定期更新批次服務 IP 位址的程式，因為這些變更一段時間。 若要瞭解如何取得 Batch 服務 IP 位址的清單，請參閱[內部部署服務標記](../virtual-network/service-tags-overview.md)。 Batch 服務 IP 位址會與`BatchNodeManagement`服務標籤（或符合您 Batch 帳戶區域的地區變數）相關聯。

### <a name="honoring-dns"></a>遵守 DNS

請確定您的系統會針對 Batch 帳戶服務 URL，接受 DNS 存留時間（TTL）。 此外，請確定 batch 服務用戶端和其他連接機制不會依賴 IP 位址。

如果您的要求會收到5xx 層級的 HTTP 回應，而且回應中有「連線：關閉」標頭，則 Batch 服務用戶端應該藉由關閉現有的連線、重新解析 Batch 帳戶服務 URL 的 DNS，然後在新的連線上嘗試下列要求，來觀察建議。

### <a name="retrying-requests-automatically"></a>自動重試要求

請確定您的 Batch 服務用戶端已有適當的重試原則，可以自動重試您的要求，即使在正常作業期間，也不會在任何服務維護時間週期內獨佔。 這些重試原則應跨越至少5分鐘的間隔。 自動重試功能是以各種 Batch Sdk 提供，例如[.Net RetryPolicyProvider 類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet)。

