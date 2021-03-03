---
title: 'Diagnostisera och Felsök tillgängligheten för Azure Cosmos SDK: er i multiregionala miljöer'
description: Lär dig allt om tillgänglighets beteendet i Azure Cosmos SDK när du arbetar i flera regionala miljöer.
author: ealsur
ms.service: cosmos-db
ms.date: 02/18/2021
ms.author: maquaran
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0720eb01920e39a9bee27e4d00d97acba55b0ad5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661434"
---
# <a name="diagnose-and-troubleshoot-the-availability-of-azure-cosmos-sdks-in-multiregional-environments"></a>Diagnostisera och Felsök tillgängligheten för Azure Cosmos SDK: er i multiregionala miljöer
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här artikeln beskriver beteendet för den senaste versionen av Azure Cosmos SDK: er när du ser ett anslutnings problem till en viss region eller när en regions växling inträffar.

Alla Azure Cosmos SDK: er ger dig möjlighet att anpassa de nationella inställningarna. Följande egenskaper används i olika SDK: er:

* Egenskapen [ConnectionPolicy. PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) i .NET v2 SDK.
* Egenskaperna [CosmosClientOptions. ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) eller [CosmosClientOptions. ApplicationPreferredRegions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions) i .net v3 SDK.
* Metoden [CosmosClientBuilder. preferredRegions](/java/api/com.azure.cosmos.cosmosclientbuilder.preferredregions) i Java v4 SDK.
* Parametern [CosmosClient.preferred_locations](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) i python SDK.
* Parametern [CosmosClientOptions. ConnectionPolicy. preferredLocations](/javascript/api/@azure/cosmos/connectionpolicy#preferredlocations) i JS SDK.

När du anger den regionala inställningen ansluter klienten till en region som anges i följande tabell:

|Kontotyp |Läsningar |Skrivningar |
|------------------------|--|--|
| Enskild Skriv region | Önskad region | Primär region  |
| Flera Skriv regioner | Önskad region | Önskad region  |

Om du **inte anger någon önskad region** använder SDK-klienten som standard den primära regionen:

|Kontotyp |Läsningar |Skrivningar |
|------------------------|--|--|
| Enskild Skriv region | Primär region | Primär region |
| Flera Skriv regioner | Primär region  | Primär region  |

> [!NOTE]
> Primär region syftar på den första regionen i [listan med Azure Cosmos-kontots region](distribute-data-globally.md).
> Om de värden som anges som nationella inställningar inte stämmer överens med några befintliga Azure-regioner kommer de att ignoreras. Om de matchar en befintlig region men kontot inte replikeras till det, kommer klienten att ansluta till nästa önskade region som matchar eller till den primära regionen.

> [!WARNING]
> Logiken för växling vid fel och tillgänglighet som beskrivs i det här dokumentet kan inaktive ras i klient konfigurationen, vilket inte rekommenderas om inte användar programmet kommer att hantera tillgänglighets fel. Detta kan uppnås genom att:
>
> * Ange egenskapen [ConnectionPolicy. EnableEndpointRediscovery](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.enableendpointdiscovery) i .NET v2 SDK till false.
> * Ange egenskapen [CosmosClientOptions. LimitToEndpoint](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.limittoendpoint) i .net v3 SDK till true.
> * Ställer in metoden [CosmosClientBuilder. endpointDiscoveryEnabled](/java/api/com.azure.cosmos.cosmosclientbuilder.endpointdiscoveryenabled) i Java v4 SDK till false.
> * Om du anger parametern [CosmosClient.enable_endpoint_discovery](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) i python SDK till false.
> * Ange parametern [CosmosClientOptions. ConnectionPolicy. enableEndpointDiscovery](/javascript/api/@azure/cosmos/connectionpolicy#enableEndpointDiscovery) i JS SDK till false.

Under normala omständigheter ansluter SDK-klienten till den önskade regionen (om en regional inställning har angetts) eller till den primära regionen (om ingen inställning har angetts) och åtgärderna kommer att begränsas till den regionen, om inte något av nedanstående scenarier inträffar.

I dessa fall exponerar klienten som använder Azure Cosmos SDK loggar och innehåller information om återförsöket som en del av **åtgärdens diagnostiska information**:

* Egenskapen *RequestDiagnosticsString* i svar i .NET v2 SDK.
* Egenskapen *diagnostik* för svar och undantag i .net v3 SDK.
* Metoden *getDiagnostics()* i svar och undantag i Java v4 SDK.

När du bestämmer nästa region i prioritetsordning använder SDK-klienten listan konto region för att prioritera önskade regioner (om det finns några).

För en omfattande information om SLA-garantier under dessa händelser, se [service avtal för tillgänglighet](high-availability.md#slas-for-availability).

## <a name="removing-a-region-from-the-account"></a><a id="remove-region"></a>Ta bort en region från kontot

När du tar bort en region från ett Azure Cosmos-konto kommer alla SDK-klienter som använder kontot att identifiera regions borttagningen via en server dels svars kod. Klienten markerar sedan den regionala slut punkten som otillgänglig. Klienten försöker utföra den aktuella åtgärden igen och alla framtida åtgärder dirigeras permanent till nästa region i prioritetsordning. Om inställnings listan bara hade en post (eller var tom) men det finns andra tillgängliga regioner för kontot kommer det att dirigeras till nästa region i konto listan.

## <a name="adding-a-region-to-an-account"></a>Lägga till en region i ett konto

Var 5: e minut läser Azure Cosmos SDK-klienten konto konfigurationen och uppdaterar de regioner som den är medveten om.

Om du tar bort en region och senare lägger till den i kontot igen, om den tillagda regionen har en högre regional prioritetsordning i SDK-konfigurationen än den aktuella anslutna regionen, kommer SDK att växla tillbaka till att använda den här regionen permanent. När regionen har identifierats dirigeras alla framtida begär anden till den.

Om du konfigurerar klienten för att helst ansluta till en region som Azure Cosmos-kontot inte har, ignoreras den önskade regionen. Om du lägger till den regionen senare identifierar klienten den och kommer att växla permanent till den regionen.

## <a name="fail-over-the-write-region-in-a-single-write-region-account"></a><a id="manual-failover-single-region"></a>Redundansväxla Skriv region i ett konto för en Skriv region

Om du initierar en redundansväxling av den aktuella Skriv regionen kommer nästa Skriv förfrågan att Miss förfalla med ett känt Server dels svar. När det här svaret identifieras frågar klienten om kontot för att lära sig den nya Skriv regionen och fortsätter med att försöka utföra den aktuella åtgärden igen och dirigera alla framtida Skriv åtgärder till den nya regionen permanent.

## <a name="regional-outage"></a>Regionalt avbrott

Om kontot är en enda Skriv region och det regionala avbrottet inträffar under en Skriv åtgärd, liknar beteendet en [manuell redundansväxling](#manual-failover-single-region). För Läs begär Anden eller konton för flera Skriv regioner liknar beteendet att [ta bort en region](#remove-region).

## <a name="session-consistency-guarantees"></a>Konsekvens garanti för sessioner

När du använder konsekvens kontroll av [sessioner](consistency-levels.md#guarantees-associated-with-consistency-levels)måste klienten garantera att den kan läsa sina egna skrivningar. I ett konto med en Skriv region där inställningen Läs region skiljer sig från Skriv regionen, kan det finnas fall där användaren får en Skriv åtgärd och när en läsning görs från en lokal region, den lokala regionen har ännu inte tagit emot datareplikeringen (hastighet för ljus begränsning). I sådana fall identifierar SDK ett särskilt fel för Läs åtgärden och gör om läsningen på den primära regionen för att säkerställa konsekvensen i sessionen.

## <a name="transient-connectivity-issues-on-tcp-protocol"></a>Tillfälliga anslutnings problem i TCP-protokollet

I scenarier där Azure Cosmos SDK-klienten är konfigurerad för att använda TCP-protokollet för en viss begäran, kan det finnas situationer där nätverks förhållandena tillfälligt påverkar kommunikationen med en viss slut punkt. Dessa tillfälliga nätverks villkor kan vara en yta som TCP-tidsgräns och tjänsten inte är tillgänglig (HTTP 503)-fel. Klienten gör om begäran lokalt på samma slut punkt i några sekunder innan felet Visa.

Om användaren har konfigurerat en lista över önskade regioner med mer än en region och Azure Cosmos-kontot är flera Skriv regioner eller en enskild Skriv region och åtgärden är en Read-begäran, identifierar klienten det lokala fel och försöker igen med en enda åtgärd i nästa region från listan över inställningar.

## <a name="next-steps"></a>Nästa steg

* Granska [tillgänglighets service avtal](high-availability.md#slas-for-availability).
* Använd den senaste [.NET SDK: n](sql-api-sdk-dotnet-standard.md)
* Använd den senaste [Java SDK: n](sql-api-sdk-java-v4.md)
* Använd den senaste [python SDK: n](sql-api-sdk-python.md)
* Använd den senaste [NODEN SDK](sql-api-sdk-node.md)
