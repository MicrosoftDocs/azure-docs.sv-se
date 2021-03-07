---
title: Händelsevägar
titleSuffix: Azure Digital Twins
description: Förstå hur du dirigerar händelser i Azure Digitals dubbla och till andra Azure-tjänster.
author: baanders
ms.author: baanders
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ea412b695c12f3ff7fdfa6250e2a474b618b8032
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430929"
---
# <a name="route-events-within-and-outside-of-azure-digital-twins"></a>Dirigera händelser inom och utanför Azures digitala dubbla

Azure Digitals dubbla använder **händelse vägar** för att skicka data till konsumenter utanför tjänsten. 

Det finns två viktiga fall för att skicka Azure Digitals dubbla data:
* Skicka data från en mitt i det digitala Azure-diagrammet till en annan. Om du till exempel har en egenskap på en digital, dubbla ändringar kanske du vill meddela och uppdatera en annan digital i enlighet med detta.
* Skicka data till underordnade data tjänster för ytterligare lagring eller bearbetning (kallas även *utgående data*). Till exempel,
  - Ett sjukhus kan vilja skicka data för Azure Digitals dubbla data till [Time Series Insights (TSD)](../time-series-insights/overview-what-is-tsi.md)för att registrera Time Series-data för handwashing-relaterade händelser för Mass analys.
  - En verksamhet som redan använder [Azure Maps](../azure-maps/about-azure-maps.md) kanske vill använda Azure Digital-dubbla för att förbättra sin lösning. De kan snabbt aktivera en Azure-karta när du har konfigurerat Azure Digitals-enheter, ta Azure Map-entiteter i Azure Digitals-enheter som [digitala](concepts-twins-graph.md) delar i den dubbla grafen eller kör kraftfulla frågor som använder sina Azure Maps och Azure Digitals sammanställda data tillsammans.

Händelse vägar används för båda dessa scenarier.

## <a name="about-event-routes"></a>Om händelse vägar

Med en händelse väg kan du skicka händelse data från digitala sändningar i Azure Digitals dubbla till anpassade slut punkter i dina prenumerationer. Tre Azure-tjänster stöds för närvarande för slut punkter: [händelsehubben](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md)och [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). Var och en av dessa Azure-tjänster kan anslutas till andra tjänster och fungerar som mellanman och skickar data till slutgiltiga destinationer, till exempel TSD eller Azure Maps för vilken bearbetning du behöver.

I följande diagram illustreras flödet av händelse data via en större IoT-lösning med en digital Azure-dataaspekt:

:::image type="content" source="media/concepts-route-events/routing-workflow.png" alt-text="Azure Digital sammanflätar data via slut punkter till flera underordnade tjänster" border="false":::

Vanliga underordnade mål för händelse vägar är resurser som TSD, Azure Maps, lagring och analys lösningar.

### <a name="event-routes-for-internal-digital-twin-events"></a>Händelse vägar för interna digitala dubbla händelser

Händelse vägar används också för att hantera händelser i den dubbla grafen och skicka data från digitala dubbla till digitala dubbla. Detta görs genom att du ansluter händelse vägar via Event Grid för att beräkna resurser, till exempel [Azure Functions](../azure-functions/functions-overview.md). Dessa funktioner definierar sedan hur dubbla ska ta emot och svara på händelser. 

När en beräknings resurs vill ändra det dubbla diagrammet baserat på en händelse som tas emot via händelse vägen, är det bra att veta vilken som är den som ska ändras i förväg. 

Alternativt innehåller händelse meddelandet också ID: t för den källa som skickade meddelandet, så att beräknings resursen kan använda frågor eller passera relationer för att hitta ett mål som är två för önskad åtgärd. 

Beräknings resursen måste också upprätta säkerhets-och åtkomst behörigheter oberoende av varandra.

Information om hur du konfigurerar en Azure-funktion för att bearbeta digitala dubbla händelser finns i [*How-to: Konfigurera en Azure-funktion för bearbetning av data*](how-to-create-azure-function.md).

## <a name="create-an-endpoint"></a>Skapa en slutpunkt

För att kunna definiera en händelseväg måste utvecklarna först definiera slutpunkter. En **slut punkt** är ett mål utanför Azures digitala dubbla, som har stöd för en väg anslutning. Destinationer som stöds är:
* Event Grid anpassade ämnen
* Händelsehubb
* Service Bus

Om du vill skapa en slut punkt kan du använda Azure Digitals [REST-API: er, CLI-kommandon](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins)eller [Azure Portal](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins).

