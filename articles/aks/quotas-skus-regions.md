---
title: 'Begränsningar för resurser, SKU: er, regioner'
titleSuffix: Azure Kubernetes Service
description: Lär dig mer om standard kvoter, begränsad VM SKU-storlekar och regions tillgänglighet för Azure Kubernetes service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 3e1e74834153584525d2093d2a1bb8ba8e991e5a
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011472"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Kvoter, storleks begränsningar för virtuella datorer och regions tillgänglighet i Azure Kubernetes service (AKS)

Alla Azure-tjänster ställer in standard gränser och kvoter för resurser och funktioner, inklusive användnings begränsningar för vissa virtuella datorer (VM) SKU: er.

Den här artikeln beskriver standard resurs gränserna för Azure Kubernetes service-resurser (AKS) och tillgängligheten för AKS i Azure-regioner.

## <a name="service-quotas-and-limits"></a>Kvoter och begränsningar för tjänsten

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Etablerad infrastruktur

Alla andra begränsningar för nätverk, beräkning och lagring gäller för den etablerade infrastrukturen. För relevanta gränser, se [prenumerations-och tjänst begränsningar i Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).

> [!IMPORTANT]
> När du uppgraderar ett AKS-kluster förbrukas extra resurser tillfälligt. Bland dessa resurser ingår tillgängliga IP-adresser i ett virtuellt nätverks undernät eller en virtuell dators vCPU kvot. 
>
> För Windows Server-behållare kan du utföra en uppgraderings åtgärd för att tillämpa de senaste uppdateringarna av noden. Om du inte har det tillgängliga IP-adressutrymmet eller vCPU-kvoten för att hantera dessa tillfälliga resurser, kommer kluster uppgraderings processen att Miss Don. Mer information om uppgraderings processen för Windows Server Node finns [i uppgradera en Node-pool i AKS][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Begränsade VM-storlekar

Varje nod i ett AKS-kluster innehåller en fast mängd beräknings resurser, till exempel vCPU och minne. Om en AKS-nod innehåller otillräckliga beräknings resurser kan poddar kanske inte köras korrekt. **Använd inte följande VM-SKU: er i AKS** för att säkerställa att nödvändiga *Kube-* poddar och dina program kan schemaläggas på ett tillförlitligt sätt:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Mer information om VM-typer och deras beräknings resurser finns i [storlekar för virtuella datorer i Azure][vm-skus].

## <a name="region-availability"></a>Regional tillgänglighet

Den senaste listan över var du kan distribuera och köra kluster finns i [AKS regions tillgänglighet][region-availability].

## <a name="next-steps"></a>Nästa steg

Du kan öka vissa standard gränser och kvoter. Om din resurs har stöd för en ökning begär du en ökning via en [support förfrågan för Azure][azure-support] (för **typ av problem**, Välj **kvot**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
