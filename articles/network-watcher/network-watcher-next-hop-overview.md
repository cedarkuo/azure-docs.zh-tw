---
title: Azure 網路監看員中的下一個躍點簡介 | Microsoft Docs
description: 本文提供網路監看員下一個躍點功能的概觀。
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 33c22b5b68b6677e8cf271dc185007316ec44500
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76844053"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>使用下一個躍點來診斷虛擬機器路由問題

來自虛擬機器 (VM) 的流量會根據與網路介面 (NIC) 相關聯的有效路由來傳送到目的地。 下一個躍點會從特定 VM 和 NIC 取得封包的下一個躍點類型和 IP 位址。 了解下一個躍點可協助您判斷流量是否會導向預定目的地，或流量是否不會傳送到其他地方。 若未正確設定路由，而讓流量導向內部部署位置或虛擬應用裝置，就可能會導致連線問題。 下一個躍點也會傳回與下一個躍點相關聯的路由表。 如果路由定義為使用者定義的路由，便會傳回該路由。 否則，下一個躍點會傳回**系統路由**。

![下一個躍點概觀](./media/network-watcher-next-hop-overview/figure1.png)

下一個躍點功能可能會傳回的下一個躍點如下所示：

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VirtualNetwork
* VirtualNetworkPeering
* VirtualNetworkServiceEndpoint 
* MicrosoftEdge
* 無

若要深入了解每個下一個躍點類型，請參閱[路由概觀](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)。

## <a name="next-steps"></a>後續步驟

若要了解如何使用下一個躍點來診斷 VM 網路路由問題，請參閱使用 [Azure 入口網站](diagnose-vm-network-routing-problem.md)、[PowerShell](diagnose-vm-network-routing-problem-powershell.md) 或 [Azure CLI](diagnose-vm-network-routing-problem-cli.md) 來診斷 VM 網路路由問題。
