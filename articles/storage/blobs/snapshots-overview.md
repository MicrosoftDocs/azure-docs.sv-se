---
title: BLOB-ögonblicksbilder
titleSuffix: Azure Storage
description: Förstå hur BLOB-ögonblicksbilder fungerar och hur de faktureras.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/02/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: debce0a1b4c09bb89cdceb1cd29e59e1976c939a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99539168"
---
# <a name="blob-snapshots"></a>BLOB-ögonblicksbilder

En ögonblicks bild är en skrivskyddad version av en blob som tas vid en tidpunkt.

> [!NOTE]
> BLOB-versioner erbjuder ett överlägset sätt att underhålla tidigare versioner av en blob. Mer information finns i [BLOB-versioner](versioning-overview.md).

## <a name="about-blob-snapshots"></a>Om BLOB-ögonblicksbilder

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

En ögonblicks bild av en BLOB är identisk med dess bas-BLOB, förutom att BLOB-URI: n har ett **datetime** -värde som läggs till i BLOB-URI: n för att ange tiden då ögonblicks bilden togs. Om till exempel en URI-URI för en sida är `http://storagesample.core.blob.windows.net/mydrives/myvhd` , liknar ögonblicks bildens URI `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z` .

> [!NOTE]
> Alla ögonblicks bilder delar bas-blobens URI. Den enda skillnaden mellan bas-bloben och ögonblicks bilden är det tillagda **datetime** -värdet.

En BLOB kan ha valfritt antal ögonblicks bilder. Ögonblicks bilder behålls tills de tas bort uttryckligen, antingen oberoende eller som en del av en [Delete-BLOB](/rest/api/storageservices/delete-blob) -åtgärd för bas-bloben. Du kan räkna upp ögonblicks bilderna som är kopplade till bas-bloben för att spåra dina befintliga ögonblicks bilder.

När du skapar en ögonblicks bild av en BLOB kopieras blobens system egenskaper till ögonblicks bilden med samma värden. Bas-blobens metadata kopieras också till ögonblicks bilden, om du inte anger separata metadata för ögonblicks bilden när du skapar den. När du har skapat en ögonblicks bild kan du läsa, kopiera eller ta bort den, men du kan inte ändra den.

Alla lån som är associerade med bas-bloben påverkar inte ögonblicks bilden. Du kan inte förvärva ett lån för en ögonblicks bild.

En VHD-fil används för att lagra aktuell information och status för en VM-disk. Du kan koppla från en disk inifrån den virtuella datorn eller stänga av den virtuella datorn och sedan ta en ögonblicks bild av sin VHD-fil. Du kan använda den ögonblicks bild filen senare för att hämta VHD-filen vid den tidpunkten och återskapa den virtuella datorn.

## <a name="understand-how-snapshots-accrue-charges"></a>Förstå hur ögonblicks bilder debiteras

### <a name="important-billing-considerations"></a>Viktiga fakturerings överväganden

I följande lista finns viktiga punkter att tänka på när du skapar en ögonblicks bild.

- Ditt lagrings konto debiteras för unika block eller sidor, oavsett om de finns i blobben eller i ögonblicks bilden. Ditt konto debiterar inte ytterligare avgifter för ögonblicks bilder som är kopplade till en BLOB förrän du uppdaterar den blob som de baseras på. När du har uppdaterat bas-bloben avviker den från ögonblicks bilderna. När detta inträffar debiteras du för unika block eller sidor i varje BLOB eller ögonblicks bild.
- När du ersätter ett block i en Block-Blob debiteras det här blocket som ett unikt block. Detta gäller även om blocket har samma block-ID och samma data som i ögonblicks bilden. När blocket har allokerats igen skiljer det sig från dess motsvarighet i alla ögonblicks bilder och du debiteras för data. Samma undantag gäller för en sida i en sid-blob som uppdateras med identiska data.
- Att uppdatera en block-BLOB genom att anropa en metod som skriver över hela innehållet i blobben ersätter alla block i blobben. Om du har en ögonblicks bild som är associerad med denna BLOB så kommer alla block i bas-blob och ögonblicks bilder att debiteras för alla block i båda blobarna. Detta gäller även om data i bas-bloben och ögonblicks bilden är identiska.
- Azure-Blob Service har inget sätt att avgöra om två block innehåller identiska data. Varje block som överförs och bekräftas behandlas som unikt, även om det har samma data och samma block-ID. Eftersom avgifterna är för unika block, är det viktigt att överväga att uppdatera en blob som har en ögonblicks bild som resulterar i ytterligare unika block och ytterligare kostnader.

### <a name="minimize-costs-with-snapshot-management"></a>Minimera kostnaderna med ögonblicks bild hantering

Vi rekommenderar att du hanterar ögonblicks bilderna noggrant för att undvika extra kostnader. Du kan följa dessa metod tips för att minimera kostnaderna för lagringen av dina ögonblicks bilder:

- Ta bort och återskapa ögonblicks bilder som är associerade med en BLOB varje gång du uppdaterar blobben, även om du uppdaterar med identiska data, om inte program designen kräver att du underhåller ögonblicks bilder. Genom att ta bort och återskapa blobens ögonblicks bilder kan du se till att bloben och ögonblicks bilderna inte avviker.
- Om du behåller ögonblicks bilder för en BLOB bör du undvika att anropa metoder som skriver över hela blobben när du uppdaterar blobben. Uppdatera i stället minsta möjliga antal block för att hålla kostnaderna nere.

### <a name="snapshot-billing-scenarios"></a>Fakturerings scenarier för ögonblicks bilder

Följande scenarier visar hur avgifterna påförs för en Block-Blob och dess ögonblicks bilder.

## <a name="pricing-and-billing"></a>Priser och fakturering

När du skapar en ögonblicks bild, som är en skrivskyddad kopia av en BLOB, kan det leda till ytterligare avgifter för data lagring till ditt konto. När du designar ditt program är det viktigt att vara medveten om hur dessa avgifter kan påföras så att du kan minimera kostnaderna.

BLOB-ögonblicksbilder, som BLOB-versioner, faktureras enligt samma taxa som aktiva data. Hur ögonblicks bilder faktureras beror på om du uttryckligen har angett nivån för bas-bloben eller för någon av dess ögonblicks bilder (eller versioner). Mer information om BLOB-nivåer finns i [Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv](storage-blob-storage-tiers.md)lag rings nivåer.

