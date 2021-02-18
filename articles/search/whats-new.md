---
title: Vad är nytt i Azure Kognitiv sökning
description: Meddelanden om nya och förbättrade funktioner, inklusive tjänst byte för Azure Search till Azure Kognitiv sökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 02/09/2021
ms.custom: references_regions
ms.openlocfilehash: 6de91d51f3700c25ba1e0f361c948dc8ab338c43
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590558"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Vad är nytt i Azure Kognitiv sökning

Läs om vad som är nytt i tjänsten. Skapa ett bok märke för den här sidan för att hålla dig uppdaterad med tjänsten. Läs för [hands versions funktions listan](search-api-preview.md) för att visa funktioner i offentlig för hands version.

## <a name="february-2021"></a>Februari 2021

|Zoomfunktionen&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Description | Tillgänglighet  |
|------------------------------|---------------|---------------|
| [Återställ dokument (förhands granskning)](search-howto-run-reset-indexers.md) |  Ombearbetar individuellt valda Sök dokument i Indexer-arbetsbelastningar. | [Sök REST API 2020-06-30 – för hands version](/rest/api/searchservice/index-preview) |
| [Tillgänglighetszoner](search-performance-optimization.md#availability-zones)| Sök tjänster med två eller fler repliker i vissa regioner, som listas i [skala för prestanda](search-performance-optimization.md#availability-zones), få återhämtning genom att ha repliker på två eller flera distinkta fysiska platser.  | Regionen och datumet för skapandet av Sök tjänsten fastställer tillgänglighet. Mer information finns i artikeln om skalning för prestanda. |
| [Azure CLI](/cli/azure/search) </br>[Azure PowerShell](/powershell/module/az.search/) | Nya ändringar ger nu en fullständig mängd åtgärder i hanterings REST API 2020-08-01, inklusive stöd för IP-brandvägg och privat slut punkt. | Allmänt tillgänglig. |

## <a name="january-2021"></a>Januari 2021

|Zoomfunktionen&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Description | Tillgänglighet  |
|------------------------------|-------------|---------------|
| [Solution Accelerator för Azure Kognitiv sökning och QnA Maker](https://github.com/Azure-Samples/search-qna-maker-accelerator) | Hämtar frågor och svar från dokumentet och föreslår de mest relevanta svaren. Du hittar en live demo-app på [https://aka.ms/qnaWithAzureSearchDemo](https://aka.ms/qnaWithAzureSearchDemo) .  | Projekt med öppen källkod (inget service avtal) |

## <a name="2020-archive"></a>2020-Arkiv

| Månad | Funktion | Beskrivning |
|-------|---------|-------------|
| November | [Kundhanterad nyckel kryptering (utökad)](search-security-manage-encryption-keys.md) | Utökar kundhanterad kryptering över hela det antal till gångar som skapas och hanteras av en Sök tjänst. Allmänt tillgänglig.|
| September | [Visual Studio Code-tillägg för Azure Kognitiv sökning](search-get-started-vs-code.md) | Lägger till en arbets yta, navigering, IntelliSense och mallar för att skapa index, indexerare, data källor och färdighetsuppsättningar. | Offentlig för hands version |
| September | [Hanterad tjänst identitet (indexerare)](search-howto-managed-identities-data-sources.md) | Allmänt tillgänglig.  |
| September | [Utgående begär Anden med en privat länk](search-indexer-howto-access-private.md) | Allmänt tillgänglig.  |
| September | [Hanterings REST API (2020-08-01)](/rest/api/searchmanagement/management-api-versions) | Allmänt tillgänglig. |
| September | [Hanterings REST API (2020-08-01-för hands version)](/rest/api/searchmanagement/management-api-versions) | Lägger till delad privat länk resurs för Azure Functions och Azure SQL för MySQL-databaser. |
| September | [Hantering .NET SDK 4,0](/dotnet/api/overview/azure/search/management) |  Azure SDK-uppdatering för hanterings-SDK, riktad REST API version 2020-08-01. Allmänt tillgänglig.|
| Augusti | [dubbel kryptering](search-security-overview.md#encryption) | Allmänt tillgänglig på alla Sök tjänster som skapats efter den 1 augusti 2020 i dessa regioner: västra USA 2, östra USA, södra centrala USA, US Gov, Virginia, US Gov, Arizona. |
| Juli | [Azure.Search.Docklient bibliotek för uments](/dotnet/api/overview/azure/search.documents-readme) | Azure SDK för .NET, som är allmänt tillgänglig. |
| Juli | [azure.search.docklient bibliotek för uments](/python/api/overview/azure/search-documents-readme)  | Azure SDK för python, som är allmänt tillgänglig. |
| Juli | [@azure/search-documents klient bibliotek](/javascript/api/overview/azure/search-documents-readme)  | Azure SDK för Java Script, som är allmänt tillgänglig. |
| Juni | [Knowledge Store](knowledge-store-concept-intro.md) | Allmänt tillgänglig. |
| Juni | [Sök REST API 2020-06-30](/rest/api/searchservice/) | Allmänt tillgänglig. |
| Juni | [Sök REST API 2020-06-30 – för hands version](/rest/api/searchservice/) | Lägger till återställnings färdigheter för att selektivt bearbeta kunskaper och stegvisa anrikning. |
| Juni | [Okapi BM25](index-ranking-similarity.md) | Allmänt tillgänglig. |
| Juni |  **executionEnvironment** (gäller Sök tjänster med Azure Private-länk.) | Allmänt tillgänglig. |
| Juni | [AML-kunskaper (för hands version)](cognitive-search-aml-skill.md) | En kognitiv färdighet som utökar AI-anrikningen med en anpassad Azure Machine Learning modell (AML). |
| Maj | [Felsöka sessioner (för hands version)](cognitive-search-debug-session.md) | Färdigheter-felsökning i portalen.  |
| Maj | [IP-regler för Inbound brand Väggs stöd](service-configure-firewall.md) | Allmänt tillgänglig.  |
| Maj | [Azure privat länk för en privat Sök slut punkt](service-create-private-endpoint.md) | Allmänt tillgänglig.  |
| Maj | [Hanterad tjänst identitet (indexerare) – (för hands version)](search-howto-managed-identities-data-sources.md) | Anslut till Azure-datakällor med hjälp av en hanterad identitet.  |
| Maj | [parametern SessionID-fråga](index-similarity-and-scoring.md), [scoringStatistics = global parameter](index-similarity-and-scoring.md#scoring-statistics)  | Global Sök statistik, användbar för [Machine Learning-modeller (LearnToRank) för Sök relevans](https://github.com/Azure-Samples/search-ranking-tutorial).  |
| Maj | [featuresMode av betydelse för att utöka poäng (för hands version)](index-similarity-and-scoring.md#featuresMode-param)  |   |
|Mars  | [Intern BLOB-mjuk borttagning (förhands granskning)](search-howto-index-changed-deleted-blobs.md) | Tar bort Sök dokument om käll-bloben är mjuk-borttagen i Blob Storage. |
|Mars  | [Hanterings REST API (2020-03-13)](/rest/api/searchmanagement/management-api-versions) | Allmänt tillgänglig. |
|Februari | [Kunskaper om identifiering av PII (för hands version)](cognitive-search-skill-pii-detection.md)  | En kognitiv färdighet som extraherar och maskerar personlig information. |
|Februari | [Sök kompetens för anpassad entitet (förhands granskning)](cognitive-search-skill-custom-entity-lookup.md) | En kognitiv färdighet som söker efter ord och fraser från en lista och etiketter alla dokument med matchande entiteter.  |
|Januari | [Kund hanterad nyckel kryptering](search-security-manage-encryption-keys.md) | Allmänt tillgänglig  |
|Januari | [IP-regler för Inbound brand Väggs stöd (för hands version)](service-configure-firewall.md) | Nya egenskaper för **IpRule** och **NetworkRuleSet** i [CreateOrUpdate-API: et](/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service).  |
|Januari | [Skapa en privat slut punkt (förhands granskning)](service-create-private-endpoint.md) | Konfigurera en privat länk för säkra anslutningar till din Sök tjänst. Den här förhands gransknings funktionen har en beroende [Azure Private-länk](../private-link/private-link-overview.md) och [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) som en del av lösningen. |

## <a name="2019-archive"></a>2019-Arkiv

| Månad | Funktion | Beskrivning |
|-------|---------|-------------|
|December | [Skapa demo-app (för hands version)](search-create-app-portal.md) | En guide som genererar en nedladdnings bar HTML-fil med fråga (skrivskyddad) åtkomst till ett index, som är avsedd som ett verifierings-och test verktyg i stället för en kort klipp till en fullständig klient app.|
|November | [Stegvis anrikning (för hands version)](cognitive-search-incremental-indexing-conceptual.md) | Cachelagrar färdigheter bearbetning för framtida åter användning.  |
|November | [Dokument extraherings kunskaper (för hands version)](cognitive-search-skill-document-extraction.md) | En kognitiv färdighet att extrahera innehållet i en fil inifrån en färdigheter.|
|November | [Text översättnings kunskap](cognitive-search-skill-text-translation.md) | En kognitiv kunskap som används vid indexering som utvärderar och översätter text. Allmänt tillgänglig.|
|November | [Power BI mallar](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) | Mall för visualisering av innehåll i kunskaps lager |
|November | [Azure Data Lake Storage Gen2 (för hands version)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin-API (för hands version)](search-howto-index-cosmosdb.md)och [Cosmos DB API för Cassandra (förhands granskning)](search-howto-index-cosmosdb.md) | Nya indexerare data källor i offentlig för hands version. |
|Juli | [Azure Government Cloud Support](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia&products=search) | Allmänt tillgänglig.|

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nytt tjänstnamn

Azure Search bytt namn till **Azure kognitiv sökning** i oktober 2019 för att avspegla den utökade (ännu valfria) användningen av kognitiva kunskaper och AI-bearbetning i kärn åtgärder. API-versioner, NuGet-paket, namnrymder och slut punkter är oförändrade. Nya och befintliga Sök lösningar påverkas inte av tjänst namns ändringen.

## <a name="service-updates"></a>Tjänstuppdateringar

[Tjänst uppdaterings meddelanden](https://azure.microsoft.com/updates/?product=search&status=all) för Azure kognitiv sökning finns på Azure-webbplatsen.