---
title: Förstå omstarter av virtuella datorer
description: Förstå VM-omstarter – underhåll vs nedtid
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: 9342e0c39b558cf8b6ff5773d623ce55062f9ab5
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607481"
---
# <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Förstå VM-omstarter – underhåll jämfört med drift stopp
Det finns tre scenarier som kan leda till att virtuella datorer i Azure påverkas: oplanerat maskin varu underhåll, oväntad stillestånds tid och planerat underhåll.

## <a name="unplanned-hardware-maintenance-event"></a>Oplanerat maskin varu underhålls evenemang
Oplanerat maskin varu underhåll sker när Azure-plattformen förutsäger att maskin varan eller någon plattforms komponent som är kopplad till en fysisk dator är i färd med att fungera. När plattformen förutser ett problem skickar den en händelse om oplanerat maskinvaruunderhåll för att minska påverkan på virtuella datorer som finns på maskinvaran i fråga. Azure använder [Direktmigrering](./maintenance-and-updates.md) teknik för att migrera Virtual Machines från maskin varu fel till en felfri fysisk dator. Direktmigrering är en åtgärd för att skydda virtuella datorer, som endast pausar den virtuella datorn en kort stund. Minne, öppna filer och nätverksanslutningar bevaras, men prestanda kan försämras före och/eller efter händelsen. I de fall då det inte går att använda direktmigrering uppstår ett oväntat driftavbrott på den virtuella datorn (se nedan).


## <a name="unexpected-downtime"></a>Oväntad stillestånds tid
Oväntad stillestånds tid är när maskin varan eller den fysiska infrastrukturen för den virtuella datorn Miss lyckas oväntat. Detta kan omfatta lokala nätverks haverier, lokala diskfel eller andra rack nivå problem. När den identifieras migrerar Azure-plattformen automatiskt (läka) den virtuella datorn till en felfri fysisk dator i samma data Center. Återställningsprocessen medför driftavbrott (omstart) på virtuella datorer och i vissa fall förlust av den temporära enheten. Anslutna operativsystems- och datadiskar bevaras alltid.

Virtuella datorer kan också uppleva stillestånds tid i osannolikt fall av ett avbrott eller en katastrof som påverkar ett helt data Center, eller till och med en hel region. I dessa scenarier tillhandahåller Azure skydds alternativ, inklusive  [tillgänglighets zoner](../availability-zones/az-overview.md) och [kopplade regioner](regions.md#region-pairs).

## <a name="planned-maintenance-events"></a>Planerade underhållshändelser
Planerat underhåll är periodiska uppdateringar som Microsoft utför i syfte att förbättra tillförlitligheten, prestandan och säkerheten för den plattformsinfrastruktur som dina virtuella datorer körs i. De flesta av dessa uppdateringar utförs utan någon inverkan på Virtual Machines eller Cloud Services (se [underhåll som inte kräver omstart](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)). Azure-plattformen försöker alltid att utföra underhåll utan att påverka virtuella datorer, men i sällsynta fall kräver dessa uppdateringar en omstart av den virtuella datorn för att de nödvändiga uppdateringarna av den underliggande infrastrukturen ska kunna installeras. I detta fall kan du utföra planerat underhåll i Azure med underhålls- och omdistributionsåtgärden genom att initiera underhållet för de virtuella datorerna vid en lämplig tidpunkt. Mer information finns i [Planned Maintenance for Virtual Machines](maintenance-and-updates.md) (Planerat underhåll för virtuella datorer).

## <a name="reduce-downtime"></a>Minska nedtid
För att undvika påverkan av den här typen av avbrott rekommenderar vi att du gör följande för att säkerställa hög tillgänglighet för dina virtuella datorer:

* Använd [Tillgänglighetszoner](../availability-zones/az-overview.md) för att skydda från data Center problem
* Konfigurera flera virtuella datorer i en [tillgänglighets uppsättning](availability-set-overview.md) för redundans
* Använd [schemalagda händelser för Linux](/azure/virtual-machines/linux/scheduled-events) eller [schemalagda händelser för Windows](/azure/virtual-machines/windows/scheduled-events) för att proaktivt svara på händelser som påverkar virtuella datorer
* Konfigurera varje programnivå i separata tillgänglighetsuppsättningar
* Kombinera en [belastningsutjämnare](../load-balancer/load-balancer-overview.md) med tillgänglighets zoner eller uppsättningar

## <a name="next-steps"></a>Nästa steg
Mer information om tillgänglighets alternativ i Azure finns i [Översikt över tillgänglighet](availability.md).
