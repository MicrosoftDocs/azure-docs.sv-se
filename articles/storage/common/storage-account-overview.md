---
title: Översikt över lagringskonto
titleSuffix: Azure Storage
description: Lär dig mer om de olika typerna av lagrings konton i Azure Storage. Granska konto namn, prestanda nivåer, åtkomst nivåer, redundans, kryptering, slut punkter och mycket annat.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/02/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d7eca7d8f3cd40f4a3961f0ac478fba290be3041
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279671"
---
# <a name="storage-account-overview"></a>Översikt över lagringskonto

Ett Azure Storage-konto innehåller alla dina Azure Storage data objekt: blobbar, filer, köer, tabeller och diskar. Lagrings kontot tillhandahåller ett unikt namn område för dina Azure Storage data som är tillgängliga från var som helst i världen via HTTP eller HTTPS. Data i ditt Azure Storage-konto är tåliga och mycket tillgängliga, säkra och enorma skalbara.

Information om hur du skapar ett Azure-lagringskonto finns i [Skapa ett lagringskonto](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Typer av lagringskonton

Azure Storage erbjuder flera typer av lagrings konton. Varje typ stöder olika funktioner och har en egen pris modell. Tänk på dessa skillnader innan du skapar ett lagrings konto för att fastställa vilken typ av konto som passar bäst för dina program. Typerna av lagrings konton är:

- **Allmänna-Purpose v2-konton**: grundläggande lagrings konto typ för blobbar, filer, köer och tabeller. Rekommenderas för de flesta scenarier som använder Azure Storage.
- **Generella v1-konton**: typ av äldre konto för blobbar, filer, köer och tabeller. Använd allmänna-Purpose v2-konton i stället när det är möjligt.
- **BlockBlobStorage-konton**: lagrings konton med förstklassiga prestanda egenskaper för block-blobbar och bifogade blobbar. Rekommenderas för scenarier med höga transaktioner, eller scenarier som använder mindre objekt eller kräver konsekvent låg lagrings fördröjning.
- **FileStorage-konton**: endast filer lagrings konton med förstklassiga prestanda egenskaper. Rekommenderas för program med företags-eller hög prestanda skalning.
- **BlobStorage-konton**: äldre BLOB-endast lagrings konton. Använd allmänna-Purpose v2-konton i stället när det är möjligt.

I följande tabell beskrivs de olika typerna av lagrings konton, de tjänster som stöds och de distributions modeller som stöds för varje konto typ:

| Typ av lagringskonto | Tjänster som stöds | Alternativ för redundans | Distributions modell<sup>1</sup> |
|--|--|--|--|
| Allmänt-syfte v2 | BLOB, fil, kö, tabell, disk och Data Lake Gen2<sup>2</sup> | LRS, GRS, RA-GRS, ZRS, GZRS, RA-GZRS<sup>3</sup> | Resource Manager |
| Generell användning v1 | BLOB, fil, kö, tabell och disk | LRS, GRS, RA-GRS | Resource Manager, klassisk |
| BlockBlobStorage | BLOB (endast block-blobar och bifogade blobbar) | LRS, ZRS<sup>3</sup> | Resource Manager |
| FileStorage | Endast fil | LRS, ZRS<sup>3</sup> | Resource Manager |
| BlobStorage | BLOB (endast block-blobar och bifogade blobbar) | LRS, GRS, RA-GRS | Resource Manager |

<sup>1</sup> Vi rekommenderar att du använder Azure Resource Manager distributions modell. Lagrings konton som använder den klassiska distributions modellen kan fortfarande skapas på vissa platser och befintliga klassiska konton fortsätter att stödjas. Mer information finns i [Azure Resource Manager vs. klassisk distribution: förstå distributions modeller och status för dina resurser](../../azure-resource-manager/management/deployment-models.md).

<sup>2</sup> Azure Data Lake Storage Gen2 är en uppsättning funktioner som är avsedda för stor data analys och bygger på Azure Blob Storage. Data Lake Storage Gen2 stöds endast för allmänna-syfte v2-lagrings konton med hierarkiskt namn område aktiverat. Mer information om Data Lake Storage Gen2 finns i [Introduktion till Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md).

<sup>3</sup> Zone-redundant lagring (ZRS) och geo-Zone-redundant lagring (GZRS/RA-GZRS) är endast tillgängliga för allmänna generella v2-, BlockBlobStorage-och FileStorage-konton i vissa regioner. Mer information om alternativ för Azure Storage redundans finns i [Azure Storage redundans](storage-redundancy.md).

### <a name="storage-account-redundancy"></a>Redundans för lagrings konto

Alternativ för redundans för ett lagrings konto är:

- **Lokalt Redundant lagring (LRS)**: en enkel, låg kostnads redundans strategi. Data kopieras synkront tre gånger inom en enda fysisk plats i den primära regionen.
- **Zone-redundant lagring (ZRS)**: redundans för scenarier som kräver hög tillgänglighet. Data kopieras synkront över tre tillgänglighets zoner i Azure i den primära regionen.
- **Geo-redundant lagring (GRS)**: Cross-regional redundans för att skydda mot regionala avbrott. Data kopieras synkront tre gånger i den primära regionen och kopieras sedan asynkront till den sekundära regionen. För Läs åtkomst till data i den sekundära regionen aktiverar du Geo-redundant lagring med Läs behörighet (RA-GRS).
- **Geo-Zone-redundant lagring (GZRS)**: redundans för scenarier som kräver både hög tillgänglighet och maximal hållbarhet. Data kopieras synkront över tre tillgänglighets zoner i Azure i den primära regionen och kopieras sedan asynkront till den sekundära regionen. För Läs åtkomst till data i den sekundära regionen aktiverar du Läs åtkomst geo-Zone-redundant lagring (RA-GZRS).

Mer information om alternativ för redundans i Azure Storage finns [Azure Storage redundans](storage-redundancy.md).

### <a name="general-purpose-v2-accounts"></a>General-purpose v2-konton (GPv2)

Allmänna-Purpose v2-lagrings konton har stöd för de senaste Azure Storage funktionerna och inkluderar alla funktioner i generella v1-och Blob Storage-konton. Allmänna-Purpose v2-konton ger de lägsta priserna per Gigabyte för Azure Storage, samt priser för priser som är konkurrerande för branschen. Allmänna-Purpose v2-lagrings konton har stöd för dessa Azure Storage tjänster:

- Blobbar (alla typer: blockera, Lägg till, sida)
- Data Lake Gen2
- Filer
- Diskar
- Köer
- Tabeller

> [!NOTE]
> Microsoft rekommenderar att du använder ett allmänt-syfte v2-lagrings konto för de flesta scenarier. Du kan enkelt uppgradera ett allmänt v1-eller Blob Storage-konto till ett allmänt-syfte v2-konto utan avbrott och utan att behöva kopiera data. Uppgraderingen kan dock inte återställas.
>
> Mer information om hur du uppgraderar till ett allmänt-syfte v2-konto finns i [Uppgradera till ett allmänt-syfte v2-lagrings konto](storage-account-upgrade.md).

Allmänna-syfte v2-lagrings konton ger flera åtkomst nivåer för lagring av data baserat på dina användnings mönster. Mer information finns i [åtkomst nivåer för block BLOB-data](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>General-purpose v1-konton (GPv1)

Generella v1-lagrings konton ger till gång till alla Azure Storage-tjänster, men de har inte de senaste funktionerna eller de lägsta priset per Gigabyte. Generella v1-lagrings konton har stöd för dessa Azure Storage tjänster:

- Blobbar (alla typer)
- Filer
- Diskar
- Köer
- Tabeller

Du kan använda generella v1-konton i följande scenarier:

- Dina program kräver den klassiska Azure-distributions modellen. Allmänna-Purpose v2-konton och Blob Storage-konton stöder bara Azure Resource Manager distributions modellen.

- Dina program är transaktions intensiva eller använder avsevärd bandbredd för geo-replikering, men kräver inte stor kapacitet. I det här fallet kan generell användning v1 vara det mest ekonomiska valet.

- Du använder en version av [lagrings tjänster REST API](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) som är tidigare än 2014-02-14 eller ett klient bibliotek med en version som är lägre än 4. x. Du kan inte uppgradera ditt program.

> [!NOTE]
> Även om Microsoft rekommenderar General-Purpose v2-konton för de flesta scenarier kommer Microsoft att fortsätta att stödja allmänna v1-konton för nya och befintliga kunder. Du kan skapa generella v1-lagrings konton i nya regioner när Azure Storage är tillgängliga i dessa regioner. Microsoft har för närvarande inte en plan för att föråldra support för allmänna v1-konton och ger minst ett års varsel innan det går att använda en Azure Storage-funktion. Microsoft fortsätter att tillhandahålla säkerhets uppdateringar för generella v1-konton, men ingen ny funktions utveckling förväntas för den här konto typen.
>
> Från och med den 1 oktober 2020 är prissättningen för allmänna v1-konton i nya Azure Storage regioner likvärdig med prissättningen för General-Purpose v2-konton i dessa regioner. Priser i befintliga Azure Storage-regioner har inte ändrats. Pris information för allmänna v1-konton i en speciell region finns på sidan med Azure Storage priser. **Välj din** region och välj sedan bredvid **prissättnings erbjudanden**.

### <a name="blockblobstorage-accounts"></a>BlockBlobStorage-konton

Ett BlockBlobStorage-konto är ett specialiserat lagrings konto i Premium Performance-nivån för att lagra ostrukturerade objekt data som block-blobbar eller bifoga blobbar. Jämfört med General-Purpose v2-och BlobStorage-konton ger BlockBlobStorage-konton låg, konsekvent svars tid och högre transaktions taxa.

BlockBlobStorage-konton stöder för närvarande inte nivåer för frekvent åtkomst, låg frekvent åtkomst eller Arkiv lag rings nivåer. Den här typen av lagrings konto har inte stöd för sid-blobar, tabeller eller köer.

### <a name="filestorage-accounts"></a>FileStorage-konton

Ett FileStorage-konto är ett specialiserat lagrings konto som används för att lagra och skapa Premium-filresurser. Den här typen av lagrings konto stöder filer, men inte blockering av blobbar, tillägg av blobbar, sid-BLOB, tabeller eller köer.

FileStorage-konton ger unika prestanda dedikerade egenskaper som IOPS-burst. Mer information om dessa egenskaper finns i avsnittet [fil resurs lagrings nivåer](../files/storage-files-planning.md#storage-tiers) i planerings guiden för filer.

## <a name="naming-storage-accounts"></a>Namnge lagrings konton

Tänk på dessa regler när du namnger lagringskontot:

- Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.
- Namnet på ditt lagringskonto måste vara unikt i Azure. Det får inte finnas två lagringskonton med samma namn.

## <a name="performance-tiers"></a>Prestandanivåer

Beroende på vilken typ av lagrings konto du skapar kan du välja mellan standard-och Premium prestanda nivåer. I följande tabell sammanfattas vilka prestanda nivåer som är tillgängliga för vilken typ av lagrings konto.

| Typ av lagringskonto | Prestanda nivåer som stöds |
|--|--|
| Allmänt-syfte v2 | Standard, Premium<sup>1</sup> |
| Generell användning v1 | Standard, Premium<sup>1</sup> |
| BlockBlobStorage | Premium |
| FileStorage | Premium |
| BlobStorage | Standard |

<sup>1</sup> Premium-prestanda för General-Purpose v2-och General-Purpose v1-konton är endast tillgängligt för disk-och sid-blob. Premium-prestanda för block-eller append-blobar är bara tillgängliga på BlockBlobStorage-konton. Premium-prestanda för filer är bara tillgängliga på FileStorage-konton.

### <a name="general-purpose-storage-accounts"></a>Allmänna lagringskonton

Allmänna lagrings konton kan konfigureras för någon av följande prestanda nivåer:

- En standard prestanda nivå för att lagra blobbar, filer, tabeller, köer och virtuella Azure-datorer. Mer information om skalbarhets mål för standard lagrings konton finns i [skalbarhets mål för standard lagrings konton](scalability-targets-standard-account.md).
- En Premium Performance-nivå för lagring av ohanterade virtuella dator diskar. Microsoft rekommenderar att du använder hanterade diskar med virtuella Azure-datorer i stället för ohanterade diskar. Mer information om skalbarhets mål för prestanda nivån Premium finns i [skalbarhets mål för Premium Page Blob Storage-konton](../blobs/scalability-targets-premium-page-blobs.md).

### <a name="blockblobstorage-storage-accounts"></a>BlockBlobStorage lagrings konton

BlockBlobStorage lagrings konton ger en förstklassig prestanda nivå för lagring av block-blobbar och tillägg av blobbar. Mer information finns i [skalbarhets mål för Premium Block Blob Storage-konton](../blobs/scalability-targets-premium-block-blobs.md).

### <a name="filestorage-storage-accounts"></a>FileStorage lagrings konton

FileStorage lagrings konton ger en förstklassig prestanda nivå för Azure-filresurser. Mer information finns i [Azure Files skalbarhets-och prestanda mål](../files/storage-files-scale-targets.md).

## <a name="access-tiers-for-block-blob-data"></a>Åtkomst nivåer för block BLOB-data

Azure Storage tillhandahåller olika alternativ för åtkomst till block BLOB-data baserat på användnings mönster. Varje åtkomst nivå i Azure Storage är optimerad för ett visst mönster för data användning. Genom att välja rätt åtkomst nivå för dina behov kan du lagra block BLOB-data på det mest kostnads effektiva sättet.

Tillgängliga åtkomst nivåer är:

- Frekvent **åtkomst nivå** . Den här nivån är optimerad för frekvent åtkomst av objekt i lagrings kontot. Att komma åt data på frekvent nivå är mest kostnads effektivt, medan lagrings kostnaderna är högre. Nya lagrings konton skapas som standard på frekvent nivå.
- Låg **frekvent åtkomst nivå** . Den här nivån är optimerad för att lagra stora mängder data som används sällan och som lagras i minst 30 dagar. Att lagra data i den låg frekventa nivån är mer kostnads effektivt, men att komma åt dessa data kan vara dyrare än att komma åt data på frekvent nivå.
- Nivån **Arkiv**. Den här nivån är endast tillgänglig för enskilda block blobbar. Arkiv nivån är optimerad för data som kan tolerera flera timmars svars tid för hämtning och som kommer att finnas kvar på Arkiv nivån i minst 180 dagar. Arkiv lag rings nivån är det mest kostnads effektiva alternativet för att lagra data. Att komma åt dessa data är dock dyrare än att komma åt data på frekventa eller låg frekventa nivåer.

Om det finns en ändring i användnings mönstret för dina data kan du när som helst växla mellan dessa åtkomst nivåer. Mer information om åtkomst nivåer finns i [Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv](../blobs/storage-blob-storage-tiers.md)lag rings nivåer.

I följande tabell visas vilka åtkomst nivåer som är tillgängliga för blobbar i varje typ av lagrings konto.

| Typ av lagringskonto | Åtkomst nivåer som stöds |
|--|--|
| Allmänt-syfte v2 | Frekvent, låg frekvent, arkiv<sup>1</sup> |
| Generell användning v1 | Ej tillämpligt |
| BlockBlobStorage | Ej tillämpligt |
| FileStorage | Ej tillämpligt |
| BlobStorage | Frekvent, låg frekvent, arkiv<sup>1</sup> |

<sup>1</sup> Arkiv lag ring och skiktning på BLOB-nivå stöder bara block-blobar. Arkiv nivån är bara tillgänglig på nivån för en enskild BLOB, inte på lagrings konto nivå. Mer information finns i [åtkomst nivåer för Azure Blob Storage – frekvent,](../blobs/storage-blob-storage-tiers.md)låg frekvent och Arkiv lag ring.

> [!IMPORTANT]
> Om du ändrar åtkomst nivån för ett befintligt lagrings konto eller BLOB kan ytterligare kostnader uppstå. Mer information finns i [debitering av lagrings konto](#storage-account-billing).

## <a name="storage-account-endpoints"></a>Slutpunkter för lagringskonto

På ett lagringskonto finns ett unikt namnområde i Azure för dina data. Alla objekt som du lagrar i Azure Storage har en adress som innehåller ditt unika kontonamn. Kombinationen av kontonamnet och Azure Storage-tjänstens slutpunkt bildar slutpunkterna för ditt lagringskonto.

I följande tabell visas slut punkterna för var och en av de Azure Storage tjänsterna.

| Lagrings tjänst | Slutpunkt |
|--|--|
| Blob Storage | `https://<storage-account>.blob.core.windows.net` |
| Azure Data Lake Storage Gen2 | `https://<storage-account>.dfs.core.windows.net` |
| Azure Files | `https://<storage-account>.file.core.windows.net` |
| Queue Storage | `https://<storage-account>.queue.core.windows.net` |
| Table Storage | `https://<storage-account>.table.core.windows.net` |

> [!NOTE]
> Block-Blob-och Blob Storage-konton exponerar endast Blob Service-slutpunkten.

Skapa URL: en för att komma åt ett objekt i ett lagrings konto genom att lägga till objektets plats i lagrings kontot till slut punkten. En blobbadress kan till exempel ha följande format: http://*mittlagringskonto*.blob.core.windows.net/*mincontainer*/*minblobb*.

Du kan också konfigurera ditt lagrings konto så att det använder en anpassad domän för blobbar. Mer information finns i [Konfigurera ett anpassat domän namn för ditt Azure Storage-konto](../blobs/storage-custom-domain-name.md).  

## <a name="migrating-a-storage-account"></a>Migrera ett lagrings konto

Följande tabell sammanfattar och pekar på vägledning om hur du flyttar, uppgraderar eller migrerar ett lagrings konto:

| Migreringsscenario | Information |
|--|--|
| Flytta ett lagrings konto till en annan prenumeration | Azure Resource Manager innehåller alternativ för att flytta en resurs till en annan prenumeration. Mer information finns i [Flytta resurser till en ny resurs grupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md). |
| Flytta ett lagrings konto till en annan resurs grupp | Azure Resource Manager innehåller alternativ för att flytta en resurs till en annan resurs grupp. Mer information finns i [Flytta resurser till en ny resurs grupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md). |
| Flytta ett lagrings konto till en annan region | Om du vill flytta ett lagringskonto skapar du en kopia av lagringskontot i en annan region. Sedan flyttar du dina data till det kontot med hjälp av AzCopy eller ett annat verktyg som du själv väljer. Mer information finns i [Flytta ett Azure Storage konto till en annan region](storage-account-move.md). |
| Uppgradera till ett allmänt-syfte v2-lagrings konto | Du kan uppgradera ett allmänt v1-lagrings konto eller Blob Storage-konto till ett allmänt-syfte v2-konto. Observera att den här åtgärden inte kan återställas. Mer information finns i [Uppgradera till ett allmänt-syfte v2-lagrings konto](storage-account-upgrade.md). |
| Migrera ett klassiskt lagrings konto till Azure Resource Manager | Azure Resource Manager distributions modellen är överlägsen för den klassiska distributions modellen vad gäller funktioner, skalbarhet och säkerhet. Mer information om hur du migrerar ett klassiskt lagrings konto till Azure Resource Manager finns i [migrering av lagrings konton](../../virtual-machines/migration-classic-resource-manager-overview.md#migration-of-storage-accounts) i **plattforms stöd för migrering av IaaS-resurser från klassisk till Azure Resource Manager**. |

## <a name="copying-data-into-a-storage-account"></a>Kopiera data till ett lagrings konto

Microsoft tillhandahåller verktyg och bibliotek för att importera data från lokala lagrings enheter eller moln lagrings leverantörer från tredje part. Vilken lösning du använder beror på mängden data som du överför.

När du uppgraderar till ett allmänt-syfte v2-konto från ett allmänt v1-eller Blob Storage-konto migreras dina data automatiskt. Microsoft rekommenderar den här väg för att uppgradera ditt konto. Men om du bestämmer dig för att flytta data från ett allmänt v1-konto till ett Blob Storage-konto migrerar du dina data manuellt med hjälp av de verktyg och bibliotek som beskrivs nedan.

### <a name="azcopy"></a>AzCopy

AzCopy är ett Windows-kommandoradsverktyg för högpresterande kopiering av data till och från Azure Storage. Du kan använda AzCopy för att kopiera data till ett Blob Storage-konto från ett befintligt allmänt lagrings konto, eller för att ladda upp data från lokala lagrings enheter. Mer information finns i [Överföra data med kommandoradsverktyget AzCopy](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Bibliotek för flytt av data

Azure Storage-biblioteket för flytt av data för .NET är baserat på det ramverk för flytt av data som används för AzCopy. Biblioteket är utformat för högpresterande, tillförlitliga och enkla åtgärder för dataöverföring och liknar de som används i AzCopy. Du kan använda biblioteket för data förflyttning för att dra nytta av AzCopy-funktioner internt. Mer information finns i [Azure Storage data flyttnings bibliotek för .net](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>REST-API eller klientbibliotek

Du kan skapa ett anpassat program för att migrera dina data från ett allmänt v1-lagrings konto till ett Blob Storage-konto. Använd en av Azures klient bibliotek eller Azure Storage tjänsterna REST API. Azure Storage innehåller omfattande klientbibliotek för flera språk och plattformar som .NET, Java, C++, Node.JS, PHP, Ruby och Python. Klientbiblioteken har avancerade funktioner, t.ex. logik för omprövning, loggning och parallell överföring. Du kan också utveckla direkt mot REST-API:t, som kan anropas med valfritt språk som kan skicka HTTP/HTTPS-begäranden.

Mer information om Azure Storage REST API finns i [Azure Storage Services REST API Reference](/rest/api/storageservices/).

> [!IMPORTANT]
> Blobar som krypteras med kryptering på klientsidan lagrar krypteringsrelaterade metadata tillsammans med bloben. Om du kopierar en blob som är krypterad med kryptering på klientsidan bör du se till att kopieringen bevarar blobmetadata och framför allt krypteringsrelaterade metadata. Om du kopierar en blob utan krypteringsmetadata kan blobinnehållet inte hämtas igen. Mer information om krypteringsrelaterade metadata finns i [Azure Storage Client Side Encryption](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="encryption"></a>Kryptering

Alla data i ditt lagrings konto krypteras på tjänst sidan. Mer information om kryptering finns i [Azure Storage tjänst kryptering för vilande data](storage-service-encryption.md).

## <a name="storage-account-billing"></a>Fakturering för lagringskonto

Azure Storage räkningar baserat på lagrings kontots användning. Alla objekt i ett lagringskonto faktureras tillsammans som en grupp. Lagrings kostnaderna beräknas enligt följande faktorer:

- **Region** syftar på den geografiska region där ditt konto är baserat.
- **Konto typ** avser den typ av lagrings konto som du använder.
- **Åtkomst nivån** syftar på det data användnings mönster som du har angett för ditt General-Purpose v2-eller Blob Storage-konto.
- **Kapaciteten** avser hur mycket av din tilldelning av lagrings konto som du använder för att lagra data.
- **Replikeringen** avgör hur många kopior av dina data som bevaras samtidigt och på vilka platser.
- **Transaktioner** avser alla Läs-och skriv åtgärder till Azure Storage.
- **Utgående data** syftar på data som överförs från en Azure-region. När data i ditt lagrings konto används av ett program som inte körs i samma region debiteras du för utgående data. Information om hur du använder resurs grupper för att gruppera dina data och tjänster i samma region för att begränsa utgående kostnader finns i [Vad är en Azure-resurs grupp?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group).

Sidan [Pris för Azure Storage](https://azure.microsoft.com/pricing/details/storage/) innehåller detaljerad prisinformation baserat på kontotyp, lagringskapacitet, replikering och transaktioner. Sidan [Prisinformation för dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/) innehåller detaljerad prisinformation för utgående datatrafik. Du kan använda [priskalkylatorn för Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) för att hjälpa att uppskatta dina kostnader.

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Nästa steg

- [Skapa ett lagringskonto](storage-account-create.md)
- [Skapa ett blockblobslagringskonto](../blobs/storage-blob-create-account-block-blob.md)
- [Uppgradera till ett allmänt-syfte v2-lagrings konto](storage-account-upgrade.md)
- [Återställa ett borttaget lagringskonto](storage-account-recover.md)