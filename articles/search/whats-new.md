---
title: Vad är nytt i Azure Kognitiv sökning
description: Meddelanden om nya och förbättrade funktioner, inklusive tjänst byte för Azure Search till Azure Kognitiv sökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 11/12/2020
ms.custom: references_regions
ms.openlocfilehash: f38725f4f00b4b21edc1f564654d1d5137aa2915
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201985"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Vad är nytt i Azure Kognitiv sökning

Läs om vad som är nytt i tjänsten. Skapa ett bok märke för den här sidan för att hålla dig uppdaterad med tjänsten. Läs för [hands versions funktions listan](search-api-preview.md) för att visa funktioner i offentlig för hands version.

## <a name="november-2020"></a>November 2020

|Zoomfunktionen&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategori | Beskrivning | Tillgänglighet  |
|------------------------------|----------|-------------|---------------|
|[Kundhanterad nyckel kryptering över indexerare, data källor och färdighetsuppsättningar](search-security-manage-encryption-keys.md) | Säkerhet | Detta tillägg utökar kundhanterad kryptering över hela det antal till gångar som skapas och hanteras av en Sök tjänst. Kom ihåg att det Kundhanterade nyckel stödet lägger till ytterligare ett krypterings lager utöver grundläggande kryptering som utförs och hanteras av Microsoft. | Allmänt tillgänglig med REST API-version = 2020-06-30 |  

## <a name="september-2020"></a>September 2020

Skapa en identitet för en Sök tjänst i Azure Active Directory och Använd sedan Azure RBAC-behörigheter för att ge identiteten Läs behörighet till Azure-datakällor. Du kan också välja funktionen [undantag för betrodda tjänster](search-indexer-howto-access-trusted-service-exception.md) om IP-regler inte är ett alternativ.

|Zoomfunktionen&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategori | Beskrivning | Tillgänglighet  |
|------------------------------|----------|-------------|---------------|
| [Hanterad tjänst identitet](search-howto-managed-identities-data-sources.md) | Indexerare, säkerhet | Skapa en identitet för en Sök tjänst i Azure Active Directory och Använd sedan Azure RBAC-behörigheter för att bevilja åtkomst till Azure-datakällor. Den här metoden eliminerar behovet av autentiseringsuppgifter för anslutnings strängen. <br><br>Ett annat sätt att använda en hanterad tjänst identitet är via ett [betrott undantag](search-indexer-howto-access-trusted-service-exception.md) om IP-regler inte är ett alternativ. | Allmänt tillgänglig. Använd den här funktionen när du använder portalen eller [skapa data källa (rest)](/rest/api/searchservice/create-data-source) med API-version = 2020-06-30. |
| [Utgående begär Anden med en privat länk](search-indexer-howto-access-private.md) | Indexerare, säkerhet | Skapa en delad privat länk resurs som indexerare kan använda för att få åtkomst till Azure-resurser som skyddas av en privat Azure-länk. Mer information om alla sätt som du kan använda för att skydda indexerings anslutningar finns i [indexerare åtkomst till innehåll som skyddas av Azure Network Security-funktioner](search-indexer-securing-resources.md). | Allmänt tillgänglig. Använd den här funktionen när du använder portalen eller den [delade privata länk resursen](/rest/api/searchmanagement/sharedprivatelinkresources) med API-version = 2020-08-01. |
| [Hanterings REST API (2020-08-01)](/rest/api/searchmanagement/management-api-versions) | REST | Ny stabil REST API ger stöd för att skapa delade privata länk resurser. | Allmänt tillgänglig. |
| [Hanterings REST API (2020-08-01-för hands version)](/rest/api/searchmanagement/management-api-versions) | REST | Lägger till delad privat länk resurs för Azure Functions och Azure SQL för MySQL-databaser. | Offentlig för hands version. |
| [Hantering .NET SDK 4,0](/dotnet/api/overview/azure/search/management) | .NET SDK | Azure SDK-uppdatering för hanterings-SDK, riktad REST API version 2020-08-01. | Allmänt tillgänglig. |

