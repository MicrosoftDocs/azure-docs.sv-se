---
title: Protokollstöd för HTTP-huvuden i Azure Front Door | Microsoft Docs
description: I den här artikeln beskrivs protokoll för HTTP-Front Door stöder.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2020
ms.author: duau
ms.openlocfilehash: 2ad97656b822bc5ffc957469842436ec84d9e812
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785765"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Protokollstöd för HTTP-huvuden i Azure Front Door
Den här artikeln beskriver det protokoll Front Door stöder med delar av anropssökvägen (se bild). Följande avsnitt innehåller mer information om HTTP-huvuden som stöds av Front Door.

:::image type="content" source="./media/front-door-http-headers-protocol/front-door-protocol-summary.png" alt-text="Azure Front Door protokoll för HTTP-huvuden":::

>[!IMPORTANT]
>Front Door certifierar inte http-huvuden som inte dokumenteras här.

## <a name="client-to-front-door"></a>Klient till Front Door
Front Door accepterar de flesta huvuden för den inkommande begäran utan att ändra dem. Vissa reserverade huvuden tas bort från den inkommande begäran om den skickas, inklusive rubriker med prefixet X-FD-*.

## <a name="front-door-to-backend"></a>Front Door till backend

Front Door innehåller huvuden för en inkommande begäran om de inte tas bort på grund av begränsningar. Front Door lägger också till följande huvuden:

