---
title: BLOB-versioner
titleSuffix: Azure Storage
description: Blob Storage-versioner hanterar automatiskt tidigare versioner av ett objekt och identifierar dem med tidsstämplar. Du kan återställa tidigare versioner av en BLOB för att återställa dina data om de felaktigt ändras eller tas bort.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 692a820bea69071485a973a988ae91bd70b74f35
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100380822"
---
# <a name="blob-versioning"></a>BLOB-versioner

Du kan aktivera Blob Storage-versioner för att automatiskt underhålla tidigare versioner av ett objekt.  När BLOB-versioner har Aktiver ATS kan du återställa en tidigare version av en BLOB för att återställa dina data om de felaktigt ändras eller tas bort.

BLOB-versioner aktive ras på lagrings kontot och gäller för alla blobar i lagrings kontot. När du har aktiverat BLOB-versioner för ett lagrings konto behåller Azure Storage automatiskt versioner för varje BLOB i lagrings kontot.

Microsoft rekommenderar att du använder BLOB-versioner för att underhålla tidigare versioner av en BLOB för överlägsen data skydd. Använd om möjligt BLOB-versioner i stället för BLOB-ögonblicksbilder för att underhålla tidigare versioner. BLOB-ögonblicksbilder ger liknande funktionalitet i att de behåller tidigare versioner av en BLOB, men ögonblicks bilder måste behållas manuellt av ditt program.

Information om hur du aktiverar BLOB-versioner finns i [Aktivera och hantera BLOB-versioner](versioning-enable.md).

