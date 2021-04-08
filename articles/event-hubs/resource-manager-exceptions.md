---
title: Azure Event Hubs – Resource Manager-undantag | Microsoft Docs
description: Lista över Azure Event Hubs-undantag som har Azure Resource Manager och föreslagna åtgärder.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: cec24696d0d49ba408860f6562c34dd14876c311
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91334216"
---
# <a name="azure-event-hubs---resource-manager-exceptions"></a>Azure Event Hubs – Resource Manager-undantag
Den här artikeln innehåller undantag som genereras när du interagerar med Azure Event Hubs att använda Azure Resource Manager via mallar eller direkta anrop.

> [!IMPORTANT]
> Det här dokumentet uppdateras ofta. Kom tillbaka efter uppdateringar.

I följande avsnitt finns olika undantag/fel som är anslutna till Azure Resource Manager.

## <a name="error-code-conflict"></a>Felkod: konflikt

| Felkod | Fel under kod | Felmeddelande | Beskrivning | Rekommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Konflikt | 40300 | Det maximala antalet resurser av typen EventHub har nåtts eller överskridits. Faktiskt: #, Max tillåtet: # | Namn området har nått sin [kvot](event-hubs-quotas.md) för antalet Event Hubs det kan innehålla. | Ta bort oanvända eller främmande händelse nav från namn området eller Överväg att uppgradera till ett [dedikerat kluster](event-hubs-dedicated-overview.md). |
| Konflikt | inget | Det går inte att ta bort DR-konfigurationen (haveri beredskap) eftersom replikering pågår. Redundansväxla eller Bryt ihopparning innan du försöker ta bort DR-konfigurationen. | [Följande geodr-replikering](event-hubs-geo-dr.md) pågår, så det går inte att ta bort konfigurationen för tillfället. | Om du vill avblockera borttagning av konfigurationen måste du antingen vänta tills replikeringen har slutförts, utlösa en redundansväxling eller bryta följande geodr-ihopparningen. |
| Konflikt | inget | Namn områdes uppdateringen misslyckades med en konflikt i Server delen. | En annan åtgärd utförs för närvarande på det här namn området. | Vänta tills den aktuella åtgärden har slutförts och försök sedan igen. |

## <a name="error-code-429"></a>Felkod: 429

| Felkod | Fel under kod | Felmeddelande | Beskrivning | Rekommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | inget | Namn områdes etablering i över gång | En annan åtgärd utförs för närvarande på det här namn området. | Vänta tills den aktuella åtgärden har slutförts och försök sedan igen. |
| 429 | inget | Katastrof återställnings åtgärd pågår. | En [följande geodr](event-hubs-geo-dr.md) -åtgärd utförs för närvarande på det här namn området eller ihopparningen. | Vänta tills den aktuella följande geodr-åtgärden har slutförts och försök sedan igen. |

## <a name="error-code-badrequest"></a>Felkod: BadRequest

| Felkod | Fel under kod | Felmeddelande | Beskrivning | Rekommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| BadRequest | 40000 | PartitionCount kan inte ändras för en Event Hub. | Basic-eller standard-nivån av Azure Event Hubs stöder inte ändring av partitioner. | Skapa en ny händelsehubben med önskat antal partitioner i namn området för Basic eller standard-nivån. Partitions utskalning stöds för [dedikerade kluster](event-hubs-dedicated-overview.md). |
| BadRequest | 40000 | Värdet # för MessageRetentionInDays är inte giltigt för Basic-nivån. värdet får inte överstiga 1 dag (ar). | Basic-nivån Event Hubs-namnområden har endast stöd för meddelande kvarhållning på upp till 1 dag. | Om fler än en dag för kvarhållning av meddelanden önskas, [skapar du ett standard namn område för Event Hubs](event-hubs-create.md). | 
| BadRequest | inget | Det angivna namnet är inte tillgängligt. | Namn rymds namn måste vara unika och det angivna namnet är redan upptaget. | Om du är ägare till det befintliga namn området med det angivna namnet kan du ta bort det, vilket leder till data förlust. Försök sedan igen med samma namn. Om namn området inte är säkert att ta bort (eller om du inte är ägare) väljer du ett annat namn på namn området. |
| BadRequest | inget | Den angivna prenumerationen har nått sin kvot för namn områden. | Din prenumeration har nått [kvoten](event-hubs-quotas.md) för antalet namn områden som den kan innehålla. | Överväg att ta bort oanvända namn rymder i den här prenumerationen, skapa en annan prenumeration eller uppgradera till ett [dedikerat kluster](event-hubs-dedicated-overview.md). |
| BadRequest | inget | Det går inte att uppdatera ett namn område som är sekundärt | Det går inte att uppdatera namn området eftersom det är ett sekundärt namn område i en [följande geodr-koppling](event-hubs-geo-dr.md). | Om det behövs gör du ändringen i det primära namn området i denna koppling i stället. Ta annars bort följande geodr-ihopparningen för att göra ändringen. |
| BadRequest | inget | Det går inte att ställa in automatisk ökning i Basic SKU | Det går inte att aktivera automatisk ökning på Basic-nivå Event Hubs namn områden. | Om du vill [Aktivera automatisk](event-hubs-auto-inflate.md) ökning i ett namn område kontrollerar du att det är av standard nivån. |
| BadRequest | inget | Det finns inte tillräckligt med kapacitet för att skapa namn området. Kontakta Event Hubs administratören. | Den valda regionen har en kapacitet och fler namn områden kan inte skapas. | Välj en annan region för att skriva ditt namn område. |
| BadRequest | inget | Det går inte att utföra åtgärden på entitetstypen ConsumerGroup eftersom namn områdets namn områdes namn använder Basic-nivån.  | Basic-nivå Event Hubs namn områden har en kvot (Event-Hub-quotas.md) för en konsument grupp (standard). Det finns inte stöd för att skapa fler konsument grupper. | Fortsätt att använda standard konsument gruppen ($Default), eller om det behövs, bör du överväga att använda ett standard-Event Hubs namn område i stället. | 
| BadRequest | inget | Namn områdets namn område finns inte. | Det gick inte att hitta det angivna namn området. | Kontrol lera att namn rymds namnet är rätt och att det finns i din prenumeration. Om den inte är det [skapar du ett Event Hubs-namnområde](event-hubs-create.md). | 
| BadRequest | inget | Resursens plats egenskap matchar inte den innehåll ande namn rymden. | Det gick inte att skapa en Event Hub i en angiven region eftersom den inte matchar regionen i namn området. | Försök att skapa händelsehubben i samma region som namn området. | 

## <a name="error-code-internal-server-error"></a>Felkod: internt Server fel

| Felkod | Fel under kod | Felmeddelande | Beskrivning | Rekommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Internt Server fel | inget | Internt Server fel. | Ett internt fel uppstod i tjänsten Event Hubs. | Försök att utföra åtgärden igen. Kontakta supporten om åtgärden fortfarande fungerar. |