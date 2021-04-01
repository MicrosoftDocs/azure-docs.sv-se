---
title: Introduktion till Azure Storage – Molnlagring i Azure | Microsoft Docs
description: Core Azure Storage Platform är Microsofts moln lagrings lösning. Azure Storage tillhandahåller lagring för dataobjekt som har hög tillgänglighet och är säkra, robusta, mycket skalbara och redundanta.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: b49e780714bd5e0662c6cb7ef208c473a8b45ccc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96498157"
---
# <a name="introduction-to-the-core-azure-storage-services"></a>Introduktion till Core Azure Storage Services

Azure Storages plattformen är Microsofts moln lagrings lösning för moderna data lagrings scenarier. Core Storage Services erbjuder en enorm skalbar objekt lagring för data objekt, disk lagring för virtuella Azure-datorer (VM), en fil system tjänst för molnet, ett meddelande arkiv för Reliable Messaging och ett NoSQL-lager. Tjänsterna är:

- **Pålitlig och mycket tillgänglig.** Redundans garanterar att dina data är säkra i händelse av tillfälliga maskinvarufel. Du kan också välja att replikera data i datacenter eller geografiska regioner för ytterligare skydd mot lokala allvarliga händelser eller naturkatastrofer. Data som replikeras på det här sättet förblir mycket tillgängliga vid ett oväntat avbrott.
- **Skydda.** Alla data som skrivs till ett Azure Storage-konto krypteras av tjänsten. Med Azure Storage får du detaljerad kontroll över vem som har tillgång till dina data.
- **Scalable.** Azure Storage är utformat för att vara mycket skalbart för att uppfylla krav på datalagring och prestanda för dagens program.
- **Leda.** Azure hanterar maskin varu underhåll, uppdateringar och kritiska problem åt dig.
- **Åtkomlig.** Data i Azure Storage är åtkomlig från hela världen via HTTP eller HTTPS varifrån som helst. Microsoft tillhandahåller klient bibliotek för Azure Storage på flera olika språk, t. ex. .NET, Java, Node.js, python, PHP, ruby, go med mera, samt en vuxen REST API. Azure Storage stöder skriptning i Azure PowerShell eller Azure CLI. Azure-portalen och Azure Storage Explorer erbjuder även enkla visuella lösningar för att arbeta med dina data.  

## <a name="core-storage-services"></a>Kärn lagrings tjänster

Azure Storage plattform innehåller följande data tjänster:

- [Azure Blobs](../blobs/storage-blobs-introduction.md): En mycket skalbar objektlagring för textdata och binära data. Innehåller även stöd för stor data analys via Data Lake Storage Gen2.
- [Azure Files](../files/storage-files-introduction.md): hanterade fil resurser för molnbaserade eller lokala distributioner.
- [Azure Queues](../queues/storage-queues-introduction.md): Ett meddelandearkiv för tillförlitliga meddelandefunktioner mellan programkomponenter.
- [Azure Tables](../tables/table-storage-overview.md): En NoSQL-lagring av schemalös lagring av strukturerade data.
- [Azure-diskar](../../virtual-machines/managed-disks-overview.md): lagrings volymer på Block nivå för virtuella Azure-datorer.

Varje tjänst nås via ett lagringskonto. För att komma igång läser du [Skapa ett lagringskonto](storage-account-create.md).

## <a name="example-scenarios"></a>Exempelscenarier

I följande tabell jämförs filer, blobbar, diskar, köer och tabeller, och exempel scenarier för var och en visas.

