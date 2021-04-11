---
title: Tillstånd och fakturering av Azure-Virtual Machines
description: Översikt över olika tillstånd en virtuell dator kan ange och när en användare faktureras.
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: c206cba3b23a0bf41ce32481980aa466e869bcde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104596305"
---
# <a name="states-and-billing-of-azure-virtual-machines"></a>Tillstånd och fakturering av Azure-Virtual Machines

Azure Virtual Machines (VM) går igenom olika tillstånd som kan kategoriseras i *etablerings* -och *energi* tillstånd. Syftet med den här artikeln är att beskriva dessa tillstånd och särskilt Markera när kunder faktureras för användning av instanser. 

## <a name="get-states-using-instance-view"></a>Hämta tillstånd med hjälp av instans visning

Instans visnings-API: et tillhandahåller information om den virtuella datorns körnings tillstånd. Mer information finns i dokumentationen för [Virtual Machines-instans Visa](/rest/api/compute/virtualmachines/instanceview) API.

Azures resurs Utforskaren innehåller ett enkelt användar gränssnitt för att visa den virtuella datorn som kör tillstånd: [Resursläsaren](https://resources.azure.com/).

Etablerings tillstånd visas på VM-egenskaper och instans visning. Energi spar lägen är tillgängliga i instans visning av virtuell dator.

Om du vill hämta energi läget för alla virtuella datorer i din prenumeration använder du [Virtual Machines-List all API](/rest/api/compute/virtualmachines/listall) med parametern **statusOnly** inställd på *True*.

> [!NOTE]
> [Virtual Machines – Visa alla API: er](/rest/api/compute/virtualmachines/listall) med parametern **statusOnly** inställd på True hämtar energi tillstånden för alla virtuella datorer i en prenumeration. Men i vissa sällsynta fall kanske inte energi läget är tillgängligt på grund av tillfälliga problem i hämtnings processen. I sådana situationer rekommenderar vi att du försöker använda samma API eller använda [Azure Resource Health](../service-health/resource-health-overview.md) eller [Azure Resource Graph](..//governance/resource-graph/overview.md) för att kontrol lera de virtuella datorernas energi nivå.
 
## <a name="power-states-and-billing"></a>Energi spar lägen och fakturering

Energi läget motsvarar det senaste kända läget för den virtuella datorn.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-power-states.png" alt-text="Bild visar diagram över de energi tillstånd som en virtuell dator kan gå igenom. ":::

Följande tabell innehåller en beskrivning av varje instans tillstånd och indikerar om det debiteras för användning av instanser eller inte.

| Energi spar läge | Beskrivning | Fakturering |  
|---|---|---|
| Startar| Den virtuella datorn har startats. |Ej fakturerat * | 
| Körs | Den virtuella datorn är helt igång. Detta är standard arbets läget. | Debiteras | 
| Stoppas | Detta är ett över gångs tillstånd mellan att köra och stoppas. | Debiteras| 
|Stoppad | Den virtuella datorn har stängts av från gäst operativ systemet eller använder avstängnings läge-API: er. I det här läget kommer den virtuella datorn fortfarande att leasa den underliggande maskin varan. Den här statusen kallas även *stoppad (tilldelad)*. | Debiteras | 
| Frigör | Detta är över gångs läget mellan att köra och frigöra. | Ej fakturerat * | 
| Frigjord | Den virtuella datorn har släppt lånet för den underliggande maskin varan och är helt avstängd. Det här läget kallas även *stoppad (Frigjord)*. | Ej fakturerat * | 

&#42; vissa Azure-resurser, till exempel [diskar](https://azure.microsoft.com/pricing/details/managed-disks) och [nätverk](https://azure.microsoft.com/pricing/details/bandwidth/) , kommer att fortsätta debiteras.


## <a name="provisioning-states"></a>Etablerings tillstånd

Ett etablerings tillstånd är status för en användarinitierad, kontroll Plans åtgärd på den virtuella datorn. Dessa tillstånd är skilda från den virtuella datorns energi tillstånd.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-provisioning-states.png" alt-text="Bild visar de etablerings tillstånd en virtuell dator kan gå igenom.":::

| Etablerings status | Beskrivning | Energi spar läge | Fakturering | 
|---|---|---|---|
| Skapa | Skapa virtuell dator. | Startar | Ej fakturerat * | 
| Uppdatera | Uppdaterar modellen för en befintlig virtuell dator. Vissa ändringar i en virtuell dator som inte är modeller, till exempel start och omstart, faller under uppdaterings läget. | Körs | Debiteras | 
| Ta bort | Borttagning av virtuell dator. | Frigör | Ej fakturerat * |
| Frigöra | Den virtuella datorn har stoppats och tagits bort från den underliggande värden. Att avallokera en virtuell dator betraktas som en uppdatering och visar etablerings status liknande uppdatering. | Frigör | Ej fakturerat * | 

&#42; vissa Azure-resurser, till exempel [diskar](https://azure.microsoft.com/pricing/details/managed-disks) och [nätverk](https://azure.microsoft.com/pricing/details/bandwidth/) , kommer att fortsätta debiteras.

## <a name="os-provisioning-states"></a>Etablerings tillstånd för operativ system
Etablerings tillstånd för operativ system gäller endast för virtuella datorer som skapats med en operativ system avbildning. Specialiserade bilder visar inte dessa tillstånd. 

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/os-provisioning-states.png" alt-text="Bild visar de OS-etablerings tillstånd som en virtuell dator kan gå igenom.":::

| Etablerings status för operativ system | Beskrivning | Energi spar läge | Fakturering | 
|---|---|---|---|
| OSProvisioningInProgress | Den virtuella datorn körs och installationen av gäst operativ systemet pågår. | Körs | Debiteras | 
| OSProvisioningComplete | Detta är ett tillstånd för kort livs längd. Den virtuella datorn övergår snabbt från det här läget till **framgång**. Om tillägg fortfarande installeras kommer du att fortsätta att se det här läget tills det är klart. | Körs | Debiteras | 
| Lyckades | Åtgärderna som initierats av användaren har slutförts. | Körs | Debiteras | 
| Misslyckad | Representerar en misslyckad åtgärd. Mer information och möjliga lösningar hittar du i felkoden. | Körs  | Debiteras | 


## <a name="next-steps"></a>Nästa steg
- Läs [Azure Cost Management-och fakturerings dokumentationen](../cost-management-billing/index.yml)
- Använd [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/) för att planera dina distributioner.
- Läs mer om övervakning av den virtuella datorn [i övervaka virtuella datorer i Azure](../azure-monitor/vm/monitor-vm-azure.md).