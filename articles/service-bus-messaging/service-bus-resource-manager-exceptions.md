---
title: Azure Service Bus Resource Manager-undantag | Microsoft Docs
description: Lista över Service Bus undantag som har inAzure Resource Manager och föreslagna åtgärder.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a0b0338da0f002c7b667748ffd2bf5a40c91c580
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85336968"
---
# <a name="service-bus-resource-manager-exceptions"></a>Undantag för Service Bus Resource Manager

Den här artikeln innehåller undantag som genereras när du interagerar med Azure Service Bus som använder Azure Resource Manager via mallar eller direkta anrop.

> [!IMPORTANT]
> Det här dokumentet uppdateras ofta. Kom tillbaka efter uppdateringar.

Nedan visas de olika undantag/fel som finns på Azure Resource Manager.

## <a name="error-bad-request"></a>Fel: felaktig begäran

"Felaktig begäran" innebär att begäran som mottagits av resurs hanteraren misslyckades med verifieringen.

| Felkod | Fel under kod | Felmeddelande | Beskrivning | Rekommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Felaktig begäran | 40000 | Subcode = 40000. Egenskapens *egenskaps namn* kan inte anges när du skapar en kö eftersom namn områdets namn områdes *namn* använder Basic-nivån. Den här åtgärden stöds endast i nivån standard eller Premium. | På Azure Service Bus Basic-nivån kan inte nedanstående egenskaper anges eller uppdateras – <ul> <li> RequiresDuplicateDetection </li> <li> AutoDeleteOnIdle </li> <li>RequiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> EnableExpress </li> <li> ForwardTo </li> <li> Ämnen </li> </ul> | Överväg att uppgradera från nivån Basic till standard eller Premium för att använda den här funktionen. |
| Felaktig begäran | 40000 | Subcode = 40000. Det går inte att ändra värdet för egenskapen "requiresDuplicateDetection" för en befintlig kö (eller ämne). | Dubblettidentifiering måste aktive ras/inaktive ras när entiteten skapas. Det går inte att ändra konfigurations parametern för dubblettidentifiering när den har skapats. | Om du vill aktivera dubblettidentifiering i en tidigare skapad kö/ett ämne kan du skapa en ny kö/ett ämne med dubblettidentifiering och sedan vidarebefordra från den ursprungliga kön till den nya kön/det nya avsnittet. |
| Felaktig begäran | 40000 | Subcode = 40000. Det angivna värdet 16384 är ogiltigt. Egenskapen MaxSizeInMegabytes måste vara något av följande värden: 1024; 2048; 3072; 4096; 5 120. | MaxSizeInMegabytes-värdet är ogiltigt. | Se till att MaxSizeInMegabytes är en av följande: 1024, 2048, 3072, 4096, 5120. |
| Felaktig begäran | 40000 | Subcode = 40000. Det går inte att ändra partitionering för kö/ämne. | Det går inte att ändra partitionering för entiteten. | Skapa en ny entitet (kö eller ämne) och aktivera partitioner. | 
| Felaktig begäran | inget | Namn *områdets namn område* finns inte. | Namn området finns inte i din Azure-prenumeration. | Försök att lösa det här felet <ul> <li> Se till att Azure-prenumerationen är korrekt. </li> <li> Se till att namn området finns. </li> <li> Kontrol lera att namn områdets namn är korrekt (inga stavfel eller null-strängar). </li> </ul> | 
| Felaktig begäran | 40400 | Subcode = 40400. Målentiteten för automatisk vidarebefordran finns inte. | Mål enheten för den vidarebefordrande målentiteten finns inte. | Målentiteten (kö eller ämne) måste finnas innan källan skapas. Försök igen när du har skapat målentiteten. |
| Felaktig begäran | 40000 | Subcode = 40000. Den angivna lås tiden överskrider den tillåtna max längden på 5 minuter. | Den tid som ett meddelande kan låsas upp måste vara mellan 1 minut (minst) och 5 minuter (max). | Se till att den angivna lås tiden är mellan 1 min och 5 minuter. |
| Felaktig begäran | 40000 | Subcode = 40000. Det går inte att aktivera både DelayedPersistence och RequiresDuplicateDetection-egenskapen tillsammans. | Entiteter med dubblettidentifiering aktiverat på dem måste vara permanenta, så persistence kan inte fördröjas. | Läs mer om [dubblettidentifiering](duplicate-detection.md) |
| Felaktig begäran | 40000 | Subcode = 40000. Det går inte att ändra värdet för egenskapen RequiresSession för en befintlig kö. | Stöd för sessioner måste vara aktiverat när entiteten skapas. När du har skapat kan du inte aktivera/inaktivera sessioner på en befintlig entitet (kö eller prenumeration) | Ta bort och återskapa en ny kö (eller prenumeration) med egenskapen "RequiresSession" aktive rad. |
| Felaktig begäran | 40000 | Subcode = 40000. URI_PATH innehåller tecknen som inte tillåts av Service Bus. Enhets segment får bara innehålla bokstäver, siffror, punkter (.), bindestreck (-) och under streck (_). | Enhets segment får bara innehålla bokstäver, siffror, punkter (.), bindestreck (-) och under streck (_). Andra tecken gör att begäran inte kan utföras. | Se till att det inte finns några ogiltiga tecken i URI-sökvägen. |


