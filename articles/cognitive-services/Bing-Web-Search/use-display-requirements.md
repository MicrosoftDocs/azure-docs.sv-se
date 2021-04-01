---
title: Användnings-och visnings krav för API:er för Bing-sökresultat
titleSuffix: Azure Cognitive Services
description: Kraven för att Visa Sök Resultat från API:er för Bing-sökresultat i dina program.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 4300860039b76bd076f19acfc252f59ef10d462b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96351852"
---
# <a name="bing-search-api-use-and-display-requirements"></a>Användnings- och visningskrav för API:er för Bing-sökresultat

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Dessa användnings-och visnings krav gäller för all implementering av innehållet och associerad information från följande API:er för Bing-sökresultat, inklusive relationer, metadata och andra signaler.

- Anpassad sökning i Bing
- Entitetssökning i Bing
- Bildsökning i Bing
- Nyhetssökning i Bing
- Videosökning i Bing
- Visuell sökning i Bing
- Webbsökning i Bing
- Stavningskontroll i Bing
- Automatiska förslag i Bing

## <a name="definitions"></a>Definitioner


|Period  |Beskrivning  |
|---------|---------|
|Svar     | En kategori av resultat som returneras i ett svar. Till exempel kan ett svar från API för webbsökning i Bing innehålla svar i kategorier av webb sidas resultat, bild, video, visuellt objekt och nyheter. |
|Svarsåtgärder     | Alla svar och associerade data som tas emot som svar på ett enskilt anrop till ett API för sökning. |
|Resultat    | Ett informations objekt i ett svar. Data uppsättningen som är kopplad till en enda nyhets artikel är till exempel ett resultat av ett nyhets svar. |
|Sök-API: er    | gemensamt, Anpassad Bing-sökning, Entitetssökning, Bildsökning, Nyhetssökning, Videosökning, Visuell sökning, lokal företags sökning och Webbsökning-API: er. |

## <a name="bing-spell-check-and-bing-autosuggest-api-restrictions"></a>Stavningskontroll i Bing och API för automatiska förslag i Bing begränsningar

Gör inte:

- Kopiera, lagra eller cachelagra data som du tar emot från Stavningskontroll i Bing-eller Automatiska förslag i Bing-API: er.
- Använd data som du tar emot från Stavningskontroll i Bing eller Automatiska förslag i Bing-API: er som en del av maskin inlärning eller liknande algoritmiska aktiviteter. Använd inte dessa data för att träna, utvärdera eller förbättra nya eller befintliga tjänster som du eller tredje part kan erbjuda.

## <a name="bing-search-apis"></a>API:er för Bing-sökresultat

> [!NOTE]
> Kraven i det här avsnittet gäller endast Sök-API: er, som inte innehåller Stavningskontroll i Bing eller Automatiska förslag i Bing. 

### <a name="internet-search-experience-requirements"></a>Krav för Internets ökning

Alla data som returneras i svar får bara användas i Internets öknings upplevelser. En Internets ökning innebär att innehållet visas: 

- Är relevant och svarar på slutanvändarens direkta fråga eller andra uppgifter om deras sökintresse och avsikt (till exempel en användardefinierad Sök fråga). 

- Hjälper användare att hitta och navigera till svarets data källor. Du kan till exempel erbjuda länkar i svaret genom att klicka på länkar.

- Innehåller flera resultat som användaren kan välja bland. 

- Är i en placering som gör det möjligt för användarna att söka.

- Innehåller en synlig indikation på att innehållet är ett Sök Resultat för Internet. Till exempel en instruktion som innehållet är "från webben".

- Innehåller andra lämpliga åtgärder för att se till att dina Bing-sökning-API-data inte strider mot gällande lagar eller tredje parts rättigheter. Kontakta dina juridiska rådgivare för att fastställa vilka åtgärder som kan vara lämpliga.

Det enda undantaget till dessa krav för Internets ökning är för URL-identifiering, enligt beskrivningen längre fram i den här artikeln. 

### <a name="restrictions"></a>Begränsningar

Gör inte:

- Kopiera, lagra eller cachelagra data från svar (förutom kvarhållning i den utsträckning som tillåts av [tjänstens kontinuitet](#continuity-of-service). 

- Använd data som hämtats från Sök-API: er som en del av Machine Learning eller liknande algoritmiska aktiviteter. Använd inte dessa data för att träna, utvärdera eller förbättra nya eller befintliga tjänster som du eller tredje part kan erbjuda.

- Ändra innehållet i resultat (förutom att omformatera dem på ett sätt som inte strider mot något annat krav), såvida det inte krävs enligt lag eller har överenskommits av Microsoft. 

- Utelämnar information om tilldelning och URL: er som är associerade med resultat innehållet.

- Ändra ordning, inklusive utelämnande, resultaten som visas i ett svar när en beställning eller rangordning tillhandahålls, såvida det inte krävs av lagen eller godkänts av Microsoft. 

    > [!NOTE]
    > Detta krav gäller inte för att sortera om implementerat i portalen för API för anpassad Bing-sökning.

- Visa annat innehåll inom någon del av ett svar på ett sätt som skulle leda till att det andra innehållet är en del av svaret. 

- Visa annonsering som inte tillhandahålls av Microsoft på någon sida som visar någon del av ett svar. 

- Visa annonser på sidor med svar:
    - Från Bing-avbildningen, Nyhetssökning, Videosökning eller Visuell sökning API: er
    - Som filtreras eller begränsas främst (eller enbart) till bilder, nyheter och/eller video eller visuella Sök resultat.

### <a name="notices-and-branding"></a>Meddelanden och varumärke 
Inte

- Ta en funktionell hyperlänk till [Microsofts sekretess policy](https://go.microsoft.com/fwlink/?LinkId=521839), nära varje punkt i användar upplevelsen (UX) som ger användaren möjlighet att ange en Sök fråga. Etikett på hyperlänken **Microsoft sekretess policy**.

- Väl synlig Bing-anpassning, konsekvent med [användnings rikt linjerna för Bing-varumärke](https://go.microsoft.com/fwlink/?linkid=833278), nära varje punkt i UX-gränssnittet som ger användaren möjlighet att mata in en Sök fråga. Detta varumärke måste tydligt ange användaren att Microsoft ska starta Internets ökningen.

- Du kan använda varje svar (eller en del av ett svar) som visas från Webbsökning i Bing-, Bildsökning-, Nyhetssökning-, Videosökning-och Visuell sökning-API: er till Microsoft, om inte Microsoft anger något annat i form av användning. Detta beskrivs i [rikt linjerna för användning av Bing-varumärken](https://go.microsoft.com/fwlink/?linkid=833278). 

Gör inte:

- Attribute-svar (eller delar av svar) som visas från API för anpassad Bing-sökning till Microsoft, om inte Microsoft anger något annat i skrivning till din specifika användning.

### <a name="transferring-responses"></a>Överför svar

Om du gör det möjligt för en användare att överföra ett svar från ett Sök-API till en annan användare, t. ex. via ett meddelande program eller en bokning av sociala medier, gäller följande: 

- Överförda svar måste:
  - Bestå av innehåll som inte har ändrats från innehållet i svaren som visas för den överförda användaren. Formateringsändringar är tillåtet.
  - Ta inte med några data i formuläret metadata.
  - För svar från Bing-webb-, bild-, nyhets-, video-och visualiserings-API: er, visnings språk som visar att svaret erhållits via en Internets öknings upplevelse som drivs av Bing. Du kan till exempel Visa språk som "drivs av Bing" eller "Läs mer om den här bilden" i Bing "eller så kan du använda Bing-logotypen.
  - För svar från API för anpassad Bing-sökning visas språket som visar att svaret har erhållits genom en Internets ökning. Du kan till exempel Visa språk som "Lär dig mer om det här Sök resultatet".
  - Visa den fullständiga frågan som används för att generera svaret på en framträdande plats.
  - Ta med en framträdande länk eller liknande behörighet till den underliggande källan för svaret, antingen direkt eller via sökmotorn (bing.com, m.bing.com eller din anpassade Sök tjänst, beroende på vad som är tillämpligt).
- Du får inte automatisera överföringen av svar. En överföring måste initieras av en användar åtgärd klart evidencing en avsikt att överföra ett svar.
- Du kan bara aktivera en användare för att överföra svar som visas som svar på den överförda användarens fråga.

### <a name="continuity-of-service"></a>Kontinuitet för tjänsten 

Kopiera, lagra eller cachelagra inga data från Sök-API-svar. Men om du vill aktivera kontinuitet för tjänst åtkomst och data åter givning kan du behålla resultaten enbart under följande omständigheter:

#### <a name="device"></a>Enhet

Du kan göra det möjligt för en användare att behålla resultat på en enhet under den lägsta av (i) 24 timmar från tidpunkten för frågan, eller (II) tills en användare skickar en annan fråga om uppdaterade resultat, förutsatt att kvarhållna resultat endast får användas:

- För att ge användaren åtkomst till resultat som tidigare returnerats till den användaren på enheten (till exempel om tjänsten avbryts).
- För att lagra resultat som returneras för den proaktiva frågan personligt anpassad i förväntan av användarens behov, baserat på användarens signaler (till exempel i händelse av förväntat tjänst avbrott).

#### <a name="server"></a>Server

Du kan behålla resultat som är speciella för en enskild användare på ett säkert sätt på en server som du styr och bara visa de kvarhållna resultaten:

- För att ge användaren åtkomst till en historisk rapport med resultat som tidigare returnerats till användaren i din lösning. Resultaten kanske inte (i) bevaras i mer än 21 dagar från tiden för slutanvändarens första fråga och (II) som visas som svar på en användares nya eller upprepade fråga.
- För att lagra resultat som returneras för den proaktiva frågan anpassad i förväntad användar behov, baserat på användarens signaler. Du kan lagra dessa resultat under den lägsta av (i) 24 timmarna från tidpunkten för frågan, eller (II) tills en användare skickar en ny fråga för uppdaterade resultat.

När du har behållits får inte resultat för en speciell användare commingled med resultat för en annan användare. Det vill säga att resultaten av varje användare måste behållas och levereras separat.

### <a name="general"></a>Allmänt 

För all presentation av kvarhållna resultat:

- Ta med ett tydligt, synligt meddelande om när frågan skickades.
- Presentera användaren med en knapp eller liknande sätt för att skicka frågan igen och få uppdaterade resultat. 
- Behåll Bing-anpassningen i presentationen av resultaten.
- Ta bort (och uppdatera med en ny fråga om det behövs) de lagrade resultaten inom de angivna tids ramarna.

### <a name="non-display-url-discovery"></a>URL-identifiering som inte visas 

Du kan bara använda Sök svar i en icke-Internet-sökmiljö för att identifiera URL: er för informations källor som svarar på en fråga från din användare eller kund. Du kan kopiera dessa URL: er i en rapport eller liknande svar som du tillhandahåller:

- Endast för den användaren eller kunden som svar på frågan.
- Endast om det innehåller betydande ytterligare värdefullt innehåll, som är relevant för frågan.

I föregående avsnitt av Sök-API: er används och visnings kraven gäller inte för den här användningen, förutom för följande: 

- Cachelagra, kopiera eller lagra inte data eller innehåll från eller härleds från Sök svaret, förutom den begränsade URL: en som beskrivs ovan.
- Se till att din användning av data (inklusive URL: er) som tas emot från Sök-API: erna inte strider mot gällande lagar eller tredje parts rättigheter.
- Använd inte de data (inklusive URL: er) som tagits emot från Sök-API: erna som en del av ett sökindex eller Machine Learning eller en liknande algoritms-aktivitet. Använd inte dessa data för att skapa träna, utvärdera eller förbättra tjänster som du eller tredje part kan erbjuda.

## <a name="gdpr-compliance"></a>GDPR-efterlevnad  

Med avseende på person uppgifter som omfattas av Europeiska unionens allmän dataskyddsförordning (GDPR) och som bearbetas i samband med anrop till Sök-API: er, API för stavningskontroll i Bing eller API för automatiska förslag i Bing, förstår du att du och Microsoft är oberoende data kontroller under GDPR. Du ansvarar oberoende för din efterlevnad av GDPR.