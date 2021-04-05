---
title: Konfigurera ett värdbaserat användar gränssnitt för Anpassad Bing-sökning | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Använd den här artikeln för att konfigurera och integrera ett värdbaserad gränssnitt för Anpassad Bing-sökning.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: ca3f9da681c60608b5b196b17191f0b4d549f305
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96338375"
---
# <a name="configure-your-hosted-ui-experience"></a>Konfigurera värdbaserad UI-upplevelse

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Anpassad Bing-sökning tillhandahåller ett värdbaserad gränssnitt som du enkelt kan integrera med dina webb sidor och webb program som ett JavaScript-kodfragment. Med hjälp av Anpassad Bing-sökning Portal kan du konfigurera alternativ för layout, färg och sökning i användar gränssnittet.



## <a name="configure-the-custom-hosted-ui"></a>Konfigurera det anpassade värdbaserade gränssnittet

Följ dessa steg om du vill konfigurera ett värdbaserat användar gränssnitt för dina webb program. När du gör ändringar får du en förhands granskning av ditt användar gränssnitt i rutan till höger. De visade Sök resultaten är inte faktiska resultat för din instans.

1. Logga in på Anpassad Bing-sökning [Portal](https://customsearch.ai).  
  
2. Välj din Anpassad Bing-sökning-instans.

3. Klicka på fliken för **värdbaserat användargränssnitt**.  
  
4. Välj en layout.

    - Sökfält och resultat (standard): visar en sökruta med Sök Resultat nedanför.
    - Endast resultat: visar endast Sök resultat, utan en sökruta. När du använder den här layouten måste du ange Sök frågan ( `&q=<query string>` ). Lägg till frågeparametern i fråge-URL: en i JavaScript-kodfragmentet eller på HTML-slutpunktens länk.
    - Pop-on: innehåller en sökruta och visar Sök resultatet i ett glidande överlägg.

5. Välj ett färgtema. Du kan anpassa färgerna så att de passar ditt program genom att klicka på **Anpassa tema**. Om du vill ändra en färg anger du antingen RGB HEX-värdet (till exempel) som färg `#366eb8` eller klickar på förhands visningen av färg.

   Du kan förhandsgranska dina ändringar på höger sida av portalen. Om du klickar på **Återställ till standard** återställs dina ändringar till standard färgerna för det valda temat.

   > [!NOTE]
   > Överväg åtkomst när du väljer färger.

6. Under **ytterligare konfigurationer** anger du värden efter behov för din app. De här inställningarna är valfria. Information om hur du använder eller tar bort dem finns i förhands gransknings fönstret till höger. Tillgängliga konfigurations alternativ:  

7. Ange Sök prenumerations nyckeln eller Välj en i list rutan. List rutan fylls i med nycklar från ditt Azure-kontos prenumerationer. Se [COGNITIVE Services API-konto](../cognitive-services-apis-create-account.md).  

8. Om du har aktiverat automatiska förslag anger du den automatiska förslags prenumerations nyckeln eller väljer en från List rutan. List rutan fylls i med nycklar från ditt Azure-kontos prenumerationer. Anpassad automatiska förslag kräver en speciell prenumerations nivå, se [prissättningen](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Använda anpassat användar gränssnitt

Om du vill använda det värdbaserade gränssnittet kan du antingen: 

- Inkludera skriptet på din webb sida  
  
  ```html
  <html>
      <body>
          <script type="text/javascript" 
              id="bcs_js_snippet"
              src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
          </script>
      </body>    
  </html>
  ```

- Du kan också använda följande URL i en webbläsare.   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > Lägg till följande frågeparametrar i URL: en efter behov. Information om dessa parametrar finns [API för anpassad sökning](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) referens.
  >
  > - q
  > - mkt
  > - SafeSearch
  > - setlang

  > [!IMPORTANT]
  > Sidan kan inte Visa din sekretess policy eller andra meddelanden och villkor. Lämpligheten för din användning kan variera.  

Om du vill ha mer information, inklusive ditt anpassade konfigurations-ID, går du till **slut punkter** på fliken **produktion** .

## <a name="configuration-options"></a>Konfigurationsalternativ

Du kan konfigurera beteendet för ditt värdbaserade gränssnitt genom att klicka på **ytterligare konfigurationer** och ange värden. De här inställningarna är valfria. Information om hur du använder eller tar bort dem finns i förhands gransknings fönstret till höger. 

### <a name="web-search-configurations"></a>Konfigurationer för Webbs ökning

- Webb resultat har Aktiver ATS: avgör om Webbs ökning är aktiverat (en webbflik visas längst upp på sidan)
- Aktivera automatiska förslag: anger om anpassad autoföreslå är aktiverat (se [prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) för ytterligare kostnad).
- Webb resultat per sida: antal Webbs öknings resultat som ska visas i taget (det högsta är 50 resultat per sida).
- Bild beskrivning: anger om bilder visas med Sök resultat.

Följande konfigurationer visas om du klickar på **Visa avancerade konfigurationer**:

- Markera ord: anger om resultat visas med Sök termer i fetstil.
- Länk mål: anger om webb sidan öppnas i en ny flik i webbläsaren (tom) eller samma flik i webbläsaren (Self) när användaren klickar på ett Sök resultat.

### <a name="image-search-configurations"></a>Konfigurationer för avbildnings sökning

- Bild resultat har Aktiver ATS: avgör om bilds ökning är aktiverat (fliken bilder visas överst på sidan).
- Bild resultat per sida: antal bilds öknings resultat som ska visas i taget (det högsta är 150 resultat per sida).

Följande konfiguration visas om du klickar på **Visa avancerade konfigurationer**.  
  
- Aktivera filter: lägger till filter som användaren kan använda för att filtrera de bilder som Bing returnerar. Användaren kan till exempel filtrera resultaten enbart för animerade GIF-filer.

### <a name="video-search-configurations"></a>Konfigurationer för videos ökning

- Video resultat har Aktiver ATS: avgör om Videos ökning är aktiverat (fliken videor visas överst på sidan).
- Video resultat per sida: antal video Sök resultat som ska visas i taget (det högsta är 150 resultat per sida).

Följande konfiguration visas om du klickar på **Visa avancerade konfigurationer**.  
  
- Aktivera filter: lägger till filter som användaren kan använda för att filtrera videor som Bing returnerar. Användaren kan till exempel filtrera resultaten för videor med en bestämd upplösning eller videor som identifierats under de senaste 24 timmarna.

### <a name="miscellaneous-configurations"></a>Diverse konfigurationer

- Sid rubrik: text som visas i rubrik ytan på sidan Sök resultat (inte för popup-överlayout).
- Verktygsfälts tema: anger bakgrunds färgen för rubrik ytan på Sök Resultat sidan.

Följande konfigurationer visas om du klickar på **Visa avancerade konfigurationer**.  

|Column1  |Column2  |
|---------|---------|
|Text plats hållare för sökruta   | Text som visas i sökrutan innan indatamängden.        |
|Rubrik länkens URL    |Mål för rubrik länken.         |
|URL för logo typ     | Bild som visas bredvid rubriken.         |
|Favicon    | Ikon som visas i webbläsarens namn List.          |

Följande konfigurationer gäller bara om du använder det värdbaserade användar gränssnittet via HTML-slutpunkten (de gäller inte om du använder JavaScript-kodfragmentet).

- Sidrubrik
- Verktygsfälts tema
- Rubrik länkens URL
- URL för logo typ
- Faviicon-URL  

## <a name="next-steps"></a>Nästa steg

- [Använda dekorationsmarkörer för att markera text](../bing-web-search/hit-highlighting.md)
- [Webbsidor för sida](../bing-web-search/paging-search-results.md)