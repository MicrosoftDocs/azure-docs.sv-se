---
title: Konsumtions-baserat Server erbjudande i Azure Cosmos DB
description: Läs mer om Azure Cosmos DB förbruknings-baserade server lös erbjudande.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/08/2021
ms.openlocfilehash: 3ee8d5f36977a5a9f20c7e636118ffa9f6ee0b6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570991"
---
# <a name="azure-cosmos-db-serverless-preview"></a>Azure Cosmos DB utan server (för hands version)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Med Azure Cosmos DB server utan server kan du använda ditt Azure Cosmos-konto i en förbruknings punkt där du bara debiteras för de enheter för programbegäran som används av databas åtgärderna och det lagrings utrymme som förbrukas av dina data. Server fria behållare kan hantera tusentals begär Anden per sekund utan minsta avgift och ingen kapacitets planering krävs.

> [!IMPORTANT] 
> Har du några synpunkter om Server lös? Vi vill gärna höra! Du kan ta bort ett meddelande till Azure Cosmos DB utan Server Team: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

När du använder Azure Cosmos DB har varje databas åtgärd en kostnad uttryckt i [enheter för programbegäran](request-units.md). Hur du debiteras för den här kostnaden beror på vilken typ av Azure Cosmos-konto du använder:

- I det [etablerade data flödes](set-throughput.md) läget måste du genomföra en viss mängd data flöde (uttryckt i enheter för programbegäran per sekund) som är etablerade i databaserna och behållarna. Kostnaden för dina databas åtgärder dras sedan av från antalet enheter för programbegäran som är tillgängliga varje sekund. I slutet av fakturerings perioden debiteras du för den mängd data flöde som du har allokerat.
- I Server lös läge behöver du inte etablera några data flöden när du skapar behållare i ditt Azure Cosmos-konto. I slutet av din fakturerings period debiteras du för antalet enheter för programbegäran som har använts av databas åtgärderna.

## <a name="use-cases"></a>Användnings fall

Azure Cosmos DB server lös bästa passar scenarier där du förväntar dig **återkommande och oförutsägbar trafik** med långa inaktiva tider. Eftersom etablerings kapaciteten i sådana situationer inte är obligatorisk och kan vara kostnads förhindrad, bör Azure Cosmos DB utan server beaktas i följande användnings fall:

- Komma igång med Azure Cosmos DB
- Köra program med
    - burst, tillfällig trafik som är svår att prognostisera, eller
    - låg (<10%) genomsnittlig trafik grad i genomsnitt till högsta
- Utveckla, testa, prototypa och köra nya produktions program där trafik mönstret är okänt
- Integrera med Server lös beräknings tjänster som [Azure Functions](../azure-functions/functions-overview.md)

Mer information om hur du väljer det erbjudande som bäst passar din användnings fråga finns i artikeln om att välja mellan ett insamlat [data flöde och en server](throughput-serverless.md) lös artikel.

## <a name="using-serverless-resources"></a>Använda resurser utan Server

Server lös är en ny typ av Azure Cosmos-konto, vilket innebär att du måste välja mellan **allokerat data flöde** och **Server** lös när du skapar ett nytt konto. Du måste skapa ett nytt Server lös konto för att komma igång med Server lös. Under för hands versionen är det enda stödda sättet att skapa ett nytt Server lös konto genom [att använda Azure Portal](create-cosmosdb-resources-portal.md). Migrering av befintliga konton till/från läge utan Server stöds inte för närvarande.

Alla behållare som har skapats i ett Server lös konto är en server lös behållare. Server lös behållare exponerar samma funktioner som behållare som skapats i det etablerade data flödes läget, så att du kan läsa, skriva och fråga dina data på exakt samma sätt. Dock har Server lös konton och behållare också särskilda egenskaper:

> [!IMPORTANT]
> Vissa av de här begränsningarna kan vara minskat eller tas bort när servern blir allmänt tillgänglig och **din feedback** hjälper oss att avgöra! Kontakta oss och berätta mer om din server lös upplevelse: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

- Ett Server lös konto kan bara köras i en enda Azure-region. Det går inte att lägga till ytterligare Azure-regioner i ett Server lös konto när du har skapat den.
- Det går inte att aktivera för [hands versions funktionen för Synapse](synapse-link.md) på ett Server lös konto.
- Etablering av data flöde krävs inte på Server lös behållare, så följande uttryck är tillämpliga:
    - Du kan inte skicka några data flöden när du skapar en server lös behållare och gör så returnerar ett fel.
    - Du kan inte läsa eller uppdatera data flödet på en server lös behållare och detta returnerar ett fel.
    - Du kan inte skapa en delad data flödes databas i ett Server lös konto och detta returnerar ett fel.
- Server lös behållare kan lagra högst 50 GB data och index.

## <a name="monitoring-your-consumption"></a>Övervaka förbrukningen

Om du har använt Azure Cosmos DB i fördefinierat data flödes läge tidigare, kommer du att se att Server lös är mer kostnads effektivt när trafiken inte berättigar till etablerad kapacitet. Kompromissen är att kostnaderna blir mindre förutsägbara eftersom du faktureras baserat på antalet begär Anden som databasen har bearbetat. Därför är det viktigt att hålla ett öga på din nuvarande förbrukning.

När du bläddrar i fönstret **mått** i ditt konto hittar du ett diagram med namnet **enheter som förbrukas** på fliken **Översikt** . Det här diagrammet visar hur många enheter för programbegäran som ditt konto har förbrukat:

:::image type="content" source="./media/serverless/request-units-consumed.png" alt-text="Diagram över förbrukade begär ande enheter" border="false":::

Du kan hitta samma diagram när du använder Azure Monitor, enligt beskrivningen [här](monitor-request-unit-usage.md). Observera att Azure Monitor låter dig konfigurera [aviseringar](../azure-monitor/alerts/alerts-metric-overview.md), som kan användas för att meddela dig när den begärda enhets förbrukningen har passerat ett visst tröskelvärde.

## <a name="performance"></a><a id="performance"></a>Prestanda

Utan server resurser ger särskilda prestanda egenskaper som skiljer sig från vad etablerade data flödes resurser levererar. När det Server lösa erbjudandet blir allmänt tillgängligt, kommer svars tiden för Server lösa behållare att omfattas av ett service nivå mål på 10 millisekunder eller mindre för punkt-och 30 millisekunder eller mindre för skrivningar. En punkt-Läs åtgärd består i att hämta ett enskilt objekt med dess ID-och partitionsnyckel-värde.

## <a name="next-steps"></a>Nästa steg

Kom igång med Server lös med följande artiklar:

- [Enheter för programbegäran i Azure Cosmos DB](request-units.md)
- [Välj mellan etablerat dataflöde och serverlöst](throughput-serverless.md)
- [Prismodell i Azure Cosmos DB](how-pricing-works.md)