## <a name="august-2020"></a>Augusti 2020

|Zoomfunktionen&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategori | Beskrivning | Tillgänglighet  |
|---------|------------------|-------------|---------------|
| [dubbel kryptering](search-security-overview.md#encryption) | Säkerhet | Aktivera dubbel kryptering på lagrings nivån genom att konfigurera kundhanterad nyckel kryptering på nya Sök tjänster. Skapa en ny tjänst, [Konfigurera och Använd Kundhanterade nycklar](search-security-manage-encryption-keys.md) för index eller synonyma kartor och dra nytta av dubbel kryptering över det innehållet. | Allmänt tillgänglig på alla Sök tjänster som skapats efter den 1 augusti 2020 i dessa regioner: västra USA 2, östra USA, södra centrala USA, US Gov, Virginia, US Gov, Arizona. Använd portalen, hanterings REST API: er eller SDK: er för att skapa tjänsten. |

## <a name="july-2020"></a>Juli 2020

|Zoomfunktionen&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategori | Beskrivning | Tillgänglighet  |
|---------|------------------|-------------|---------------|
| [Azure.Search.Docklient bibliotek för uments](/dotnet/api/overview/azure/search.documents-readme) | Azure SDK för .NET | .NET-klient bibliotek som släpps av Azure SDK-teamet och har utformats för konsekvens med andra .NET-klient bibliotek. <br/><br/>Version 11 riktar sig mot search REST API-version = 2020-06-30, men har ännu inte stöd för kunskaps lager eller geospatiala typer. <br/><br/>Mer information finns i  [snabb start: skapa ett index](search-get-started-dotnet.md) och [Uppgradera till Azure.Search.Documents (V11)](search-dotnet-sdk-migration-version-11.md). | Allmänt tillgänglig. </br> Installera [Azure.Search.Documents-paketet](https://www.nuget.org/packages/Azure.Search.Documents/) från NuGet. |
| [azure.search.docklient bibliotek för uments](/python/api/overview/azure/search-documents-readme)  | Azure SDK för Python| Python-klientcertifikat som publicerats av Azure SDK-teamet och som utformats för konsekvens med andra python-klient bibliotek. <br/><br/>Version 11 riktar sig mot search REST API-version = 2020-06-30. | Allmänt tillgänglig. </br> Installera [paketet Azure-Search-Documents](https://pypi.org/project/azure-search-documents/) från pypi. |
| [@azure/search-documents klient bibliotek](/javascript/api/overview/azure/search-documents-readme)  | Azure SDK för Java Script | JavaScript-klientcertifikat som publicerats av Azure SDK-teamet och som har utformats för konsekvens med andra Java Script-klient bibliotek. <br/><br/>Version 11 riktar sig mot search REST API-version = 2020-06-30. | Allmänt tillgänglig. </br> Installera [ @azure/search-documents paketet](https://www.npmjs.com/package/@azure/search-documents) från NPM. |

## <a name="june-2020"></a>Juni 2020

|Zoomfunktionen&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategori | Beskrivning | Tillgänglighet  |
|---------|------------------|-------------|---------------|
[Knowledge Store](knowledge-store-concept-intro.md) | AI-berikning | Utdata från en AI-berikad indexerare som lagrar innehåll i Azure Storage för användning i andra appar och processer. | Allmänt tillgänglig. </br> Använd [sök REST API 2020-06-30](/rest/api/searchservice/) eller senare, eller portalen. |
| [Sök REST API 2020-06-30](/rest/api/searchservice/) | REST | En ny stabil version av REST-API: erna. Förutom kunskaps lager innehåller den här versionen förbättringar för att söka efter relevans och poäng. | Allmänt tillgänglig. |
| [Okapi BM25](index-ranking-similarity.md) | Söka i data | Ny algoritm för rankning av relevans används automatiskt för alla nya Sök tjänster som skapats efter den 15 juli. För tjänster som skapats tidigare kan du välja att ange `similarity` egenskapen för index fält. | Allmänt tillgänglig. </br> Använd [Search REST API 2020-06-30](/rest/api/searchservice/) eller senare, eller REST API 2019-05-06. |
| **executionEnvironment** | Säkerhet (indexerare) | Ange uttryckligen konfigurations egenskapen för indexerare för `private` att tvinga alla anslutningar till externa data källor över en privat slut punkt. Gäller endast för Sök tjänster som utnyttjar Azures privata länk. | Allmänt tillgänglig. </br> Använd [sök REST API 2020-06-30](/rest/api/searchservice/) för att ange den här allmänna konfigurations parametern. |

## <a name="may-2020-microsoft-build"></a>Maj 2020 (Microsoft-version)

|Zoomfunktionen&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategori | Beskrivning | Tillgänglighet  |
|---------|------------------|-------------|---------------|
| [Felsökningssessioner](cognitive-search-debug-session.md) | AI-berikning | Fel söknings sessioner tillhandahåller ett Portal gränssnitt för att undersöka och lösa problem med en befintlig färdigheter. Korrigeringar som skapats i felsökningssessionen kan sparas till produktionens färdighetsuppsättningar. Kom igång med [den här självstudien](cognitive-search-tutorial-debug-sessions.md). | Offentlig för hands version i portalen. |
| [IP-regler för Inbound brand Väggs stöd](service-configure-firewall.md) | Säkerhet | Begränsa åtkomsten till en Sök tjänst slut punkt till vissa IP-adresser. | Allmänt tillgänglig. </br> Använd [Management REST API 2020-03-13](/rest/api/searchmanagement/) eller senare, eller portalen. |
| [Azure privat länk för en privat Sök slut punkt](service-create-private-endpoint.md) | Säkerhet| Skärma en Sök tjänst från det offentliga Internet genom att köra den som en privat länk resurs som endast är tillgänglig för klient program och andra Azure-tjänster i samma virtuella nätverk. | Allmänt tillgänglig. </br> Använd [Management REST API 2020-03-13](/rest/api/searchmanagement/) eller senare, eller portalen. |
| [Systemhanterad identitet (förhands granskning)](search-howto-managed-identities-data-sources.md) | Säkerhet (indexerare) | Registrera en Sök tjänst som en betrodd tjänst med Azure Active Directory konfigurera anslutningar till Azure-datakälla som stöds för indexering. Gäller för [indexerare](search-indexer-overview.md) som inhämtar innehåll från Azure-datakällor som Azure SQL Database, Azure Cosmos DB och Azure Storage. | Offentlig för hands version. </br> Använd portalen för att registrera Sök tjänsten. |
| [parametern SessionID-fråga](index-similarity-and-scoring.md), [scoringStatistics = global parameter](index-similarity-and-scoring.md#scoring-statistics) | Fråga (relevans) | Lägg till sessionID i en fråga för att upprätta en session för att beräkna Sök resultat, med scoringStatistics = global för att samla in resultat från alla Shards, för mer konsekventa Sök Resultat beräkningar. | Allmänt tillgänglig. </br> Använd [Search REST API 2020-06-30](/rest/api/searchservice/) eller senare, eller REST API 2019-05-06. |
| [featuresMode av betydelse för att utöka poäng (för hands version)](index-similarity-and-scoring.md#featuresMode-param) | Söka i data | Lägg till den här Frågeparametern om du vill visa mer information: per fält likhets poäng, per fält term frekvens och per fält antal unika tokens matchade. <br/><br/>Anpassade bedömnings algoritmer kan använda den här informationen. *Inlärning till rankade* algoritmer är en avancerad anpassad bedömnings funktion som kan implementeras när du anger information om relevans poäng. Ett exempel som visar den här funktionen finns i [Lägg till Machine Learning (LearnToRank) för att söka efter relevans](https://github.com/Azure-Samples/search-ranking-tutorial). | Offentlig för hands version. </br> Använd [sök REST API 2020-06-30 – för hands version](/rest/api/searchservice/index-preview) eller REST API 2019-05-06 – för hands version. |

## <a name="march-2020"></a>Mars 2020

|Zoomfunktionen&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategori | Beskrivning | Tillgänglighet  |
|---------|------------------|-------------|---------------|
| [Intern BLOB-mjuk borttagning (förhands granskning)](search-howto-index-changed-deleted-blobs.md) | Indexerare | En Azure Blob Storage-indexerare i Azure Kognitiv sökning identifierar blobbar som är i ett mjukt borttaget tillstånd och tar bort motsvarande Sök dokument under indexeringen. | Offentlig för hands version. </br> Använd [sök REST API 2020-06-30 – för hands version](/rest/api/searchservice/index-preview) och REST API 2019-05-06 – för hands version med kör indexeraren mot en Azure Blob-datakälla som har inbyggd "mjuk borttagning" aktive rad. |
| [Hanterings REST API (2020-03-13)](/rest/api/searchmanagement/management-api-versions) | REST | Ny stabil REST API för att skapa och hantera en Sök tjänst. Lägger till stöd för IP-brandvägg och privat länk | Allmänt tillgänglig. |

## <a name="february-2020"></a>Februari 2020

|Zoomfunktionen&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategori | Beskrivning | Tillgänglighet  |
|---------|------------------|-------------|---------------|
| [PII-identifiering (förhands granskning)](cognitive-search-skill-pii-detection.md) | AI-berikning | En ny kognitiv kunskap som används vid indexering som hämtar personlig information från en indata-text och ger dig möjlighet att maskera den från den texten på olika sätt. | Offentlig för hands version. </br> Använd portalen eller [sök REST API 2020-06-30 – för hands version](/rest/api/searchservice/index-preview) eller REST API 2019-05-06 – för hands version. |
| [Sökning efter anpassade entiteter (för hands version)](cognitive-search-skill-custom-entity-lookup.md )| AI-berikning | En ny kognitiv färdighet som söker efter text från en anpassad, användardefinierad lista med ord och fraser. Med den här listan etiketteras alla dokument med matchande entiteter. Kompetensen har även stöd för en viss fuzzy-matchning som kan användas för att hitta matchningar som liknar varandra men inte exakt. | Offentlig för hands version. </br> Använd portalen eller [sök REST API 2020-06-30 – för hands version](/rest/api/searchservice/index-preview) eller REST API 2019-05-06 – för hands version. |

## <a name="january-2020"></a>Januari 2020

|Zoomfunktionen&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategori | Beskrivning | Tillgänglighet  |
|---------|------------------|-------------|---------------|
| [Kundhanterade krypterings nycklar](search-security-manage-encryption-keys.md) |Säkerhet | Lägger till ett extra lager med kryptering utöver plattformens inbyggda kryptering. Med hjälp av en krypterings nyckel som du skapar och hanterar kan du kryptera index innehåll och synonym Maps innan nytto lasten når en Sök tjänst. | Allmänt tillgänglig. </br> Använd search REST API 2019-05-06 eller senare. För hanterad kod är rätt paket fortfarande [.NET SDK version 8,0 – förhands granskning](search-dotnet-sdk-migration-version-9.md) trots att funktionen inte är i förhands granskning. |
| [IP-regler för Inbound brand Väggs stöd (för hands version)](service-configure-firewall.md) | Säkerhet | Begränsa åtkomsten till en Sök tjänst slut punkt till vissa IP-adresser. För hands versions-API: et har nya egenskaper för **IpRule** och **NETWORKRULESET** i [CreateOrUpdate API](/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service). Den här förhands gransknings funktionen är tillgänglig i valda regioner. |  Offentlig för hands version med API-version = 2019-10 01 – för hands version.  |
| [Privat Azure-länk för en privat Sök slut punkt (för hands version)](service-create-private-endpoint.md) | Säkerhet| Skärma en Sök tjänst från det offentliga Internet genom att köra den som en privat länk resurs som endast är tillgänglig för klient program och andra Azure-tjänster i samma virtuella nätverk. | Offentlig för hands version med API-version = 2019-10 01 – för hands version.  |

## <a name="2019-feature-announcements"></a>2019 funktions meddelanden

### <a name="december-2019"></a>December 2019

+ [Skapa demo program (förhands granskning)](search-create-app-portal.md) är en ny guide i portalen som genererar en nedladdnings bar HTML-fil med fråga (skrivskyddad) åtkomst till ett index. Filen levereras med inbäddat skript som återger en fungerande "localhost"-webbapp som är knutet till ett index i din Sök tjänst. Sidor kan konfigureras i guiden och kan innehålla ett sökfält, resultat område, navigerings fält för navigering och stöd för typeahead-frågor. Du kan ändra HTML-koden offline för att utöka eller anpassa arbets flödet eller utseendet. En demo app är inte enkelt att utöka till att omfatta säkerhets-och värd lager som vanligt vis behövs i produktions scenarier. Du bör tänka på det som ett verifierings-och testnings verktyg i stället för en kort klipp till en fullständig klient app.

+ [Skapa en privat slut punkt för säkra anslutningar (förhands granskning)](service-create-private-endpoint.md) förklarar hur du konfigurerar en privat länk för säkra anslutningar till din Sök tjänst. Den här förhands gransknings funktionen är tillgänglig på begäran och använder [Azures privata länk](../private-link/private-link-overview.md) och [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) som en del av lösningen.

### <a name="november-2019---ignite-conference"></a>2019 november-inbrännings konferens

+ [Stegvis anrikning (för hands version)](cognitive-search-incremental-indexing-conceptual.md) lägger till cachelagring och statefullness i en anriknings pipeline så att du kan arbeta med vissa steg eller faser utan att förlora innehåll som redan har bearbetats. Tidigare krävde alla ändringar i en anriknings pipeline en fullständig återuppbyggnad. Med stegvis anrikning bevaras utdata från kostsam analys, särskilt bild analys.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [Extrahering av dokument (för hands version)](cognitive-search-skill-document-extraction.md) är en kognitiv kunskap som används vid indexering och som gör att du kan extrahera innehållet i en fil från en färdigheter. Tidigare inträffade endast dokument sprickor innan färdigheter kördes. Med att lägga till den här kunskapen kan du också utföra den här åtgärden i färdigheter-körning.

+ [Översättning av text](cognitive-search-skill-text-translation.md) är en kognitiv kunskap som används vid indexering som utvärderar text och, för varje post, returnerar texten översatt till det angivna mål språket.

+ [Power BI mallar](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) kan rivstart med visualiseringar och analys av berikat innehåll i ett kunskaps lager på Power BI Desktop. Den här mallen är utformad för Azure Table-projektioner som skapats via [guiden Importera data](knowledge-store-create-portal.md).

+ [Azure Data Lake Storage Gen2 (för hands version)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin API (för hands version)](search-howto-index-cosmosdb.md)och [Cosmos DB API för Cassandra (för hands version)](search-howto-index-cosmosdb.md) stöds nu i indexerare. Du kan registrera dig med [det här formuläret](https://aka.ms/azure-cognitive-search/indexer-preview). Du får ett bekräftelse meddelande när du har accepterat i för hands versions programmet.

### <a name="july-2019"></a>Juli 2019

+ Allmänt tillgänglig i [Azure Government Cloud](../azure-government/compare-azure-government-global-azure.md#azure-cognitive-search).

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nytt tjänstnamn

Azure Search har nu bytt namn till **Azure kognitiv sökning** för att återspegla den utökade (ännu valfria) användningen av kognitiva kunskaper och AI-bearbetning i kärn åtgärder. API-versioner, NuGet-paket, namnrymder och slut punkter är oförändrade. Nya och befintliga Sök lösningar påverkas inte av tjänst namns ändringen.

## <a name="service-updates"></a>Tjänstuppdateringar

[Tjänst uppdaterings meddelanden](https://azure.microsoft.com/updates/?product=search&status=all) för Azure kognitiv sökning finns på Azure-webbplatsen.