| Huvud  | Exempel och beskrivning |
| ------------- | ------------- |
| Via |  *Via: 1.1 Azure* </br> Front Door lägger till klientens HTTP-version följt *av Azure* som värde för Via-huvudet. Det här huvudet anger klientens HTTP-version och Front Door mellanliggande mottagare för begäran mellan klienten och backend.  |
| X-Azure-ClientIP | *X-Azure-ClientIP: 127.0.0.1* </br> Representerar klientens IP-adress som är associerad med begäran som bearbetas. En begäran som kommer från en proxy kan till exempel lägga till huvudet X-Forwarded-For för att ange IP-adressen för den ursprungliga anroparen. |
| X-Azure-SocketIP |  *X-Azure-SocketIP: 127.0.0.1* </br> Representerar den socket-IP-adress som är associerad med TCP-anslutningen som den aktuella begäran kommer från. En begärans klient-IP-adress kanske inte är lika med dess socket-IP-adress eftersom klientens IP-adress godtyckligt kan skrivas över av en användare.|
| X-Azure-Ref | *X-Azure-Ref: 0zxV+XAAAABKMMOjBv2NT4TY6SQVjC0zV1NPATIRHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> En unik referenssträng som identifierar en begäran som Front Door. Den används för att söka efter åtkomstloggar och kritiska för felsökning.|
| X-Azure-RequestChain | *X-Azure-RequestChain: hops=1* </br> En rubrik som Front Door använder för att identifiera begärandeloopar och användarna bör inte ha något beroende av den. |
| X-Azure-FDID | *X-Azure-FDID: 55ce4ed1-4b06-4bf1-b40e-4638452104da* <br/> En referenssträng som identifierar begäran kom från en specifik Front Door resurs. Värdet kan visas i Azure Portal hämtas med hjälp av hanterings-API:et. Du kan använda det här huvudet i kombination med IP-ACL:er för att låsa slutpunkten så att den endast accepterar begäranden från en Front Door resurs. Mer information finns i [Vanliga frågor och svar](front-door-faq.yml#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-) |
| X-Forwarded-For | *X-Forwarded-For: 127.0.0.1* </br> HTTP-rubrikfältet X-Forwarded-For (XFF) identifierar ofta den ursprungliga IP-adressen för en klient som ansluter till en webbserver via en HTTP-proxy eller lastbalanserare. Om det finns ett befintligt XFF-huvud lägger Front Door klientsocketens IP-adress till den eller lägger till XFF-huvudet med klientsocketens IP-adress. |
| X-Forwarded-Host | *X-Forwarded-Host: contoso.azurefd.net* </br> HTTP-huvudfältet X-Forwarded-Host är en vanlig metod som används för att identifiera den ursprungliga värden som begärdes av klienten i http-begärandehuvudet för värden. Det beror på att värdnamnet från Front Door kan skilja sig åt för den backend-server som hanterar begäran. |
| X-Forwarded-Proto | *X-Forwarded-Proto: http* </br> HTTP-huvudfältet X-Forwarded-Proto används ofta för att identifiera det ursprungliga protokollet för en HTTP-begäran. Front Door baserat på konfigurationen kan kommunicera med serveruppsättningen med hjälp av HTTPS. Detta gäller även om begäran till den omvända proxyn är HTTP. |
| X-FD-HealthProbe | HTTP-rubrikfältet X-FD-HealthProbe används för att identifiera hälsoavsökningen från Front Door. Om det här huvudet är inställt på 1 är begäran hälsoavsökning. Du kan använda när du vill strikt åtkomst från Front Door med rubrikfältet X-Forwarded-Host. |
| X-Azure-FDID | *X-Azure-FDID-rubrik: 437c82cd-360a-4a54-94c3-5ff707647783* </br> Det här fältet innehåller frontdoorID som kan användas för att identifiera vilka Front Door den inkommande begäran kommer från. Det här fältet fylls i Front Door tjänsten. | 

## <a name="front-door-to-client"></a>Front Door till klient

Alla huvuden som Front Door skickas till från backend-datorn skickas också till klienten. Följande är huvuden som skickas från Front Door till klienter.

| Huvud  | Exempel och beskrivning |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAABKMMOjBv2NT4TY6SQVjC0zV1NRITRHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Det här är en unik referenssträng som identifierar en begäran som Front Door, vilket är viktigt för felsökning eftersom den används för att söka i åtkomstloggar.|
| X-Cache | *X-Cache: TCP_HIT* </br> Det här huvudet beskriver cachestatusen för begäran, vilket gör att du kan identifiera om svarsinnehållet behandlas från cacheminnet för Front Door. |

Du måste skicka begärandehuvudet "X-Azure-DebugInfo: 1" för att aktivera följande valfria svarshuvuden.

| Huvud  | Exempel och beskrivning |
| ------------- | ------------- |
| X-Azure-OriginStatusCode |  *X-Azure-OriginStatusCode: 503* </br> Det här huvudet innehåller HTTP-statuskoden som returneras av backend. Med det här huvudet kan du identifiera HTTP-statuskoden som returneras av programmet som körs i din backend utan att gå igenom backend-loggarna. Den här statuskoden kan vara annorlunda än HTTP-statuskoden i svaret som skickas till klienten av Front Door. Med det här huvudet kan du avgöra om backend-backend beter sig felaktigt eller om problemet beror på Front Door tjänsten. |
| X-Azure-InternalError | Det här huvudet innehåller den felkod som Front Door visas när begäran bearbetas. Det här felet indikerar att problemet är internt för Front Door tjänsten/infrastrukturen. Rapportera problem till supporten.  |
| X-Azure-ExternalError | *X-Azure-ExternalError: 0x830c1011, certifikatutfärdaren är okänd.* </br> Det här huvudet visar felkoden som Front Door på servrarna när de upprättar en anslutning till backend-servern för att bearbeta en begäran. Det här huvudet hjälper dig att identifiera problem i anslutningen mellan Front Door och backend-programmet. Det här huvudet innehåller ett detaljerat felmeddelande som hjälper dig att identifiera anslutningsproblem till din server (till exempel DNS-upplösning, ogiltigt certifikat och så vidare). |

## <a name="next-steps"></a>Nästa steg

- [Skapa en Front Door](quickstart-create-front-door.md)
- [Så Front Door fungerar](front-door-routing-architecture.md)