Om du inte har ändrat en BLOB eller ögonblicks bilds nivå debiteras du för unika data block över denna BLOB, dess ögonblicks bilder och eventuella versioner den kan ha. Mer information finns i [fakturering när BLOB-nivån inte har angetts explicit](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Om du har ändrat en BLOB eller ögonblicks bilds nivå debiteras du för hela objektet, oavsett om blobben och ögonblicks bilden slutligen är på samma nivå igen. Mer information finns i [fakturera när BLOB-nivån uttryckligen har ställts in](#billing-when-the-blob-tier-has-been-explicitly-set).

Mer information om fakturerings information för BLOB-versioner finns i [BLOB](versioning-overview.md)-versioner.

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>Fakturera när BLOB-nivån inte har angetts explicit

Om du inte uttryckligen har angett BLOB-nivån för en bas-BLOB eller någon av dess ögonblicks bilder debiteras du för unika block eller sidor över bloben, dess ögonblicks bilder och de versioner den kan ha. Data som delas i en blob och dess ögonblicks bilder debiteras bara en gång. När en BLOB uppdateras avviker data i en bas-BLOB från de data som lagras i sina ögonblicks bilder och unika data debiteras per block eller sida.

När du ersätter ett block i en Block-Blob debiteras det här blocket som ett unikt block. Detta gäller även om blocket har samma block-ID och samma data som i ögonblicks bilden. När blocket har allokerats igen avviker det från dess motsvarighet i ögonblicks bilden och du debiteras för data. Samma undantag gäller för en sida i en sid-blob som uppdateras med identiska data.

Blob Storage har inget sätt att avgöra om två block innehåller identiska data. Varje block som överförs och bekräftas behandlas som unikt, även om det har samma data och samma block-ID. Eftersom avgifterna börjar med unika block, är det viktigt att du kommer ihåg att uppdatera en BLOB när denna BLOB har ögonblicks bilder eller versioner resulterar i ytterligare unika block och ytterligare kostnader.

När en BLOB har ögonblicks bilder, anropa uppdaterings åtgärder på block-blobbar så att de uppdaterar det minsta möjliga antalet block. Skriv åtgärder som tillåter detaljerad kontroll över [block är att blockera och blockera](/rest/api/storageservices/put-block) [listor](/rest/api/storageservices/put-block-list). Åtgärden för att [lägga till BLOB](/rest/api/storageservices/put-blob) ersätter å andra sidan hela innehållet i en blob och kan leda till ytterligare kostnader.

Följande scenarier visar hur avgifterna påförs för en Block-Blob och dess ögonblicks bilder när BLOB-nivån inte har angetts explicit.

#### <a name="scenario-1"></a>Scenario 1

I Scenario 1 har bas-bloben inte uppdaterats efter att ögonblicks bilden togs, så avgifterna debiteras bara för unika block 1, 2 och 3.

![Diagram 1 visar fakturering för unika block i bas-blob och ögonblicks bild.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scenario 2

I scenario 2 har bas-bloben uppdaterats, men ögonblicks bilden har inte det. Block 3 har uppdaterats och även om det innehåller samma data och samma ID är det inte samma som för Block 3 i ögonblicks bilden. Det innebär att kontot debiteras för fyra block.

![Diagram 2 visar fakturering för unika block i bas-blob och ögonblicks bild.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scenario 3

I scenario 3 har bas-bloben uppdaterats, men ögonblicks bilden har inte det. Block 3 har ersatts med block 4 i bas-blobben, men ögonblicks bilden visar fortfarande Block 3. Det innebär att kontot debiteras för fyra block.

![Diagram 3 visar fakturering för unika block i bas-blob och ögonblicks bild.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scenario 4

I Scenario 4 har bas-bloben uppdaterats helt och innehåller inget av de ursprungliga blocken. Kontot debiteras därför för alla åtta unika block.

![Diagram 4 visar fakturering för unika block i bas-blob och ögonblicks bild.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

> [!TIP]
> Undvik att anropa metoder som skriver över hela blobben och uppdatera i stället enskilda block för att hålla kostnaderna nere.

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>Fakturera när BLOB-nivån uttryckligen har angetts

Om du uttryckligen har angett BLOB-nivån för en BLOB eller ögonblicks bild (eller version) debiteras du för objektets fullständiga innehålls längd på den nya nivån, oavsett om den delar block med ett objekt på den ursprungliga nivån. Du debiteras också för den äldsta versionen av innehålls längden på den ursprungliga nivån. Alla versioner och ögonblicks bilder som finns kvar på den ursprungliga nivån debiteras för unika block som de kan dela, enligt beskrivningen i [fakturering när BLOB-nivån inte har angetts explicit](#billing-when-the-blob-tier-has-not-been-explicitly-set).

#### <a name="moving-a-blob-to-a-new-tier"></a>Flytta en blob till en ny nivå

I följande tabell beskrivs fakturerings beteendet för en BLOB eller ögonblicks bild när den flyttas till en ny nivå.

| När BLOB-nivån uttryckligen anges... | Sedan faktureras du för... |
|-|-|
| En bas-BLOB med en ögonblicks bild | Bas-bloben på den nya nivån och den äldsta ögonblicks bilden på den ursprungliga nivån samt eventuella unika block i andra ögonblicks bilder. <sup>1</sup> |
| En bas-BLOB med en tidigare version och en ögonblicks bild | Bas-bloben på den nya nivån, den äldsta versionen på den ursprungliga nivån och den äldsta ögonblicks bilden på den ursprungliga nivån samt eventuella unika block i andra versioner eller ögonblicks bilder<sup>1</sup>. |
| En ögonblicks bild | Ögonblicks bilden på den nya nivån och bas-bloben på den ursprungliga nivån samt eventuella unika block i andra ögonblicks bilder. <sup>1</sup> |

<sup>1</sup> Om det finns andra tidigare versioner eller ögonblicks bilder som inte har flyttats från ursprungs nivån debiteras dessa versioner eller ögonblicks bilder utifrån antalet unika block som de innehåller, enligt beskrivningen i [fakturering när BLOB-nivån inte har angetts explicit](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Följande diagram illustrerar hur objekt faktureras när en blob med ögonblicks bilder flyttas till en annan nivå.

:::image type="content" source="media/snapshots-overview/snapshot-billing-tiers.png" alt-text="Diagram över hur objekt faktureras när en blob med ögonblicks bilder tas i nivå.":::

Det går inte att göra en återställning av nivån för en BLOB, version eller ögonblicks bild. Om du flyttar en blob till en ny nivå och sedan flyttar tillbaka den till den ursprungliga nivån debiteras du för objektets fullständiga innehålls längd även om det delar block med andra objekt på den ursprungliga nivån.

Åtgärder som uttryckligen anger nivån för en BLOB, version eller ögonblicks bild är:

- [Ange blobnivå](/rest/api/storageservices/set-blob-tier)
- [Lägg till BLOB](/rest/api/storageservices/put-blob) med angiven nivå
- [Lägg till blockeringslistan](/rest/api/storageservices/put-block-list) med den angivna nivån
- [Kopiera BLOB](/rest/api/storageservices/copy-blob) med angiven nivå

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>Att ta bort en BLOB när mjuk borttagning har Aktiver ATS

Om du har aktiverat BLOB Soft Delete, och om du tar bort eller skriver över en bas-blob som har haft nivån explicit angiven, faktureras alla tidigare versioner eller ögonblicks bilder av den mjuka borttagna blobben med full innehålls längd. Mer information om hur BLOB-versioner och mjuk borttagning fungerar tillsammans finns i [BLOB-versioner och mjuk borttagning](versioning-overview.md#blob-versioning-and-soft-delete).

I följande tabell beskrivs fakturerings beteendet för en blob som är mjuk borttagning, beroende på om versions hantering har Aktiver ATS eller inaktiverats. När versions hantering har Aktiver ATS skapas en ny version när en BLOB är mjuk-borttagen. När versions hantering är inaktive rad skapar mjuk borttagning av en BLOB en ögonblicks bild av mjuk borttagning.

| När du skriver över en bas-BLOB med dess nivå explicit anges... | Sedan faktureras du för... |
|-|-|
| Om både BLOB-mjuk borttagning och versions hantering är aktiverade | Alla befintliga versioner med full innehålls längd oavsett nivå. |
| Om mjuk borttagning av BLOB är aktiverat men versions hantering är inaktive rad | Alla befintliga ögonblicks bilder av mjuk borttagning med full innehålls längd oavsett nivå. |

## <a name="next-steps"></a>Nästa steg

- [BLOB-versioner](versioning-overview.md)
- [Skapa och hantera en BLOB-ögonblicksbild i .NET](snapshots-manage-dotnet.md)
- [Säkerhetskopiera Azure ohanterade virtuella dator diskar med stegvisa ögonblicks bilder](../../virtual-machines/windows/incremental-snapshots.md)
