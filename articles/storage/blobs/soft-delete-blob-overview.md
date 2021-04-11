---
title: Mjuk borttagning för blobar
titleSuffix: Azure Storage
description: Mjuk borttagning för blobbar skyddar dina data så att du enkelt kan återställa dina data när de är felaktigt ändrade eller borttagna av ett program eller en annan lagrings konto användare.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: ef1ed584a609b2e4baa27111e47343df99146f5a
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257508"
---
# <a name="soft-delete-for-blobs"></a>Mjuk borttagning för blobar

BLOB-mjuk borttagning skyddar en enskild BLOB, ögonblicks bild eller version från oavsiktliga borttagningar eller överskrivning genom att underhålla borttagna data i systemet under en angiven tids period. Under kvarhållningsperioden kan du återställa ett mjukt borttaget objekt till dess tillstånd vid den tidpunkt då det togs bort. När kvarhållningsperioden har gått ut tas objektet bort permanent.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="recommended-data-protection-configuration"></a>Rekommenderad data skydds konfiguration

BLOB-mjuk borttagning är en del av en omfattande data skydds strategi för BLOB-data. För optimalt skydd för dina BLOB-data rekommenderar Microsoft att du aktiverar alla följande data skydds funktioner:

- Behållare för mjuk borttagning, för att återställa en behållare som har tagits bort. Information om hur du aktiverar mjuk borttagning av behållare finns i [Aktivera och hantera mjuk borttagning för behållare](soft-delete-container-enable.md).
- BLOB-versioner för att automatiskt underhålla tidigare versioner av en blob. När BLOB-versioner har Aktiver ATS kan du återställa en tidigare version av en BLOB för att återställa dina data om de felaktigt ändras eller tas bort. Information om hur du aktiverar BLOB-versioner finns i [Aktivera och hantera BLOB-versioner](versioning-enable.md).
- BLOB-mjuk borttagning, för att återställa en BLOB, ögonblicks bild eller version som har tagits bort. Information om hur du aktiverar mjuk borttagning av BLOB finns i [Aktivera och hantera mjuk borttagning för blobbar](soft-delete-blob-enable.md).

Mer information om Microsofts rekommendationer för data skydd finns i [Översikt över data skydd](data-protection-overview.md).

## <a name="how-blob-soft-delete-works"></a>Så här fungerar BLOB-mjuk borttagning

När du aktiverar BLOB-mjuk borttagning för ett lagrings konto anger du en kvarhållningsperiod för borttagna objekt på mellan 1 och 365 dagar. Kvarhållningsperioden anger hur länge data är tillgängliga när de tas bort eller skrivs över. Klockan börjar på kvarhållningsperioden så snart ett objekt tas bort eller skrivs över.

Även om kvarhållningsperioden är aktiv kan du återställa en borttagen BLOB, tillsammans med dess ögonblicks bilder eller en borttagen version genom att anropa åtgärden [ta bort BLOB](/rest/api/storageservices/undelete-blob) . Följande diagram visar hur ett borttaget objekt kan återställas när BLOB-mjuk borttagning är aktiverat:

:::image type="content" source="media/soft-delete-blob-overview/blob-soft-delete-diagram.png" alt-text="Diagram över hur en mjuk borttagen BLOB kan återställas":::

Du kan när som helst ändra lagrings perioden för mjuk borttagning. En uppdaterad kvarhållningsperiod gäller endast för data som har tagits bort efter att kvarhållningsperioden ändrades. Alla data som togs bort innan kvarhållningsperioden ändrades, omfattas av den kvarhållningsperiod som gällde när den togs bort.

Försök att ta bort ett mjukt borttaget objekt påverkar inte förfallo tiden.

Om du inaktiverar mjuk borttagning av BLOB kan du fortsätta att komma åt och återställa mjuka borttagna objekt i ditt lagrings konto tills perioden för mjuk borttagning har förflutit.

BLOB-versioner är tillgängligt för General-Purpose v2-, Block Blob-och Blob Storage-konton. Lagrings konton med hierarkiskt namn område som är aktiverade för användning med Azure Data Lake Storage Gen2 stöds inte för närvarande.

Version 2017-07-29 och högre av Azure Storage REST API stöd för BLOB-mjuk borttagning.

