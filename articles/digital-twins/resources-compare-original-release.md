---
title: Skillnader från den ursprungliga versionen
titleSuffix: Azure Digital Twins
description: Förstå vad som har ändrats i den nya versionen av Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 1/28/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 2bfd36b79ad800c14a68948aa8488e842d3f4def
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481182"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-original-version-2018"></a>Vad är det nya Azure Digital Twins? Hur skiljer det sig från den ursprungliga versionen (2018)?

Den första offentliga förhandsversionen av Azure Digital Twins släpptes i oktober 2018. Kärnbegreppen från den ursprungliga versionen har gått vidare till den aktuella tjänsten, men många av gränssnitten och implementeringsdetaljerna har ändrats för att göra tjänsten mer flexibel och tillgänglig. Dessa ändringar motiveras av kundfeedback.

> [!IMPORTANT]
> Mot bakgrund av den nya tjänstens utökade funktioner har den ursprungliga Azure Digital Twins tjänsten dragits tillbaka. Från och med januari 2021 är dess API:er och associerade data inte längre tillgängliga.

Om du använde den första versionen av Azure Digital Twins i den första offentliga förhandsversionen använder du informationen och bästa praxis i den här artikeln för att lära dig hur du arbetar med den aktuella tjänsten och dra nytta av dess funktioner.

## <a name="differences-by-topic"></a>Skillnader efter ämne

Diagrammet nedan innehåller en sida-vid-sida-vy över begrepp som har ändrats mellan den ursprungliga versionen av tjänsten och den aktuella tjänsten.

| Avsnitt | I den ursprungliga versionen | I den aktuella versionen |
| --- | --- | --- | --- |
| **Modellering**<br>*Mer flexibel* | Den ursprungliga versionen utformades runt smarta utrymmen, så den kom med en inbyggd vokabulär för byggnader. | Den aktuella Azure Digital Twins är domänoberoende. Du kan definiera dina egna anpassade vokabulär och anpassade modeller för din lösning för att representera fler typer av miljöer på mer flexibla sätt.<br><br>Läs mer i [*Begrepp: Anpassade modeller*](concepts-models.md). |
| **Topologi**<br>*Mer flexibel*| Den ursprungliga versionen hade stöd för en träddatastruktur som skräddarsytts för smarta utrymmen. Digitala tvillingar var kopplade till hierarkiska relationer. | Med den aktuella versionen kan dina digitala tvillingar anslutas till godtyckliga graf-topologier, ordnade hur du vill. Detta ger dig mer flexibilitet för att uttrycka de komplexa relationerna i den verkliga världen.<br><br>Läs mer i [*Begrepp: Digitala tvillingar och tvillingdiagrammet*](concepts-twins-graph.md). |
| **Beräkning**<br>*Mer omfattande och mer flexibelt* | I den ursprungliga versionen definierades logik för bearbetning av händelser och telemetri i användardefinierade JavaScript-funktioner (UDF: er). Felsökningen med UDF:er var begränsad. | Den aktuella versionen har en öppen beräkningsmodell: du tillhandahåller anpassad logik genom att koppla externa beräkningsresurser som [Azure Functions](../azure-functions/functions-overview.md). På så sätt kan du använda ett val av programmeringsspråk, komma åt anpassade kodbibliotek utan begränsningar och dra nytta av utvecklings- och felsökningsresurser som den externa tjänsten kan ha.<br><br>Läs mer i [*How-to: Set up an Azure function for processing data .: Konfigurera en Azure-funktion för bearbetning av data*](how-to-create-azure-function.md). |
| **Enhetshantering med IoT Hub**<br>*Mer tillgängligt* | Den ursprungliga versionen av hanterade enheter med en [instans IoT Hub](../iot-hub/about-iot-hub.md) som var intern för Azure Digital Twins tjänsten. Den här integrerade hubben var inte helt tillgänglig för utvecklare. | I den aktuella versionen "bring your own" IoT Hub, genom att koppla en oberoende skapad IoT Hub-instans (tillsammans med alla enheter som den redan hanterar). Detta ger dig fullständig åtkomst IoT Hub funktioner och ger dig kontroll över enhetshanteringen.<br><br>Läs mer [*i Så här matar du in telemetri från IoT Hub*](how-to-ingest-iot-hub-data.md). |
| **Säkerhet**<br>*Mer standard* | Den ursprungliga versionen hade fördefinierade roller som du kan använda för att hantera åtkomsten till din instans. | Den aktuella versionen integreras med samma [Azure-backend-tjänst för rollbaserad åtkomstkontroll (Azure RBAC)](../role-based-access-control/overview.md) som andra Azure-tjänster använder. Detta kan göra det enklare att autentisera mellan andra Azure-tjänster i din lösning, till exempel IoT Hub, Azure Functions, Event Grid med mera.<br>Med RBAC kan du fortfarande använda fördefinierade roller, eller så kan du skapa och konfigurera anpassade roller.<br><br>Läs mer i [*Begrepp: Säkerhet för Azure Digital Twins lösningar*](concepts-security.md). |
| **Skalbarhet**<br>*Större* | Den ursprungliga versionen hade skalningsbegränsningar för enheter, meddelanden, grafer och skalningsenheter. Endast en instans av Azure Digital Twins stöds per prenumeration.  | Den aktuella versionen förlitar sig på en ny arkitektur med förbättrad skalbarhet och har större beräkningskraft. Det stöder också 10 instanser per region, per prenumeration.<br><br>Se [*Azure Digital Twins tjänstbegränsningar*](reference-service-limits.md) för information om gränserna i den aktuella versionen. |

## <a name="service-limits"></a>Tjänstbegränsningar

En lista över Azure Digital Twins finns i Azure Digital Twins [*tjänstbegränsningar.*](reference-service-limits.md)

## <a name="next-steps"></a>Nästa steg

* Fördjupa dig i att arbeta med den aktuella versionen i snabbstarten: [*Snabbstart: Utforska ett exempelscenario*](quickstart-azure-digital-twins-explorer.md).

* Eller börja läsa om viktiga begrepp med [*Begrepp: Anpassade modeller*](concepts-models.md).