| Funktion | Beskrivning | När du ska använda detta |
|--------------|-------------|-------------|
| **Azure Files** |Erbjuder fullständigt hanterade moln fil resurser som du kan komma åt var som helst via SMB-protokollet (Server Message Block) i branschen.<br><br>Du kan montera Azure-filresurser från molnet eller lokala distributioner av Windows, Linux och macOS. | Du vill "lyfta och byta" ett program till molnet som redan använder inbyggda API: er för fil system för att dela data mellan dem och andra program som körs i Azure.<br/><br/>Du vill ersätta eller komplettera lokala fil servrar eller NAS-enheter.<br><br> Du vill lagra utvecklings-och fel söknings verktyg som behöver nås från många virtuella datorer. |
| **Azure-blobar** | Tillåter att ostrukturerade data lagras och används i en enorm skala i block-blobar.<br/><br/>Stöder också [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) för företags lösningar för Big data analys. | Du vill att ditt program ska stödja strömnings-och slumpmässiga åtkomst scenarier.<br/><br/>Du vill kunna komma åt program data var som helst.<br/><br/>Du vill skapa ett företags data Lake på Azure och utföra stor data analys. |
| **Azure-diskar** | Tillåter att data lagras permanent och nås från en ansluten virtuell hård disk. | Du vill använda "lyft och Shift"-program som använder interna fil system-API: er för att läsa och skriva data till beständiga diskar.<br/><br/>Du vill lagra data som inte behöver nås från utanför den virtuella dator som disken är ansluten till. |
| **Azure Queues** | Tillåter asynkron meddelande kön mellan program komponenter. | Du vill koppla bort program komponenter och använda asynkrona meddelanden för att kommunicera mellan dem.<br><br>Information om hur du använder Queue Storage och Service Bus köer finns i [lagrings köer och Service Bus köer – jämförelse och kontrast](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md). |
| **Azure-tabeller** | Gör att du kan lagra strukturerade NoSQL-data i molnet och tillhandahålla ett nyckel-attributarkiv med en schema lös design. | Du vill lagra flexibla data uppsättningar som användar data för webb program, adress böcker, enhets information eller andra typer av metadata som din tjänst kräver. <br/><br/>Information om hur du använder tabell lagring jämfört med Azure Cosmos DB Tabell-API finns i [utveckla med Azure Cosmos DB tabell-API och Azure Table Storage](../../cosmos-db/table-support.md). |

## <a name="blob-storage"></a>Blob Storage

Azure Blob Storage är Microsofts objektlagringslösning för molnet. Blob Storage är optimerat för att lagra stora mängder ostrukturerade data, exempelvis text eller binära data.

Blob Storage är perfekt för att:

- Leverera bilder eller dokument direkt till en webbläsare.
- Lagra filer för distribuerad åtkomst.
- Direktuppspelning av video och ljud.
- Lagra data för säkerhetskopiering och återställning, haveriberedskap och arkivering.
- Lagra data för analys av en tjänst som kan vara lokal eller Azure-värdbaserad.

