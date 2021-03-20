---
title: Haveri beredskap och geo-distribution av Azure Durable Functions
description: Lär dig mer om haveri beredskap och geo-distribution i Durable Functions.
author: MS-Santi
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: azfuncdf
ms.openlocfilehash: 01c400f51cce85ef39e9d39bcad1221253c6942d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89071218"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Haveri beredskap och geo-distribution i Azure Durable Functions

Microsoft strävar efter att se till att Azure-tjänster alltid är tillgängliga. Oplanerade drifts avbrott kan dock uppstå. Om ditt program kräver återhämtning rekommenderar Microsoft att du konfigurerar din app för GEO-redundans. Dessutom bör kunderna ha en katastrof återställnings plan för hantering av ett regionalt tjänst avbrott. En viktig del av en haveri beredskaps plan förbereds för växling till den sekundära repliken av din app och lagring i händelse av att den primära repliken blir otillgänglig.

I Durable Functions sparas all status som standard i Azure Storage. En [aktivitets hubb](durable-functions-task-hubs.md) är en logisk behållare för Azure Storage resurser som används för [dirigering](durable-functions-types-features-overview.md#orchestrator-functions) och [entiteter](durable-functions-types-features-overview.md#entity-functions). Funktioner för Orchestrator, aktivitet och entitet kan bara samverka med varandra när de tillhör samma aktivitets nav. Det här dokumentet refererar till aktivitets hubbar när du beskriver scenarier för att se till att dessa Azure Storage resurser har hög tillgänglighet.

Dirigeringar och entiteter kan utlösas med hjälp av [klient funktioner](durable-functions-types-features-overview.md#client-functions) som aktive ras via http eller någon av de andra typerna av utlösare som stöds Azure Functions. De kan också utlösas med [inbyggda HTTP-API: er](durable-functions-http-features.md#built-in-http-apis). För enkelhetens skull fokuserar den här artikeln på scenarier som rör Azure Storage-och HTTP-baserade funktions utlösare, samt alternativ för att öka tillgängligheten och minimera stillestånds tiden vid haveri beredskap. Andra utlösare, till exempel Service Bus eller Cosmos DB utlösare, kommer inte uttryckligen att omfattas.

Följande scenarier baseras på Active-Passive konfigurationer, eftersom de guidas av användningen av Azure Storage. Det här mönstret består av att distribuera en säkerhets kopiering (passiv) Function-app till en annan region. Traffic Manager övervakar den primära (aktiva) Function-appen för HTTP-tillgänglighet. Appen växlar över till säkerhets kopierings funktionen om den primära Miss lyckas. Mer information finns i Traffic-Routing-metoden för [Azure Traffic Managers](https://azure.microsoft.com/services/traffic-manager/) [prioritet.](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)

> [!NOTE]
> - Den föreslagna Active-Passive konfigurationen säkerställer att en klient alltid kan utlösa nya dirigeringar via HTTP. Till följd av att två Functions-appar delar samma aktivitetsvy i lagring, fördelas vissa lagrings transaktioner i bakgrunden mellan dem. Den här konfigurationen medför därmed några extra kostnader för den sekundära Function-appen.
> - Det underliggande lagrings kontot och aktivitets navet skapas i den primära regionen och delas av båda funktions apparna.
> - Alla funktions program som distribueras redundanta måste dela samma funktions åtkomst nycklar om de ska aktive ras via HTTP. Functions-körningen exponerar ett [hanterings-API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) som gör det möjligt för användare att program mässigt lägga till, ta bort och uppdatera funktions nycklar. Nyckel hantering är också möjligt med [Azure Resource Manager API: er](https://www.markheath.net/post/managing-azure-functions-keys-2).

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Scenario 1 – belastnings bal anse rad beräkning med delad lagring

Om beräknings infrastrukturen i Azure Miss lyckas kan Function-appen bli otillgänglig. I det här scenariot används två Function-appar som distribuerats till olika regioner för att minimera risken för sådana nedtid.
Traffic Manager har kon figurer ATS för att identifiera problem i den primära Function-appen och automatiskt omdirigera trafik till Function-appen i den sekundära regionen. Den här funktions appen delar samma Azure Storage konto och aktivitets nav. Därför försvinner inte statusen för Function-apparna och arbetet kan återupptas normalt. När hälso tillståndet har återställts till den primära regionen börjar Azure Traffic Manager dirigera begär anden till den Function-appen automatiskt.

![Diagram som visar scenario 1.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Det finns flera fördelar med att använda det här distributions scenariot:

- Om beräknings infrastrukturen Miss lyckas kan arbetet återupptas i området redundans utan data förlust.
- Traffic Manager tar hand om automatisk redundans till appen för felfri funktion automatiskt.
- Traffic Manager automatiskt återupprätta trafik till den primära Function-appen när avbrottet har åtgärd ATS.

Men i det här scenariot bör du tänka på följande:

- Om Function-appen distribueras med hjälp av en dedikerad App Service plan, ökar kostnaderna genom att replikera beräknings infrastrukturen i data centret för växling vid fel.
- Det här scenariot täcker avbrott i beräknings infrastrukturen, men lagrings kontot fortsätter att vara den enda felpunkten för Function-appen. Om ett lagrings avbrott inträffar uppstår det avbrott i programmet.
- Om Function-appen har redundansväxlats, ökar svars tiden eftersom den kommer åt sitt lagrings konto över flera regioner.
- Åtkomst till lagrings tjänsten från en annan region där den befinner sig i högre kostnad på grund av utgående trafik i nätverket.
- Det här scenariot är beroende av Traffic Manager. Med tanke på [hur Traffic Manager fungerar](../../traffic-manager/traffic-manager-how-it-works.md), kan det ta en stund innan ett klient program som förbrukar en varaktig funktion måste fråga igen appens adress från Traffic Manager.

> [!NOTE]
> Från och med **v-2.3.0** av Durable Functions-tillägget kan två Functions-appar köras på ett säkert sätt med samma lagrings konto och aktivitets NAVs konfiguration. Den första appen som startar kommer att få ett BLOB-lån på program nivå som förhindrar att andra appar kan stjäla meddelanden från aktivitets nav köer. Om den första appen slutar att köras upphör lånet att gälla och kan förvärvas av en andra app, som sedan fortsätter att bearbeta aktivitets NAVs meddelanden.
> 
> Innan v-2.3.0 kommer funktions appar som är konfigurerade att använda samma lagrings konto att bearbeta meddelanden och uppdatera lagrings artefakter samtidigt, vilket resulterar i mycket högre fördröjnings-och utgångs kostnader. Om de primära apparna och replik programmen någonsin har en annan kod som har distribuerats till dem, kan det också leda till att dirigeringen inte kan köras korrekt på grund av inkonsekvenser i Orchestrator-funktionen i de två apparna. Vi rekommenderar därför att alla appar som kräver geo-distribution för haveri beredskap använder v-2.3.0 eller högre av det varaktiga tillägget.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Scenario 2 – belastnings bal anse rad beräkning med regional lagring

Föregående scenario täcker bara händelse av ett haveri i beräknings infrastrukturen. Om lagrings tjänsten Miss lyckas resulterar det i ett avbrott i Function-appen.
För att säkerställa kontinuerlig drift av de varaktiga funktionerna använder det här scenariot ett lokalt lagrings konto i varje region som funktions programmen distribueras till.

![Diagram som visar scenario 2.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Den här metoden lägger till förbättringar i föregående scenario:

- Om funktions programmet Miss lyckas, Traffic Manager ta hand om att redundansväxla till den sekundära regionen. Men eftersom Function-appen är beroende av sitt eget lagrings konto fortsätter de varaktiga funktionerna att fungera.
- Under en redundansväxling finns det ingen ytterligare fördröjning i redundansklustret eftersom Function-appen och lagrings kontot samplaceras.
- Fel i lagrings lagret orsakar fel i de varaktiga funktionerna, vilket i sin tur utlöser en omdirigering till redundansväxlingen. Eftersom Function-appen och lagringen isoleras per region fortsätter de varaktiga funktionerna att fungera.

Viktiga överväganden för det här scenariot:

- Om Function-appen distribueras med hjälp av en dedikerad App Service plan, ökar kostnaderna genom att replikera beräknings infrastrukturen i data centret för växling vid fel.
- Det aktuella läget är inte redundansväxlats, vilket innebär att befintliga dirigeringar och entiteter kommer att pausas och vara otillgängliga tills den primära regionen återställs.

För att sammanfatta, är kompromissen mellan det första och andra scenariot att fördröjningen bevaras och utgående kostnader minimeras, men befintliga dirigeringar och entiteter blir otillgängliga under drift stoppet. Huruvida dessa kompromisser är acceptabla beror på programmets krav.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Scenario 3 – belastnings bal anse rad beräkning med GRS delad lagring

Det här scenariot är en ändring i det första scenariot, vilket implementerar ett delat lagrings konto. Den största skillnaden är att lagrings kontot skapas med geo-replikering aktiverat.
Det här scenariot ger samma fördelar som scenario 1, men det ger ytterligare fördelar för data återställning:

- Geo-redundant lagring (GRS) och Read-Access GRS (RA-GRS) maximerar tillgängligheten för ditt lagrings konto.
- Om det finns ett regionalt avbrott i lagrings tjänsten kan du [manuellt starta en redundansväxling till den sekundära repliken](../../storage/common/storage-initiate-account-failover.md). I extrema fall där en region försvinner på grund av en betydande katastrof kan Microsoft initiera en regional redundansväxling. I det här fallet krävs ingen åtgärd på din del.
- När en redundansväxling inträffar kommer tillstånd för de varaktiga funktionerna att bevaras upp till den senaste replikeringen av lagrings kontot, vilket vanligt vis sker vart några minuter.

Precis som med andra scenarier finns det viktiga överväganden:

- Det kan ta en stund att redundansväxla replikeringen till repliken. När redundansväxlingen är klar och Azure Storage DNS-poster har uppdaterats kommer funktions programmet att drabbas av ett avbrott.
- Det finns en ökad kostnad för att använda geo-replikerade lagrings konton.
- GRS-replikeringen kopierar dina data asynkront. Några av de senaste transaktionerna kan gå förlorade på grund av svars tiden för replikeringen.

![Diagram som visar scenario 3.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

> [!NOTE]
> Som beskrivs i Scenario 1 rekommenderar vi starkt att Function-appar som distribueras med den här strategin använder **v-2.3.0** eller högre av Durable Functions-tillägget.

Mer information finns i dokumentationen för [Azure Storage haveri beredskap och lagrings kontots redundans](../../storage/common/storage-disaster-recovery-guidance.md) .

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om att utforma program med hög tillgänglighet i Azure Storage](../../storage/common/geo-redundant-design.md)