> [!IMPORTANT]
> Du kan bara använda BLOB-mjuk borttagning för att återställa en enskild BLOB, ögonblicks bild eller version. Om du vill återställa en behållare och dess innehåll måste behållaren mjuk borttagning också vara aktive rad för lagrings kontot. Microsoft rekommenderar att du aktiverar mjuk borttagning av behållare och blob-versioner tillsammans med BLOB-mjuk borttagning för att garantera fullständigt skydd för BLOB-data. Mer information finns i [Översikt över data skydd](data-protection-overview.md).
>
> BLOB-mjuk borttagning skyddar inte mot borttagning av ett lagrings konto. Om du vill skydda ett lagrings konto från borttagning konfigurerar du ett lås på lagrings konto resursen. Mer information om hur du låser ett lagrings konto finns i [tillämpa ett Azure Resource Manager lås till ett lagrings konto](../common/lock-account-resource.md).

### <a name="how-deletions-are-handled-when-soft-delete-is-enabled"></a>Hur borttagningar hanteras när mjuk borttagning är aktiverat

När BLOB Soft Delete är aktiverat tar du bort en BLOB-markering som är mjuk borttagen. Ingen ögonblicks bild skapas. När kvarhållningsperioden upphör tas den mjuk-borttagna blobben bort permanent.

Om en BLOB har ögonblicks bilder kan inte bloben tas bort om inte ögonblicks bilderna också tas bort. När du tar bort en blob och dess ögonblicks bilder markeras både blob och ögonblicks bilder som mjuk borttagning. Inga nya ögonblicks bilder skapas.

Du kan också ta bort en eller flera aktiva ögonblicks bilder utan att ta bort bas-bloben. I det här fallet är ögonblicks bilden mjuk – borttagen.

Mjuka, borttagna objekt är osynliga om de inte uttryckligen visas eller visas. Mer information om hur du visar mjuka borttagna objekt finns i [Hantera och återställa mjuka borttagna blobbar](soft-delete-blob-manage.md).

### <a name="how-overwrites-are-handled-when-soft-delete-is-enabled"></a>Hur överskrivning hanteras när mjuk borttagning är aktiverat

Att anropa en åtgärd, till exempel att använda [BLOB](/rest/api/storageservices/put-blob), [list block lista](/rest/api/storageservices/put-block-list)eller [Kopiera BLOB](/rest/api/storageservices/copy-blob) skriver över data i en blob. När BLOB Soft Delete är aktiverat skapar överskrivning av en BLOB automatiskt en permanent borttagen ögonblicks bild av blobens tillstånd före Skriv åtgärden. När kvarhållningsperioden går ut tas den mjuka borttagna ögonblicks bilden bort permanent.

Mjuk borttagning av ögonblicks bilder är osynliga om inte mjuka borttagna objekt visas eller visas. Mer information om hur du visar mjuka borttagna objekt finns i [Hantera och återställa mjuka borttagna blobbar](soft-delete-blob-manage.md).

För att skydda en kopierings åtgärd måste BLOB-mjuk borttagning vara aktiverat för mål lagrings kontot.

BLOB-mjuk borttagning skyddar inte mot åtgärder för att skriva BLOB-metadata eller egenskaper. Ingen mjuk, borttagen ögonblicks bild skapas när en blobs metadata eller egenskaper uppdateras.

BLOB-mjuk borttagning ger inte överskrivning av blobar på Arkiv nivån. Om en BLOB på Arkiv nivån skrivs över med en ny BLOB på valfri nivå tas den överskrivna blobben bort permanent.

För Premium Storage-konton räknas inte mjuka borttagna ögonblicks bilder mot per BLOB-gränsen på 100 ögonblicks bilder.

### <a name="restoring-soft-deleted-objects"></a>Återställa Soft-borttagna objekt

Du kan återställa mjuka borttagna blobbar genom att anropa åtgärden [ta bort BLOB](/rest/api/storageservices/undelete-blob) i kvarhållningsperioden. **Undelete BLOB** -åtgärden återställer en blob och eventuella borttagna ögonblicks bilder som är kopplade till den. Alla ögonblicks bilder som togs bort under kvarhållningsperioden återställs.

Om du anropar **Undelete BLOB** i en blob som inte är mjuk borttagning, återställs eventuella borttagna ögonblicks bilder som är associerade med blobben. Om blobben inte har några ögonblicks bilder och inte är mjukare tas bort, har anropet **Undelete-BLOB** ingen påverkan.

Om du vill befordra en mjuk borttagen ögonblicks bild till bas-bloben ska du först anropa **Undelete BLOB** på bas-bloben för att återställa blobben och dess ögonblicks bilder. Sedan kopierar du önskad ögonblicks bild över bas-bloben. Du kan också kopiera ögonblicks bilden till en ny blob.

Det går inte att läsa data i en mjuk borttagen BLOB eller ögonblicks bild förrän objektet har återställts.

