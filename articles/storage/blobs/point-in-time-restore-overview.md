---
title: Återställning av tidpunkter för block-blobar
titleSuffix: Azure Storage
description: Återställning av tidpunkter för block-blobar ger skydd mot oavsiktlig borttagning eller skada genom att göra det möjligt att återställa ett lagrings konto till dess tidigare tillstånd vid en viss tidpunkt.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b959038753dd15282de357da746ef9b0e0cf2be5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802275"
---
# <a name="point-in-time-restore-for-block-blobs"></a>Återställning av tidpunkter för block-blobar

Återställning vid tidpunkt ger skydd mot oavsiktlig borttagning eller skada genom att göra det möjligt att återställa block BLOB-data till ett tidigare tillstånd. Återställning på plats-till-tid är användbart i scenarier där en användare eller ett program oavsiktligt tar bort data eller där ett program fel skadar data. Vid tidpunkts återställning aktive ras även testnings scenarier som kräver att en data uppsättning återställs till ett känt tillstånd innan ytterligare tester körs.

Återställning av punkt-till-tid stöds endast för generella v2-lagrings konton i standard prestanda nivån. Endast data i nivåerna frekvent och låg frekvent åtkomst kan återställas med återställning vid tidpunkter.

Information om hur du aktiverar återställning av tidpunkter för ett lagrings konto finns i [utföra en tidpunkts återställning på block BLOB-data](point-in-time-restore-manage.md).

## <a name="how-point-in-time-restore-works"></a>Så här fungerar återställning av tidpunkt

Om du vill aktivera återställning vid tidpunkter skapar du en hanterings princip för lagrings kontot och anger en kvarhållningsperiod. Under kvarhållningsperioden kan du återställa block-blobbar från det aktuella läget till ett tillstånd vid en tidigare tidpunkt.

Om du vill initiera en tidpunkts återställning anropar du åtgärden [Återställ BLOB-intervall](/rest/api/storagerp/storageaccounts/restoreblobranges) och anger en återställnings punkt i UTC-tid. Du kan ange lexicographical-intervall för behållare och blob-namn som ska återställas, eller utelämna intervallet för att återställa alla behållare i lagrings kontot. Upp till 10 lexicographical-intervall stöds per återställnings åtgärd.

Azure Storage analyserar alla ändringar som har gjorts i de angivna Blobbarna mellan den begärda återställnings punkten, anges i UTC-tid och för tillfället. Återställnings åtgärden är Atomic, så den slutförs fullständigt i återställningen av alla ändringar, eller så Miss lyckas den. Om det finns blobbar som inte kan återställas, Miss lyckas åtgärden och Läs-och skriv åtgärder till de berörda behållarna återupptas.

Följande diagram visar hur tidpunkts återställning fungerar. En eller flera behållare eller BLOB-intervall återställs till sitt tillstånd för *n* dagar sedan, där *n* är mindre än eller lika med kvarhållningsperioden som definierats för återställning vid tidpunkter. Resultatet är att återställa Skriv-och borttagnings åtgärder som har skett under kvarhållningsperioden.

:::image type="content" source="media/point-in-time-restore-overview/point-in-time-restore-diagram.png" alt-text="Diagram som visar hur punkt-i-tid återställer behållare till ett tidigare tillstånd":::

Endast en återställnings åtgärd kan köras på ett lagrings konto i taget. En återställnings åtgärd kan inte avbrytas när den pågår, men en andra återställnings åtgärd kan utföras för att ångra den första åtgärden.

Åtgärden **Återställ BLOB-intervall** returnerar ett återställnings-ID som unikt identifierar åtgärden. Om du vill kontrol lera statusen för en tidpunkts återställning anropar du åtgärden **Hämta återställnings status** med det återställnings-ID som returnerades från åtgärden **Återställ BLOB-intervall** .

