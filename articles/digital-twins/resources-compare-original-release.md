---
title: Skillnader jämfört med den ursprungliga versionen
titleSuffix: Azure Digital Twins
description: Förstå vad som har ändrats i den nya versionen av Azure Digitals
author: baanders
ms.author: baanders
ms.date: 1/28/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 8010581667354f2e8484bc7341227ec41713d10f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99072726"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-original-version-2018"></a>Vad är den nya digitala Azure-gruppen? Hur skiljer den sig från den ursprungliga versionen (2018)?

Den första offentliga för hands versionen av Azure Digitals, släpptes i oktober 2018. När kärn begreppen från den ursprungliga versionen har överförts till den aktuella tjänsten har många av gränssnitten och implementerings detaljerna ändrats för att göra tjänsten mer flexibel och tillgänglig. Ändringarna har motiverats av kundfeedback.

> [!IMPORTANT]
> Med hänsyn till de nya tjänsternas utökade funktioner, har den ursprungliga Azure Digital-tjänsten som har dragits tillbaka. Från och med januari 2021 är dess API: er och associerade data inte längre tillgängliga.

Om du använde den första versionen av Azure Digitals dubbla i den första offentliga för hands versionen kan du använda informationen och metod tipsen i den här artikeln för att lära dig hur du arbetar med den aktuella tjänsten och hur du utnyttjar dess funktioner.

## <a name="differences-by-topic"></a>Skillnader efter ämne

I diagrammet nedan visas en sida-vid-sida-vy över koncept som har ändrats mellan den ursprungliga versionen av tjänsten och den aktuella tjänsten.

| Avsnitt | I ursprunglig version | I aktuell version |
| --- | --- | --- | --- |
| **Modellering**<br>*Mer flexibelt* | Den ursprungliga versionen är utformad runt smarta utrymmen, så den följde med en inbyggd vokabulär för byggnader. | De aktuella Azure Digital-dubbla är domän-oberoende. Du kan definiera en egen anpassad vokabulär och anpassade modeller för din lösning, för att representera fler typer av miljöer på mer flexibla sätt.<br><br>Lär dig mer i [*begrepp: anpassade modeller*](concepts-models.md). |
| **Topologi**<br>*Mer flexibelt*| Den ursprungliga versionen stödde en träd data struktur som är anpassad till smarta utrymmen. Digitala dubbla band var anslutna med hierarkiska relationer. | Med den aktuella versionen kan dina digitala dubbla, vara anslutna till valfria grafer, som du också vill. Detta ger dig större flexibilitet att uttrycka de komplexa relationerna i verkligheten.<br><br>Lär dig mer i [*begrepp: Digital-och dubbel diagram*](concepts-twins-graph.md). |
| **Beräkning**<br>*Rikare, mer flexibel* | I den ursprungliga versionen har logik för bearbetning av händelser och telemetri definierats i JavaScript-användardefinierade funktioner (UDF: er). Fel sökning med UDF: er var begränsad. | Den aktuella versionen har en öppen beräknings modell: du anger anpassad logik genom att koppla externa beräknings resurser som [Azure Functions](../azure-functions/functions-overview.md). På så sätt kan du använda ett valfritt programmeringsspråk, komma åt anpassade kod bibliotek utan begränsning och dra nytta av utvecklings-och fel söknings resurser som den externa tjänsten kan ha.<br><br>Läs mer i [*anvisningar: Konfigurera en Azure-funktion för bearbetning av data*](how-to-create-azure-function.md). |
| **Enhets hantering med IoT Hub**<br>*Mer tillgängligt* | Den ursprungliga versionen hanterade enheter med en instans av [IoT Hub](../iot-hub/about-iot-hub.md) som var intern för tjänsten Azure Digitals dubbla. Det här integrerade navet var inte fullt åtkomligt för utvecklare. | I den aktuella versionen kan du "ta med din egen" IoT Hub genom att koppla en fristående IoT Hub instans (tillsammans med alla enheter som den redan hanterar). Detta ger dig fullständig åtkomst till IoT Hubs funktioner och ger dig kontroll över enhets hantering.<br><br>Lär dig mer i [*How-to: intag av telemetri från IoT Hub*](how-to-ingest-iot-hub-data.md). |
| **Säkerhet**<br>*Mer standard* | Den ursprungliga versionen hade fördefinierade roller som du kan använda för att hantera åtkomst till din instans. | Den aktuella versionen integreras med samma [Azure-baserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md) backend-tjänst som andra Azure-tjänster använder. Detta kan göra det enklare att autentisera mellan andra Azure-tjänster i din lösning, t. ex. IoT Hub, Azure Functions, Event Grid med mera.<br>Med RBAC kan du fortfarande använda fördefinierade roller, eller så kan du bygga och konfigurera anpassade roller.<br><br>Läs mer i [*begrepp: säkerhet för Azure Digitals dubbla lösningar*](concepts-security.md). |
| **Skalbarhet**<br>*Störst* | Den ursprungliga versionen hade skalnings begränsningar för enheter, meddelanden, grafer och skalnings enheter. Endast en instans av Azure Digitals dubbla stöds per prenumeration.  | Den aktuella versionen är beroende av en ny arkitektur med förbättrad skalbarhet och har större beräknings kraft. Den har också stöd för 10 instanser per region, per prenumeration.<br><br>Mer information om gränserna i den aktuella versionen finns i [*gränser för Azure Digitals dubbla tjänster*](reference-service-limits.md) . |

## <a name="service-limits"></a>Tjänstbegränsningar

En lista över gränserna för Azure Digitals dubbla gränser finns i [*gränser för Azure Digitals dubbla tjänster*](reference-service-limits.md).

## <a name="next-steps"></a>Nästa steg

* Lär dig att arbeta med den aktuella versionen i snabb starten: [*snabb start: utforska ett exempel scenario*](quickstart-adt-explorer.md).

* Eller börja läsa om viktiga begrepp med [*koncept: anpassade modeller*](concepts-models.md).