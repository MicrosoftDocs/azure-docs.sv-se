---
title: Viktig information för Azure SQL Edge
description: Viktig information om vad som är nytt eller vad som har ändrats i Azure SQL Edge-avbildningarna.
keywords: versions anteckningar SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 11/24/2020
ms.openlocfilehash: 6218715878ec40fecee79f1c93bf2ca1820af007
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277019"
---
# <a name="azure-sql-edge-release-notes"></a>Viktig information om Azure SQL Edge 

I den här artikeln beskrivs vad som är nytt och vad som har ändrats med varje ny version av Azure SQL Edge.

## <a name="azure-sql-edge-103"></a>Azure SQL Edge-1.0.3

SQL-motor build-15.0.2000.1554

### <a name="fixes"></a>Korrigeringar

- Uppgradera ONNX runtime till 1.5.3
- Uppdatera till Microsoft. SqlServer. DACFx-version 150.5084.2
- Diverse fel korrigeringar  
   
## <a name="azure-sql-edge-102"></a>Azure SQL Edge-1.0.2

SQL-motor build-15.0.2000.1554

### <a name="fixes"></a>Korrigeringar

- T-SQL-strömning  
   - Åtgärda i ägarskap och behörigheter för strömmande objekt
   - Loggning av förbättringar med logg rotation och logg för-prefix
   - Azure Stream Analytics: loggnings förbättringar, förbättra felkoden/fel meddelanden på kort 

- ONNX
    - Fel korrigeringar för scenarion med parallella frågor och fel i modell rensning
    - Uppgraderade ONNX runtime till 1.5.1

## <a name="azure-sql-edge-101"></a>Azure SQL Edge-1.0.1

SQL-motor build-15.0.2000.1553

### <a name="whats-new"></a>Nyheter

- Tillåt att Date_Bucket uttryck definieras i beräknade kolumner.

### <a name="fixes"></a>Korrigeringar

- Princip för bevarande princip för att släppa en tabell som har en bevarande princip aktive rad med oändlig tids gräns
- Stöd för DacFx-distribution för strömnings funktioner och kvarhållning av princip funktioner 
- DacFx distributions korrigering för att aktivera distribution från en kapslad mapp i en SAS-URL 
- FÖRUTSÄGELSE korrigering för att stödja långa kolumn namn i fel meddelanden

## <a name="azure-sql-edge-100-rtm"></a>Azure SQL Edge-1.0.0 (RTM)

SQL-motor build-15.0.2000.1552

### <a name="whats-new"></a>Nyheter
- Behållar avbildningar baserade på Ubuntu 18,04 
- Stöd för `IGNORE NULL` och `RESPECT NULL` syntax med `LAST_VALUE()` och `FIRST_VALUE()` Functions 
- Tillförlitlighets förbättringar för förutsägelse med ONNX
- Stöd för rensning baserat på data bevarande principen:
   - Stöd för ringbufferten för en rensnings åtgärd för kvarhållning av fel sökning
- Stöd för nya funktioner: 
   - Snabb återställning
   - Automatisk justering av frågor
   - Scenarier för parallell körning
- Energi besparings förbättringar för låg energi läge
- Strömmande New-Feature-support: 
   - [Ögonblicks bilds fönster](/stream-analytics-query/snapshot-window-azure-stream-analytics): med en ny fönster typ kan du gruppera efter händelser som kommer till samma tid.
   - [TopOne](/stream-analytics-query/topone-azure-stream-analytics) och [CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics) kan aktive ras som analys funktioner. Du kan returnera poster sorterade efter valfri kolumn. De behöver inte vara en del av ett fönster. 
   - Förbättringar av [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics). 

### <a name="fixes"></a>Korrigeringar
- Ytterligare fel meddelanden och information om fel sökning av T-SQL streaming-åtgärder 
- Förbättringar för att bevara batteri tiden i vilo läge 
- Snabb korrigeringar för T-SQL-direktuppspelning: 
   - Rensa för stoppade strömmande jobb 
   - Korrigeringar för lokalisering 
   - Förbättrad Unicode-hantering 
   - Förbättrad fel sökning för SQL Edge T-SQL streaming, vilket gör det möjligt för användare att söka efter jobb fel från get_streaming_job
- Rensning baserad på data bevarande princip: 
    - Korrigeringar för kvarhållning – princip skapande och rensnings scenarier
- Korrigeringar för aktiviteter i bakgrunden för att förbättra energi besparingarna för låg energi läge

### <a name="known-issues"></a>Kända problem 
- Det går inte att använda funktionen Date_Bucket T-SQL i en beräknad kolumn.