> [!IMPORTANT]
> När du utför en återställnings åtgärd, Azure Storage blockera data åtgärder på Blobbarna i de intervall som återställs under drift tiden. Läs-, skriv-och borttagnings åtgärder blockeras på den primära platsen. Därför kan åtgärder som att Visa behållare i Azure Portal inte utföras som förväntat medan återställnings åtgärden pågår.
>
> Läs åtgärder från den sekundära platsen kan fortsätta under återställnings åtgärden om lagrings kontot är geo-replikerat.

> [!CAUTION]
> Återställning vid tidpunkt stöder återställning mot åtgärder som endast har åtgärd ATS i block-blobbar. Alla åtgärder som har åtgärd ATS i behållare kan inte återställas. Om du till exempel tar bort en behållare från lagrings kontot genom att anropa åtgärden [ta bort behållare](/rest/api/storageservices/delete-container) , kan den behållaren inte återställas med en återställnings åtgärd vid tidpunkten. Ta bort enskilda blobbar i stället för att ta bort en hel behållare om du vill återställa dem senare.

### <a name="prerequisites-for-point-in-time-restore"></a>Krav för återställning av punkt-till-tid

Vid återställning från tidpunkt krävs att följande Azure Storage funktioner aktive ras innan du kan aktivera återställning vid tidpunkter:

- [Mjuk borttagning](soft-delete-blob-overview.md)
- [Ändra feed](storage-blob-change-feed.md)
- [BLOB-versioner](versioning-overview.md)

Om du aktiverar dessa funktioner kan ytterligare kostnader uppstå. Se till att du förstår fakturerings konsekvenserna innan du aktiverar återställning av punkt-till-tid och de nödvändiga funktionerna.

### <a name="retention-period-for-point-in-time-restore"></a>Kvarhållningsperiod för återställning av tidpunkt

När du aktiverar tidpunkts återställning för ett lagrings konto anger du en kvarhållningsperiod. Block-blobbar i ditt lagrings konto kan återställas under kvarhållningsperioden.

Kvarhållningsperioden börjar några minuter efter att du har aktiverat återställning av tidpunkter. Tänk på att du inte kan återställa blobbar till ett tillstånd innan kvarhållningsperioden börjar. Om du till exempel har aktiverat återställning av tidpunkt på plats den 1 maj med en kvarhållning på 30 dagar kan du sedan återställa till högst 15 dagar. Den 1 juni kan du återställa data från mellan 1 och 30 dagar.

Kvarhållningsperioden för återställning av plats-i-tid måste vara minst en dag under den angivna kvarhållningsperioden för mjuk borttagning. Om till exempel den mjuka borttagnings perioden har angetts till 7 dagar kan den kvarhållna tiden för återställning av återställnings perioden vara mellan 1 och 6 dagar.

> [!IMPORTANT]
> Den tid det tar att återställa en data uppsättning baseras på antalet skriv-och borttagnings åtgärder som gjorts under återställnings perioden. Till exempel kräver ett konto med 1 000 000 objekt med 3 000 objekt som lagts till per dag och 1 000 objekt som tas bort per dag cirka två timmar för att återställa till en punkt 30 dagar tidigare. En kvarhållningsperiod och återställningen över 90 dagar tidigare skulle inte rekommenderas för ett konto med den här ändrings takten.

### <a name="permissions-for-point-in-time-restore"></a>Behörigheter för återställning till tidpunkt

För att initiera en återställnings åtgärd måste klienten ha Skriv behörighet till alla behållare i lagrings kontot. Om du vill bevilja behörighet att auktorisera en återställnings åtgärd med Azure Active Directory (Azure AD) tilldelar du rollen **lagrings konto deltagare** rollen som säkerhets objekt på lagrings kontots, resurs gruppens eller prenumerationens nivå.

## <a name="limitations-and-known-issues"></a>Begränsningar och kända problem

Återställning av tidpunkt för block-blobar har följande begränsningar och kända problem:

