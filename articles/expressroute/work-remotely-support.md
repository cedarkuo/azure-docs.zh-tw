---
title: 使用 Azure ExpressRoute 來支援遠端使用者
description: 此頁面說明如何使用 Azure ExpressRoute，讓您能夠在遠端執行，因為 COVID-19 流感。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/22/2020
ms.author: ajitbhu
ms.openlocfilehash: 2fe990fd9c5922dd2e059dbb212cd8bb1cd3726e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80336659"
---
# <a name="using-azure-expressroute-to-create-hybrid-connectivity-to-support-remote-users"></a>使用 Azure ExpressRoute 建立混合式連線以支援遠端使用者

本文說明如何運用 ExpressRoute、Azure、Microsoft 網路和 Azure 合作夥伴生態系統，從遠端工作。

## <a name="connecting-to-azure-services-with-expressroute"></a>使用 ExpressRoute 連接到 Azure 服務

>[!NOTE]
>本文說明如何利用 ExpressRoute、Azure、Microsoft 網路和 Azure 合作夥伴生態系統，從遠端工作，並減輕因 COVID-19 危機而面臨的網路問題。
>

[ExpressRoute](expressroute-introduction.md)是一項 Azure 服務，可在 Microsoft 資料中心和內部部署或共置設備中的基礎結構之間啟用私人連線。 ExpressRoute 連線不會經過公用網際網路。 這些服務提供安全的連線能力、可靠性和速度，而且延遲時間會比透過網際網路的一般連接更低且一致。

## <a name="useful-links"></a>實用的連結

* [如何增加現有 ExpressRoute 線路的頻寬](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [ExpressRoute 監視、計量和警示](expressroute-monitoring-metrics-alerts.md#expressroute-gateway-connections-in-bitsseconds)
* [透過 ExpressRoute 的路由優化](expressroute-optimize-routing.md)
* [適用于 O365 的 Azure ExpressRoute](https://docs.microsoft.com/office365/enterprise/azure-expressroute?redirectSourcePath=%252farticle%252f6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [非對稱式路由考慮](expressroute-asymmetric-routing.md)
* [如何在 Azure 入口網站中開啟支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="troubleshoot"></a>疑難排解

* [驗證 ExpressRoute 連線](expressroute-troubleshooting-expressroute-overview.md)
* 取得[Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)和[傳統](expressroute-troubleshooting-arp-classic.md)中的 ARP 表格
* [重設失敗的電路](reset-circuit.md)
* [針對網路效能進行疑難排解](expressroute-troubleshooting-network-performance.md)

## <a name="next-steps"></a>後續步驟

* [瞭解 ExpressRoute 連線模型](expressroute-connectivity-models.md)
* 了解 ExpressRoute 連線和路由網域。 請參閱[ExpressRoute 線路和路由網域](expressroute-circuit-peerings.md)
* 尋找服務提供者。 請參閱[ExpressRoute 合作夥伴和對等互連位置](expressroute-locations.md)
* 請確定符合所有必要條件。 請參閱 [ExpressRoute 必要條件](expressroute-prerequisites.md)。
* [建立和修改 ExpressRoute 線路](expressroute-howto-circuit-portal-resource-manager.md)
* [建立和修改 ExpressRoute 線路的對等互連](expressroute-howto-routing-portal-resource-manager.md)
* [將虛擬網路連線到 ExpressRoute 電路](expressroute-howto-linkvnet-portal-resource-manager.md)
