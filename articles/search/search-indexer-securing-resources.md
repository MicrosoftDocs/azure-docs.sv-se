---
title: Indexerare har åtkomst till skyddade resurser
titleSuffix: Azure Cognitive Search
description: Konceptuell översikt över säkerhets alternativen på nätverks nivå för Azure Data Access av indexerare i Azure Kognitiv sökning.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: bcb6e91bba367363385214806077146b1a24fe7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92503495"
---
# <a name="indexer-access-to-content-protected-by-azure-network-security-features-azure-cognitive-search"></a>Indexerare får åtkomst till innehåll som skyddas av Azure Network Security-funktioner (Azure Kognitiv sökning)

Azure Kognitiv sökning indexerare kan göra utgående anrop till olika Azure-resurser under körningen. Den här artikeln förklarar koncepten bakom indexerare åtkomst till innehåll som skyddas av IP-brandväggar, privata slut punkter eller andra Azure-säkerhetsmekanismer på nätverks nivå. En indexerare gör utgående anrop i två situationer: anslutning till data källor under indexering och anslutning till inkapslad kod via en färdigheter. En lista över alla möjliga resurs typer som en indexerare kan komma åt i en typisk körning visas i tabellen nedan.

| Resurs | Syfte inom körning av indexerare |
| --- | --- |
| Azure Storage (blobbar, tabeller, ADLS gen 2) | Datakälla |
| Azure Storage (blobbar, tabeller) | Färdighetsuppsättningar (cachelagring av berikade dokument och lagring av kunskaps lager-projektioner) |
| Azure Cosmos DB (olika API: er) | Datakälla |
| Azure SQL Database | Datakälla |
| SQL Server på virtuella Azure-datorer | Datakälla |
| SQL-hanterad instans | Datakälla |
| Azure Functions | Värd för anpassade webb-API-kunskaper |
| Cognitive Services | Ansluten till färdigheter som används för fakturerings anrikning utöver gränsen på 20 fria dokument |

> [!NOTE]
> Kognitiva tjänst resursen som är kopplad till en färdigheter används för fakturering, baserat på de anrikninger som utförs och skrivs till Sök indexet. Den används inte för att komma åt API:er för Cognitive Services. Åtkomst från en indexerare anriknings pipeline till API:er för Cognitive Services sker via en intern säker kommunikations kanal, där data är starkt krypterade vid överföring och aldrig lagras i vila.

Kunder kan skydda dessa resurser via flera nätverks isolerings mekanismer som erbjuds av Azure. Med undantag för en kognitiv tjänst resurs har indexerare begränsad möjlighet att komma åt alla andra resurser, även om de är isolerade, som beskrivs i tabellen nedan.

| Resurs | IP-begränsning | Privat slutpunkt |
| --- | --- | ---- |
| Azure Storage (blobbar, tabeller, ADLS gen 2) | Stöds endast om lagrings kontot och Sök tjänsten finns i olika regioner | Stöds |
| Azure Cosmos DB-SQL API | Stöds | Stöds |
| Azure Cosmos DB-Cassandra, Mongo och Gremlin API | Stöds | Stöd saknas |
| Azure SQL Database | Stöds | Stöds |
| SQL Server på virtuella Azure-datorer | Stöds | Ej tillämpligt |
| SQL-hanterad instans | Stöds | Ej tillämpligt |
| Azure Functions | Stöds | Stöds endast för vissa nivåer av Azure Functions |

