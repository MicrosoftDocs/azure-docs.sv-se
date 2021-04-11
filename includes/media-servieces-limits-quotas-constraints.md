---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 10/26/2020
ms.author: inhenkel
ms.openlocfilehash: 419d8d0cd20c13bffdd192060d81975ee94b47fe
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106126045"
---
> [!NOTE]
> För resurser som inte har åtgärd ATS öppnar du ett support ärende för att be om en ökning av kvoterna. Skapa inte ytterligare Azure Media Services konton i ett försök att få högre gränser.

### <a name="account-limits"></a>Konto gränser

| Resurs | Standardgräns |
| --- | --- |
| Media Services konton i en enda prenumeration | 100 (fast) |

### <a name="asset-limits"></a>Till gångs gränser

| Resurs | Standardgräns |
| --- | --- |
| Till gångar per Media Services konto | 1,000,000|

### <a name="storage-media-limits"></a>Lagrings gränser (medie)

| Resurs | Standardgräns |
| --- | --- |
| Filstorlek| I vissa fall finns det en gräns för maximal fil storlek som stöds för bearbetning i Media Services. <sup>81.1</sup> |
| Lagringskonton | 100<sup>(2)</sup> (fast) |

<sup>1</sup> den maximala storleken som stöds för en enskild BLOB är för närvarande upp till 5 TB i Azure Blob Storage. Ytterligare gränser gäller i Media Services baserat på de VM-storlekar som används av tjänsten. Storleks gränsen gäller för de filer som du överför och även de filer som genereras på grund av Media Services bearbetning (kodning eller analys). Om käll filen är större än 260 GB kommer jobbet sannolikt att Miss förväntas.

I följande tabell visas gränserna för de medie reserverade enheterna S1, S2 och S3. Om käll filen är större än de gränser som anges i tabellen, Miss lyckas kodnings jobbet. Om du kodar 4K-resolutioner med lång varaktighet måste du använda S3-medie reserverade enheter för att uppnå de prestanda som behövs. Öppna ett support ärende om du har ett 4K-innehåll som är större än 260 GB-gränsen på S3-enheternas reserverade enheter.

|Enhets typ för reserverat medium|Maximal ingångs storlek (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> lagrings kontona måste vara från samma Azure-prenumeration.

### <a name="jobs-encoding--analyzing-limits"></a>Jobb (kodning & analys) gränser

| Resurs | Standardgräns |
| --- | --- |
| Jobb per Media Services konto | 500 000 <sup>(3)</sup> (fast)|
| Jobb indata per jobb | 50 (fast)|
| Jobbets utdata per jobb | 20 (fast) |
| Transformeringar per Media Services konto | 100 (fast)|
| Transformera utdata i en transformering | 20 (fast) |
| Filer per jobb inmatat|10 (fast)|

<sup>3</sup> det här talet omfattar köade, avslutade, aktiva och avbrutna jobb. Den inkluderar inte borttagna jobb. 

Eventuella jobb poster i ditt konto som är äldre än 90 dagar tas bort automatiskt, även om det totala antalet poster unders tiger den maximala kvoten. 

### <a name="live-streaming-limits"></a>Begränsningar för direkt uppspelning

| Resurs | Standardgräns |
| --- | --- |
| Live-händelser <sup>(4)</sup> per Media Services konto |5|
| Live-utdata per Live-händelse |3 <sup>(5)</sup> |
| Maximal varaktighet för Live-utdata | [Storlek på DVR-fönstret](../articles/media-services/latest/live-event-cloud-dvr-time-how-to.md) |

<sup>4</sup> mer detaljerad information om begränsningar för Live-händelser finns i [jämförelse och begränsningar för live event types](../articles/media-services/latest/live-event-types-comparison-reference.md).

<sup>5</sup> Live-utdata börjar skapas och stoppas när de tas bort.

### <a name="packaging--delivery-limits"></a>Leverans gränser för paketering &

| Resurs | Standardgräns |
| --- | --- |
| Slut punkter för direkt uppspelning (stoppad eller igång) per Media Services konto| 2 |
| Filter för dynamiskt manifest|100|
| Principer för direktuppspelning | 100 <sup>(6)</sup> |
| Unika strömmande positionerare som är associerade med en till gång vid ett tillfälle | 100<sup>(7)</sup> (fast) |

<sup>6</sup> när du använder en anpassad [strömnings princip](/rest/api/media/streamingpolicies)bör du utforma en begränsad uppsättning sådana principer för ditt Media Service-konto och sedan använda dem igen för din StreamingLocators när samma krypterings alternativ och protokoll behövs. Du bör inte skapa en ny strömningsprincip för varje positionerare för direktuppspelning.

<sup>7</sup> strömmande positionerare är inte utformade för att hantera åtkomst kontroll per användare. Om du vill ge olika åtkomsträttigheter till enskilda användare kan du använda DRM-lösningar (Digital Rights Management).

### <a name="protection-limits"></a>Skydds gränser

| Resurs | Standardgräns |
| --- | --- |
| Alternativ per innehålls nyckel princip | 30 |
| Licenser per månad för varje DRM-typ på Media Services Key Delivery Service per konto|1,000,000|

### <a name="support-ticket"></a>Support ärende

För resurser som inte har åtgärd ATS kan du be att kvoterna höjs, genom att öppna ett [support ärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Ta med detaljerad information i begäran om önskade kvot ändringar, användnings Falls scenarier och regioner som krävs. <br/>Försök **inte** få högre gränser genom att skapa ytterligare Azure Media Services-konton.