---
title: Felsöka konfigurations problem i Azure front dörr
description: I den här självstudien får du lära dig att själv felsöka några av de vanliga problem som du kan använda för din Azures front dörr instans.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 15cdcefe628a392704e650b560243e2f6a134ec2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94629996"
---
# <a name="troubleshooting-common-routing-problems"></a>Felsöka vanliga problem med Routning

I den här artikeln beskrivs hur du felsöker vanliga problem med routning som du kan använda för din Azure-konfiguration av front dörren.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>503 svar från Azures front dörr efter några sekunder

### <a name="symptom"></a>Symptom

* Vanliga förfrågningar som skickas till din server del utan att gå igenom Azures front dörr lyckas. Genom att gå via Azures front dörr resulterar vi i 503 fel svar.
* Problemet från Azures front dörr visar vanligt vis efter cirka 30 sekunder.

### <a name="cause"></a>Orsak

Orsaken till det här problemet kan vara något av två saker:
 
* Server delen tar längre tid än den konfigurerade tids gränsen (Standardvärdet är 30 sekunder) för att ta emot begäran från Azures front dörr.
* Den tid det tar att skicka ett svar till begäran från Azure-frontend tar längre tid än tids gräns värdet. 

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

* Skicka begäran till Server delen direkt (utan att gå igenom Azures front dörr). Se hur lång tid det tar för din server del att svara.
* Skicka begäran via Azures front dörr och se om du får 503 svar. Om inte, kanske problemet inte är ett timeout-problem. Kontakta supporten.
* Om du går igenom Azures front dörr resulterar du i en 503-fel svars kod, konfigurerar du `sendReceiveTimeout` fältet för Azure-frontend. Du kan utöka standard tids gränsen upp till 4 minuter (240 sekunder). Inställningen är under `backendPoolSettings` och kallas `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>Begär Anden som skickas till den anpassade domänen returnerar status koden 400

### <a name="symptom"></a>Symptom

* Du har skapat en instans av Azures frontend-dörr, men en begäran till domänen eller klient dels värden returnerar en HTTP 400-status kod.
* Du har skapat en DNS-mappning för en anpassad domän till den klient dels värd som du konfigurerade. Men om du skickar en begäran till det anpassade domän värd namnet returneras en HTTP 400-status kod. Den visas inte för att dirigera till den server del som du har konfigurerat.

### <a name="cause"></a>Orsak

Problemet uppstår om du inte har konfigurerat en regel för routning för den anpassade domänen som har lagts till som klient dels värd. En regel för routning måste läggas till explicit för den klient dels värden. Det är sant även om en routningsregler redan har kon figurer ATS för klient dels värden under under domänen Azure frontend-dörr (*. azurefd.net).

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

Lägg till en routningsprincip för den anpassade domänen för att dirigera trafik till den valda backend-poolen.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Azures front dörr omdirigerar inte HTTP till HTTPS

### <a name="symptom"></a>Symptom

Azures front dörr har en regel för routning som omdirigerar HTTP till HTTPS, men åtkomst till domänen upprätthåller HTTP som protokoll.

### <a name="cause"></a>Orsak

Det här problemet kan inträffa om du inte konfigurerade routningsregler korrekt för Azures front dörr. Den aktuella konfigurationen är i princip inte särskilt speciell och kan ha motstridiga regler.

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

## <a name="request-to-a-frontend-host-name-returns-a-404-status-code"></a>Begäran till en klient dels värd namn returnerar en 404-status kod

### <a name="symptom"></a>Symptom

Du har skapat en instans av Azures frontend-dörr genom att konfigurera en klient dels värd, en backend-pool med minst en server del i den och en routningsprincip som ansluter klient dels värden till backend-poolen. Ditt innehåll är inte tillgängligt när du gör en begäran till den konfigurerade klient dels värden. Därför returnerar begäran en HTTP 404-status kod.

### <a name="cause"></a>Orsak

Det finns flera möjliga orsaker till det här problemet:

* Server delen är inte en offentlig Server del och är inte synlig för Azures front dörr.
* Server delen är felkonfigurerad, vilket gör att Azure-fronten kan skicka fel förfrågan. Det innebär att Server delen bara accepterar HTTP och att HTTPS inte tillåts. Så Azures front dörr försöker vidarebefordra HTTPS-begäranden.
* Server delen avvisar värd rubriken som vidarebefordrades med begäran till Server delen.
* Konfigurationen för Server delen har ännu inte distribuerats fullständigt.

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

* Kontrol lera distributions tiden:
   * Se till att du har väntat minst 10 minuter på att konfigurationen ska distribueras.

* Kontrol lera Server dels inställningarna:
    * Gå till den backend-pool som begäran ska dirigeras till. (Det beror på hur du har konfigurerat Routningsprincipen.) Verifiera att värd typen för Server delen och värd namnet för Server delen är korrekt. Om Server delen är en anpassad värd kontrollerar du att du har stavat den korrekt. 

    * Kontrol lera dina HTTP-och HTTPS-portar. I de flesta fall är 80 och 443 (respektive) korrekta och inga ändringar krävs. Det finns dock en chans att din server del inte har kon figurer ATS på det här sättet och lyssnar på en annan port.

    * Kontrol lera Server dels värd rubriken som kon figurer ATS för de Server delar som klient dels värden ska dirigeras till. I de flesta fall ska den här rubriken vara samma som server dels värd namnet. Ett felaktigt värde kan dock orsaka olika status koder för HTTP-4xx om Server delen förväntar sig något annat. Om du anger en IP-adress för Server delen kan du behöva ange Server delens värd rubrik till Server delens värd namn.

* Kontrol lera inställningarna för routningsregler:
    * Gå till regeln för routning som ska vidarebefordra från klient dels värdens namn i fråga till en backend-pool. Kontrol lera att de godkända protokollen är korrekt konfigurerade när begäran vidarebefordras. Fältet **godtagna protokoll** avgör vilka begär Anden som Azures front dörr ska godkänna. Vidarebefordrings protokollet avgör vilken protokoll Azure-frontend ska använda för att vidarebefordra begäran till Server delen.
      
      Om Server delen till exempel bara accepterar HTTP-förfrågningar, är följande konfigurationer giltiga:
            
      * Godkända protokoll är HTTP och HTTPS. Vidarebefordrings protokollet är HTTP. En matchnings förfrågan fungerar inte, eftersom HTTPS är ett tillåtet protokoll. Om en begäran kom in som HTTPS försöker Azure-frontend att vidarebefordra den med hjälp av HTTPS.

      * Det accepterade protokollet är HTTP. Vidarebefordrings protokollet är antingen en matchnings förfrågan eller HTTP.
    - **URL-omskrivning** är inaktive rad som standard. Det här fältet används bara om du vill begränsa omfattningen av de Server dels värd resurser som du vill göra tillgängliga. När det här fältet är inaktiverat vidarebefordrar Azure front dörren samma sökväg för begäran som den tar emot. Det är möjligt att felaktigt konfigurera det här fältet. Så när Azures front dörr begär en resurs från Server delen som inte är tillgänglig, returnerar den en HTTP 404-status kod.

## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>Begäran till klient dels värd namnet returnerar en 411-status kod

### <a name="symptom"></a>Symptom

Du har skapat en Azure frontend-instans och konfigurerat en klient dels värd, en backend-pool med minst en server del i den och en routningsprincip som ansluter klient dels värden till backend-poolen. Ditt innehåll verkar inte vara tillgängligt när en begäran går till den konfigurerade klient dels värden eftersom en status kod för HTTP 411 returneras.

Svar på dessa förfrågningar kan också innehålla en HTML-felsida i svars texten som innehåller en för klar ande instruktion. Exempel: `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>Orsak

Det finns flera möjliga orsaker till det här problemet. Den övergripande orsaken är att HTTP-begäran inte är fullständigt RFC-kompatibel. 

Ett exempel på inkompatibilitet är en `POST` begäran som skickas utan något `Content-Length` eller en `Transfer-Encoding` rubrik (till exempel med `curl -X POST https://example-front-door.domain.com` ). Den här begäran uppfyller inte kraven som anges i [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2). Azures front dörr blockerar den med ett HTTP 411-svar.

Det här beteendet skiljer sig från WAF-funktionerna i Azures front dörr. För närvarande finns det inget sätt att inaktivera det här beteendet. Alla HTTP-begäranden måste uppfylla kraven, även om WAF-funktionen inte används.

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

- Kontrol lera att dina begär Anden uppfyller de krav som anges i de nödvändiga rapporterna.

- Anteckna vilken HTML-meddelande text som returneras som svar på din begäran. En meddelande text förklarar ofta exakt *hur* din begäran är inkompatibel.