Objekt i Blob storage kan nås från vilken plats som helst i världen via HTTP eller HTTPS. Användare eller klientprogram kan få åtkomst till blobar via URL-adresser, [Azure Storage REST API](/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](/powershell/module/azure.storage), [Azure CLI](/cli/azure/storage) eller ett Azure Storage-klientbibliotek. Det finns lagringsklientbibliotek för flera språk, bland annat [.NET](/dotnet/api/overview/azure/storage), [Java](/java/api/overview/azure/storage), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) och [Ruby](https://azure.github.io/azure-storage-ruby).

Mer information om Blob Storage finns i [Introduktion till bloblagring](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>Azure Files

Med [Azure Files](../files/storage-files-introduction.md) kan du konfigurera nätverks fil resurser med hög tillgänglighet som kan nås med hjälp av SMB-standardprotokollet (Server Message Block). Det innebär att flera virtuella datorer kan dela samma filer med både läs- och skrivbehörighet. Du kan också läsa filerna med hjälp av REST-gränssnittet eller klientbiblioteken för lagring.

En sak som skiljer Azure Files från filer på en företagsfilresurs är att du kan komma åt filerna var som helst i världen med en URL som pekar på filen och som innehåller en SAS-token (signatur för delad åtkomst). Du kan generera SAS-token, som ger specifik åtkomst till en privat resurs under en viss tidsperiod.

Filresurser kan användas för många vanliga scenarier:

- Många lokala program använder filresurser. Den här funktionen gör det enklare att migrera program som delar data till Azure. Om du monterar filresursen med samma enhetsbeteckning som det lokala programmet använder, bör den del av programmet som använder filresursen fungera med minimala, om några, ändringar.

- Konfigurationsfiler kan lagras på en filresurs och nås från flera virtuella datorer. Verktyg och hjälpmedel som används av flera utvecklare i ett team kan lagras på en filresurs, så att alla enkelt kan hitta dem och för att säkerställa att alla använder samma version.

- Resurs loggar, Mät värden och krasch dum par är bara tre exempel på data som kan skrivas till en fil resurs och bearbetas eller analyseras senare.

Mer information om Azure Files finns i [Introduktion till Azure Files](../files/storage-files-introduction.md).

Vissa SMB-funktioner kan inte användas i molnet. Mer information finns i [funktioner som inte stöds av Azure File Service](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).

## <a name="queue-storage"></a>Queue Storage

Azure-kötjänsten används för att lagra och hämta meddelanden. Kömeddelanden kan vara upp till 64 kB och en kö kan innehålla miljontals meddelanden. Köer används vanligtvis för att lagra listor med meddelanden som ska bearbetas asynkront.

Anta exempelvis att du vill att kunderna ska kunna ladda upp bilder och du vill skapa miniatyrer för varje bild. Du kan låta kunden vänta på att du skapar miniatyrerna medan bilderna laddas upp. Ett alternativ är att använda en kö. När kunden är klar med överföringen skriver du ett meddelande till kön. Sedan hämtar en Azure-funktion meddelandet från kön och skapar miniatyrerna. Alla delar av bearbetningen kan skalas separat, vilket ger dig större kontroll när du anpassar den för din användning.

Mer information om Azure Queues finns i [introduktionen till Queues](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Table Storage

Azure Table Storage är nu en del av Azure Cosmos DB. Du hittar dokumentationen till Azure Table Storage i [Översikt över Azure Table Storage](../tables/table-storage-overview.md). Utöver den befintliga Azure Table Storage-tjänsten finns ett nytt tabell-API för Azure Cosmos DB som tillhandahåller genomströmningsoptimerade tabeller, global distribution och automatiska sekundärindex. Mer information och testa den nya Premium-upplevelsen finns i [Azure Cosmos DB tabell-API](../../cosmos-db/table-introduction.md).

Mer information om Table Storage finns i [översikten över Azure Table Storage](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Disklagring

En Azure-hanterad disk är en virtuell hård disk (VHD). Du kan tänka på det som en fysisk disk på en lokal server, men virtualiserad. Azure-hanterade diskar lagras som Page BLOB-objekt, som är ett slumpmässigt IO-lagringssystem i Azure. Vi kallar en hanterad disk hanterad eftersom det är en abstraktion över Page blobbar, BLOB-behållare och Azure Storage-konton. Med hanterade diskar behöver du bara etablera disken och Azure tar hand om resten.

Mer information om hanterade diskar finns i [Introduktion till Azure Managed disks](../../virtual-machines/managed-disks-overview.md).

## <a name="types-of-storage-accounts"></a>Typer av lagringskonton

Azure Storage erbjuder flera typer av lagrings konton. Varje typ stöder olika funktioner och har en egen pris modell. Mer information om typer av lagringskonton finns i [Översikt över Azure Storage-konton](storage-account-overview.md).

## <a name="secure-access-to-storage-accounts"></a>Säker åtkomst till lagrings konton

Varje begäran till Azure Storage måste vara auktoriserad. Azure Storage stöder följande autentiseringsmetoder:

- **Azure Active Directory (Azure AD)-integration för blob-och Queue-data.** Azure Storage stöder autentisering och auktorisering med Azure AD för blob-och Queue Services via rollbaserad åtkomst kontroll i Azure (Azure RBAC). Att auktorisera begär Anden med Azure AD rekommenderas för överlägsen säkerhet och enkel användning. Mer information finns i [bevilja åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](storage-auth-aad.md).
- **Azure AD-auktorisering över SMB för Azure Files.** Azure Files stöder identitetsbaserade auktorisering över SMB (Server Message Block) via antingen Azure Active Directory Domain Services (Azure AD DS) eller lokalt Active Directory Domain Services (för hands version). Dina domänanslutna virtuella Windows-datorer har åtkomst till Azure-filresurser med Azure AD-autentiseringsuppgifter. Mer information finns i [Översikt över Azure Files Identity-based Authentication-stöd för SMB-åtkomst](../files/storage-files-active-directory-overview.md) och [planera för en Azure Files-distribution](../files/storage-files-planning.md#identity).
- **Auktorisering med delad nyckel.** Azure Storage Blob-, filer-, kö-och tabell tjänster har stöd för auktorisering med delad nyckel. En klient som använder autentisering med delad nyckel skickar ett huvud till varje begäran som är signerad med lagrings kontots åtkomst nyckel. Mer information finns i [auktorisera med delad nyckel](/rest/api/storageservices/authorize-with-shared-key).
- **Auktorisering med signaturer för delad åtkomst (SAS).** En signatur för delad åtkomst (SAS) är en sträng som innehåller en säkerhetstoken som kan läggas till i URI: n för en lagrings resurs. Säkerhetstoken kapslar in begränsningar, till exempel behörigheter och åtkomst intervallet. Mer information finns i [använda signaturer för delad åtkomst (SAS)](storage-sas-overview.md).
- **Anonym åtkomst till behållare och blobbar.** En behållare och dess blobbar kan vara offentligt tillgängliga. När du anger att en behållare eller BLOB är offentlig, kan vem som helst läsa den anonymt. Ingen autentisering krävs. Mer information finns i [Hantera anonym Läs behörighet till behållare och blobbar](../blobs/anonymous-read-access-configure.md).

## <a name="encryption"></a>Kryptering

Det finns två grundläggande typer av kryptering för kärn lagrings tjänsterna. Mer information om säkerhet och kryptering finns i [Säkerhetsguiden för Azure Storage](../blobs/security-recommendations.md).

### <a name="encryption-at-rest"></a>Kryptering i vila

Azure Storage kryptering skyddar och skyddar dina data så att de uppfyller organisationens säkerhets-och efterlevnads åtaganden. Azure Storage krypterar automatiskt alla data innan de bevaras kvar på lagrings kontot och dekrypterar det innan hämtning. Processerna för kryptering, dekryptering och nyckel hantering är transparenta för användarna. Kunder kan också välja att hantera sina egna nycklar med hjälp av Azure Key Vault. Mer information finns i [Azure Storage kryptering för vilande data](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Kryptering av klientsidan

De Azure Storage klient biblioteken innehåller metoder för att kryptera data från klient biblioteket innan de skickas över kabeln och Dekrypteringen av svaret. Data som krypteras via klient sidans kryptering krypteras också i vila genom att Azure Storage. Mer information om kryptering på klient sidan finns i [kryptering på klient sidan med .net för Azure Storage](storage-client-side-encryption.md).

## <a name="redundancy"></a>Redundans

För att säkerställa att dina data är beständiga, Azure Storage lagra flera kopior av dina data. När du konfigurerar ditt lagringskonto väljer du ett redundansalternativ. Läs mer i [Redundansalternativ för Azure Storage](./storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="transfer-data-to-and-from-azure-storage"></a>Överföra data till och från Azure Storage

Du kan flytta data till eller från Azure Storage på flera olika sätt. Vilket alternativ du väljer beror på storleken på datauppsättningen och din nätverksbandbredd. Mer information finns i [Välja en Azure-lösning för dataöverföring](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Priser

När du fattar beslut om hur dina data lagras och används bör du också tänka på vilka kostnader som ingår. Mer information finns i [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/).

## <a name="storage-apis-libraries-and-tools"></a>API:er, bibliotek och verktyg för Azure Storage

Du kan komma åt resurser i ett lagrings konto på valfritt språk som kan göra HTTP/HTTPS-begäranden. Dessutom erbjuder Core Azure Storage Services programmerings bibliotek för flera populära språk. Dessa bibliotek förenklar många aspekter av arbetet med Azure Storage genom att hantera information som till exempel synkrona och asynkrona anrop, massbearbetning av åtgärder, undantagshantering, automatiska omförsök, funktionsbeteenden och så vidare. Bibliotek är för närvarande tillgängliga för följande språk och plattformar, med andra i pipeline:

### <a name="azure-storage-data-api-and-library-references"></a>Referenser till Azure Storage-data-API och -bibliotek

- [Azure Storage REST API](/rest/api/storageservices/)
- [Azure Storage klient bibliotek för .NET](/dotnet/api/overview/azure/storage)
- [Azure Storage klient bibliotek för Java/Android](/java/api/overview/azure/storage)
- [Azure Storage klient bibliotek för Node.js](/javascript/api/overview/azure/storage-overview)
- [Azure Storage klient bibliotek för python](https://github.com/Azure/azure-storage-python)
- [Azure Storage klient bibliotek för PHP](https://github.com/Azure/azure-storage-php)
- [Azure Storage klient bibliotek för ruby](https://github.com/Azure/azure-storage-ruby)
- [Azure Storage klient bibliotek för C++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Referenser till Azure Storage-hanterings-API och -bibliotek

- [REST-API för Storage Resource Provider](/rest/api/storagerp/)
- [Klientbibliotek för Storage Resource Provider för .NET](/dotnet/api/overview/azure/storage/management)
- [REST-API för Service Management för Storage (klassisk)](/previous-versions/azure/reference/ee460790(v=azure.100))

### <a name="azure-storage-data-movement-api-and-library-references"></a>Referenser till Azure Storage-dataflyttnings-API och -bibliotek

- [REST-API för Storage Import/Export Service](/rest/api/storageimportexport/)
- [Klientbibliotek för Storage Data Movement för .NET](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>Verktyg och hjälpmedel

- [Azure PowerShell-cmdletar för Storage](/powershell/module/az.storage)
- [Azure CLI-cmdletar för Storage](/cli/azure/storage)
- [AzCopy Command-Line-verktyg](https://aka.ms/downloadazcopy)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
- [Azure Resource Manager mallar för Azure Storage](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång med Core Azure Storage Services finns i [skapa ett lagrings konto](storage-account-create.md).