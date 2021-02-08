---
title: API-versioner
titleSuffix: Azure Cognitive Search
description: 'Versions princip för Azure Kognitiv sökning REST API: er och klient biblioteket i .NET SDK.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: 489c6be7bf977d01207fee4d1bd1fda13dc12025
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820525"
---
# <a name="api-versions-in-azure-cognitive-search"></a>API-versioner i Azure Kognitiv sökning

Azure Kognitiv sökning samlar ut funktions uppdateringar regelbundet. Ibland, men inte alltid, kräver de här uppdateringarna en ny version av API: n för att bevara bakåtkompatibilitet. Genom att publicera en ny version kan du styra när och hur du integrerar Sök tjänst uppdateringar i din kod.

Som regel publicerar Azure Kognitiv sökning-teamet nya versioner endast när det behövs, eftersom det kan omfatta viss ansträngning för att uppgradera din kod för att använda en ny API-version. En ny version krävs bara om någon aspekt av API: et har ändrats på ett sätt som bryter mot bakåtkompatibilitet. Sådana ändringar kan inträffa på grund av korrigeringar av befintliga funktioner eller på grund av nya funktioner som ändrar befintliga API-Surface-områden.

Samma regel gäller för SDK-uppdateringar. Azure Kognitiv sökning SDK följer reglerna för [semantisk version](https://semver.org/) , vilket innebär att dess version har tre delar: Major, mindre och build-nummer (till exempel 1.1.0). En ny huvud version av SDK släpps bara för ändringar som bryter mot bakåtkompatibilitet. Icke-brytande funktions uppdateringar kommer att öka den lägre versionen, och fel korrigeringarna ökar bara versions versionen.

> [!Important]
> Azure-SDK: er för .NET, Java, python och Java Script tar bort nya klient bibliotek för Azure Kognitiv sökning. För närvarande har inget av Azure SDK-biblioteken fullständigt stöd för de senaste Sök REST-API: erna (2020-06-30) eller hantering REST API: er (2020-03-13), men det kommer att ändras med tiden. Du kan regelbundet kontrol lera den här sidan eller [vad som är nytt](whats-new.md) för meddelanden om funktionella förbättringar.

<a name="unsupported-versions"></a>

## <a name="unsupported-versions"></a>Versioner som inte stöds

Uppgradera befintliga Sök lösningar till den senaste versionen av REST API den 15 oktober 2020. Vid detta tillfälle kommer följande versioner av Azure Kognitiv sökning REST API att dras tillbaka och stöds inte längre:

+ **2015-02-28**
+ **2015-02-28 – för hands version**
+ **2014-07-31 – för hands version**
+ **2014-10-20 – för hands version**

Dessutom dras versioner av Azure Kognitiv sökning .NET SDK som är äldre än [**3.0.0-RC**](https://www.nuget.org/packages/Microsoft.Azure.Search/3.0.0-rc) tillbaka, eftersom de är riktade mot någon av dessa REST API-versioner. 

Efter det här datumet kommer program som använder någon av de inaktuella REST API-eller SDK-versionerna inte längre att fungera och måste uppgraderas. Som med alla ändringar av den här typen ger vi 12 månaders varsel, så du har tillräckligt med tid för att justera.

Om du vill fortsätta att använda Azure Kognitiv sökning måste du migrera den befintliga koden som är avsedd för [REST API](search-api-migration.md) till [REST API version 2020-06-30](/rest/api/searchservice/) eller till en nyare SDK senast den 15 oktober 2020.  Om du har frågor om att uppdatera till den senaste versionen skickar du e-post till den azuresearch_contact@microsoft.com 15 maj 2020 för att se till att du har tillräckligt med tid för att uppdatera koden.

## <a name="rest-apis"></a>REST API:er

En Azure Kognitiv sökning-tjänstinstans stöder flera REST API versioner, inklusive den senaste. Du kan fortsätta att använda en version när den inte längre är den senaste, men vi rekommenderar att du [migrerar din kod](search-api-migration.md) för att använda den senaste versionen. När du använder REST API måste du ange API-versionen i varje begäran via parametern API-version. När du använder .NET SDK fastställer den version av SDK som du använder motsvarande version av REST API. Om du använder en äldre SDK kan du fortsätta att köra koden utan ändringar även om tjänsten uppgraderas till att stödja en nyare API-version.

Följande tabell innehåller versions historiken för aktuella och tidigare utgivna versioner av Search Service REST API. Dokumentationen publiceras bara för de senaste stabila och för hands versions versionerna.

### <a name="search-service-apis"></a>Search Service-API: er

Skapa och hantera innehåll i en Sök tjänst.

| 2.0.1&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Status | Beskrivning |
|-------------------------|--------|------------------------------|
| [Search 2020-06-30](/rest/api/searchservice/index)| Stable | Den senaste stabila versionen av Sök REST-API: er, med framsteg i relevans-Poäng och allmänt tillgängliga för kunskaps lager.|
| [Search 2020-06-30-förhandsversion](/rest/api/searchservice/index-preview)| Förhandsgranskning | För hands version som är associerad med stabil version. Innehåller flera för [hands versions funktioner](search-api-preview.md). |
| Sökning 2019-05-06 | Stable  | Lägger till [komplexa typer](search-howto-complex-data-types.md). |
| Search 2019-05-06-Preview | Förhandsgranskning | För hands version som är associerad med stabil version. |
| Sök 2017-11-11 | Stable | Lägger till färdighetsuppsättningar och [AI-anrikning](cognitive-search-concept-intro.md). |
| Sök 2017-11-11 – för hands version | Förhandsgranskning | För hands version som är associerad med stabil version. |
| Sök 2016-09-01 |Stable | Lägger till [indexerare](search-indexer-overview.md). |
| Sök 2016-09-01 – för hands version | Förhandsgranskning | För hands version som är associerad med stabil version.|
| Sök 2015-02-28 | Stöds inte efter 10-10-2020   | Första allmänt tillgängliga version.  |
| Sök 2015-02-28 – för hands version | Stöds inte efter 10-10-2020  | För hands version som är associerad med stabil version. |
| Sök 2014-10-20 – för hands version | Stöds inte efter 10-10-2020 | Andra offentliga för hands versionen. |
| Sök 2014-07-31 – för hands version | Stöds inte efter 10-10-2020  | Första offentliga för hands versionen. |

### <a name="management-rest-apis"></a>Management REST API:er

Skapa och konfigurera en Sök tjänst och hantera API-nycklar.

| 2.0.1&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Status | Beskrivning |
|-------------------------|--------|------------------------------|
| [Hantering 2020-08-01](/rest/api/searchmanagement/) | Stable | Senaste stabila versionen av hanterings REST-API: erna. Lägger till allmänt tillgänglig delad privat länk resurs stöd för alla utgående resurser förutom de som anges i för hands versionen |
| [Hantering 2020-08-01 – för hands version](/rest/api/searchmanagement/index-preview) | Förhandsgranskning  | Finns för närvarande i för hands version: resurs stöd för delad privat länk för Azure Functions och Azure Database for MySQL. |
| Hantering 2020-03-13  | Stable | Lägger till [privat slut punkt](service-create-private-endpoint.md) via privat länk och [nätverks-IP-regler](service-configure-firewall.md) för nya tjänster. Mer information finns i den här [Swagger-specifikationen](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2020-08-01). |
| Hantering 2019-10-01-förhandsversion | Förhandsgranskning  | Inga förhands gransknings funktioner har introducerats i den här listan. Den här för hands versionen är funktionellt likvärdig med 2020-03-13. Mer information finns i den här [Swagger-specifikationen](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/preview/2019-10-01-preview). |
| Hantering 2015-08-19  | Stable | Den första allmänt tillgängliga versionen av hanterings REST-API: erna. Tillhandahåller tjänst etablering, skalning och hantering av API-nycklar. Mer information finns i den här [Swagger-specifikationen](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2015-08-19). |
| Hantering 2015-08-19 – för hands version  | Förhandsgranskning | Den första för hands versionen av hanterings REST-API: erna. Mer information finns i den här [Swagger-specifikationen](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2015-08-19). |

## <a name="azure-sdk-for-net"></a>Azure SDK för .NET

Följande tabell innehåller länkar till nyare SDK-versioner. 

| SDK-version | Status | Beskrivning |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11](/dotnet/api/overview/azure/search.documents-readme) | Stable | Nytt klient bibliotek från Azure .NET SDK, lanserat juli 2020. Riktar in the search REST API-version = 2020-06-30 REST API men inte har inbyggt stöd för geo-filter. Vi rekommenderar [Microsoft. spatial](https://www.nuget.org/packages/Microsoft.Spatial/) paket för geografiska åtgärder. Exempel är tillgängliga för [System.Text.Jspå](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial/README.md) och [Newtonsoft.Jspå](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial.NewtonsoftJson/README.md). |
| [Microsoft. Azure. search 10](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Stable | Lanserad maj 2019. Mål för search REST API-version = 2019-05-06.|
| [Microsoft. Azure. Management. search 4.0.0](/dotnet/api/overview/azure/search/management) | Stable | Rikta in the Management REST API-version = 2020-08-01.  |
| Microsoft. Azure. Management. search 3.0.0 | Stable | Rikta in the Management REST API-version = 2015-08-19.  |

## <a name="azure-sdk-for-java"></a>Azure SDK för Java

| SDK-version | Status | Beskrivning  |
|-------------|--------|------------------------------|
| [Java Azure-Search-dokument 11](https://newreleases.io/project/github/Azure/azure-sdk-for-java/release/azure-search-documents_11.1.0) | Stable | Nytt klient bibliotek från Azure .NET SDK, lanserat juli 2020. Mål för search REST API-version = 2019-05-06. |
| [1.35.0 för Java-hanterings klient](/java/api/overview/azure/search/management) | Stable | Rikta in the Management REST API-version = 2015-08-19. |

## <a name="azure-sdk-for-javascript"></a>Azure SDK för Java Script

| SDK-version | Status | Beskrivning  |
|-------------|--------|------------------------------|
| [Java Script – Sök 11,0](https://azure.github.io/azure-sdk-for-node/azure-search/latest/) | Stable | Nytt klient bibliotek från Azure .NET SDK, lanserat juli 2020. Mål för search REST API-version = 2016-09-01. |
| [Java Script-arm-search](https://azure.github.io/azure-sdk-for-node/azure-arm-search/latest/) | Stable | Rikta in the Management REST API-version = 2015-08-19. |

## <a name="azure-sdk-for-python"></a>Azure SDK för Python

| SDK-version | Status | Beskrivning  |
|-------------|--------|------------------------------|
| [Python Azure-Search-dokument 11,0](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-search-documents/11.0.0/index.html) | Stable | Nytt klient bibliotek från Azure .NET SDK, lanserat juli 2020. Mål för search REST API-version = 2019-05-06. |
| [Python Azure-MGMT-search 1,0](/python/api/overview/azure/search) | Stable | Rikta in the Management REST API-version = 2015-08-19. |