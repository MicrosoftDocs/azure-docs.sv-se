---
title: Bli en lokal Encoder-partner
description: Den här artikeln beskriver hur du verifierar dina lokala kodare för direkt uppspelning.
services: media-services
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 08/31/2020
ms.topic: how-to
ms.service: media-services
ms.openlocfilehash: a8b40206a73672b1d6bbb0ecded26b9f974be6b9
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277597"
---
# <a name="how-to-verify-your-on-premises-live-streaming-encoder"></a>Så här verifierar du din lokala Live streaming-kodare

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Som en Azure Media Services lokal Encoder-partner Media Services befordrar din produkt genom att rekommendera din kodare till företags kunder. För att bli en lokal Encoder-partner måste du kontrol lera kompatibiliteten för din lokala kodare med Media Services. Det gör du genom att utföra följande verifieringar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="pass-through-live-event-verification"></a>Direkt händelse verifiering

1. Kontrol lera att **slut punkten för direkt uppspelning** körs i ditt Media Services konto. 
2. Skapa och starta direkt **sändnings** evenemanget. <br/> Mer information finns i [livehändelsetillstånd och fakturering](live-event-states-billing-concept.md).
3. Hämta URL: en för inmatning och konfigurera din lokala kodare för att använda URL: en för att skicka en Live-dataström med flera bit hastigheter till Media Services.
4. Hämta för hands versionen av URL: en och Använd den för att kontrol lera att inmatarna faktiskt tas emot.
5. Skapa ett nytt **till gångs** objekt.
6. Skapa en **Live-utdata** och Använd namnet på den till gång som du skapade.
7. Skapa en **strömmande lokaliserare** med de inbyggda typer av **strömmande principer** .
8. Visa en lista över Sök vägarna för den **strömmande lokaliseraren** för att få tillbaka de webb adresser som ska användas.
9. Hämta värd namnet för den **slut punkt för direkt uppspelning** som du vill strömma från.
10. Kombinera URL: en från steg 8 med värd namnet i steg 9 för att få den fullständiga URL: en.
11. Kör din Live-kodare i cirka 10 minuter.
12. Stoppa livehändelsen. 
13. Använd en spelare som [Azure Media Player](https://aka.ms/azuremediaplayer) för att se den arkiverade till gången för att säkerställa att uppspelningen inte har några synliga fel på alla kvalitets nivåer. Du kan också titta på och validera via URL: en för för hands versionen under Live-sessionen.
14. Registrera till gångs-ID, den publicerade streaming-URL: en för Live-arkivet och de inställningar och den version som används i din Live Encoder.
15. Återställ Live händelse tillstånd när du har skapat varje exempel.
16. Upprepa steg 5 till och med 15 för alla konfigurationer som stöds av kodaren (med och utan AD-signalering, under texter eller olika kodnings hastigheter).

## <a name="live-encoding-live-event-verification"></a>Live-kodning av Live-händelse

1. Kontrol lera att **slut punkten för direkt uppspelning** körs i ditt Media Services konto. 
2. Skapa och starta Live **encoding** Live-händelsen. <br/> Mer information finns i [livehändelsetillstånd och fakturering](live-event-states-billing-concept.md).
3. Hämta URL: en för inmatning och konfigurera kodaren för att skicka en Live-dataström med en bit hastighet till Media Services.
4. Hämta för hands versionen av URL: en och Använd den för att kontrol lera att inmatarna faktiskt tas emot.
5. Skapa ett nytt **till gångs** objekt.
6. Skapa en **Live-utdata** och Använd namnet på den till gång som du skapade.
7. Skapa en **strömmande lokaliserare** med de inbyggda typer av **strömmande principer** .
8. Visa en lista över Sök vägarna för den **strömmande lokaliseraren** för att få tillbaka de webb adresser som ska användas.
9. Hämta värd namnet för den **slut punkt för direkt uppspelning** som du vill strömma från.
10. Kombinera URL: en från steg 8 med värd namnet i steg 9 för att få den fullständiga URL: en.
11. Kör din Live-kodare i cirka 10 minuter.
12. Stoppa livehändelsen.
13. Använd en spelare som [Azure Media Player](https://aka.ms/azuremediaplayer) för att se den arkiverade till gången för att säkerställa att uppspelningen inte har några synliga fel för alla kvalitets nivåer. Du kan också titta på och validera via URL: en för för hands versionen under Live-sessionen.
14. Registrera till gångs-ID, den publicerade streaming-URL: en för Live-arkivet och de inställningar och den version som används i din Live Encoder.
15. Återställ Live händelse tillstånd när du har skapat varje exempel.
16. Upprepa steg 5 till och med 15 för alla konfigurationer som stöds av kodaren (med och utan AD-signalering, under texter eller olika kodnings hastigheter).

## <a name="longevity-verification"></a>Longevity-verifiering

Följ samma steg som i direkt [sändnings händelse verifiering](#pass-through-live-event-verification) , förutom steg 11. <br/>I stället för 10 minuter kan du köra din Live-kodare i en vecka eller längre. Använd en spelare som [Azure Media Player](https://aka.ms/azuremediaplayer) för att se direkt uppspelningen från tid till gång (eller till en arkiverad till gång) för att säkerställa att uppspelningen inte har några synliga fel.

## <a name="email-your-recorded-settings"></a>E-posta dina inspelade inställningar

Skicka sedan dina inspelade inställningar och Live Archive-parametrarna till Azure Media Services vid amshelp@microsoft.com ett meddelande om att alla själv verifierings kontroller har slutförts. Ta även med din kontakt information för alla följare. Du kan kontakta Azure Media Servicess teamet och få frågor om den här processen.

## <a name="see-also"></a>Se även

[Testade lokala kodare](encode-recommended-on-premises-live-encoders.md)

## <a name="next-steps"></a>Nästa steg

[Direktsänd strömning med Media Services v3](stream-live-streaming-concept.md)
