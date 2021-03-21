---
title: Azure Function-aktivitet i Azure Data Factory
description: Lär dig hur du använder Azure Function-aktiviteten för att köra en Azure-funktion i en Data Factory pipeline
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: 615fb1b9f5a9d87a8d69778930b7359823e1ec39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738029"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure Function-aktivitet i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Med Azure Function-aktiviteten kan du köra [Azure Functions](../azure-functions/functions-overview.md) i en Data Factory pipeline. Om du vill köra en Azure-funktion måste du skapa en länkad tjänst anslutning och en aktivitet som anger den Azure-funktion som du vill köra.

I en åtta minuters introduktion och demonstration av den här funktionen kan du titta på följande video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Länkad Azure Function-tjänst

Retur typen för Azure-funktionen måste vara giltig `JObject` . (Tänk på att [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *inte* är en `JObject` .) Andra retur typer än `JObject` Miss lyckas och det går inte att skicka användar fel *svars innehållet är inte ett giltigt JObject*.

| **Egenskap** | **Beskrivning** | **Obligatoriskt** |
| --- | --- | --- |
| typ   | Egenskapen Type måste anges till: **AzureFunction** | ja |
| URL till Function-app | URL för Azure-Funktionsapp. Formatet är `https://<accountname>.azurewebsites.net` . URL: en är värdet under **URL** -avsnittet när du visar Funktionsapp i Azure Portal  | ja |
| funktions nyckel | Åtkomst nyckel för Azure-funktionen. Klicka på **Hantera** -avsnittet för respektive funktion och kopiera antingen **funktions nyckeln** eller **värd nyckeln**. Läs mer här: [Azure Functions HTTP-utlösare och bindningar](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | ja |
|   |   |   |

## <a name="azure-function-activity"></a>Azure Function-aktivitet

| **Egenskap**  | **Beskrivning** | **Tillåtna värden** | **Obligatoriskt** |
| --- | --- | --- | --- |
| name  | Namn på aktiviteten i pipelinen  | Sträng | ja |
| typ  | Typ av aktivitet är ' AzureFunctionActivity ' | Sträng | ja |
| länkad tjänst | Den länkade Azure Function-tjänsten för motsvarande Azure-Funktionsapp  | Länkad tjänst referens | ja |
| funktions namn  | Namnet på funktionen i Azure-Funktionsapp som den här aktiviteten anropar | Sträng | ja |
| metod  | REST API metod för funktions anropet | Strängar som stöds: "GET", "POST", "placera"   | ja |
| sidhuvud  | Huvuden som skickas till begäran. Om du till exempel vill ange språk och typ på en begäran: "huvuden": {"Accept-language": "en-US", "innehålls typ": "Application/JSON"} | Sträng (eller uttryck med resultType för sträng) | Inga |
| body  | brödtext som skickas tillsammans med begäran till API-metoden för funktioner  | Sträng (eller uttryck med resultType för sträng) eller objekt.   | Krävs för metoder för att skicka/publicera |
|   |   |   | |

Se schemat för nytto lasten för begäran i [nytto Last schema](control-flow-web-activity.md#request-payload-schema) avsnittet.

## <a name="routing-and-queries"></a>Routning och frågor

Azure Function-aktiviteten stöder **routning**. Om din Azure-funktion till exempel har slut punkten  `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>` , `functionName` är att använda i Azure Function-aktiviteten `<functionName>/<value>` . Du kan Parameterisera den här funktionen för att tillhandahålla önskad `functionName` vid körning.

Azure Function-aktiviteten stöder också **frågor**. En fråga måste inkluderas som en del av `functionName` . Om funktions namnet till exempel är `HttpTriggerCSharp` och den fråga som du vill inkludera är `name=hello` , kan du skapa `functionName` i Azure Function-aktiviteten som `HttpTriggerCSharp?name=hello` . Den här funktionen kan vara parameterstyrda så att värdet kan fastställas vid körning.

## <a name="timeout-and-long-running-functions"></a>Tids gräns och tids krävande funktioner

Azure Functions nådde tids gränsen efter 230 sekunder oavsett vilken `functionTimeout` inställning du har konfigurerat i inställningarna. Mer information finns i [den här artikeln](../azure-functions/functions-versions.md#timeout). Undvik det här problemet genom att följa ett asynkront mönster eller använda Durable Functions. Fördelen med Durable Functions är att de erbjuder sin egen tillstånds spårnings funktion så att du inte behöver implementera din egen.

Läs mer om Durable Functions i [den här artikeln](../azure-functions/durable/durable-functions-overview.md). Du kan konfigurera en Azure Function-aktivitet för att anropa en varaktig funktion som returnerar ett svar med en annan URI, till exempel [det här exemplet](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery). Eftersom `statusQueryGetUri` returnerar HTTP-Status 202 när funktionen körs, kan du avsöka funktionens status med hjälp av en webb aktivitet. Skapa helt enkelt en webb aktivitet med `url` fältet inställt på `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri` . När den varaktiga funktionen har slutförts blir utdata från funktionen resultatet av webb aktiviteten.


## <a name="sample"></a>Exempel

Du kan hitta ett exempel på en Data Factory som använder en Azure-funktion för att extrahera innehållet i en tar-fil [här](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om aktiviteter i Data Factory i [pipelines och aktiviteter i Azure Data Factory](concepts-pipelines-activities.md).
