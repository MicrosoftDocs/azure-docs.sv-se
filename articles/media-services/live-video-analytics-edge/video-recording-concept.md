---
title: Video inspelning – Azure
description: I samband med ett video hanterings system för CCTV-kameror refererar videoinspelningen till processen för att hämta video från kamerarna och registrera den för visning via mobil-och webbläsarbaserade appar. Videoinspelning kan kategoriseras som kontinuerlig video inspelning och händelsebaserade videoinspelningar.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: c03472f577e67d5b2705a2ebe3f53e85e0849819
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84260948"
---
# <a name="video-recording"></a>Videoinspelning

I samband med ett video hanterings system för CCTV-kameror refererar videoinspelningen till processen för att hämta video från kamerarna och registrera den för visning via mobil-och webbläsarbaserade appar. Videoinspelning kan kategoriseras som kontinuerlig video inspelning och händelsebaserade videoinspelningar. 

## <a name="continuous-video-recording"></a>Kontinuerlig videoinspelning  

Kontinuerlig videoinspelning (CVR) avser processen att kontinuerligt spela in all video som hämtas från en video källa. CVR garanterar att alla videoklipp är tillgängliga (dikterade av [registrerings principen](#recording-policy)) för att analysera och/eller granska vid en senare tidpunkt.

## <a name="event-based-video-recording"></a>Händelsebaserad videoinspelning  

Event-based Video Recording (EVR) syftar på att spela in videon som utlöses av en händelse. Händelsen i fråga kan ha sitt ursprung på grund av bearbetning av själva video signalen eller kan komma från en oberoende källa (till exempel från en dörr sensor). Event-baserad videoinspelning (EVR) kan resultera i lagrings besparingar, men det krävs ytterligare komponenter som genererar händelserna och utlöser video inspelningen (per en fördefinierad princip). Med andra ord, kräver EVR ytterligare beslut om de händelser som ska utlösa videoinspelningen och den princip som är associerad med videoinspelningen (kallas även för inspelnings princip). Ett exempel på en princip kan vara något som liknar: spela in videon i 2 minuter från 30 sekunder före händelse tiden. Händelserna i fråga kan ha sitt ursprung på grund av video bearbetning i själva kameran. Ett exempel är att flera kameror har stöd för att skapa en signal händelse för rörelse identifiering när rörelsen identifieras i en förkonfigurerad zon av intresse i kamerans visnings område. Händelserna kan också genereras genom bearbetning av videon på en annan enhet som samlar in videon från kameran och analyserar den med hjälp av enkla bild bearbetnings metoder eller avancerade maskin inlärnings modeller. 

## <a name="choosing-recording-modes"></a>Välja inspelnings lägen  

Valet av om du vill använda CVR eller EVR beror på affärs målen. Live video analys på IoT Edge tillhandahåller plattforms funktioner för både CVR och EVR. Du kan läsa mer om det i artiklarna [CVR](continuous-video-recording-concept.md) och [EVR](event-based-video-recording-concept.md) -scenario.

## <a name="recording-policy"></a>Registrerings princip  

Inspelnings principen refererar till de principer som dikterar start-/stopp tiden för registrering (i händelse av EVR), inspelnings tid och inspelning av inspelning. Genom att registrera principer kan du balansera lagrings kostnaden med företags krav. Inspelning av principer skiljer sig mellan CVR och EVR. För CVR definierar registrerings principen hur många dagars video som ska lagras (till exempel de senaste 7 dagarna). För EVR definierar registrerings principen när inspelningen ska börja och sluta, tillsammans med videons varaktighet. Du kan läsa mer om det i artiklarna [CVR](continuous-video-recording-concept.md) och [EVR](event-based-video-recording-concept.md) -scenario.

## <a name="next-steps"></a>Nästa steg

* [Identifiera rörelse och spela in videoklipp till Azure Media Services](detect-motion-record-video-clips-media-services-quickstart.md)
* [Identifiera rörelse, spela in video klipp till gräns enheter](detect-motion-record-video-clips-edge-devices-quickstart.md)

