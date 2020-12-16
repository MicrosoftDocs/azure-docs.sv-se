---
title: Skillnader från den första versionen
titleSuffix: Azure Digital Twins
description: Förstå vad som har ändrats i den nya versionen av Azure Digitals
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: fb0d3e3c57a26f7ca14b74edc42cb657ba6074c3
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605013"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-previous-version-2018"></a>Vad är den nya digitala Azure-gruppen? Hur skiljer den sig från den tidigare versionen (2018)?

Den första offentliga för hands versionen av Azure Digitals, släpptes i oktober 2018. Även om kärn begreppen från den första versionen har överförts till den nya tjänsten, har många av gränssnitten och implementerings detaljerna ändrats för att göra tjänsten mer flexibel och tillgänglig. Ändringarna har motiverats av kundfeedback.

> [!IMPORTANT]
> Med hänsyn till de nya tjänsternas utökade funktioner kommer den tidigare Azure Digital-tjänsten att dras tillbaka, med API: er och associerade data som inte längre är tillgängliga från och med 18 januari 2021.

Om du använde den första versionen av Azure Digitals dubbla i den första offentliga för hands versionen kan du använda informationen och metod tipsen i den här artikeln för att lära dig hur du arbetar med den nya tjänsten och hur du utnyttjar dess funktioner.

## <a name="differences-by-topic"></a>Skillnader efter ämne

I diagrammet nedan visas en sida-vid-sida-vy över koncept som har ändrats mellan den tidigare versionen av tjänsten och den nya (aktuella) tjänsten.

| Ämne | I tidigare version | I ny version |
| --- | --- | --- | --- |
| **Modellering**<br>*Mer flexibelt* | Den tidigare versionen har utformats runt smarta utrymmen, så den följde med en inbyggd vokabulär för byggnader. | De nya Azure Digital-nyheterna är domän-oberoende. Du kan definiera en egen anpassad vokabulär och anpassade modeller för din lösning, för att representera fler typer av miljöer på mer flexibla sätt.<br><br>Lär dig mer i [*begrepp: anpassade modeller*](concepts-models.md). |
| **Topologi**<br>*Mer flexibelt*| Den tidigare versionen stödde en träd data struktur som är anpassad till smarta utrymmen. Digitala dubbla band var anslutna med hierarkiska relationer. | Med den nya versionen kan dina digitala dubbla, vara anslutna till valfria grafer, som du också har ordnat. Detta ger dig större flexibilitet att uttrycka de komplexa relationerna i verkligheten.<br><br>Lär dig mer i [*begrepp: Digital-och dubbel diagram*](concepts-twins-graph.md). |
| **Beräkning**<br>*Rikare, mer flexibel* | I den tidigare versionen har logik för bearbetning av händelser och telemetri definierats i JavaScript-användardefinierade funktioner (UDF: er). Fel sökning med UDF: er var begränsad. | Den nya versionen har en öppen Compute-modell: du anger anpassad logik genom att koppla externa beräknings resurser som [Azure Functions](../azure-functions/functions-overview.md). På så sätt kan du använda ett valfritt programmeringsspråk, komma åt anpassade kod bibliotek utan begränsning och dra nytta av utvecklings-och fel söknings resurser som den externa tjänsten kan ha.<br><br>Läs mer i [*anvisningar: Konfigurera en Azure-funktion för bearbetning av data*](how-to-create-azure-function.md). |
| **Enhets hantering med IoT Hub**<br>*Mer tillgängligt* | Den tidigare versionen hanterade enheter med en instans av [IoT Hub](../iot-hub/about-iot-hub.md) som var intern för tjänsten Azure Digitals dubbla. Det här integrerade navet var inte fullt åtkomligt för utvecklare. | I den nya versionen kan du "ta med din egen" IoT Hub genom att koppla en fristående IoT Hub instans (tillsammans med alla enheter som den redan hanterar). Detta ger dig fullständig åtkomst till IoT Hubs funktioner och ger dig kontroll över enhets hantering.<br><br>Lär dig mer i [*How-to: intag av telemetri från IoT Hub*](how-to-ingest-iot-hub-data.md). |
| **Säkerhet**<br>*Mer standard* | Den tidigare versionen hade fördefinierade roller som du kan använda för att hantera åtkomst till din instans. | Den nya versionen integreras med samma [Azure rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md) backend-tjänst som andra Azure-tjänster använder. Detta kan göra det enklare att autentisera mellan andra Azure-tjänster i din lösning, t. ex. IoT Hub, Azure Functions, Event Grid med mera.<br>Med RBAC kan du fortfarande använda fördefinierade roller, eller så kan du bygga och konfigurera anpassade roller.<br><br>Läs mer i [*begrepp: säkerhet för Azure Digitals dubbla lösningar*](concepts-security.md). |
| **Skalbarhet**<br>*Störst* | Den tidigare versionen hade skalnings begränsningar för enheter, meddelanden, grafer och skalnings enheter. Endast en instans av Azure Digitals dubbla stöds per prenumeration.  | Den nya versionen är beroende av en ny arkitektur med förbättrad skalbarhet och har större beräknings kraft. Den har också stöd för 10 instanser per region, per prenumeration.<br><br>Se [*referens: tjänst begränsningar*](reference-service-limits.md) för mer information om gränserna i den aktuella versionen. |

## <a name="service-limits"></a>Tjänstbegränsningar

En lista över gränser för digital Digitals dubbla gränser finns i [*referens: tjänst begränsningar*](reference-service-limits.md).

## <a name="next-steps"></a>Nästa steg

Börja sedan med att arbeta med Azure Digitals dubbla med den första självstudien:

[*Självstudie: koda en klient app*](tutorial-code.md)