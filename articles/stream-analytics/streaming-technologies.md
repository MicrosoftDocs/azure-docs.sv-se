---
title: Välj en bearbetnings lösning i real tid och Stream i Azure
description: Lär dig mer om hur du väljer rätt teknik för analys och strömning i real tid för att bygga ditt program på Azure.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 4c10a91971357001723adcb783253c9867cf6d87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019065"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Välj en teknik för analys och strömning i real tid i Azure

Det finns flera tillgängliga tjänster för analys och strömning i real tid i Azure. Den här artikeln innehåller den information du behöver för att bestämma vilken teknik som passar bäst för ditt program.

## <a name="when-to-use-azure-stream-analytics"></a>När du ska använda Azure Stream Analytics

Azure Stream Analytics är den rekommenderade tjänsten för Stream Analytics på Azure. Den är avsedd för ett brett utbud av scenarier som omfattar men är inte begränsat till:

* Instrument paneler för data visualisering
* [Aviseringar](stream-analytics-set-up-alerts.md) i real tid från temporala och spatiala mönster eller avvikelser
* Extrahera, transformera, läsa in (ETL)
* [Mönster för händelse källor](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

Att lägga till ett Azure Stream Analytics jobb i ditt program är det snabbaste sättet att få fram direkt uppspelnings analys i Azure med det SQL-språk som du redan känner till. Azure Stream Analytics är en jobb tjänst så att du inte behöver ägna tid åt att hantera kluster, och du behöver inte bekymra dig om drift stopp med ett service avtal på 99,9% på jobb nivå. Faktureringen görs också på jobb nivå med låg belastning (en enhet för strömning), men skalbar (upp till 192 enheter för strömning). Det är mycket mer kostnads effektivt att köra några Stream Analytics jobb än att köra och underhålla ett kluster.

Azure Stream Analytics har en omfattande upplevelse av sig. Du kan direkt dra nytta av följande funktioner utan ytterligare inställningar:

* Inbyggda temporala operatorer, till exempel [fönster mängd](stream-analytics-window-functions.md), temporala kopplingar och temporala analys funktioner.
* Interna Azure- [indata](stream-analytics-add-inputs.md) och [utmatnings](stream-analytics-define-outputs.md) kort
* Stöd för långsam ändring av [referens data](stream-analytics-use-reference-data.md) (kallas även för uppslags tabeller), inklusive anslutning med geospatiala referens data för polystaket.
* Integrerade lösningar, till exempel [avvikelse identifiering](stream-analytics-machine-learning-anomaly-detection.md)
* Flera Time-fönster i samma fråga
* Möjlighet att skapa flera temporala operatörer i godtycklig sekvenser.
* Under 100-MS svars tid från slut punkt till slut punkt från indata till Event Hubs, för att nå landning i Event Hubs, inklusive nätverks fördröjning från och till Event Hubs, i varaktigt högt data flöde

## <a name="when-to-use-other-technologies"></a>När du ska använda andra tekniker

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Du vill skriva UDF: er, Uda: er och anpassade deserialiserare på ett annat språk än java script eller C #

Azure Stream Analytics stöder UDF (User Defined Functions) eller användardefinierade agg regeringar (UDA) i Java Script för moln jobb och C# för IoT Edge jobb. C#-användardefinierade avserialiseringar stöds också. Om du vill implementera en deserialiserare, en UDF-eller en UDA på andra språk, till exempel Java eller python, kan du använda Spark Structured streaming. Du kan också köra Event Hubs- **EventProcessorHost** på dina egna virtuella datorer för att utföra godtycklig strömnings bearbetning.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>Din lösning är i en miljö med flera moln eller i en lokal miljö

Azure Stream Analytics är Microsofts patentskyddade teknik och är bara tillgänglig i Azure. Om du behöver din lösning för att vara portabel över moln eller lokalt kan du använda tekniker med öppen källkod, till exempel Spark-strukturerad strömning eller storm.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Stream Analytics jobb genom att använda Azure Portal](stream-analytics-quick-create-portal.md)
* [Skapa ett Stream Analytics-jobb med hjälp av Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Skapa ett Stream Analytics jobb med hjälp av Visual Studio](stream-analytics-quick-create-vs.md)
* [Skapa ett Stream Analytics jobb med hjälp av Visual Studio Code](quick-create-visual-studio-code.md)
