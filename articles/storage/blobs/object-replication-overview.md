---
title: Översikt över objekt replikering
titleSuffix: Azure Storage
description: Objekt replikeringen kopierar asynkront block blobbar mellan ett käll lagrings konto och ett mål konto. Använd objekt replikering för att minimera svars tiden för Läs begär Anden, för att öka effektiviteten för beräknings arbets belastningar, optimera data distribution och minimera kostnaderna.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 391c33e72f45e7c0c0b56128b32a8e73399e417a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99834331"
---
# <a name="object-replication-for-block-blobs"></a>Objekt replikering för block-blobar

Objekt replikeringen kopierar asynkront block blobbar mellan ett käll lagrings konto och ett mål konto. Vissa scenarier som stöds av objekt replikering är:

- **Minimerar svars tid.** Objekt replikering kan minska svars tiden för Läs begär Anden genom att göra det möjligt för klienter att använda data från en region som ligger närmare fysisk närhet.
- **Öka effektiviteten för beräknings arbets belastningar.** Med objekt replikering kan beräknings arbets belastningar bearbeta samma uppsättningar block blobbar i olika regioner.
- **Optimering av data distribution.** Du kan bearbeta eller analysera data på en och samma plats och sedan replikera bara resultaten till ytterligare regioner.
- **Optimera kostnader.** När dina data har repliker ATS kan du minska kostnaderna genom att flytta den till Arkiv nivån med hanterings principer för livs cykel.

Följande diagram visar hur objekt replikeringen replikerar block blobbar från ett käll lagrings konto i en region till mål konton i två olika regioner.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Diagram som visar hur objekt replikering fungerar":::

