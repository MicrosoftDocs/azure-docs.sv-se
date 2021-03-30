---
title: Flytta Azure nätverks resurser till en ny prenumeration eller resurs grupp
description: Använd Azure Resource Manager för att flytta virtuella nätverk och andra nätverks resurser till en ny resurs grupp eller prenumeration.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 0cd6887d3489f2ffede0f5e3d63533a33a6ccc04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75485239"
---
# <a name="move-guidance-for-networking-resources"></a>Flytta vägledning för nätverks resurser

Den här artikeln beskriver hur du flyttar virtuella nätverk och andra nätverks resurser för vissa scenarier.

## <a name="dependent-resources"></a>Beroende resurser

När du flyttar ett virtuellt nätverk måste du också flytta dess beroende resurser. För VPN-gatewayer måste du flytta IP-adresser, virtuella nätverks-gatewayer och alla associerade anslutnings resurser. Lokala nätverksgateway kan finnas i en annan resurs grupp.

Om du vill flytta en virtuell dator med ett nätverkskort till en ny prenumeration måste du flytta alla beroende resurser. Flytta det virtuella nätverket för nätverks gränssnitts kortet, alla andra nätverkskort för det virtuella nätverket och VPN-gatewayerna.

Mer information finns i [scenario för att flytta mellan prenumerationer](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions).

## <a name="peered-virtual-network"></a>Peer-kopplat virtuellt nätverk

Om du vill flytta ett peer-kopplat virtuellt nätverk måste du först inaktivera det virtuella nätverkets peering. När du har inaktiverat kan du flytta det virtuella nätverket. När du har flyttat aktiverar du det virtuella nätverkets peering.

## <a name="subnet-links"></a>Under näts länkar

Du kan inte flytta ett virtuellt nätverk till en annan prenumeration om det virtuella nätverket innehåller ett undernät med länkar till resurs navigering. Om till exempel en Azure-cache för Redis-resurs distribueras till ett undernät har det under nätet en resurs navigerings länk.

## <a name="next-steps"></a>Nästa steg

För kommandon för att flytta resurser, se [Flytta resurser till ny resurs grupp eller prenumeration](../move-resource-group-and-subscription.md).
