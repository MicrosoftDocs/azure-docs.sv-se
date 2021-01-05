---
title: Översikt över Azure Functions
description: Lär dig hur Azure Functions kan hjälpa dig att bygga robusta appar utan server.
author: craigshoemaker
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 11/20/2020
ms.author: cshoe
ms.custom: contperf-fy21q2
ms.openlocfilehash: 41b627259d84539c868f95eb3cf33db5dbdab52c
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97862396"
---
# <a name="introduction-to-azure-functions"></a>Introduktion till Azure Functions

Azure Functions är en server lös lösning som gör att du kan skriva mindre kod, upprätthålla mindre infrastruktur och spara pengar. I stället för att oroa dig för att distribuera och underhålla servrar tillhandahåller moln infrastrukturen alla uppdaterade resurser som behövs för att se till att dina program körs.

Du fokuserar på de delar av koden som är viktigast för dig och Azure Functions hanterar resten.<br /><br />

> [!VIDEO https://www.youtube.com/embed/8-jz5f_JyEQ]

Vi skapar ofta system för att reagera på en serie kritiska händelser. Oavsett om du skapar ett webb-API, svarar på databas ändringar, bearbetar IoT-dataströmmar eller till och med hanterar meddelande köer – varje program behöver ett sätt att köra viss kod när dessa händelser inträffar.

För att uppfylla detta behov tillhandahåller Azure Functions "Compute on-demand" på två betydande sätt.

För det första, Azure Functions gör det möjligt att implementera systemets logik i snabbt tillgängliga kodblock. Kodblock kallas för "Functions". Olika funktioner kan köras när du behöver svara på kritiska händelser.

För det andra, som begär Anden ökar, Azure Functions uppfyller efter frågan med så många resurser och funktions instanser som behövs – men bara vid behov. När förfrågningarna blir borta, släpps alla extra resurser och program instanser automatiskt.

Var kommer alla beräknings resurser från? Azure Functions [tillhandahåller så många eller så få beräknings resurser som behövs](./functions-scale.md) för att uppfylla ditt programs behov.

Att tillhandahålla beräknings resurser på begäran är grunden för [data behandling utan server](https://azure.microsoft.com/solutions/serverless/) i Azure Functions.

## <a name="scenarios"></a>Scenarier

I många fall [integreras en funktion med en matris med moln tjänster](./functions-triggers-bindings.md) för att tillhandahålla funktions rika implementeringar.

Följande är ett vanligt, _men inte heller en uttömmande_ uppsättning scenarier för Azure Functions.

| Om du vill... | sedan... |
| --- | --- |
| **Bygg ett webb-API** | Implementera en slut punkt för dina webb program med hjälp av [http-utlösaren](./functions-bindings-http-webhook.md) |
| **Bearbeta fil överföringar** | Kör kod när en fil laddas upp eller ändras i [Blob Storage](./functions-bindings-storage-blob.md) |
| **Bygg ett Server lös arbets flöde** | Kedja en serie funktioner tillsammans med hjälp av [varaktiga funktioner](./durable/durable-functions-overview.md) |
| **Svara på databas ändringar** | Kör anpassad logik när ett dokument skapas eller uppdateras i [Cosmos DB](./functions-bindings-cosmosdb-v2.md) |
| **Köra schemalagda aktiviteter** | Kör kod vid [angivna tider](./functions-bindings-timer.md) |
| **Skapa Reliable Message Queue-system** | Bearbeta meddelande köer med hjälp av [Queue Storage](./functions-bindings-storage-queue.md), [Service Bus](./functions-bindings-service-bus.md)eller [Event Hubs](./functions-bindings-event-hubs.md) |
| **Analysera IoT-dataströmmar** | Samla in och bearbeta [data från IoT-enheter](./functions-bindings-event-iot.md) |
| **Bearbeta data i real tid** | Använd [Functions och signalerer](./functions-bindings-signalr-service.md) för att svara på data för tillfället |

När du skapar dina funktioner har du följande alternativ och tillgängliga resurser:

- **Använd önskat språk**: Skriv funktioner i [C#, Java, Java Script, PowerShell eller python](./supported-languages.md), eller Använd en [anpassad hanterare](./functions-custom-handlers.md) för att använda i stort sett alla andra språk.

- **Automatisera distribution**: från en verktyg-baserad metod till att använda externa pipeliner finns det en [myriaden tillgängliga distributions alternativ](./functions-deployment-technologies.md) .

- **Felsöka en funktion**: använda [övervaknings verktyg](./functions-monitoring.md) och [testnings strategier](./functions-test-a-function.md) för att få insikter om dina appar.

- **Flexibla pris alternativ**: med [förbruknings](./pricing.md) planen betalar du bara medan funktionerna körs, medan [Premium](./pricing.md) -och [App Service](./pricing.md) -planerna erbjuder funktioner för specialiserade behov.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång genom lektioner, exempel och interaktiva självstudier](./functions-get-started.md)