- Endast block-blobar i ett standard lagrings konto för allmän användning v2 kan återställas som en del av en återställnings åtgärd vid tidpunkten. Det går inte att lägga till blobar, Page blob-och Premium block-blobar. 
- Om du har tagit bort en behållare under kvarhållningsperioden, återställs inte den behållaren med återställnings åtgärden vid tidpunkten. Om du försöker återställa ett intervall med blobbar som innehåller blobbar i en borttagen behållare, Miss lyckas återställnings åtgärden vid tidpunkten. Om du vill veta mer om att skydda behållare från borttagning, se [mjuk borttagning för behållare (för hands version)](soft-delete-container-overview.md).
- Om en BLOB har flyttats mellan frekventa och låg frekventa nivåer under perioden mellan den aktuella tidpunkten och återställnings punkten, återställs blobben till den tidigare nivån. Det finns inte stöd för att återställa block-blobar på Arkiv nivå. Om till exempel en blog på frekvent nivå flyttades till en arkivnivå för två dagar sedan, och en återställningsåtgärd återställer till en punkt för tre dagar sedan, återställs inte bloben till frekvent nivå. För att återställa en arkiverad BLOB, flytta först bort den från Arkiv lag rings nivån. Mer information finns i [rehydratisera BLOB-data från Arkiv](storage-blob-rehydration.md)lag rings nivån.
- Ett block som har överförts via funktionen för att [blockera eller](/rest/api/storageservices/put-block) [blockera från URL](/rest/api/storageservices/put-block-from-url), men som inte har allokerats via [list block](/rest/api/storageservices/put-block-list), är inte en del av en blob och återställs därför inte som en del av en återställnings åtgärd.
- Det går inte att återställa en blob med ett aktivt lån. Om en blob med ett aktivt lån ingår i det intervall med blobbar som ska återställas, Miss Miss kan återställnings åtgärden samköras. Bryt eventuella aktiva lån innan du påbörjar återställnings åtgärden.
- Ögonblicks bilder skapas eller tas inte bort som en del av en återställnings åtgärd. Endast bas-bloben återställs till sitt tidigare tillstånd.
- Det finns inte stöd för att återställa Azure Data Lake Storage Gen2 enkla och hierarkiska namn områden.

> [!IMPORTANT]
> Om du återställer block blobbar till en punkt som är tidigare än den 22 september 2020 kommer för hands versions begränsningar för återställning av punkt-i-tid att tillämpas. Microsoft rekommenderar att du väljer en återställnings punkt som är lika med eller senare än den 22 september 2020 för att dra nytta av den allmänt tillgängliga funktionen för återställning av en viss tidpunkt.

## <a name="pricing-and-billing"></a>Priser och fakturering

Det kostar inget att aktivera återställning av tidpunkter. Men om du aktiverar återställning av punkt-i-tid aktive ras även BLOB-versioner, mjuk borttagning och ändrings flöden som kan resultera i ytterligare kostnader.

Fakturering för tidpunkts återställning beror på mängden data som bearbetas för att utföra återställnings åtgärden. Mängden data som bearbetas baseras på antalet ändringar som inträffat mellan återställnings punkten och aktuell tidpunkt. Om du till exempel antar en relativt konstant ändrings takt för att blockera BLOB-data i ett lagrings konto skulle en återställnings åtgärd som går tillbaka i tid 1 dag kosta 1/10 av en återställning som går tillbaka om 10 dagar.

Om du vill uppskatta kostnaden för en återställnings åtgärd granskar du loggen för ändrings flöden för att beräkna den mängd data som ändrades under återställnings perioden. Om till exempel kvarhållningsperioden för ändrings flöde är 30 dagar och storleken på ändrings flödet är 10 MB, så kostar det ett tredje tredjedel av priset som anges för ett LRS-konto i den regionen. Att återställa till en punkt som är 27 dagar tidigare skulle kosta cirka nio tiondelar av priset i listan.

Mer information om priser för återställning av tidpunkter finns i [blocking BLOB-prissättning](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="next-steps"></a>Nästa steg

- [Utföra en tidpunkts återställning på block BLOB-data](point-in-time-restore-manage.md)
- [Ändra stöd för feed i Azure Blob Storage](storage-blob-change-feed.md)
- [Aktivera mjuk borttagning för blobar](./soft-delete-blob-enable.md)
- [Aktivera och hantera BLOB-versioner](versioning-enable.md)
