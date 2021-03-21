---
title: Resurs tillgänglighet per region
description: Tillgänglighet för beräknings-och minnes resurser för tjänsten Azure Container Instances i olika Azure-regioner.
ms.topic: article
ms.date: 04/27/2020
ms.custom: references_regions
ms.openlocfilehash: 4decf29be23c2f1ed51f422052869e99abe4a511
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103573616"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Resurs tillgänglighet för Azure Container Instances i Azure-regioner

Den här artikeln innehåller information om tillgängligheten för Azure Container Instances beräknings-, minnes-och lagrings resurser i Azure-regioner och efter mål operativ system. En allmän lista över tillgängliga regioner för Azure Container Instances finns i [tillgängliga regioner](https://azure.microsoft.com/regions/services/).

Värden som anges är de maximala tillgängliga resurserna per distribution av en [behållar grupp](container-instances-container-groups.md). Värden är aktuella vid tidpunkten för publiceringen.

> [!NOTE]
> Behållar grupper som skapas inom dessa resurs gränser är beroende av tillgängligheten i distributions regionen. Om en region har hög belastning kan du uppleva fel vid distribution av instanser. Om du vill undvika ett sådant distributions haveri försök att distribuera instanser med lägre resurs inställningar, eller försök att distribuera vid ett senare tillfälle eller i en annan region med tillgängliga resurser.

Information om kvoter och andra begränsningar i dina distributioner finns i [kvoter och gränser för Azure Container instances](container-instances-quotas.md).

## <a name="linux-container-groups"></a>Linux container grupper

Följande regioner och maximala resurser är tillgängliga för behållar grupper med Linux-behållare i allmänna distributioner, [Azure Virtual Network](container-instances-vnet.md) -distributioner och distributioner med [GPU-resurser](container-instances-gpu.md) (för hands version).

> [!IMPORTANT]
> De maximala resurserna i en region är olika beroende på din distribution. En region kan till exempel ha en annan maximal processor-och minnes storlek i en Azure Virtual Network-distribution än för en allmän distribution. Samma region kan också ha en annan uppsättning av högsta värden för en distribution med GPU-resurser. Verifiera distributions typen innan du kontrollerar de följande tabellerna för att se de maximala värdena i din region.

| Region | Maximal CPU | Högsta mängd minne (GB) | Max-CPU för VNET | Största mängd minne i VNET (GB) | Lagring (GB) | GPU SKU: er (för hands version) |
| -------- | :---: | :---: | :----: | :-----: | :-------: | :----: |
| Australien, östra | 4 | 16 | 4 | 16 | 50 | Ej tillämpligt |
| Brasilien, södra | 4 | 16 | 2 | 8 | 50 | Ej tillämpligt |
| Kanada, centrala | 4 | 16 | 4 | 16 | 50 | Ej tillämpligt |
| Indien, centrala | 4 | 16 | 4 | 4 | 50 | V100 |
| Central US | 4 | 16 | 4 | 16 | 50 | Ej tillämpligt |
| Asien, östra | 4 | 16 | 4 | 16 | 50 | Ej tillämpligt |
| East US | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| USA, östra 2 | 4 | 16 | 4 | 16 | 50 | Ej tillämpligt |
| Frankrike, centrala | 4 | 16 | 4 | 16 | 50 | Ej tillämpligt |
| Tyskland, västra centrala | 3 | 16 | Saknas | Saknas | 50 | Ej tillämpligt |
| Japan, östra | 2 | 8 | 4 | 16 | 50 | Ej tillämpligt |
| Sydkorea, centrala | 4 | 16 | Saknas | Saknas | 50 | Ej tillämpligt |
| USA, norra centrala | 2 | 3.5 | 4 | 16 | 50 | K80, P100, V100 |
| Europa, norra | 4 | 16 | 4 | 16 | 50 | K80 |
| USA, södra centrala | 4 | 16 | 4 | 16 | 50 | Ej tillämpligt |
| Sydostasien | 4 | 16 | 4 | 16 | 50 | P100, V100 |
| Indien, södra | 4 | 16 | Saknas | Saknas | 50 | Ej tillämpligt |
| Schweiz, norra | 3 | 16 | Saknas | Saknas | 50 | Ej tillämpligt |
| Storbritannien, södra | 4 | 16 | 4 | 16 | 50 | Ej tillämpligt |
| Förenade Arabemiraten, norra | 3 | 16 | Saknas | Saknas | 50 | Ej tillämpligt |
| USA, västra centrala| 4 | 16 | 4 | 16 | 50 | Ej tillämpligt |
| Europa, västra | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| USA, västra | 4 | 16 | 4 | 16 | 50 | Ej tillämpligt |
| USA, västra 2 | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |

Följande maximala resurser är tillgängliga för en behållar grupp som distribueras med [GPU-resurser](container-instances-gpu.md) (för hands version).

> [!IMPORTANT]
> För närvarande stöds inte distributioner med GPU-resurser i en Azure Virtual Network-distribution och är bara tillgängliga i Linux container Groups.

| GPU-SKU: er | GPU-antal | Maximal CPU | Högsta mängd minne (GB) | Lagring (GB) |
| --- | --- | --- | --- | --- |
| K80 | 1 | 6 | 56 | 50 |
| K80 | 2 | 12 | 112 | 50 |
| K80 | 4 | 24 | 224 | 50 |
| P100, V100 | 1 | 6 | 112 | 50 |
| P100, V100 | 2 | 12 | 224 | 50 |
| P100, V100 | 4 | 24 | 448 | 50 |

## <a name="windows-container-groups"></a>Grupper med Windows-behållare

Följande regioner och maximalt antal resurser är tillgängliga för behållar grupper med [stödda och för hands versioner](container-instances-faq.md#what-windows-base-os-images-are-supported) av Windows Server-behållare.

> [!IMPORTANT]
> För närvarande stöds inte distributioner med grupper med Windows-behållare i en Azure Virtual Network-distribution.

###  <a name="windows-server-2016"></a>Windows Server 2016

> [!NOTE]
> Se [kompatibilitet för värd-och container version](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility) för mer information om 1b, 2b och 3b värdar.

| Region | 1B/2B Max CPU | 1B/2B Max minne (GB) |3B Max CPU | 3B Max minne (GB) | Lagring (GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| Australien, östra | 2 | 8 | 2 | 8 | 20 |
| Brasilien, södra | 4 | 16 | 4 | 16 | 20 |
| Kanada, centrala | 2 | 8 | 2 | 3.5 | 20 |
| Indien, centrala | 2 | 3.5 | 2 | 3.5 | 20 |
| Central US | 2 | 3.5 | 2 | 3.5 | 20 |
| Asien, östra | 2 | 3.5 | 2 | 3.5 | 20 |
| East US | 4 | 16 | 2 | 8 | 20 |
| USA, östra 2 | 2 | 3.5 | 4 | 16 | 20 |
| Japan, östra | 4 | 16 | 4 | 16 | 20 |
| Sydkorea, centrala | 4 | 16 | 4 | 16 | 20 |
| USA, norra centrala | 4 | 16 | 4 | 16 | 20 |
| Europa, norra | 2 | 8 | 2 | 8 | 20 |
| USA, södra centrala | 2 | 3.5 | 2 | 8 | 20 |
| Sydostasien | Saknas | Saknas | 2 | 3.5 | 20 |
| Indien, södra | 2 | 3.5 | 2 | 3.5 | 20 |
| Storbritannien, södra | 2 | 8 | 2 | 3.5 | 20 |
| USA, västra centrala | 4 | 16 | 2 | 8 | 20 |
| Europa, västra | 4 | 16 | 4 | 16 | 20 |
| USA, västra | 4 | 16 | 2 | 8 | 20 |
| USA, västra 2 | 2 | 8 | 2 | 3.5 | 20 |


### <a name="windows-server-2019-ltsc"></a>Windows Server 2019 LTSC

> [!NOTE]
> Se [kompatibilitet för värd-och container version](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility) för mer information om 1b, 2b och 3b värdar.

| Region | 1B/2B Max CPU | 1B/2B Max minne (GB) |3B Max CPU | 3B Max minne (GB) | Lagring (GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| Australien, östra | 4 | 16 | 4 | 16 | 20 |
| Brasilien, södra | 4 | 16 | 4 | 16 | 20 |
| Kanada, centrala | 4 | 16 | 4 | 16 | 20 |
| Indien, centrala | 4 | 16 | 4 | 16 | 20 |
| Central US | 4 | 16 | 4 | 16 | 20 |
| Asien, östra | 4 | 16 | 4 | 16 | 20 |
| East US | 4 | 16 | 4 | 16 | 20 |
| USA, östra 2 | 2 | 3.5 | 2 | 3.5 | 20 |
| Frankrike, centrala | 4 | 16 | 4 | 16 | 20 |
| Japan, östra | Saknas | Saknas | 4 | 16 | 20 |
| Sydkorea, centrala | 4 | 16 | 4 | 16 | 20 |
| USA, norra centrala | 4 | 16 | 4 | 16 | 20 |
| Europa, norra | 4 | 16 | 4 | 16 | 20 |
| USA, södra centrala | 4 | 16 | 4 | 16 | 20 |
| Sydostasien | 4 | 16 | 4 | 16 | 20 |
| Indien, södra | 4 | 16 | 4 | 16 | 20 |
| Storbritannien, södra | 4 | 16 | 4 | 16 | 20 |
| USA, västra centrala | 4 | 16 | 4 | 16 | 20 |
| Europa, västra | 4 | 16 | 4 | 16 | 20 |
| USA, västra | 4 | 16 | 4 | 16 | 20 |
| USA, västra 2 | 2 | 8 | 4 | 16 | 20 |

## <a name="next-steps"></a>Nästa steg

Låt teamet veta om du vill se fler regioner eller ökad resurs tillgänglighet på [aka.MS/ACI/feedback](https://aka.ms/aci/feedback).

Information om hur du felsöker distribution av container instans finns i [Felsöka distributions problem med Azure Container instances](container-instances-troubleshooting.md).


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