Mer information om hur du återställer mjuka borttagna objekt finns i [Hantera och återställa mjuka borttagna blobbar](soft-delete-blob-manage.md).

## <a name="blob-soft-delete-and-versioning"></a>BLOB-mjuk borttagning och versions hantering

Om BLOB-versioner och mjuk borttagning av BLOB båda är aktiverade för ett lagrings konto skapas en ny version av en BLOB automatiskt. Den nya versionen är inte mjuk och tas inte bort när kvarhållningsperioden för mjuk borttagning upphör att gälla. Inga borttagnings bara ögonblicks bilder har skapats. När du tar bort en BLOB blir den aktuella versionen av blobben en tidigare version och det finns inte längre en aktuell version. Ingen ny version skapas och inga ögonblicks bilder av mjuka rader skapas.

Att aktivera mjuk borttagning och versioner tillsammans skyddar BLOB-versioner från borttagning. När mjuk borttagning är aktiverat skapar en version av en version som tar bort en mjuk borttagning. Du kan använda åtgärden **ta bort BLOB** för att återställa mjuka borttagna versioner under perioden för kvarhållning av mjuk borttagning. Undelete- **BLOB** -åtgärden återställer alltid alla avsoft-borttagna versioner av blobben. Det går inte att återställa en enda mjuk borttagnings version.

När tids perioden för mjuk borttagning har förflutit tas eventuella borttagna BLOB-versioner bort permanent.

> [!NOTE]
> Om du anropar åtgärden **ångra borttagning av BLOB** i en borttagen BLOB när versions hantering har Aktiver ATS återställs alla borttagnings bara eller ögonblicks bilder, men den aktuella versionen återställs inte. Om du vill återställa den aktuella versionen höjer du en tidigare version genom att kopiera den till den aktuella versionen.