## <a name="error-code-429"></a>Felkod: 429

Precis som i HTTP visar "Felkod 429" för många begär Anden. Det innebär att den angivna resursen (namnrymd) begränsas på grund av för många begär Anden (eller på grund av motstridiga åtgärder) på den resursen.

| Felkod | Fel under kod | Felmeddelande | Beskrivning | Rekommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | Subcode = 50004. Begäran avbröts eftersom namn området *där namn området* begränsas. | Det här fel tillståndet inträffar när antalet inkommande begär Anden överskrider resurs begränsningen. | Vänta några sekunder och försök igen. <br/> <br/> Läs mer om [kvoter](service-bus-quotas.md) och [begränsningar för Azure Resource Manager begär Anden](../azure-resource-manager/management/request-limits-and-throttling.md)|
| 429 | 40901 | Subcode = 40901. En annan motstridig åtgärd pågår. | En annan motstridig åtgärd pågår på samma resurs/entitet | Vänta tills den pågående pågående åtgärden har slutförts innan du försöker igen. |
| 429 | 40900 | Subcode = 40900. Uppstod. Du begär en åtgärd som inte är tillåten i resursens aktuella tillstånd. | Detta tillstånd kan uppstå när flera begär Anden görs för att utföra åtgärderna på samma entitet (kö, ämne, prenumeration eller regel) på samma gång. | Vänta några sekunder och försök igen |
| 429 | 40901 | Begäran om enhets *enhets namn* står i konflikt med en annan begäran | En annan motstridig åtgärd pågår på samma resurs/entitet | Vänta tills den föregående åtgärden har slutförts innan du försöker igen |
| 429 | 40901 | En annan uppdateringsbegäran pågår för entitetens *enhets namn*. | En annan motstridig åtgärd pågår på samma resurs/entitet | Vänta tills den föregående åtgärden har slutförts innan du försöker igen |
| 429 | inget | Resurs konflikt uppstod. En annan motstridig åtgärd kanske pågår. Om det här är ett nytt försök för den misslyckade åtgärden, väntar det fortfarande på att rensa i bakgrunden. Försök igen senare. | Detta tillstånd kan uppstå när det finns en väntande åtgärd mot samma entitet. | Vänta tills den föregående åtgärden har slutförts innan du försöker igen. |


## <a name="error-code-not-found"></a>Felkod: hittades inte

Den här fel klassen anger att resursen inte hittades.

| Felkod | Fel under kod | Felmeddelande | Beskrivning | Rekommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Hittades inte | inget | Det gick inte att hitta entitetens *enhets namn* . | Den entitet som åtgärden inte hittades mot. | Kontrol lera att entiteten finns och försök igen. |
| Hittades inte | inget | Hittades inte. Åtgärden finns inte. | Åtgärden som du försöker utföra finns inte. | Kontrol lera åtgärden och försök igen. |
| Hittades inte | inget | Den inkommande begäran identifieras inte som en begäran om att lagra en namn områdes princip. | Den inkommande begär ande texten är null och kan därför inte köras som en begäran. | Kontrol lera begär ande texten och se till att den inte är null. | 
| Hittades inte | inget | Det gick inte att hitta *enhets namnet* för meddelande enheten. | Det gick inte att hitta den entitet som du försöker köra åtgärden mot. | Kontrol lera om entiteten finns och försök sedan igen. |

## <a name="error-code-internal-server-error"></a>Felkod: internt Server fel

Den här fel klassen anger att det uppstod ett internt Server fel

| Felkod | Fel under kod | Felmeddelande | Beskrivning | Rekommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Internt Server fel | 50000 | Under kod = 50000. Internt Server fel| Kan bero på olika orsaker. Några av symptomen är- <ul> <li> Klientbegäran/bröd texten är skadad och leder till ett fel. </li> <li> Klientbegäran nådde tids gränsen på grund av bearbetnings problem på tjänsten. </li> </ul> | Så här löser du detta <ul> <li> Se till att parametrarna för begär Anden inte är null eller felaktiga. </li> <li> Gör om begäran. </li> </ul> |

## <a name="error-code-unauthorized"></a>Felkod: obehörig

Den här fel klassen indikerar frånvaron av auktorisering för att köra kommandot.

| Felkod | Fel under kod | Felmeddelande | Beskrivning | Rekommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Behörighet saknas | inget | Ogiltig åtgärd för sekundärt namn område. Det sekundära namn området är skrivskyddat. | Åtgärden utfördes mot det sekundära namn området, som installeras som en skrivskyddad namnrymd. | Gör om kommandot mot det primära namn området. Läs mer om [sekundär namnrymd](service-bus-geo-dr.md) |
| Behörighet saknas | inget | MissingToken: det gick inte att hitta Authorization-huvudet. | Felet uppstår när auktoriseringen har null eller felaktiga värden. | Se till att det token-värde som anges i Authorization-huvudet är korrekt och inte null. |