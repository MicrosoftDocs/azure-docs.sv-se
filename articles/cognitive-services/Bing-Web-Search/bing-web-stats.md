---
title: Lägg till analyser i API för webbsökning i Bing
titleSuffix: Azure Cognitive Services
description: Bing-statistik ger analys till API för bildsökning i Bing. Analytics inkluderar anrops volym, högsta frågesträng, geografisk distribution med mera.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 02fea02cca5950ef8467377a866e9a765af9e2e1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96349562"
---
# <a name="add-analytics-to-the-bing-search-apis"></a>Lägg till analyser i API:er för Bing-sökresultat

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bing-statistik ger analyser för API:er för Bing-sökresultat. Dessa analyser inkluderar anrops volym, högsta frågesträng, geografisk distribution med mera. Du kan aktivera Bing-statistik i [Azure Portal](https://ms.portal.azure.com) genom att gå till Azure-resursen och klicka på **Aktivera Bing-statistik**.

> [!IMPORTANT]
> * Bing-statistik är inte tillgänglig med resurser på den kostnads fria `F0` pris nivån.
> * Du får inte använda några tillgängliga data via Bing Statistics-instrumentpanelen för att skapa program för distribution till tredje part.
> * Om du aktiverar Bing-statistik ökar prenumerations hastigheten något. Se [pris](https://aka.ms/bingstatisticspricing) information.


Följande bild visar tillgängliga analyser för varje Bing-sökning API-slutpunkt.

![Support mat ris för distribution per slut punkt](./media/bing-statistics/bing-statistics-matrix.png)

## <a name="access-your-analytics"></a>Få åtkomst till din analys

Bing uppdaterar analys data var 24: e timme och underhåller upp till 13 månaders historik som du kan komma åt från [analys instrument panelen](https://bingapistatistics.com). Kontrol lera att du har loggat in med samma Microsoft-konto (MSA) som du använde för att registrera dig för Bing-statistik.

> [!NOTE]  
> * Det kan ta upp till 24 timmar innan måtten på instrument panelen fungerar. Instrument panelen visar datum och tid då data senast uppdaterades.  
> * Mått är tillgängliga från den tidpunkt då du aktiverar Bing Statistics-tillägget.

## <a name="filter-the-data"></a>Filtrera data

Som standard visar diagram och grafer alla mått och data som du har åtkomst till. Du kan filtrera data som visas i diagram och diagram genom att välja resurser, marknader, slut punkter och rapporterings perioden som du är intresse rad av. Du kan ändra följande filter:

- **Resurs-ID**: det unika resurs-ID: t som identifierar din Azure-prenumeration. Listan innehåller flera ID: n om du prenumererar på fler än en Bing-sökning API-nivå. Som standard är alla resurser markerade.  
  
- **Marknader**: de marknader där resultatet kommer från. Till exempel en-US (engelska, USA). Som standard är alla marknader markerade. `en-WW`Marknaden är marknaden som Bing använder om samtalet inte anger någon marknad och Bing inte kan fastställa användarens marknad.  
  
- **Slut punkter**: Bing-sökning API-slutpunkter. Listan innehåller alla slut punkter som du har en betald prenumeration för. Som standard är alla slut punkter markerade.  

- **Tidsram**: rapporterings perioden. Du kan ange:
  - **Alla**: innehåller upp till 13 månader av data  
  - **Senaste 24 timmarna**: omfattar analys från de senaste 24 timmarna  
  - **Senaste veckan**: omfattar analys från föregående 7 dagar  
  - **Senaste månaden**: omfattar analyser från de senaste 30 dagarna  
  - **Ett anpassat datum intervall**: inkluderar analyser från det angivna datum intervallet, om det är tillgängligt  

## <a name="charts-and-graphs"></a>Diagram och diagram

Instrument panelen visar diagram och diagram över mått som är tillgängliga för den valda slut punkten. Alla mått är inte tillgängliga för alla slut punkter. Diagram och diagram för varje slut punkt är statiska (du kan inte välja de tabeller och diagram som ska visas). På instrument panelen visas endast diagram och diagram som innehåller data.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Följande är möjliga mått och slut punkts begränsningar.

- **Anrops volym**: visar antalet anrop som gjorts under rapporterings perioden. Om rapporterings perioden är för en dag visar diagrammet antalet anrop som görs per timme. Annars visar diagrammet antalet anrop som gjorts per dag i rapporterings perioden.  
  
  > [!NOTE]
  > Anrops volymen kan skilja sig från fakturerings rapporter, som vanligt vis bara innehåller lyckade anrop.

- **Vanligaste frågor**: visar de vanligaste frågorna och antalet förekomster av varje fråga under rapporterings perioden. Du kan konfigurera antalet frågor som visas. Du kan till exempel Visa de 25 vanligaste 50-eller 75-frågorna. De vanligaste frågorna är inte tillgängliga för följande slut punkter:  

  - /images/trending
  - /images/details
  - /images/visualsearch
  - /videos/trending
  - /videos/details
  - /news
  - /news/trendingtopics
  - /suggestions  
  
  > [!NOTE]  
  > Vissa sökord kan undertryckas för att ta bort konfidentiell information, till exempel e-post, telefonnummer, SSN osv.

- **Geografisk distribution**: de marknader där Sök resultaten kommer. Till exempel `en-us` (engelska, USA). Bing använder `mkt` Frågeparametern för att fastställa marknaden, om den har angetts. Annars använder Bing signaler som anroparens IP-adress för att fastställa marknaden.

- **Distribution av svarskod**: HTTP-statuskod för alla anrop under rapporterings perioden.

- **Anropa ursprunglig distribution**: de typer av webbläsare som används av användarna. Till exempel Microsoft Edge, Chrome, Safari och FireFox. Anrop som görs utanför en webbläsare (t. ex. robotar, Postman eller Använd vändning från en konsol app) grupperas under bibliotek. Ursprunget bestäms med hjälp av User-Agent huvud värde för begäran. Om begäran inte innehåller User-Agents huvudet försöker Bing härleda ursprunget från andra signaler.  

- **Säker Sök distribution**: distribution av säkra Sök värden. Till exempel, för, måttlig eller strikt. `safeSearch`Frågeparametern innehåller värdet, om det anges. Annars använder Bing standardvärdet som måttlig.  

- **Svar på begärd distribution**: de webbsökning API-svar som du har begärt i `responseFilter` Frågeparametern.  

- **Svar som returnerade distribution**: svaren som webbsökning-API: t returnerade i svaret.

- **Distribution av svars Server**: den program server som HANTERAde API-begärandena. De möjliga värdena är Bing.com (för trafik som hanteras från Station ära och bärbara enheter) och Bing.com-mobil (för trafik som hanteras från mobila enheter). Servern bestäms med hjälp av User-Agent huvud värde för begäran. Om begäran inte innehåller User-Agents huvudet försöker Bing härleda servern från andra signaler.

## <a name="next-steps"></a>Nästa steg

* [Vad är API:er för Bing-sökresultat?](bing-api-comparison.md)
* [Användnings- och visningskrav för API:er för Bing-sökresultat](use-display-requirements.md)