Microsoft rekommenderar att du aktiverar både versions-och blob-mjuk borttagning för dina lagrings konton för optimalt data skydd. Mer information om hur du använder BLOB-versioner och mjuk borttagning finns i [BLOB-versioner och mjuk borttagning](versioning-overview.md#blob-versioning-and-soft-delete).

## <a name="blob-soft-delete-protection-by-operation"></a>BLOB-Soft borttagnings skydd per åtgärd

I följande tabell beskrivs förväntat beteende för borttagnings-och skriv åtgärder när BLOB Soft Delete är aktiverat, antingen med eller utan BLOB-versioner:

| REST API åtgärder | Mjuk borttagning är aktiverat | Mjuk borttagning och versions hantering har Aktiver ATS |
|--|--|--|
| [Ta bort lagrings konto](/rest/api/storagerp/storageaccounts/delete) | Ingen förändring. Behållare och blobbar i det borttagna kontot går inte att återskapa. | Ingen förändring. Behållare och blobbar i det borttagna kontot går inte att återskapa. |
| [Ta bort container](/rest/api/storageservices/delete-container) | Ingen förändring. Blobbar i den borttagna behållaren går inte att återskapa. | Ingen förändring. Blobbar i den borttagna behållaren går inte att återskapa. |
| [Ta bort blob](/rest/api/storageservices/delete-blob) | Om den används för att ta bort en BLOB markeras denna blob som mjuk borttagning. <br /><br /> Om den används för att ta bort en BLOB-ögonblicksbild markeras ögonblicks bilden som mjuk borttagen. | Om du använder för att ta bort en BLOB blir den aktuella versionen en tidigare version och den aktuella versionen tas bort. Ingen ny version skapas och inga ögonblicks bilder av mjuka rader skapas.<br /><br /> Om du använder för att ta bort en blob-version markeras versionen som mjuk borttagen. |
| [Ångra borttagning av BLOB](/rest/api/storageservices/delete-blob) | Återställer en blob och eventuella ögonblicks bilder som togs bort under kvarhållningsperioden. | Återställer en blob och eventuella versioner som togs bort under kvarhållningsperioden. |
| [Placera blob](/rest/api/storageservices/put-blob)<br />[Lista över blockerade](/rest/api/storageservices/put-block-list)<br />[Kopiera blob](/rest/api/storageservices/copy-blob)<br />[Kopiera BLOB från URL](/rest/api/storageservices/copy-blob) | Om den anropas på en aktiv BLOB genereras en ögonblicks bild av blobens tillstånd innan åtgärden genereras automatiskt. <br /><br /> Om den anropas med en mjuk borttagen BLOB genereras en ögonblicks bild av blobens tidigare tillstånd endast om den ersätts av en blob av samma typ. Om blobben är av en annan typ tas alla befintliga Soft Deleted-data bort permanent. | En ny version som fångar upp blobens tillstånd innan åtgärden genereras automatiskt. |
| [Spärra block](/rest/api/storageservices/put-block) | Om det används för att genomföra ett block till en aktiv BLOB sker ingen ändring.<br /><br />Om den används för att genomföra ett block till en blob som är mjuk borttagning, skapas en ny blob och en ögonblicks bild skapas automatiskt för att avbilda statusen för den mjuk-borttagna blobben. | Ingen förändring. |
| [Placerings sida](/rest/api/storageservices/put-page)<br />[Lägg till sida från URL](/rest/api/storageservices/put-page-from-url) | Ingen förändring. Sid-BLOB-data som skrivs över eller rensas med den här åtgärden sparas inte och går inte att återskapa. | Ingen förändring. Sid-BLOB-data som skrivs över eller rensas med den här åtgärden sparas inte och går inte att återskapa. |
| [Lägg till block](/rest/api/storageservices/append-block)<br />[Lägg till block från URL](/rest/api/storageservices/append-block-from-url) | Ingen förändring. | Ingen förändring. |
| [Ange BLOB-egenskaper](/rest/api/storageservices/set-blob-properties) | Ingen förändring. Det går inte att återskapa de överskrivna BLOB-egenskaperna. | Ingen förändring. Det går inte att återskapa de överskrivna BLOB-egenskaperna. |
| [Ange BLOB-metadata](/rest/api/storageservices/set-blob-metadata) | Ingen förändring. Överskrivna BLOB-metadata går inte att återskapa. | En ny version som fångar upp blobens tillstånd innan åtgärden genereras automatiskt. |
| [Ange blobnivå](/rest/api/storageservices/set-blob-tier) | Bas-bloben flyttas till den nya nivån. Alla aktiva eller mjuka borttagna ögonblicks bilder finns kvar på den ursprungliga nivån. Ingen mjuk, borttagen ögonblicks bild skapas. | Bas-bloben flyttas till den nya nivån. Alla aktiva eller mjuka borttagna versioner finns kvar på den ursprungliga nivån. Ingen ny version har skapats. |

## <a name="pricing-and-billing"></a>Priser och fakturering

Alla mjuka borttagna data debiteras enligt samma taxa som aktiva data. Du kommer inte att debiteras för data som tas bort permanent efter att kvarhållningsperioden har gått ut.

När du aktiverar mjuk borttagning rekommenderar Microsoft att du använder en kort kvarhållningsperiod för att bättre förstå hur funktionen kommer att påverka din faktura. Den minsta rekommenderade kvarhållningsperioden är sju dagar.

Om du aktiverar mjuk borttagning för ofta överskrivna data kan det leda till ökade kapacitets kostnader för lagring och ökad fördröjning vid registrering av blobbar. Du kan minimera denna ytterligare kostnad och svars tid genom att lagra ofta skrivna data i ett separat lagrings konto där mjuk borttagning har inaktiverats.

Du debiteras inte för transaktioner relaterade till automatisk generering av ögonblicks bilder eller versioner när en BLOB skrivs över eller raderas. Du debiteras för anrop till åtgärden **ångra borttagning av BLOB** vid transaktions takten för Skriv åtgärder.

Mer information om priser för Blob Storage finns på sidan för [Blob Storage priser](https://azure.microsoft.com/pricing/details/storage/blobs/) .

## <a name="blob-soft-delete-and-virtual-machine-disks"></a>BLOB-mjuk borttagning och virtuella dator diskar  

BLOB-mjuk borttagning är tillgängligt för både Premium-och standard ohanterade diskar, som är sid-blobar under försättsblad. Mjuk borttagning kan hjälpa dig att återställa data som tagits bort eller skrivs över av **Delete-blobben**, att endast använda **BLOB**, **list Blocks listan** och **Kopiera BLOB** -åtgärder.

Data som skrivs över av ett anrop till en **sida** går inte att återställa. En virtuell Azure-dator skriver till en ohanterad disk med hjälp av anrop till att använda **sidan**, så Använd mjuk borttagning för att ångra skrivningar till en ohanterad disk från en virtuell Azure-dator är inte ett scenario som stöds.

## <a name="next-steps"></a>Nästa steg

- [Aktivera mjuk borttagning för blobar](./soft-delete-blob-enable.md)
- [Hantera och återställa mjuka borttagna blobar](soft-delete-blob-manage.md)
- [BLOB-versioner](versioning-overview.md)