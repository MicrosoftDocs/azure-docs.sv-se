---
title: Felsöka konfigurations problem i Azures front dörr standard/Premium
description: I den här självstudien får du lära dig hur du felsöker några av de vanliga problem som du kan använda som standard/Premium-instans för Azure frontend-dörren.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 4690a513494d794377ee0c2e8cfb101e8fd66a0f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100572"
---
# <a name="troubleshooting-common-routing-problems-with-azure-front-door-standardpremium"></a>Felsöka vanliga problem med Routning med Azures främre dörr standard/Premium

I den här artikeln beskrivs hur du felsöker vanliga problem med routning som du kan använda för din Azure-konfiguration av front dörren.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>503 svar från Azures front dörr efter några sekunder

### <a name="symptom"></a>Symptom

* Vanliga förfrågningar som skickas till din server del utan att gå igenom Azures front dörr lyckas. Genom att gå via Azures front dörr resulterar vi i 503 fel svar.
* Problemet från Azures front dörr visar vanligt vis efter cirka 30 sekunder.

### <a name="cause"></a>Orsak

Orsaken till det här problemet kan vara något av två saker:
 
* Ditt ursprung tar längre tid än vad som har kon figurer ATS (Standardvärdet är 30 sekunder) för att ta emot begäran från Azures front dörr.
* Den tid det tar att skicka ett svar till begäran från Azure-frontend tar längre tid än tids gräns värdet. 

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

* Skicka begäran till Server delen direkt (utan att gå igenom Azures front dörr). Se hur lång tid det tar för din server del att svara.
* Skicka begäran via Azures front dörr och se om du får 503 svar. Om inte, kanske problemet inte är ett timeout-problem. Kontakta supporten.
* Om du går igenom Azures front dörr resulterar du i en 503-fel svars kod, konfigurerar du `sendReceiveTimeout` fältet för Azure-frontend. Du kan utöka standard tids gränsen upp till 4 minuter (240 sekunder). Inställningen är under `Endpoint Setting` och kallas `Origin response timeout` . 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>Begär Anden som skickas till den anpassade domänen returnerar status koden 400

### <a name="symptom"></a>Symptom

* Du har skapat en instans av Azures frontend-dörr, men en begäran till domänen eller klient dels värden returnerar en HTTP 400-status kod.
* Du har skapat en DNS-mappning för en anpassad domän till den klient dels värd som du konfigurerade. Men om du skickar en begäran till det anpassade domän värd namnet returneras en HTTP 400-status kod. Den visas inte för att dirigera till den server del som du har konfigurerat.

### <a name="cause"></a>Orsak

Problemet uppstår om du inte har konfigurerat en regel för routning för den anpassade domänen som har lagts till som klient dels värd. En regel för routning måste läggas till explicit för den klient dels värden. Det är sant även om en routningsregler redan har kon figurer ATS för klient dels värden under under domänen Azure frontend-dörr (*. azurefd.net).

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

Lägg till en regel för routning för den anpassade domänen för att dirigera trafik till den valda ursprungs gruppen.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Azures front dörr omdirigerar inte HTTP till HTTPS

### <a name="symptom"></a>Symptom

Azures front dörr har en regel för routning som omdirigerar HTTP till HTTPS, men åtkomst till domänen upprätthåller HTTP som protokoll.

### <a name="cause"></a>Orsak

Det här problemet kan inträffa om du inte konfigurerade routningsregler korrekt för Azures front dörr. Den aktuella konfigurationen är i princip inte särskilt speciell och kan ha motstridiga regler.

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar


## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>Begäran till klient dels värd namnet returnerar en 411-status kod

### <a name="symptom"></a>Symptom

Du har skapat en Azure front dörr standard-/Premium-instans och konfigurerat en klient dels värd, en ursprungs grupp med minst ett ursprung i den och en regel för routning som ansluter klient dels värden till ursprungs gruppen. Ditt innehåll verkar inte vara tillgängligt när en begäran går till den konfigurerade klient dels värden eftersom en status kod för HTTP 411 returneras.

Svar på dessa förfrågningar kan också innehålla en HTML-felsida i svars texten som innehåller en för klar ande instruktion. Exempel: `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>Orsak

Det finns flera möjliga orsaker till det här problemet. Den övergripande orsaken är att HTTP-begäran inte är fullständigt RFC-kompatibel. 

Ett exempel på inkompatibilitet är en `POST` begäran som skickas utan något `Content-Length` eller en `Transfer-Encoding` rubrik (till exempel med `curl -X POST https://example-front-door.domain.com` ). Den här begäran uppfyller inte kraven som anges i [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2). Azures front dörr blockerar den med ett HTTP 411-svar.

Det här beteendet skiljer sig från WAF-funktionerna i Azures front dörr. För närvarande finns det inget sätt att inaktivera det här beteendet. Alla HTTP-begäranden måste uppfylla kraven, även om WAF-funktionen inte används.

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

- Kontrol lera att dina begär Anden uppfyller de krav som anges i de nödvändiga rapporterna.

- Anteckna vilken HTML-meddelande text som returneras som svar på din begäran. En meddelande text förklarar ofta exakt *hur* din begäran är inkompatibel.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar en front dörrs standard/Premium](create-front-door-portal.md).