Information om hur du konfigurerar objekt replikering finns i [Konfigurera objekt replikering](object-replication-configure.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="prerequisites-for-object-replication"></a>Förhandskrav för objektreplikering

Objekt replikering kräver att följande Azure Storage funktioner också är aktiverade:

- [Ändra feed](storage-blob-change-feed.md): måste vara aktiverat på käll kontot. Information om hur du aktiverar ändrings flöden finns i [Aktivera och inaktivera ändrings flödet](storage-blob-change-feed.md#enable-and-disable-the-change-feed).
- [BLOB-versioner](versioning-overview.md): måste vara aktive rad på både käll-och mål kontona. Information om hur du aktiverar versions hantering finns i [Aktivera och hantera BLOB-versioner](versioning-enable.md).

Att aktivera ändrings flöde och blob-versioner kan medföra ytterligare kostnader. Mer information finns på [sidan med Azure Storage priser](https://azure.microsoft.com/pricing/details/storage/).

Objekt replikering stöds endast för allmänna-syfte v2-lagrings konton. Både käll-och mål kontona måste vara generell-Purpose v2. 

## <a name="how-object-replication-works"></a>Så här fungerar objekt replikering

Med objekt replikering kopieras block blobbar asynkront i en behållare enligt de regler som du konfigurerar. Innehållet i blobben, alla versioner som är associerade med blobben och blobens metadata och egenskaper kopieras från käll behållaren till mål behållaren.

> [!IMPORTANT]
> Eftersom block BLOB-data replikeras asynkront synkroniseras inte käll kontot och mål kontot omedelbart. Det finns för närvarande inget service avtal för hur lång tid det tar att replikera data till mål kontot. Du kan kontrol lera replikeringsstatus på käll-bloben för att avgöra om replikeringen är klar. Mer information finns i [kontrol lera replikeringsstatus för en BLOB](object-replication-configure.md#check-the-replication-status-of-a-blob).

### <a name="blob-versioning"></a>BLOB-versioner

Objekt replikering kräver att BLOB-versioner aktive ras på både käll-och mål kontona. När en replikerad BLOB i käll kontot ändras, skapas en ny version av blobben i det käll konto som motsvarar det tidigare läget för blobben innan ändringen. Den aktuella versionen (eller bas-BLOB) i käll kontot återspeglar de senaste uppdateringarna. Både den uppdaterade aktuella versionen och den nya tidigare versionen replikeras till mål kontot. Mer information om hur Skriv åtgärder påverkar BLOB-versioner finns i [versions hantering för Skriv åtgärder](versioning-overview.md#versioning-on-write-operations).

När en BLOB i käll kontot tas bort, fångas den aktuella versionen av blobben i en tidigare version och tas sedan bort. Alla tidigare versioner av blobben behålls även efter att den aktuella versionen har tagits bort. Det här läget replikeras till mål kontot. Mer information om hur borttagnings åtgärder påverkar BLOB-versioner finns i [versions hantering för borttagning](versioning-overview.md#versioning-on-delete-operations).

### <a name="snapshots"></a>Ögonblicksbilder

Objekt replikering har inte stöd för BLOB-ögonblicksbilder. Eventuella ögonblicks bilder på en BLOB i käll kontot replikeras inte till mål kontot.

### <a name="blob-tiering"></a>BLOB-skiktning

Objekt replikering stöds när käll-och mål kontona är på frekvent eller låg frekvent nivå. Käll-och mål kontona kan finnas på olika nivåer. Det går dock inte att replikera objekt om en BLOB i antingen käll-eller mål kontot har flyttats till Arkiv nivån. Mer information om BLOB-nivåer finns i [åtkomst nivåer för Azure Blob Storage – frekvent,](storage-blob-storage-tiers.md)låg frekvent och Arkiv lag ring.

### <a name="immutable-blobs"></a>Blobar som inte kan ändras

Objekt replikering stöder inte oföränderliga blobbar. Om en käll-eller mål behållare har en tidsbaserad bevarande princip eller ett juridiskt undantag, Miss lyckas objekt replikeringen. Mer information om oföränderliga blobbar finns i [lagra affärs kritiska BLOB-data med oföränderlig lagring](storage-blob-immutable-storage.md).

## <a name="object-replication-policies-and-rules"></a>Principer och regler för objekt replikering

När du konfigurerar objekt replikering skapar du en replikeringsprincip som anger käll lagrings kontot och mål kontot. En replikeringsprincip innehåller en eller flera regler som anger en käll behållare och en mål behållare och anger vilka block-blobar som ska replikeras i käll behållaren.

När du har konfigurerat objekt replikering kontrollerar Azure Storage ändrings flödet för käll kontot med jämna mellanrum och replikerar eventuellt Skriv-eller borttagnings åtgärder till mål kontot. Replikeringsfördröjning beror på storleken på den Block-Blob som replikeras.

### <a name="replication-policies"></a>Replikeringsprinciper

När du konfigurerar objekt replikering skapas en replikeringsprincip på både käll kontot och mål kontot via Azure Storage resurs leverantören. Replikeringsprincipen identifieras av ett princip-ID. Principen på käll-och mål kontona måste ha samma princip-ID för att replikeringen ska kunna ske.

Ett lagrings konto kan fungera som käll konto för upp till två mål konton. Käll-och mål kontona kan finnas i samma region eller i olika regioner. De kan också finnas i olika prenumerationer och i olika Azure Active Directory-klienter (Azure AD). Det går bara att skapa en replikeringsprincip för varje käll konto/mål konto par.

### <a name="replication-rules"></a>Regler för replikering

Reglerna för replikering anger hur Azure Storage kommer att replikera blobbar från en käll behållare till en mål behållare. Du kan ange upp till 10 regler för replikering för varje replikeringsprincip. Varje replikeringsprincip definierar en enda käll-och mål behållare, och varje käll-och mål behållare kan bara användas i en regel.

När du skapar en replikeringsprincip, kopieras som standard bara nya block blobbar som senare läggs till i käll behållaren. Du kan ange att både nya och befintliga block-blobbar ska kopieras, eller så kan du definiera en anpassad kopierings omfattning som kopierar block-blobbar som skapats från en angiven tid till och med.

Du kan också ange ett eller flera filter som en del av en replikeringsprincip för att filtrera block blobbar efter prefix. När du anger ett prefix kopieras bara blobbar som matchar det prefixet i käll behållaren till mål behållaren.

Käll-och mål behållarna måste finnas innan du kan ange dem i en regel. När du har skapat replikeringsprincipen tillåts inte Skriv åtgärder till mål containern. Försök att skriva till målcontainern misslyckas med felkoden 409 (konflikt). Om du vill skriva till en mål behållare som en replikeringsprincip har kon figurer ATS för måste du antingen ta bort regeln som har kon figurer ATS för den behållaren eller ta bort replikeringsprincipen. Läs-och borttagnings åtgärder till mål behållaren tillåts när replikeringsprincipen är aktiv.

Du kan anropa åtgärden [Ange BLOB-nivå](/rest/api/storageservices/set-blob-tier) på en BLOB i mål behållaren för att flytta den till Arkiv nivån. Mer information om Arkiv nivån finns i [Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv](storage-blob-storage-tiers.md#archive-access-tier)lag rings nivåer.

## <a name="replication-status"></a>Replikeringsstatus

Du kan kontrol lera replikeringsstatus för en BLOB i käll kontot. Mer information finns i [kontrol lera replikeringsstatus för en BLOB](object-replication-configure.md#check-the-replication-status-of-a-blob).

Om replikeringsstatus för en BLOB i käll kontot indikerar fel, kan du undersöka följande möjliga orsaker:

- Se till att principen för objekt replikering är konfigurerad på mål kontot.
- Kontrol lera att mål behållaren fortfarande finns.
- Om käll-bloben har krypterats med en kundanged nyckel som en del av en Skriv åtgärd, kommer objekt replikeringen att Miss Miss sen. Mer information om kundspecifika nycklar finns i [Ange en krypterings nyckel på en begäran till Blob Storage](encryption-customer-provided-keys.md).

## <a name="billing"></a>Fakturering

Objekt replikering medför ytterligare kostnader för Läs-och skriv transaktioner mot käll-och mål kontona, samt utgående kostnader för replikering av data från käll kontot till mål kontot och Läs kostnader för att bearbeta ändrings flödet.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera objektreplikering](object-replication-configure.md)
- [Ändra stöd för feed i Azure Blob Storage](storage-blob-change-feed.md)
- [Aktivera och hantera BLOB-versioner](versioning-enable.md)