> [!IMPORTANT]
> BLOB-versioner kan inte hjälpa dig att återställa från oavsiktlig borttagning av ett lagrings konto eller behållare. Förhindra oavsiktlig borttagning av lagrings kontot genom att konfigurera ett lås på lagrings konto resursen. Mer information om hur du låser Azure-resurser finns i [låsa resurser för att förhindra oväntade ändringar](../../azure-resource-manager/management/lock-resources.md). Om du vill skydda behållare från oavsiktlig borttagning konfigurerar du behållaren mjuk borttagning för lagrings kontot. Mer information finns i [Soft Delete for containers (för hands version)](soft-delete-container-overview.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-blob-versioning-works"></a>Så här fungerar BLOB-versioner

En version fångar in statusen för en BLOB vid en viss tidpunkt. När BLOB-versioner har Aktiver ATS för ett lagrings konto skapar Azure Storage automatiskt en ny version av en BLOB varje gång bloben ändras eller tas bort.

När du skapar en blob med versions hantering aktive rad är den nya blobben den aktuella versionen av blobben (eller bas-BLOB). Om du senare ändrar denna BLOB skapar Azure Storage en version som fångar upp status för blobben innan den ändrades. Den ändrade blobben blir den nya aktuella versionen. En ny version skapas varje gången du ändrar blobben.

Följande diagram visar hur versioner skapas vid Skriv-och borttagnings åtgärder och hur en tidigare version kan uppgraderas till den aktuella versionen:

:::image type="content" source="media/versioning-overview/blob-versioning-diagram.png" alt-text="Diagram över hur BLOB-versioner fungerar":::

Att ha ett stort antal versioner per BLOB kan öka svars tiden för BLOB List-åtgärder. Microsoft rekommenderar att du behåller färre än 1000 versioner per blob. Du kan använda livs cykel hantering för att automatiskt ta bort gamla versioner. Mer information om livs cykel hantering finns i [optimera kostnader genom att automatisera Azure-Blob Storage åtkomst nivåer](storage-lifecycle-management-concepts.md).

När du tar bort en blob med versions hantering aktive rad skapar Azure Storage en version som fångar upp status för blobben innan den tas bort. Den aktuella versionen av blobben tas sedan bort, men blobens versioner kvarstår, så att den kan skapas på nytt om det behövs. 

BLOB-versioner är oföränderliga. Du kan inte ändra innehållet eller metadata för en befintlig blob-version.

BLOB-versioner är tillgängligt för General-Purpose v2-, Block Blob-och Blob Storage-konton. Lagrings konton med hierarkiskt namn område som är aktiverade för användning med Azure Data Lake Storage Gen2 stöds inte för närvarande.

Version 2019-10-10 och senare av Azure Storage REST API stöder BLOB-versioner.

### <a name="version-id"></a>Versions-ID

Varje blob-version identifieras med ett versions-ID. Värdet för versions-ID är tidsstämpeln då blobben skrevs eller uppdaterades. Versions-ID: t tilldelas vid den tidpunkt då versionen skapades.

Du kan utföra Läs-eller borttagnings åtgärder för en angiven version av en BLOB genom att ange dess versions-ID. Om du utelämnar versions-ID: t fungerar åtgärden mot den aktuella versionen (bas-blobben).

När du anropar en Skriv åtgärd för att skapa eller ändra en BLOB returnerar Azure Storage rubriken *x-MS-version-ID* i svaret. Den här rubriken innehåller versions-ID för den aktuella versionen av blobben som skapades av Skriv åtgärden.

Versions-ID: t är detsamma som för versionens livs längd.

### <a name="versioning-on-write-operations"></a>Versioner vid Skriv åtgärder

När BLOB-versioner aktive ras skapas en ny version av varje Skriv åtgärd till en blob. Skriv åtgärder är [att lägga till BLOB](/rest/api/storageservices/put-blob), sätta i [blockeringslistan](/rest/api/storageservices/put-block-list), [Kopiera BLOB](/rest/api/storageservices/copy-blob)och [Ange BLOB-metadata](/rest/api/storageservices/set-blob-metadata).

Om Skriv åtgärden skapar en ny BLOB, är den resulterande blobben den aktuella versionen av blobben. Om Skriv åtgärden ändrar en befintlig BLOB, hämtas nya data i den uppdaterade blobben, som är den aktuella versionen, och Azure Storage skapar en version som sparar blobens tidigare status.

Diagrammet som visas i den här artikeln visar versions-ID: t som ett enkelt heltals värde. I verkligheten är versions-ID en tidsstämpel. Den aktuella versionen visas i blått och tidigare versioner visas i grått.

Följande diagram visar hur Skriv åtgärder påverkar BLOB-versioner. När en BLOB skapas är denna BLOB den aktuella versionen. När samma BLOB ändras, skapas en ny version för att spara blobens tidigare tillstånd och den uppdaterade blobben blir den aktuella versionen.

:::image type="content" source="media/versioning-overview/write-operations-blob-versions.png" alt-text="Diagram som visar hur Skriv åtgärder påverkar versioner av blobar.":::

> [!NOTE]
> En blob som skapades innan versions hantering som Aktiver ATS för lagrings kontot har inget versions-ID. När denna BLOB ändras blir den ändrade blobben den aktuella versionen och en version skapas för att spara blobens tillstånd innan uppdateringen. Versionen har tilldelats ett versions-ID som är skapande tillfället.

### <a name="versioning-on-delete-operations"></a>Versions hantering för borttagnings åtgärder

När du tar bort en BLOB blir den aktuella versionen av blobben en tidigare version och bas-bloben tas bort. Alla befintliga tidigare versioner av blobben bevaras när blobben tas bort.

Om du anropar åtgärden [ta bort BLOB](/rest/api/storageservices/delete-blob) utan ett versions-ID tas bas-bloben bort. Om du vill ta bort en version anger du ID: t för den versionen för borttagnings åtgärden.

Följande diagram visar effekterna av en borttagnings åtgärd i en versions-BLOB:

:::image type="content" source="media/versioning-overview/delete-versioned-base-blob.png" alt-text="Diagram över borttagning av versions-blob.":::

När nya data skrivs till bloben skapas en ny version av blobben. Eventuella befintliga versioner påverkas inte, vilket visas i följande diagram.

:::image type="content" source="media/versioning-overview/recreate-deleted-base-blob.png" alt-text="Diagram som visar åter skapandet av versions-BLOB efter borttagning.":::

### <a name="blob-types"></a>Blobbtyper

När BLOB-versioner har Aktiver ATS för ett lagrings konto utlöser alla Skriv-och borttagnings åtgärder på block-blobar skapandet av en ny version, med undantag för åtgärden [parkera block](/rest/api/storageservices/put-block) .

För Page blobbar och tillägg av blobar utlöser bara en del av Skriv-och borttagnings åtgärder att en version skapas. Dessa åtgärder omfattar:

- [Placera blob](/rest/api/storageservices/put-blob)
- [Lista över blockerade](/rest/api/storageservices/put-block-list)
- [Ta bort blob](/rest/api/storageservices/delete-blob)
- [Ange BLOB-metadata](/rest/api/storageservices/set-blob-metadata)
- [Kopiera blob](/rest/api/storageservices/copy-blob)

Följande åtgärder utlöser inte skapandet av en ny version. Ta en manuell ögonblicks bild för att avbilda ändringar från dessa åtgärder:

- [Placerings sida](/rest/api/storageservices/put-page) (sid-BLOB)
- [Lägg till block](/rest/api/storageservices/append-block) (bifoga BLOB)

Alla versioner av en BLOB måste vara av samma Blob-typ. Om en BLOB har tidigare versioner kan du inte skriva över en blob av en typ med en annan typ om du inte först tar bort blobben och alla dess versioner.

### <a name="access-tiers"></a>Åtkomstnivåer

Du kan flytta alla versioner av en Block-Blob, inklusive den aktuella versionen, till en annan BLOB-åtkomstnivå genom att anropa [set BLOB Tier](/rest/api/storageservices/set-blob-tier) -åtgärden. Du kan dra nytta av lägre kapacitets prissättning genom att flytta äldre versioner av en blob till nivån låg frekvent eller Arkiv lag ring. Mer information finns i [Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv](storage-blob-storage-tiers.md)lag rings nivåer.

Om du vill automatisera processen med att flytta block blobbar till lämplig nivå använder du hantering av BLOB livs cykel. Mer information om livs cykel hantering finns i [Hantera livs cykeln för Azure Blob Storage](storage-lifecycle-management-concepts.md).

## <a name="enable-or-disable-blob-versioning"></a>Aktivera eller inaktivera BLOB-versioner

Information om hur du aktiverar eller inaktiverar BLOB-versioner finns i [Aktivera och hantera BLOB-versioner](versioning-enable.md).

Inaktive ring av BLOB-versioner tar inte bort befintliga blobbar, versioner eller ögonblicks bilder. När du inaktiverar BLOB-versioner är alla befintliga versioner tillgängliga i ditt lagrings konto. Inga nya versioner skapas senare.

Om en BLOB har skapats eller ändrats efter att versions hantering har inaktiverats på lagrings kontot, skapar Överskrivningen av bloben en ny version. Den uppdaterade blobben är inte längre den aktuella versionen och har inget versions-ID. Alla efterföljande uppdateringar av blobben kommer att skriva över data utan att spara det tidigare läget.

Du kan läsa eller ta bort versioner med versions-ID: t när versions hantering har inaktiverats. Du kan också lista en blobs versioner efter att versions hantering har inaktiverats.

Följande diagram visar hur ändring av en BLOB efter versions hantering är inaktive rad skapar en blob som inte är en version. Alla befintliga versioner som är associerade med blobben är kvar.

:::image type="content" source="media/versioning-overview/modify-base-blob-versioning-disabled.png" alt-text="Diagram som visar bas-BLOB ändrad efter inaktive rad versions hantering.":::

## <a name="blob-versioning-and-soft-delete"></a>BLOB-versioner och mjuk borttagning

BLOB-versioner och blob-mjuk borttagning fungerar tillsammans för att ge dig optimalt data skydd. När du aktiverar mjuk borttagning anger du hur länge Azure Storage ska behålla en mjuk, borttagen blob. Eventuell mjuk, borttagen blob-version finns kvar i systemet och kan tas bort från kvarhållningsperioden för mjuk borttagning. Mer information om mjuk borttagning av BLOB finns i [mjuk borttagning för Azure Storage blobbar](./soft-delete-blob-overview.md).

### <a name="deleting-a-blob-or-version"></a>Ta bort en BLOB eller version

Mjuk borttagning ger ytterligare skydd för att ta bort BLOB-versioner. Om både versions hantering och mjuk borttagning är aktiverade på lagrings kontot, och om du tar bort en BLOB, skapar Azure Storage en ny version för att spara status för blobben omedelbart före borttagning och tar bort den aktuella versionen. Den nya versionen är inte mjuk och tas inte bort när kvarhållningsperioden för mjuk borttagning upphör att gälla.

När du tar bort en tidigare version av bloben är versionen mjuk borttagning. Den Soft-borttagna versionen behålls under hela kvarhållningsperioden som anges i inställningarna för mjuk borttagning för lagrings kontot och tas bort permanent när kvarhållningsperioden för mjuk borttagning upphör att gälla.

Ta bort en tidigare version av en BLOB genom att uttryckligen ta bort den genom att ange versions-ID.

Följande diagram visar vad som händer när du tar bort en BLOB eller en blob-version.

:::image type="content" source="media/versioning-overview/soft-delete-historical-version.png" alt-text="Diagram som visar borttagning av en version med mjuk borttagning aktive rad.":::

Om både versions hantering och mjuk borttagning är aktiverade på ett lagrings konto skapas ingen mjuk, borttagen ögonblicks bild när en BLOB-eller blob-version ändras eller tas bort.

### <a name="restoring-a-soft-deleted-version"></a>Återställa en mjuk borttagnings version

Du kan återställa en mjuk borttagen blob-version genom att anropa åtgärden [ångra borttagning av BLOB](/rest/api/storageservices/undelete-blob) i versionen medan kvarhållningsperioden för mjuk borttagning tillämpas. **Undelete BLOB** -åtgärden återställer alla avsoft-borttagna versioner av blobben.

Att återställa avläsnings bara versioner med **Undelete-BLOB** -åtgärden befordrar inte någon version som den aktuella versionen. Om du vill återställa den aktuella versionen återställer du först alla Soft-borttagna versioner och använder sedan åtgärden [Kopiera BLOB](/rest/api/storageservices/copy-blob) för att kopiera en tidigare version för att återställa blobben.

Följande diagram visar hur du återställer avsoft-borttagna BLOB-versioner med åtgärden **ta bort BLOB** och hur du återställer den aktuella versionen av blobben med åtgärden **Kopiera BLOB** .

:::image type="content" source="media/versioning-overview/undelete-version.png" alt-text="Diagram som visar hur du återställer mjuka borttagna versioner.":::

När tids perioden för mjuk borttagning har förflutit tas eventuella borttagna BLOB-versioner bort permanent.

## <a name="blob-versioning-and-blob-snapshots"></a>BLOB-versioner och blob-ögonblicksbilder

En BLOB-ögonblicksbild är en skrivskyddad kopia av en blob som tas vid en viss tidpunkt. BLOB-ögonblicksbilder och blob-versioner liknar varandra, men en ögonblicks bild skapas manuellt av dig eller ditt program, medan en blob-version skapas automatiskt vid en Skriv-eller borttagnings åtgärd när BLOB-versioner aktive ras för ditt lagrings konto.

> [!IMPORTANT]
> Microsoft rekommenderar att när du har aktiverat BLOB-versioner kan du också uppdatera ditt program för att sluta ta ögonblicks bilder av block-blobbar. Om versions hantering har Aktiver ATS för ditt lagrings konto samlas alla block BLOB-uppdateringar och borttagningar in och bevaras av versioner. Att ta ögonblicks bilder ger inga ytterligare skydd till dina block BLOB-data om BLOB-versioner är aktiverat och kan öka kostnaderna och program komplexiteten.

### <a name="snapshot-a-blob-when-versioning-is-enabled"></a>Ögonblicks bild av en BLOB när versions hantering har Aktiver ATS

Även om det inte rekommenderas kan du ta en ögonblicks bild av en blob som också är en version. Om du inte kan uppdatera ditt program för att sluta ta ögonblicks bilder av blobbar när du aktiverar versions hantering, kan programmet stödja både ögonblicks bilder och versioner.

När du tar en ögonblicks bild av en versions-BLOB skapas en ny version på samma gång som ögonblicks bilden skapas. En ny aktuell version skapas också när en ögonblicks bild tas.

Följande diagram visar vad som händer när du tar en ögonblicks bild av en versions-blob. I diagrammet innehåller BLOB-versioner och ögonblicks bilder med versions-ID 2 och 3 identiska data.

:::image type="content" source="media/versioning-overview/snapshot-versioned-blob.png" alt-text="Diagram över ögonblicks bilder av en versions-blob.":::

## <a name="authorize-operations-on-blob-versions"></a>Auktorisera åtgärder på BLOB-versioner

Du kan ge åtkomst till BLOB-versioner med någon av följande metoder:

- Genom att använda rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att bevilja behörighet till ett Azure Active Directory (Azure AD) säkerhets objekt. Microsoft rekommenderar att du använder Azure AD för överlägsen säkerhet och enkel användning. Mer information om hur du använder Azure AD med BLOB-åtgärder finns i [bevilja åtkomst till blobbar och köer med hjälp av Azure Active Directory](../common/storage-auth-aad.md).
- Genom att använda en signatur för delad åtkomst (SAS) för att delegera åtkomst till BLOB-versioner. Ange versions-ID för den signerade resurs typen `bv` , som representerar en blob-version, för att skapa en SAS-token för åtgärder i en angiven version. Mer information om signaturer för delad åtkomst finns i [bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av signaturer för delad åtkomst (SAS)](../common/storage-sas-overview.md).
- Genom att använda kontots åtkomst nycklar för att auktorisera åtgärder mot BLOB-versioner med delad nyckel. Mer information finns i [auktorisera med delad nyckel](/rest/api/storageservices/authorize-with-shared-key).

BLOB-versioner är utformad för att skydda dina data från oavsiktlig eller skadlig borttagning. För att förbättra skyddet krävs särskilda behörigheter för att ta bort en blob-version. I följande avsnitt beskrivs de behörigheter som krävs för att ta bort en blob-version.

### <a name="azure-rbac-action-to-delete-a-blob-version"></a>Azure RBAC-åtgärd för att ta bort en blob-version

I följande tabell visas vilka Azure RBAC-åtgärder som stöder borttagning av BLOB-eller BLOB-versioner.

| Beskrivning | Blob Service åtgärd | Azure RBAC-dataåtgärd krävs | Stöd för inbyggd Azure-roll |
|----------------------------------------------|------------------------|---------------------------------------------------------------------------------------|-------------------------------|
| Tar bort den aktuella versionen av blobben | Ta bort blob | **Microsoft. Storage/storageAccounts/blobServices/containers/blobbar/Delete** | Storage Blob Data-deltagare |
| Tar bort en version | Ta bort blob | **Microsoft. Storage/storageAccounts/blobServices/containers/blobbar/deleteBlobVersion/åtgärd** | Storage Blob Data-ägare |

### <a name="shared-access-signature-sas-parameters"></a>Parametrar för signatur för delad åtkomst (SAS)

Den signerade resursen för en blob-version är `bv` . Mer information finns i [skapa en tjänst-SAS](/rest/api/storageservices/create-service-sas) eller [skapa en användar Delegerings-SAS](/rest/api/storageservices/create-user-delegation-sas).

I följande tabell visas den behörighet som krävs för en SAS för att ta bort en blob-version.

| **Behörighet** | **URI-symbol** | **Tillåtna åtgärder** |
|----------------|----------------|------------------------|
| Ta bort         | x              | Ta bort en blob-version. |

## <a name="pricing-and-billing"></a>Priser och fakturering

Att aktivera BLOB-versioner kan resultera i ytterligare avgifter för data lagring till ditt konto. När du designar ditt program är det viktigt att vara medveten om hur dessa avgifter kan påföras så att du kan minimera kostnaderna.

BLOB-versioner, t. ex. blob-ögonblicksbilder, faktureras enligt samma taxa som aktiva data. Hur versioner faktureras beror på om du uttryckligen har angett nivån för bas-bloben eller för någon av dess versioner (eller ögonblicks bilder). Mer information om BLOB-nivåer finns i [Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv](storage-blob-storage-tiers.md)lag rings nivåer.

Om du inte har ändrat en BLOB-eller versions nivå debiteras du för unika data block över denna BLOB, dess versioner och eventuella ögonblicks bilder som den kan ha. Mer information finns i [fakturering när BLOB-nivån inte har angetts explicit](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Om du har ändrat en BLOB-eller versions nivå debiteras du för hela objektet, oavsett om bloben och versionen slutligen är på samma nivå igen. Mer information finns i [fakturera när BLOB-nivån uttryckligen har ställts in](#billing-when-the-blob-tier-has-been-explicitly-set).

> [!NOTE]
> Om du aktiverar versions hantering för data som ofta skrivs över kan det leda till ökade kapacitets kostnader för lagring och ökad fördröjning under List åtgärder. För att undvika dessa problem kan du lagra ofta skrivna data i ett separat lagrings konto med versions inaktiverat.

Mer information om fakturerings information för BLOB-ögonblicksbilder finns i [BLOB-ögonblicksbilder](snapshots-overview.md).

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>Fakturera när BLOB-nivån inte har angetts explicit

Om du inte uttryckligen anger BLOB-nivån för en bas-BLOB eller någon av dess versioner debiteras du för unika block eller sidor över bloben, dess versioner och eventuella ögonblicks bilder som den kan ha. Data som delas i en blob och dess versioner debiteras bara en gång. När en BLOB uppdateras avviker data i en bas-BLOB från de data som lagras i dess versioner, och unika data debiteras per block eller sida.

När du ersätter ett block i en Block-Blob debiteras det här blocket som ett unikt block. Detta gäller även om blocket har samma block-ID och samma data som i den tidigare versionen. När blocket har allokerats igen avviker det från dess motsvarighet i den tidigare versionen och du debiteras för data. Samma undantag gäller för en sida i en sid-blob som uppdateras med identiska data.

Blob Storage har inget sätt att avgöra om två block innehåller identiska data. Varje block som överförs och bekräftas behandlas som unikt, även om det har samma data och samma block-ID. Eftersom avgifterna ska vara för unika block, är det viktigt att komma ihåg att om du uppdaterar en BLOB när versions hantering är aktive rad kommer ytterligare unika block och ytterligare kostnader att uppstå.

När BLOB-versioner är aktive rad anropar du uppdaterings åtgärder på block-blobbar så att de uppdaterar det minsta möjliga antalet block. Skriv åtgärder som tillåter detaljerad kontroll över [block är att blockera och blockera](/rest/api/storageservices/put-block) [listor](/rest/api/storageservices/put-block-list). Åtgärden för att [lägga till BLOB](/rest/api/storageservices/put-blob) ersätter å andra sidan hela innehållet i en blob och kan leda till ytterligare kostnader.

Följande scenarier visar hur avgifterna påförs för en Block-Blob och dess versioner när BLOB-nivån inte har angetts explicit.

#### <a name="scenario-1"></a>Scenario 1

I Scenario 1 har blobben en tidigare version. Bloben har inte uppdaterats sedan versionen skapades, så kostnader uppkommer bara för unika block 1, 2 och 3.

![Diagram 1 visar fakturering för unika block i bas-blob och tidigare version.](./media/versioning-overview/versions-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scenario 2

I scenario 2 har ett block (Block 3 i diagrammet) i blobben uppdaterats. Även om det uppdaterade blocket innehåller samma data och samma ID, är det inte samma som för Block 3 i den tidigare versionen. Det innebär att kontot debiteras för fyra block.

![Diagram 2 visar fakturering för unika block i bas-blob och tidigare version.](./media/versioning-overview/versions-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scenario 3

I scenario 3 har blobben uppdaterats, men versionen har inte det. Block 3 har ersatts med block 4 i bas-blobben, men den tidigare versionen visar fortfarande Block 3. Det innebär att kontot debiteras för fyra block.

![Diagram 3 visar fakturering för unika block i bas-blob och tidigare version.](./media/versioning-overview/versions-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scenario 4

I Scenario 4 har bas-bloben uppdaterats helt och innehåller inget av de ursprungliga blocken. Det innebär att kontot debiteras för alla åtta unika block &mdash; fyra i bas-blobben och fyra i den tidigare versionen. Det här scenariot kan inträffa om du skriver till en blob med åtgärden för att [Skicka BLOB](/rest/api/storageservices/put-blob) , eftersom den ersätter hela innehållet i bas-bloben.

![Diagram 4 visar fakturering för unika block i bas-blob och tidigare version.](./media/versioning-overview/versions-billing-scenario-4.png)

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>Fakturera när BLOB-nivån uttryckligen har angetts

Om du uttryckligen har angett BLOB-nivån för en BLOB eller version (eller ögonblicks bild) debiteras du för objektets fullständiga innehålls längd på den nya nivån, oavsett om den delar block med ett objekt på den ursprungliga nivån. Du debiteras också för den äldsta versionen av innehålls längden på den ursprungliga nivån. Alla tidigare versioner eller ögonblicks bilder som finns kvar på den ursprungliga nivån debiteras för unika block som de kan dela, enligt beskrivningen i [fakturering när BLOB-nivån inte har angetts explicit](#billing-when-the-blob-tier-has-not-been-explicitly-set).

#### <a name="moving-a-blob-to-a-new-tier"></a>Flytta en blob till en ny nivå

I följande tabell beskrivs fakturerings beteendet för en BLOB eller version när den flyttas till en ny nivå.

| När BLOB-nivån uttryckligen anges... | Sedan faktureras du för... |
|-|-|
| En bas-BLOB med en tidigare version | Bas-bloben på den nya nivån och den äldsta versionen på den ursprungliga nivån samt eventuella unika block i andra versioner. <sup>1</sup> |
| En bas-BLOB med en tidigare version och en ögonblicks bild | Bas-bloben på den nya nivån, den äldsta versionen på den ursprungliga nivån och den äldsta ögonblicks bilden på den ursprungliga nivån samt eventuella unika block i andra versioner eller ögonblicks bilder<sup>1</sup>. |
| En tidigare version | Versionen på den nya nivån och bas-bloben på den ursprungliga nivån samt eventuella unika block i andra versioner. <sup>1</sup> |

<sup>1</sup> Om det finns andra tidigare versioner eller ögonblicks bilder som inte har flyttats från ursprungs nivån debiteras dessa versioner eller ögonblicks bilder utifrån antalet unika block som de innehåller, enligt beskrivningen i [fakturering när BLOB-nivån inte har angetts explicit](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Följande diagram illustrerar hur objekt faktureras när en versions-BLOB flyttas till en annan nivå.

:::image type="content" source="media/versioning-overview/versioning-billing-tiers.png" alt-text="Diagram över hur objekt faktureras när en versions-BLOB är en explicit nivå.":::

Det går inte att göra en återställning av nivån för en BLOB, version eller ögonblicks bild. Om du flyttar en blob till en ny nivå och sedan flyttar tillbaka den till den ursprungliga nivån debiteras du för objektets fullständiga innehålls längd även om det delar block med andra objekt på den ursprungliga nivån.

Åtgärder som uttryckligen anger nivån för en BLOB, version eller ögonblicks bild är:

- [Ange blobnivå](/rest/api/storageservices/set-blob-tier)
- [Lägg till BLOB](/rest/api/storageservices/put-blob) med angiven nivå
- [Lägg till blockeringslistan](/rest/api/storageservices/put-block-list) med den angivna nivån
- [Kopiera BLOB](/rest/api/storageservices/copy-blob) med angiven nivå

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>Att ta bort en BLOB när mjuk borttagning har Aktiver ATS

Om du har aktiverat BLOB-mjuk borttagning, och om du tar bort eller skriver över en bas-blob som har haft nivån explicit angiven, faktureras alla tidigare versioner av den mjuk-borttagna blobben med full innehålls längd. Mer information om hur BLOB-versioner och mjuk borttagning fungerar tillsammans finns i [BLOB-versioner och mjuk borttagning](#blob-versioning-and-soft-delete).

I följande tabell beskrivs fakturerings beteendet för en blob som är mjuk borttagning, beroende på om versions hantering har Aktiver ATS eller inaktiverats. När versions hantering har Aktiver ATS skapas en version när en BLOB är mjuk-borttagen. När versions hantering är inaktive rad skapar mjuk borttagning av en BLOB en ögonblicks bild av mjuk borttagning.

| När du skriver över en bas-BLOB med dess nivå explicit anges... | Sedan faktureras du för... |
|-|-|
| Om både BLOB-mjuk borttagning och versions hantering är aktiverade | Alla befintliga versioner med full innehålls längd oavsett nivå. |
| Om mjuk borttagning av BLOB är aktiverat men versions hantering är inaktive rad | Alla befintliga ögonblicks bilder av mjuk borttagning med full innehålls längd oavsett nivå. |

## <a name="see-also"></a>Se även

- [Aktivera och hantera BLOB-versioner](versioning-enable.md)
- [Skapa en ögonblicks bild av en BLOB](/rest/api/storageservices/creating-a-snapshot-of-a-blob)
- [Mjuk borttagning för Azure Storage blobbar](./soft-delete-blob-overview.md)