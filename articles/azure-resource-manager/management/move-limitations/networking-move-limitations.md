---
title: Flytta Azure nätverks resurser till en ny prenumeration eller resurs grupp
description: Använd Azure Resource Manager för att flytta virtuella nätverk och andra nätverks resurser till en ny resurs grupp eller prenumeration.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: b7aaf01b696b13136a0f4077f315b137c8917906
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120137"
---
# <a name="move-guidance-for-networking-resources"></a>Flytta vägledning för nätverks resurser

Den här artikeln beskriver hur du flyttar virtuella nätverk och andra nätverks resurser för vissa scenarier.

## <a name="dependent-resources"></a>Beroende resurser

> [!NOTE]
> Observera att VPN-gatewayer som är kopplade till offentliga IP-adresser inte kan flyttas mellan resurs grupper eller prenumerationer för närvarande.

När du flyttar en resurs måste du också flytta dess beroende resurser (t. ex. offentliga IP-adresser, virtuella nätverksgateway, alla associerade anslutnings resurser). Lokala nätverksgateway kan finnas i en annan resurs grupp.

Om du vill flytta en virtuell dator med ett nätverkskort till en ny prenumeration måste du flytta alla beroende resurser. Flytta det virtuella nätverket för nätverks gränssnitts kortet, alla andra nätverkskort för det virtuella nätverket och VPN-gatewayerna.

Mer information finns i [scenario för att flytta mellan prenumerationer](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions).

## <a name="peered-virtual-network"></a>Peer-kopplat virtuellt nätverk

Om du vill flytta ett peer-kopplat virtuellt nätverk måste du först inaktivera det virtuella nätverkets peering. När du har inaktiverat kan du flytta det virtuella nätverket. När du har flyttat aktiverar du det virtuella nätverkets peering.

## <a name="subnet-links"></a>Under näts länkar

Du kan inte flytta ett virtuellt nätverk till en annan prenumeration om det virtuella nätverket innehåller ett undernät med länkar till resurs navigering. Om till exempel en Azure-cache för Redis-resurs distribueras till ett undernät har det under nätet en resurs navigerings länk.

## <a name="next-steps"></a>Nästa steg

För kommandon för att flytta resurser, se [Flytta resurser till ny resurs grupp eller prenumeration](../move-resource-group-and-subscription.md).
