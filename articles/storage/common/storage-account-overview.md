---
title: Översikt över lagringskonto
titleSuffix: Azure Storage
description: Läs en översikt över lagrings konton i Azure Storage. Granska konto namn, prestanda nivåer, åtkomst nivåer, redundans, kryptering, slut punkter och mycket annat.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 2c9c4cd643e2e4b89f9a7d8f44a6569d0dde2b37
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357389"
---
# <a name="storage-account-overview"></a>Översikt över lagringskonto

Ett Azure Storage-konto innehåller alla dina Azure Storage data objekt: blobbar, filer, köer, tabeller och diskar. Lagrings kontot tillhandahåller ett unikt namn område för dina Azure Storage data som är tillgängliga från var som helst i världen via HTTP eller HTTPS. Data i ditt Azure Storage-konto är tåliga och mycket tillgängliga, säkra och enorma skalbara.

Information om hur du skapar ett Azure-lagringskonto finns i [Skapa ett lagringskonto](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Typer av lagringskonton

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>General-purpose v2-konton (GPv2)

Allmänna-Purpose v2-lagrings konton har stöd för de senaste Azure Storage funktionerna och inkluderar alla funktioner i generella v1-och Blob Storage-konton. Allmänna-Purpose v2-konton ger de lägsta priserna per Gigabyte för Azure Storage, samt priser för priser som är konkurrerande för branschen. Allmänna-Purpose v2-lagrings konton har stöd för dessa Azure Storage tjänster:

- Blobbar (alla typer: blockera, Lägg till, sida)
- Data Lake Gen2
- Files
- Diskar
- Köer
- Tabeller

> [!NOTE]
> Microsoft rekommenderar att du använder ett allmänt-syfte v2-lagrings konto för de flesta scenarier. Du kan enkelt uppgradera ett allmänt v1-eller Blob Storage-konto till ett allmänt-syfte v2-konto utan avbrott och utan att behöva kopiera data.
>
> Mer information om hur du uppgraderar till ett allmänt-syfte v2-konto finns i [Uppgradera till ett allmänt-syfte v2-lagrings konto](storage-account-upgrade.md).

Allmänna-syfte v2-lagrings konton ger flera åtkomst nivåer för lagring av data baserat på dina användnings mönster. Mer information finns i [åtkomst nivåer för block BLOB-data](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>General-purpose v1-konton (GPv1)

Generella v1-lagrings konton ger till gång till alla Azure Storage-tjänster, men de har inte de senaste funktionerna eller de lägsta priset per Gigabyte. Generella v1-lagrings konton har stöd för dessa Azure Storage tjänster:

- Blobbar (alla typer)
- Files
- Diskar
- Köer
- Tabeller

Microsoft rekommenderar General-Purpose v2-konton för de flesta scenarier. Du kan använda generella v1-konton i följande scenarier:

- Dina program kräver den klassiska Azure-distributions modellen. Allmänna-Purpose v2-konton och Blob Storage-konton stöder bara Azure Resource Manager distributions modellen.

- Dina program är transaktions intensiva eller använder avsevärd bandbredd för geo-replikering, men kräver inte stor kapacitet. I det här fallet kan generell användning v1 vara det mest ekonomiska valet.

- Du använder en version av [lagrings tjänster REST API](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) som är tidigare än 2014-02-14 eller ett klient bibliotek med en version som är lägre än 4. x. Du kan inte uppgradera ditt program.

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

Beroende på vilken typ av lagrings konto du skapar kan du välja mellan standard-och Premium prestanda nivåer.

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

> [!IMPORTANT]
> Om du ändrar åtkomst nivån för ett befintligt lagrings konto eller BLOB kan ytterligare kostnader uppstå. Mer information finns i [avsnittet fakturering av lagrings konto](#storage-account-billing).

## <a name="redundancy"></a>Redundans

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

## <a name="encryption"></a>Kryptering

Alla data i ditt lagrings konto krypteras på tjänst sidan. Mer information om kryptering finns i [Azure Storage tjänst kryptering för vilande data](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Slutpunkter för lagringskonto

På ett lagringskonto finns ett unikt namnområde i Azure för dina data. Alla objekt som du lagrar i Azure Storage har en adress som innehåller ditt unika kontonamn. Kombinationen av kontonamnet och Azure Storage-tjänstens slutpunkt bildar slutpunkterna för ditt lagringskonto.

Om ditt allmänna lagrings konto till exempel heter *mystorageaccount*, är standard slut punkterna för det kontot:

- Blob-lagring: `https://*mystorageaccount*.blob.core.windows.net`
- Tabell lagring: `https://*mystorageaccount*.table.core.windows.net`
- Queue Storage: `https://*mystorageaccount*.queue.core.windows.net`
- Azure Files: `https://*mystorageaccount*.file.core.windows.net`
- Azure Data Lake Storage Gen2: `https://*mystorageaccount*.dfs.core.windows.net` (använder ABFS-drivrutinen som är [optimerad för Big data](../blobs/data-lake-storage-introduction.md#key-features-of-data-lake-storage-gen2).)

> [!NOTE]
> Block-Blob-och Blob Storage-konton exponerar endast Blob Service-slutpunkten.

Skapa URL: en för att komma åt ett objekt i ett lagrings konto genom att lägga till objektets plats i lagrings kontot till slut punkten. En blobbadress kan till exempel ha följande format: http://*mittlagringskonto*.blob.core.windows.net/*mincontainer*/*minblobb*.

Du kan också konfigurera ditt lagrings konto så att det använder en anpassad domän för blobbar. Mer information finns i [Konfigurera ett anpassat domän namn för ditt Azure Storage-konto](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Kontrol lera åtkomst till konto data

Som standard är data i ditt konto endast tillgängliga för dig, kontoägaren. Du har kontroll över vem som kan komma åt dina data och vilka behörigheter de har.

Varje begäran som görs mot ditt lagrings konto måste vara auktoriserad. På tjänst nivån måste begäran innehålla ett giltigt *Authorization* -huvud. Mer specifikt innehåller rubriken all information som behövs för att tjänsten ska verifiera begäran innan den körs.

Du kan bevilja åtkomst till data i ditt lagrings konto med någon av följande metoder:

- **Azure Active Directory:** Använd Azure Active Directory (Azure AD) autentiseringsuppgifter för att autentisera en användare, grupp eller annan identitet för åtkomst till blob-och Queue-data. Om autentiseringen av en identitet lyckas returnerar Azure AD en token som ska användas för att auktorisera begäran till Azure Blob Storage eller Queue Storage. Mer information finns i [autentisera åtkomst till Azure Storage med hjälp av Azure Active Directory](storage-auth-aad.md).
- **Auktorisering av delad nyckel:** Använd din åtkomst nyckel för lagrings kontot för att skapa en anslutnings sträng som programmet använder vid körning för att få åtkomst till Azure Storage. Värdena i anslutnings strängen används för att skapa ett *Authorization* -huvud som skickas till Azure Storage. Mer information finns i [Konfigurera anslutnings strängar för Azure Storage](storage-configure-connection-string.md).
- **Signatur för delad åtkomst:** En signatur för delad åtkomst (SAS) är en token som tillåter delegerad åtkomst till resurser i ditt lagrings konto. SAS-token kapslar in all information som krävs för att auktorisera en begäran om att Azure Storage på URL: en. När du skapar en SAS kan du ange vilka behörigheter som SAS ger till en resurs och intervallet som behörigheterna är giltiga för. En SAS-token kan signeras med antingen Azure AD-autentiseringsuppgifter eller med delad nyckel. Mer information finns i [bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av signaturer för delad åtkomst (SAS)](storage-sas-overview.md).

> [!NOTE]
> Autentisering av användare eller program som använder Azure AD-autentiseringsuppgifter ger överlägsen säkerhet och lätt att använda för andra auktoriserings metoder. Även om du kan fortsätta att använda autentisering med delad nyckel med dina program kan du använda Azure AD för att kringgå behovet av att lagra din konto åtkomst nyckel med din kod. Du kan även fortsätta att använda signaturer för delad åtkomst (SAS) för att ge detaljerad åtkomst till resurser i ditt lagrings konto, men Azure AD erbjuder liknande funktioner utan att behöva hantera SAS-token eller oroa dig för att återkalla en komprometterad SAS.
>
> Microsoft rekommenderar att du använder Azure AD-auktorisering för Azure Storage blob-och Queue-program när det är möjligt.

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

## <a name="storage-account-billing"></a>Fakturering för lagringskonto

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Nästa steg

- [Skapa ett lagringskonto](storage-account-create.md)
- [Skapa ett blockblobslagringskonto](../blobs/storage-blob-create-account-block-blob.md)
- [Uppgradera till ett V2-lagringskonto för generell användning](storage-account-upgrade.md)
- [Återställa ett borttaget lagringskonto](storage-account-recover.md)