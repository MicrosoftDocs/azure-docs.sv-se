---
title: SAP HANA tillgänglighet för virtuella Azure-datorer – Översikt | Microsoft Docs
description: Beskriver SAP HANA åtgärder på virtuella Azure-datorer.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a440344a93f9dd610774a33b3960ab1a71f57c65
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671576"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>SAP HANA hög tillgänglighet för virtuella Azure-datorer

Du kan använda flera Azure-funktioner för att distribuera verksamhets kritiska databaser, t. ex. SAP HANA på virtuella Azure-datorer. Den här artikeln innehåller vägledning om hur du uppnår tillgänglighet för SAP HANA instanser som finns i virtuella Azure-datorer. I artikeln beskrivs flera scenarier som du kan implementera med hjälp av Azure-infrastrukturen för att öka tillgängligheten för SAP HANA i Azure. 

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du är bekant med IaaS-grunderna (Infrastructure as a Service) i Azure, inklusive: 

- Distribuera virtuella datorer eller virtuella nätverk via Azure Portal eller PowerShell.
- Med hjälp av kommando rads gränssnittet för Azures plattforms oberoende (Azure CLI), inklusive alternativet att använda JavaScript Object Notation-mallar (JSON).

Den här artikeln förutsätter också att du är van att installera SAP HANA instanser och med att administrera och drift SAP HANA instanser. Det är särskilt viktigt att du är bekant med installationen och åtgärderna i HANA-systemreplikering. Detta inkluderar uppgifter som säkerhets kopiering och återställning av SAP HANA-databaser.

De här artiklarna ger en översikt över hur du använder SAP HANA i Azure:

- [Manuell installation av SAP HANA med en instans på virtuella Azure-datorer](./hana-get-started.md)
- [Konfigurera SAP HANA system replikering på virtuella Azure-datorer](sap-hana-high-availability.md)
- [Säkerhetskopiera SAP HANA på virtuella Azure-datorer](./sap-hana-backup-guide.md)

Det är också en bra idé att bekanta dig med de här artiklarna om SAP HANA:

- [Hög tillgänglighet för SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [Vanliga frågor och svar: hög tillgänglighet för SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Utför systemreplikering för SAP HANA](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2,0 SPS 01 Nyheter: hög tillgänglighet](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Nätverks rekommendationer för SAP HANA system replikering](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [SAP HANA systemreplikering](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [Automatisk omstart av SAP HANAs tjänst](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [Konfigurera SAP HANA system replikering](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Förutom att du är bekant med att distribuera virtuella datorer i Azure, rekommenderar vi att du läser [Hantera tillgängligheten för virtuella Windows-datorer i Azure](../../manage-availability.md)innan du definierar din tillgänglighets arkitektur i Azure.

## <a name="service-level-agreements-for-azure-components"></a>Service nivå avtal för Azure-komponenter

Azure har olika tillgänglighets service avtal för olika komponenter, t. ex. nätverk, lagring och virtuella datorer. Alla service avtal dokumenteras. Mer information finns i [Microsoft Azure Service nivå avtal](https://azure.microsoft.com/support/legal/sla/). 

[SLA för Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) beskriver tre olika service avtal för tre olika konfigurationer:

- En enskild virtuell dator som använder [Azure Premium-SSD](../../managed-disks-overview.md) för OS-disken och alla data diskar. Det här alternativet ger en månatlig drift tid på 99,9 procent.
- Flera (minst två) virtuella datorer som är ordnade i en [Azures tillgänglighets uppsättning](../../windows/tutorial-availability-sets.md). Det här alternativet ger en månatlig drift tid på 99,95 procent.
- Flera (minst två) virtuella datorer som är ordnade i en [tillgänglighet-zon](../../../availability-zones/az-overview.md). Det här alternativet tillhandahöll en månatlig drift tid på 99,99 procent.

Mät ditt tillgänglighets krav mot service avtal som Azure-komponenter kan tillhandahålla. Välj sedan dina scenarier för SAP HANA för att uppnå önskad tillgänglighets nivå.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [SAP HANA tillgänglighet inom en Azure-region](./sap-hana-availability-one-region.md).
- Lär dig mer om [SAP HANA tillgänglighet i Azure-regioner](./sap-hana-availability-across-regions.md). 