> [!NOTE]
> Utöver de alternativ som anges ovan kan kunder som är skyddade Azure Storage-konton utnyttja det faktum att Azure Kognitiv sökning är en [betrodd Microsoft-tjänst](../storage/common/storage-network-security.md#trusted-microsoft-services). Det innebär att en bestämd Sök tjänst kan kringgå virtuella nätverk eller IP-begränsningar för lagrings kontot och kan komma åt data i lagrings kontot, om lämplig rollbaserad åtkomst kontroll har Aktiver ATS på lagrings kontot. Mer information finns i [indexerare anslutningar med hjälp av undantag för betrodda tjänster](search-indexer-howto-access-trusted-service-exception.md). Det här alternativet kan användas i stället för IP-begränsning, om lagrings kontot eller Sök tjänsten inte kan flyttas till en annan region.

När du väljer vilken säker åtkomst mekanism som en indexerare ska använda, bör du tänka på följande begränsningar:

- En indexerare kan inte ansluta till en [tjänst slut punkt för virtuellt nätverk](../virtual-network/virtual-network-service-endpoints-overview.md). Offentliga slut punkter med autentiseringsuppgifter, privata slut punkter, betrodda tjänster och IP-adresser är de enda metoder som stöds för index anslutningar.
- Det går inte att tillhandahålla en Sök tjänst till ett enskilt virtuellt nätverk som körs internt på en virtuell dator. Den här funktionen kommer inte att erbjudas av Azure Kognitiv sökning.
- Om indexerarna använder (utgående) privata slut punkter för att få åtkomst till resurser kan ytterligare [avgifter för privata länkar](https://azure.microsoft.com/pricing/details/search/) tillkomma.

## <a name="indexer-execution-environment"></a>Miljö för körning av indexerare

Azure Kognitiv sökning-indexerare kan effektivt extrahera innehåll från data källor, lägga till berikade innehåll och eventuellt generera prognoser innan resultatet skrivs till Sök indexet. Beroende på hur många ansvars områden som har tilldelats till en indexerare, kan det köras i en av två miljöer:

- En miljö som är privat för en speciell Sök tjänst. Indexerare som körs i sådana miljöer delar resurser med andra arbets belastningar (till exempel andra kundinitierade indexeringar eller frågor om arbets belastningar). Vanligt vis är det bara indexerare som utför textbaserad indexering (till exempel att inte använda en färdigheter) i den här miljön.
- En miljö som är värd för flera klient organisationer och som är resurs intensiv, till exempel med färdighetsuppsättningar. Den här miljön används för att avlasta en beräknings intensiv bearbetning, vilket lämnar tjänstspecifika resurser tillgängliga för rutin åtgärder. Den här miljön för flera innehavare hanteras och skyddas av Microsoft, utan extra kostnad för kunden.

För att det ska gå att köra en indexerare, fastställer Azure Kognitiv sökning den bästa miljön där du vill köra indexeraren. Om du använder en IP-brandvägg för att kontrol lera åtkomsten till Azure-resurser kan du veta mer om körnings miljöer som hjälper dig att skapa ett IP-intervall som inkluderar båda.

## <a name="granting-access-to-indexer-ip-ranges"></a>Bevilja åtkomst till indexerare IP-intervall

Om resursen som indexeraren försöker komma åt är begränsad till endast en viss uppsättning IP-intervall, måste du expandera uppsättningen för att inkludera möjliga IP-intervall som en indexerare-begäran kan ha sitt ursprung i. Som anges ovan finns det två möjliga miljöer där indexerare körs och från vilka åtkomst begär Anden kan komma från. Du måste lägga till IP-adresserna för **båda** miljöerna för att indexerings åtkomsten ska fungera.

- För att hämta IP-adressen för Sök tjänstens särskilda privata miljö, `nslookup` (eller `ping` ) det fullständigt kvalificerade domän namnet (FQDN) för Sök tjänsten. Till exempel är FQDN för en Sök tjänst i det offentliga molnet `<service-name>.search.windows.net` . Den här informationen finns på Azure Portal.
- IP-adresserna för flera klient miljöer är tillgängliga via `AzureCognitiveSearch` tjänst tag gen. [Azure Service-Taggar](../virtual-network/service-tags-overview.md) har ett publicerat intervall med IP-adresser för varje tjänst – detta är tillgängligt via ett [identifierings-API (för hands version)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) eller en [nedladdnings bar JSON-fil](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). I båda fallen uppdelas IP-intervall efter region – du kan bara välja de IP-adressintervall som tilldelats den region där Sök tjänsten är etablerad.

För vissa data källor kan själva tjänst tag gen användas direkt i stället för att räkna upp listan över IP-intervall (IP-adressen för Sök tjänsten måste fortfarande användas explicit). Dessa data källor begränsar åtkomsten genom att skapa en [regel för nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md), som inbyggt stöder tillägg av en service tag, till skillnad från IP-regler som de som erbjuds av Azure Storage, Cosmos DB, Azure SQL och så vidare. Data källorna som stöder möjligheten att använda `AzureCognitiveSearch` tjänst tag gen direkt utöver Sök tjänstens IP-adress är:

- [SQL Server på virtuella Azure-datorer](./search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md#restrict-access-to-the-azure-cognitive-search)

- [SQL-hanterade instanser](./search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md#verify-nsg-rules)

Mer information om det här anslutnings alternativet finns i [Indexer anslutningar via en IP-brandvägg](search-indexer-howto-access-ip-restricted.md).

## <a name="granting-access-via-private-endpoints"></a>Bevilja åtkomst via privata slut punkter

Indexerare kan använda [privata slut punkter](../private-link/private-endpoint-overview.md) för att få åtkomst till resurser, åtkomst till vilka är låsta för att välja virtuella nätverk eller inte ha någon offentlig åtkomst aktive rad.
Den här funktionen är endast tillgänglig i fakturerbara Sök tjänster med gränser för antalet privata slut punkter som skapas. Mer information finns i [tjänst begränsningar](search-limits-quotas-capacity.md#shared-private-link-resource-limits).

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>Steg 1: skapa en privat slut punkt för den säkra resursen

Kunderna bör anropa Sök hanterings åtgärden, [CreateOrUpdate-API](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) på en **delad privat länk resurs**, för att skapa en privat slut punkts anslutning till deras skyddade resurs (till exempel ett lagrings konto). Trafik som går över den här (utgående) privata slut punkts anslutningen kommer endast från det virtuella nätverk som är i Sök tjänstens aktuella "privata" index körnings miljö.

Azure Kognitiv sökning verifierar att anroparna i det här API: et har Azure RBAC-behörighet för att godkänna anslutnings begär Anden för privata slut punkter till den skyddade resursen. Om du till exempel begär en privat slut punkts anslutning till ett lagrings konto med skrivskyddad behörighet avvisas anropet.

### <a name="step-2-approve-the-private-endpoint-connection"></a>Steg 2: Godkänn anslutningen till den privata slut punkten

När den (asynkrona) åtgärd som skapar en delad privat länk-resurs slutförs, skapas en privat slut punkts anslutning i ett "väntande" läge. Ingen trafik flödar över anslutningen än.
Kunden förväntas sedan hitta denna begäran på sin säkra resurs och godkänna. Normalt kan detta göras antingen via Azure Portal eller via [REST API](/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection).

### <a name="step-3-force-indexers-to-run-in-the-private-environment"></a>Steg 3: tvinga indexerare att köras i den privata miljön

En godkänd privat slut punkt tillåter utgående anrop från Sök tjänsten till en resurs som har någon form av åtkomst begränsningar på nätverks nivå (till exempel en data källa för lagrings kontot som har kon figurer ATS för att endast nås från vissa virtuella nätverk) att lyckas.
Det innebär att alla indexerare som kan kontakta en sådan data källa över den privata slut punkten kommer att lyckas.
Om den privata slut punkten inte är godkänd, eller om indexeraren inte använder den privata slut punkts anslutningen, kommer körningen av indexet att avslutas `transientFailure` .

Om du vill aktivera indexerare för att få åtkomst till resurser via anslutningar för privata slut punkter, måste du ange att `executionEnvironment` indexeraren ska köras så att `"Private"` alla indexerare körs kan använda den privata slut punkten. Detta beror på att privata slut punkter är etablerade i den privata Sök tjänst miljön.

```json
    {
      "name" : "myindexer",
      ... other indexer properties
      "parameters" : {
          ... other parameters
          "configuration" : {
            ... other configuration properties
            "executionEnvironment": "Private"
          }
        }
    }
```

De här stegen beskrivs mer detaljerat i [Indexer-anslutningar via en privat slut punkt](search-indexer-howto-access-private.md).
När du har en godkänd privat slut punkt till en resurs får indexerare som är inställt för *privata* försök att få åtkomst via anslutningen till den privata slut punkten.

### <a name="limits"></a>Gränser

För att säkerställa optimala prestanda och stabilitet för Sök tjänsten införs begränsningar (av Sök tjänst nivån) på följande dimensioner:

- Typerna av indexerare som kan ställas in för *privat*.
- Antalet delade privata länk resurser som kan skapas.
- Antalet distinkta resurs typer som delade privata länk resurser kan skapas för.

Dessa gränser dokumenteras i [tjänst begränsningar](search-limits-quotas-capacity.md).

## <a name="next-steps"></a>Nästa steg

- [Indexerare-anslutningar via IP-brandväggar](search-indexer-howto-access-ip-restricted.md)
- [Indexerare-anslutningar med undantag för betrodda tjänster](search-indexer-howto-access-trusted-service-exception.md)
- [Indexerare ansluter till en privat slut punkt](search-indexer-howto-access-private.md)