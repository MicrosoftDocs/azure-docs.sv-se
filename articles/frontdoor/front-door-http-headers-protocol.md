---
title: Protokoll stöd för HTTP-huvuden i Azures front dörr | Microsoft Docs
description: I den här artikeln beskrivs HTTP-huvudprotokoll som stöds av front dörren.
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
ms.openlocfilehash: 7f40b48473c04238d504288307039948fcacf90a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97511152"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Protokoll stöd för HTTP-huvuden i Azures front dörr
Den här artikeln beskriver det protokoll som stöds av front dörren med delar av anrops vägen (se bilden). I följande avsnitt finns mer information om HTTP-huvuden som stöds av front dörren.

:::image type="content" source="./media/front-door-http-headers-protocol/front-door-protocol-summary.png" alt-text="Protokoll för HTTP-meddelandehuvuden i Azure front dörr":::

>[!IMPORTANT]
>Front dörren certifierar inte några HTTP-huvuden som inte dokumenteras här.

## <a name="client-to-front-door"></a>Klient till front dörr
Front dörren godkänner de flesta huvuden för den inkommande begäran utan att ändra dem. Vissa reserverade huvuden tas bort från den inkommande begäran om de skickas, inklusive huvuden med prefixet X-FD-*.

## <a name="front-door-to-backend"></a>Front dörr till Server del

Front dörren innehåller rubriker för en inkommande begäran, om de inte tas bort på grund av begränsningar. Front dörren lägger också till följande rubriker:

| Huvud  | Exempel och beskrivning |
| ------------- | ------------- |
| Rapportör |  *Via: 1,1 Azure* </br> Front dörren lägger till klientens HTTP-version följt av *Azure* som värde för via-huvudet. Den här rubriken anger klientens HTTP-version och den främre dörren var en mellanliggande mottagare för begäran mellan klienten och Server delen.  |
| X-Azure-ClientIP | *X-Azure-ClientIP: 127.0.0.1* </br> Representerar den klient-IP-adress som är kopplad till den begäran som bearbetas. En begäran som kommer från en proxyserver kan till exempel lägga till den X-vidarebefordrade-för-rubriken för att ange IP-adressen för den ursprungliga anroparen. |
| X-Azure-SocketIP |  *X-Azure-SocketIP: 127.0.0.1* </br> Representerar den IP-adress för socket som är kopplad till den TCP-anslutning som den aktuella begäran kommer från. En begär ande klients IP-adress kanske inte är lika med dess IP-adress för socket eftersom den kan skrivas över av en användare.|
| X-Azure-Ref | *X-Azure-Ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> En unik referens sträng som identifierar en begäran som betjänas av front dörren. Den används för att söka efter åtkomst loggar och viktiga för fel sökning.|
| X-Azure-RequestChain | *X-Azure-RequestChain: hopp = 1* </br> En rubrik som används av front dörren för att identifiera begär ande slingor och användare bör inte ta ett beroende på den. |
| X-Azure-FDID | *X-Azure-FDID: 55ce4ed1-4B06-4bf1-b40e-4638452104da* <br/> En referens sträng som identifierar begäran kommer från en bestämd resurs för klient delen. Värdet kan visas i Azure Portal eller hämtas med hjälp av hanterings-API: et. Du kan använda den här rubriken i kombination med IP ACL: er för att låsa slut punkten för att endast godkänna begär Anden från en speciell resurs för en klient. Se vanliga frågor och svar för [Mer information](front-door-faq.md#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door) |
| X-vidarebefordrad – för | *X-vidarebefordrad – för: 127.0.0.1* </br> HTTP-huvudfältet X-forwarded – for (XFF)-HTTP-huvud identifierar ofta den ursprungliga IP-adressen för en klient som ansluter till en webb server via en HTTP-proxy eller belastningsutjämnare. Om det finns ett befintligt XFF-huvud lägger front dörren till klient-socket-IP: en eller lägger till XFF-huvudet med klientens IP-adress för socket. |
| X-vidarebefordrad-värd | *X-vidarebefordrad-värd: contoso.azurefd.net* </br> Fältet X-forwarded-Host-HTTP-huvud är en gemensam metod som används för att identifiera den ursprungliga värddatorn som begärs av klienten i rubriken HTTP-begäran för värd. Detta beror på att värd namnet från Front dörren kan skilja sig för Server dels servern som hanterar begäran. |
| X-vidarebefordrad – proto | *X-vidarebefordrad-proto: http* </br> Fältet X-forwarded-proto HTTP-huvud används ofta för att identifiera ursprungs protokollet för en HTTP-begäran. Frontend-dörren som baseras på konfigurationen kan kommunicera med Server delen med hjälp av HTTPS. Detta gäller även om begäran till den omvända proxyn är HTTP. |
| X-FD-HealthProbe | Fältet X-FD-HealthProbe HTTP-huvud används för att identifiera hälso avsökningen från Front dörren. Om den här rubriken har angetts till 1 är begäran hälso avsökning. Du kan använda när du vill begränsa åtkomsten från en viss front dörr med X-vidarebefordrade-värd huvud fält. |
| X-Azure-FDID | *X-Azure-FDID-huvud: 437c82cd-360A-4a54-94c3-5ff707647783* </br> Det här fältet innehåller frontdoorID som kan användas för att identifiera vilken front-dörr inkommande begäran kommer från. Det här fältet fylls i av front dörrs tjänsten. | 

## <a name="front-door-to-client"></a>Klientens front dörr

Alla rubriker som skickas till frontend-dörren från Server delen skickas även till klienten. Följande är huvuden som skickas från klientens dörr till klienter.

| Huvud  | Exempel och beskrivning |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Detta är en unik referens sträng som identifierar en begäran som betjänas av en front dörr, vilket är kritiskt för fel sökning eftersom den används för att söka i åtkomst loggar.|
| X-cache | *X-cache: TCP_HIT* </br> I den här rubriken beskrivs cache-status för begäran, vilket gör att du kan identifiera om svars innehållet hanteras från cachen för front dörren. |

Du måste skicka "X-Azure-DebugInfo: 1" begär ande rubriken för att aktivera följande valfria svarshuvuden.

| Huvud  | Exempel och beskrivning |
| ------------- | ------------- |
| X-Azure-OriginStatusCode |  *X-Azure-OriginStatusCode: 503* </br> Den här rubriken innehåller den HTTP-statuskod som returnerades av Server delen. Med hjälp av den här rubriken kan du identifiera den HTTP-statuskod som returneras av programmet som körs i Server delen utan att gå igenom backend-loggar. Den här status koden kan skilja sig från HTTP-statuskoden i svaret som skickas till klienten av front dörren. Med den här rubriken kan du avgöra om Server delen är fel eller om problemet är med frontend-tjänsten. |
| X-Azure-InternalError | Rubriken kommer att innehålla den felkod som frontend-dörren kommer över när begäran bearbetas. Det här felet anger att problemet är internt för tjänsten/infrastrukturen för front dörren. Rapportera problem till stöd.  |
| X-Azure-ExternalError | *X-Azure-ExternalError: 0x830c1011, certifikat utfärdaren är okänd.* </br> I den här rubriken visas en felkod som visar att frontend-servrar kommer över vid upprättandet av anslutningen till backend-servern för bearbetning av en begäran. Den här rubriken hjälper dig att identifiera problem i anslutningen mellan front dörren och Server dels programmet. Rubriken innehåller ett detaljerat fel meddelande som hjälper dig att identifiera anslutnings problem till din server del (till exempel DNS-matchning, ogiltigt certifikat osv.). |

## <a name="next-steps"></a>Nästa steg

- [Skapa en Front Door](quickstart-create-front-door.md)
- [Så här fungerar en front dörr](front-door-routing-architecture.md)
