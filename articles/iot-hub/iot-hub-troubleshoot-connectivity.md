---
title: 使用 Azure IoT 中樞進行中斷連線的監視和疑難排解
description: 瞭解如何針對 Azure IoT 中樞的裝置連線功能進行常見錯誤的監視和疑難排解
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: 82139eef9708ff8d76e1087c71aa5445ba898385
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759601"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>使用 Azure IoT 中樞來監視、診斷和疑難排解中斷連線

IoT 裝置的連線問題可能因為有許多可能的失敗點而難以排解。 應用程式邏輯、實體網路、通訊協定、硬體、IoT 中樞和其他雲端服務都可能造成問題。 偵測並找出問題來源的能力很重要。 不過，大規模的 IoT 解決方案可能會有數千部裝置，因此不可行地手動檢查個別裝置。 為了協助您大規模偵測、診斷及疑難排解這些問題，請使用 IoT 中樞透過 Azure 監視器提供的監視功能。 這些功能僅限於 IoT 中樞可以觀察的內容，因此我們也建議您遵循監視裝置和其他 Azure 服務的最佳作法。

## <a name="get-alerts-and-error-logs"></a>取得警示和錯誤記錄

使用 Azure 監視器來取得警示，並在裝置中斷連線時寫入記錄。

### <a name="turn-on-diagnostic-logs"></a>開啟診斷記錄

若要記錄裝置連線事件和錯誤，請開啟 IoT 中樞的診斷功能。 建議您儘早開啟這些記錄檔，因為如果未啟用診斷記錄，當裝置中斷連線時，您將不會有任何資訊可以疑難排解的問題。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 瀏覽至您的 IoT 中樞。

3. 選取 [診斷設定]****。

4. 選取 [開啟診斷]****。

5. 讓 [連線]**** 記錄可供收集。

6. 若要讓分析變得更容易，您應該開啟 [傳送至 Log Analytics]**** ([請參閱定價](https://azure.microsoft.com/pricing/details/log-analytics/))。 請參閱[解決連線錯誤](#resolve-connectivity-errors)下方的範例。

   ![建議的設定](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

若要深入了解，請參閱[監視 Azure IoT 中樞的健康情況並快速診斷問題](iot-hub-monitor-resource-health.md)。

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>設定大規模裝置中斷連線的警示

若要在裝置中斷連線時取得警示，請在 [**連接的裝置（預覽）** ] 計量上設定警示。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 瀏覽至您的 IoT 中樞。

3. 選取 [警示] ****。

4. 選取 [新增警示規則]****。

5. 選取 [**新增條件**]，然後選取 [已連線的裝置（預覽）]。

6. 依照下列提示設定閾值和警示。

若要深入瞭解，請參閱[什麼是 Microsoft Azure 中的警示？](../azure-monitor/platform/alerts-overview.md)。

#### <a name="detecting-individual-device-disconnects"></a>偵測個別裝置中斷連線

若要偵測*每一裝置的*中斷連線，例如當您需要知道 factory 剛離線時，請[使用事件方格設定裝置中斷連接事件](iot-hub-event-grid.md)。

## <a name="resolve-connectivity-errors"></a>解決連線錯誤

開啟已連線裝置的診斷記錄和警示後，您會在發生錯誤時收到警示。 本節說明當您收到警示時，如何尋找常見的問題。 下列步驟假設您已設定診斷記錄的 Azure 監視器記錄。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 瀏覽至您的 IoT 中樞。

1. 選取 [**記錄**]。

1. 若要隔離 IoT 中樞的連線錯誤記錄，請輸入下列查詢，然後選取 [執行]****：

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. 如果有結果，請尋找 `OperationName`、`ResultType` (錯誤碼) 和 `ResultDescription` (錯誤訊息)，以取得關於錯誤的詳細資訊。

   ![錯誤記錄的範例](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

1. 針對最常見的錯誤，請遵循問題解析指南：

    - **[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)**
    - **[401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)**
    - **[409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)**
    - **[500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**
    - **[500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**

## <a name="i-tried-the-steps-but-they-didnt-work"></a>我嘗試了這些步驟，但它們沒有作用

如果先前的步驟沒有説明，請嘗試：

* 如果您可以存取有問題的裝置 (實體或遠端 (如 SSH))，請遵循[裝置端疑難排解指南](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices)，繼續進行疑難排解。

* 在 Azure 入口網站 > 您的 IoT 中樞 > IoT 裝置，確認您的裝置 [已啟用]****。

* 如果您的裝置使用 MQTT 通訊協定，請確認埠8883已開啟。 如需詳細資訊，請參閱[連接到 IoT 中樞（MQTT）](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

* 向 [Azure IoT 中樞論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub)、[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub) 或[Azure 支援](https://azure.microsoft.com/support/options/)取得協助。

為了協助每個人改善文件，如果此指南對您沒有幫助，請在下方的意見反應區段中留下意見。

## <a name="next-steps"></a>後續步驟

* 若要深入了解如何解決暫時性問題，請參閱[暫時性錯誤處理](/azure/architecture/best-practices/transient-faults)。

* 若要深入了解 Azure IoT SDK 以及如何管理重試，請參閱[如何使用 Azure IoT 中樞裝置 SDK 來管理連線能力和可靠傳訊](iot-hub-reliability-features-in-sdks.md#connection-and-retry)。