När du definierar en slut punkt måste du ange:
* Slut punktens namn
* Slut punkts typ (Event Grid, Händelsehubben eller Service Bus)
* Den primära anslutnings strängen och den sekundära anslutnings strängen som ska autentiseras 
* Avsnitts Sök vägen för slut punkten, till exempel *Your-topic.westus2.eventgrid.Azure.net*

Slut punkts-API: erna som är tillgängliga i kontroll planet är:
* Skapa slut punkt
* Hämta lista över slut punkter
* Hämta slut punkt efter namn
* Ta bort slut punkt efter namn

## <a name="create-an-event-route"></a>Skapa en händelse väg
 
Om du vill skapa en händelse väg kan du använda Azure Digitals [REST-API: er, CLI-kommandon](how-to-manage-routes-apis-cli.md#create-an-event-route)eller [Azure Portal](how-to-manage-routes-portal.md#create-an-event-route).

Här är ett exempel på hur du skapar en händelse väg i ett klient program med hjälp av `CreateOrReplaceEventRouteAsync` [.net (C#) SDK-](/dotnet/api/overview/azure/digitaltwins/client) anropet: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::

1. Först skapas ett `DigitalTwinsEventRoute` -objekt och konstruktorn tar namnet på en slut punkt. I det här `endpointName` fältet identifieras en slut punkt som händelsehubben, Event Grid eller Service Bus. De här slut punkterna måste skapas i din prenumeration och anslutas till Azure Digital-dubbla med kontroll Plans-API: er innan det här registrerings anropet görs.

2. Objektet för händelse vägen har också ett [**filter**](how-to-manage-routes-apis-cli.md#filter-events) fält som kan användas för att begränsa vilka typer av händelser som följer den här vägen. Ett filter för `true` aktiverar vägen utan ytterligare filtrering (ett filter av `false` inaktiverar vägen). 

3. Objektet för händelse vägen skickas sedan till `CreateOrReplaceEventRouteAsync` , tillsammans med ett namn för vägen.

> [!TIP]
> Alla SDK-funktioner ingår i synkrona och asynkrona versioner.

## <a name="dead-letter-events"></a>Händelser för obeställbara meddelanden

När en slut punkt inte kan leverera en händelse inom en viss tids period eller när händelsen försöker leverera händelsen ett visst antal gånger, kan den skicka den ej levererade händelsen till ett lagrings konto. Den här processen kallas för **obeställbara meddelanden**. Digitala Azure-dubblare kommer att döda en händelse när **något av följande** villkor uppfylls. 

* Händelsen har inte levererats inom Time-to-Live-perioden
* Antalet försök att leverera händelsen har överskridit gränsen

Om något av villkoren är uppfyllt tas händelsen bort eller tas bort från kön. Som standard aktiverar varje slut punkt **inte** obeställbara meddelanden. Om du vill aktivera det måste du ange ett lagrings konto som ska innehålla ej levererade händelser när slut punkten skapas. Du kan sedan hämta händelser från det här lagrings kontot för att lösa leveranser.

Innan du anger platsen för obeställbara meddelanden måste du ha ett lagrings konto med en behållare. Du anger URL: en för den här behållaren när du skapar slut punkten. Obeställbara meddelanden anges som en behållar-URL med en SAS-token. Denna token behöver bara `write` behörighet för mål behållaren i lagrings kontot. Den fullständigt utformade URL: en kommer att ha formatet: `https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>`

Mer information om SAS-token finns i: [ *bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av signaturer för delad åtkomst (SAS)*](../storage/common/storage-sas-overview.md)

Information om hur du skapar en slut punkt med obeställbara meddelanden finns i så här gör du för att [*Hantera slut punkter och vägar i Azure Digitals dubbla (API: er och CLI)*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering).

### <a name="types-of-event-messages"></a>Typer av händelse meddelanden

Olika typer av händelser i IoT Hub och Azure Digitals skapar olika typer av meddelanden, enligt beskrivningen nedan.

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="next-steps"></a>Nästa steg

Se så här konfigurerar och hanterar du en händelse väg:
* [*Anvisningar: hantera slut punkter och vägar*](how-to-manage-routes-apis-cli.md)

Du kan också se hur du använder Azure Functions för att dirigera händelser i Azure Digitals:
* [*Anvisningar: Konfigurera en Azure-funktion för bearbetning av data*](how-to-create-azure-function.md)