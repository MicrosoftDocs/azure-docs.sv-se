---
title: Flytta virtuella Azure-datorer till en annan region med Azure Site Recovery
description: Använd Azure Site Recovery för att flytta virtuella Azure-datorer från en Azure-region till en annan.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 61d596c4b3a65c54e1a70682adad5b7328c384f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90007374"
---
# <a name="moving-azure-vms-to-another-azure-region"></a>Flytta virtuella Azure-datorer till en annan Azure-region

Den här artikeln innehåller en översikt över de orsaker och steg som ingår i flytta virtuella Azure-datorer till en annan Azure-region med hjälp av [Azure Site Recovery](site-recovery-overview.md). 


## <a name="reasons-to-move-azure-vms"></a>Skäl att flytta virtuella Azure-datorer

Du kan flytta virtuella datorer av följande anledningar:

- Du har redan distribuerat i en region och ett nytt regions stöd har lagts till som är närmare slutanvändaren för ditt program eller din tjänst. I det här scenariot skulle du vilja flytta dina virtuella datorer till den nya regionen för att minska svars tiden. Använd samma metod om du vill konsolidera prenumerationer eller om det finns styrnings-eller organisations regler som kräver att du flyttar.
- Den virtuella datorn distribuerades som en virtuell dator med enkel instans eller som en del av en tillgänglighets uppsättning. Om du vill öka tillgänglighets service avtal kan du flytta dina virtuella datorer till en tillgänglighets zon.

## <a name="move-vms-with-resource-mover"></a>Flytta virtuella datorer med resurs förflyttning

Du kan nu flytta virtuella datorer till en annan region med [Azure Resource-arbetskraften](../resource-mover/tutorial-move-region-virtual-machines.md). Resurs förflyttning är i offentlig för hands version och ger:
- En enda hubb för att flytta resurser mellan regioner.
- Minskad flytt tid och komplexitet. Allt du behöver finns på en enda plats.
- En enkel och konsekvent upplevelse för att flytta olika typer av Azure-resurser.
- Ett enkelt sätt att identifiera beroenden för resurser som du vill flytta. Detta hjälper dig att flytta relaterade resurser tillsammans, så att allt fungerar som förväntat i mål regionen efter flytten.
- Automatisk rensning av resurser i käll regionen, om du vill ta bort dem efter flytten.
- Prestandatester. Du kan prova en flytt och sedan ta bort den om du inte vill göra en fullständig flytt.



## <a name="move-vms-with-site-recovery"></a>Flytta virtuella datorer med Site Recovery

Att flytta virtuella datorer med Site Recovery omfattar följande steg:

1. Kontrollera förutsättningarna.
2. Förbered de virtuella käll datorerna.
3. Förbered mål regionen.
4. Kopiera data till mål regionen. Använd Azure Site Recovery-teknik för replikering för att kopiera data från den virtuella käll datorn till mål regionen.
5. Testa konfigurationen. När replikeringen är klar testar du konfigurationen genom att utföra en redundanstest till ett nätverk som inte är i produktion.
6. Genomför flyttningen.
7. Ta bort resurserna i käll regionen.

> [!NOTE]
> Information om de här stegen finns i följande avsnitt.
> [!IMPORTANT]
> För närvarande har Azure Site Recovery stöd för att flytta virtuella datorer från en region till en annan men det går inte att flytta i en region.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Vanliga arkitekturer för distributioner med flera nivåer

I det här avsnittet beskrivs de vanligaste distributions arkitekturerna för ett program i flera nivåer i Azure. Exemplet är ett program med tre nivåer med en offentlig IP-adress. Varje nivå (webb, program och databas) har två virtuella datorer, och de är anslutna till de andra nivåerna av en Azure Load Balancer. Databas nivån har SQL Server Always on replikering mellan de virtuella datorerna för hög tillgänglighet.

* **Virtuella datorer med en enda instans som distribueras över olika nivåer**: varje virtuell dator i en nivå konfigureras som en virtuell dator med en instans och är ansluten av belastningsutjämnare till de andra nivåerna. Den här konfigurationen är den enklaste att anta.

     ![Val för att flytta en VM-distribution med enkel instans mellan nivåer](media/move-vm-overview/regular-deployment.png)

* **Virtuella datorer i varje nivå som distribueras över tillgänglighets uppsättningar**: varje virtuell dator i en nivå konfigureras i en tillgänglighets uppsättning. [Tillgänglighetsuppsättningarna](../virtual-machines/windows/tutorial-availability-sets.md) ser till att de virtuella datorer du distribuerar i Azure distribueras över flera isolerade maskinvarunoder i ett kluster. Detta säkerställer att om ett maskinvaru-eller program varu haveri i Azure inträffar påverkas endast en del av de virtuella datorerna och den övergripande lösningen förblir tillgänglig och fungerar.

     ![VM-distribution över tillgänglighets uppsättningar](media/move-vm-overview/avset.png)

* **Virtuella datorer på varje nivå som distribueras mellan Tillgänglighetszoner**: varje virtuell dator i en nivå konfigureras i [Tillgänglighetszoner](../availability-zones/az-overview.md). En tillgänglighetszon i en Azure-region är en kombination av en feldomän och en uppdateringsdomän. Om du skapar tre eller flera virtuella datorer över tre zoner i en Azure-region distribueras i praktiken dina virtuella datorer mellan tre feldomäner och tre uppdateringsdomäner. Azure-plattformen identifierar den här distributionen mellan uppdateringsdomänerna så att inte virtuella datorer i olika zoner uppdateras på samma gång.

     ![Distribution av tillgänglighets zon](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>Flytta virtuella datorer i befintligt skick till en målregion

Baserat på de [arkitekturer](#typical-architectures-for-a-multi-tier-deployment) som nämnts tidigare, är det här hur distributionerna kommer att se ut när du har utfört flytta som är till mål regionen.

* **Virtuella datorer med enkel instans distribuerade över olika nivåer**
* **Virtuella datorer i varje nivå som distribueras över tillgänglighets uppsättningar**
* **Virtuella datorer på varje nivå som distribueras i Tillgänglighetszoner**

## <a name="move-vms-to-increase-availability"></a>Flytta virtuella datorer för att öka tillgängligheten

* **Virtuella datorer med enkel instans distribuerade över olika nivåer**

     ![VM-distribution med enkel instans mellan nivåer](media/move-vm-overview/single-zone.png)

* **Virtuella datorer på varje nivå som distribueras över tillgänglighets uppsättningar**: du kan konfigurera dina virtuella datorer i en tillgänglighets uppsättning i separata Tillgänglighetszoner när du aktiverar replikering för den virtuella datorn med hjälp av Azure Site Recovery. Service avtalet för tillgänglighet är 99,99% när du har slutfört flytt åtgärden.

     ![VM-distribution över tillgänglighets uppsättningar och Tillgänglighetszoner](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> 
> * [Migrera virtuella Azure-datorer till en annan region](azure-to-azure-tutorial-migrate.md)
> 
> * [Flytta virtuella Azure-datorer till Tillgänglighetszoner](move-azure-vms-avset-azone.md)