## <a name="ctp-23"></a>CTP 2,3
SQL-motor build-15.0.2000.1549
### <a name="whats-new"></a>Nyheter
- Stöd för anpassade ursprung i funktionen Date_Bucket () 
- Stöd för BACPAC-filer som en del av SQL-distributionen
- Stöd för rensning baserat på data bevarande principen:      
   - DDL-stöd för att aktivera bevarande principen 
   - Rensning av lagrade procedurer och bakgrunds rensnings uppgiften
   - Utökade händelser för att övervaka rensnings aktiviteter

### <a name="fixes"></a>Korrigeringar
- Ytterligare fel meddelanden och information om fel sökning av T-SQL streaming-åtgärder 
- Förbättringar för att bevara batteri tiden i vilo läge 
- Strömnings motor för T-SQL: 
   - Korrigering för fastnad vattenstämpel i hoppande-fönstret för under strömmar 
   - Korrigera för Ramverks undantags hantering för att se till att det samlas in som ett användar åtgärds fel


## <a name="ctp-22"></a>CTP 2,2
SQL-motor build-15.0.2000.1546
### <a name="whats-new"></a>Nyheter
- Stöd för icke-rot behållare 
- Stöd för insamling av användnings data och diagnostikdata 
- Strömnings uppdateringar för T-SQL:
   - Stöd för Unicode-tecken för strömmande objekt namn

### <a name="fixes"></a>Korrigeringar
- Strömnings uppdateringar för T-SQL:
   - Förbättringar av processen rensa
   - Förbättringar av loggning och diagnostik
- Prestanda förbättring för data inmatning

## <a name="ctp-21"></a>CTP 2,1 
SQL-motor build-15.0.2000.1545
### <a name="fixes"></a>Korrigeringar
- Tillåtit förutsägelse-with-ONNX-modeller för att hantera ett CPUID-problem i ARM 
- Förbättrad hantering av felsökväg när T-SQL streaming startar
- Korrigerat värde för fördröjningen för vattenstämpel i jobb mått när det inte finns några data.
- Åtgärda ett problem med utmatnings kortet när kortet har ett variabelt schema mellan batchar  

## <a name="ctp-20"></a>CTP 2,0 
SQL-motor build-15.0.2000.1401
### <a name="whats-new"></a>Nyheter
-   Produkt namnet har uppdaterats till *Azure SQL Edge*
-  Date_Bucket funktion:
    - Stöd för datum-, tids-och DateTime-typer
- FÖRUTSÄG med ONNX:
    - ONNX-krav för KÖRNINGs parametern  
- Stöd för T-SQL streaming (begränsad förhands granskning) 
 
### <a name="known-issues"></a>Kända problem

- Problem: möjliga fel vid tillämpning av DACPAC vid start på grund av ett tids inställnings problem.
- Lösning: starta om SQL Server. Annars försöker behållaren tillämpa DACPAC igen.

### <a name="request-support"></a>Support för begäran
Du kan begära support på [support Sidan](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Välj följande fält: 
- **Ärendetyp**: *Teknisk* 
- **Tjänst**: *IoT Edge*
- **Problem typ**: *mitt problem relaterar till en IoT Edge modul*
- **Problem under typ**: *Azure SQL Edge*

:::image type="content" source="media/get-support/support-ticket.png" alt-text="Skärm bild som visar ett exempel på ett support ärende.":::

## <a name="ctp-15"></a>CTP 1,5
SQL-motor build-15.0.2000.1331
### <a name="whats-new"></a>Nyheter
- Date_Bucket funktion:
    - Stöd för DateTimeOffset-typen
- FÖRUTSÄG med ONNX-modeller:
  - NVARCHAR-stöd
 
## <a name="ctp-14"></a>CTP 1,4
SQL-motor build-15.0.2000.1247
### <a name="whats-new"></a>Nyheter
-   FÖRUTSÄG med ONNX-modeller:
    - Stöd för VARCHAR
    - Migrering till ONNX runtime version 1,0 

- Följande funktioner är aktiverade:
  - CDC-stöd
  - Historik tabell med komprimering
  - En högre faktor för logg Read-Ahead
  - Batch-läge ES filter mottagnings
  - Optimeringar i Read-Ahead
 
## <a name="ctp-13"></a>CTP 1,3
SQL-motor build-15.0.2000.1147
### <a name="whats-new"></a>Nyheter
- Distribution av Azure IoT-portalen: 
    - Stöd för distribution av AMD64-och ARM-avbildningar
    - Stöd för att skapa strömmande jobb
    - DACPAC-distribution
- FÖRUTSÄG med ONNX-modeller:
    - Stöd för numerisk typ
- Följande funktioner är aktiverade:
    - Mottagnings agg regering till kolumn lagrings sökning
    - God go – runda genomsökningar
- Minskning av storlek och minnes förbrukning
