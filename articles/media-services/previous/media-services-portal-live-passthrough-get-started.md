---
title: Live Stream med lokala kodare som använder Azure Portal | Microsoft Docs
description: Den här vägledningen visar dig stegen för att skapa en kanal som är konfigurerad för en genomströmningsleverans.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 6f4acd95-cc64-4dd9-9e2d-8734707de326
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 6dd2f8a228c582e99f7d7281bd2d31faff8cadda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103010804"
---
# <a name="perform-live-streaming-with-on-premises-encoders-using-azure-portal"></a>Utför Direktsänd strömning med lokala kodare med hjälp av Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Portal](media-services-portal-live-passthrough-get-started.md)
> * [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
> * [REST](/rest/api/media/operations/channel)
> 
> 

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Den här vägledningen visar dig stegen för att använda Azure-portalen för att skapa en **kanal** som är konfigurerad för en genomströmningsleverans. 

## <a name="prerequisites"></a>Förutsättningar
Följande krävs för att kunna genomföra vägledningen:

* Ett Azure-konto. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/). 
* Ett Media Services-konto. Information om hur du skapar ett Media Services konto finns i [så här skapar du ett Media Services konto](media-services-portal-create-account.md).
* En webbkamera. Till exempel [Telestream Wirecast-kodaren](media-services-configure-wirecast-live-encoder.md). 

Vi rekommenderar att du tittar närmare på följande artiklar:

* [Support och direktsända kodare för Azure Media Services RTMP](https://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)
* [Översikt över direktsänd strömning med Azure Media Services](media-services-manage-channels-overview.md)
* [Liveuppspelning med lokala kodare som skapar strömmar med flera bithastigheter](media-services-live-streaming-with-onprem-encoders.md)

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Vanligt scenario för direktsänd strömning

Följande steg beskriver uppgifter som ingår i att skapa vanliga appar för direktsänd strömning som använder kanaler som har konfigurerats för genomströmningsleverans. Den här vägledningen visar hur du skapar och hanterar en genomströmningskanal och direktsända händelser.

> [!NOTE]
> Kontrollera att slutpunkten för direktuppspelning som du vill spela upp innehåll från har tillståndet **Körs**. 
    
1. Anslut en videokamera till en dator. <br/>För installations tips kan du läsa mer i installations [programmet för enkel och bärbar händelse]( https://link.medium.com/KNTtiN6IeT).
1. Starta och konfigurera en lokal direktsänd kodare som matar ut en RTMP- eller fragmenterad MP4-dataström i multibithastighet. Mer information finns i [Support och direktsända kodare för Azure Media Services RTMP](https://go.microsoft.com/fwlink/?LinkId=532824).<br/>Ta också en titt på den här bloggen: [produktion av Direktsänd strömning med onlinebanksystem](https://link.medium.com/ttuwHpaJeT).
   
    Det här steget kan också utföras när du har skapat din kanal.
1. Skapa och starta en genomströmningskanal
1. Hämta kanalens infognings-URL. 
   
    Infognings-URL:en används av livekodaren för att skicka dataströmmen till kanalen.
1. Hämta kanalens förhandsgransknings-URL. 
   
    Använd denna URL för att kontrollera att din kanal tar emot den direktsända dataströmmen korrekt.
1. Skapa en direktsänd händelse eller ett direktsänt program. 
   
    När du använder Azure-portalen, skapas även en tillgång då du skapar en direktsänd händelse. 

1. Starta händelsen eller programmet när du är redo att påbörja strömning och arkivering.
1. Som alternativ kan den direktsända kodaren få signal om att starta en annons. Annonsen infogas i utdataströmmen.
1. Stoppa händelsen eller programmet när du vill stoppa strömningen och arkiveringen av händelsen.
1. Ta bort händelsen eller programmet (och ta eventuellt bort tillgången).     

> [!IMPORTANT]
> Titta närmare på [Liveuppspelning med lokala kodare som skapar dataströmmar i multibithastighet](media-services-live-streaming-with-onprem-encoders.md) för att lära dig mer om koncept och överväganden som rör liveuppspelning med lokala kodare och genomströmningskanaler.
> 
> 

## <a name="to-view-notifications-and-errors"></a>Visa meddelanden och fel
Klicka på meddelandeikonen om du vill visa meddelanden och fel som genereras av Azure-portalen.

![Meddelanden](./media/media-services-portal-passthrough-get-started/media-services-notifications.png)

## <a name="create-and-start-pass-through-channels-and-events"></a>Skapa och starta genomströmningskanaler och händelser
En kanal är associerad med händelser och program som gör att du kan styra publicering och lagring av segment i en direktsänd dataström. Kanaler hanterar händelser. 

Du kan ange det antal timmar som du vill behålla inspelat innehåll för programmet genom att ställa in längden för **Arkivfönster**. Det här värdet kan anges från minst 5 minuter till högst 25 timmar. Även arkivfönstrets längd påverkar den maximala tid som klienter kan söka bakåt i tiden från den aktuella direktsända positionen. Händelser kan köras under den angivna tidsperioden men innehåll som understiger fönsterlängden ignoreras kontinuerligt. Värdet för den här egenskapen avgör också hur länge klientmanifesten kan växa.

Varje händelse är associerad till en tillgång. För att publicera händelsen måste du skapa en OnDemand-lokaliserare för den associerade tillgången. Med den här lokaliseraren kan du skapa en strömnings-URL som du kan tillhandahålla till dina klienter.

En kanal har stöd för upp till tre händelser som körs samtidigt så du kan skapa flera arkiv för samma inkommande dataström. På så sätt kan du publicera och arkivera olika delar av en händelse efter behov. Ditt verksamhetsbehov kan till exempel vara att arkivera 6 timmar av ett program, men bara sända 10 minuter. För att åstadkomma detta måste du skapa två program som körs samtidigt. Ett program ställs in för att arkivera 6 timmar av händelsen, men programmet publiceras inte. Det andra programmet ställs in för att arkivera i 10 minuter och det här programmet publiceras.

Du bör inte återanvända befintliga direktsända händelser. Skapa och starta istället en ny händelse för varje händelse.

Starta händelsen när du är redo att påbörja strömning och arkivering. Stoppa programmet när du vill stoppa strömningen och arkiveringen av händelsen. 

Om du vill ta bort arkiverat innehåll, stoppar du och tar bort händelsen och tar sedan bort associerade tillgången. En tillgång kan inte tas bort om den används av en händelse. Händelsen måste tas bort först. 

Även efter att du stoppat och tagit bort händelsen skulle användarna kunna strömma ditt arkiverade innehåll som en video på begäran så länge du inte tar bort tillgången.

Om du vill behålla det arkiverade innehållet, men inte att det ska vara tillgängligt för strömning, tar du bort strömningslokaliseraren.

### <a name="to-use-the-portal-to-create-a-channel"></a>Använda portalen för att skapa en kanal
Detta avsnitt visar hur du använder alternativet **Snabbregistrering** för att skapa en genomströmningskanal.

Mer information om genomströmningskanaler finns i [Liveuppspelning med lokala kodare som skapar dataströmmar i multibithastighet](media-services-live-streaming-with-onprem-encoders.md).

1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. I fönstret **Inställningar** klickar du på **Direktsänd strömning**. 
   
    ![Komma igång](./media/media-services-portal-passthrough-get-started/media-services-getting-started.png)
   
    Fönstret **Direktsänd strömning** visas.
3. Klicka på **Snabbregistrering** för att skapa en genomströmningskanal med RTMP-infogningsprotokollet.
   
    Fönstret **SKAPA EN NY KANAL** visas.
4. Namnge den nya kanalen och klicka på **Skapa**. 
   
    Detta skapar en genomströmningskanal med RTMP-infogningsprotokollet.

## <a name="create-events"></a>Skapa händelser
1. Välj en kanal till vilken du vill lägga till en händelse.
2. Tryck på knappen **Direktsänd händelse**.

![Händelse](./media/media-services-portal-passthrough-get-started/media-services-create-events.png)

## <a name="get-ingest-urls"></a>Hämta infognings-URL:er
När kanalen har skapats kan du få infognings-URL:er som du tillhandahåller till livekodaren. Kodaren använder dessa URL:er för att mata in en direktsänd dataström.

![Skärm bild som visar sidan "Live Streaming" med en kanal vald och fönstret kanal visas.](./media/media-services-portal-passthrough-get-started/media-services-channel-created.png)

## <a name="watch-the-event"></a>Titta på händelsen
För att titta på händelsen klickar du på **Titta på** i Azure-portalen eller kopierar strömnings-URL:en och använder en valfri spelare. 

![Skapad](./media/media-services-portal-passthrough-get-started/media-services-default-event.png)

Direktsända händelser konverteras automatiskt till innehåll på begäran när de stoppas.

## <a name="clean-up"></a>Rensa
Mer information om genomströmningskanaler finns i [Liveuppspelning med lokala kodare som skapar dataströmmar i multibithastighet](media-services-live-streaming-with-onprem-encoders.md).

* En kanal kan stoppas endast när alla händelser eller program i kanalen har stoppats.  När kanalen har stoppats medför den inga avgifter. När du vill starta den igen har den samma infognings-URL så att du inte behöver konfigurera om din kodare.
* En kanal kan bara tas bort när alla direktsända händelser i kanalen har tagits bort.

## <a name="view-archived-content"></a>Visa arkiverat innehåll
Även efter att du stoppat och tagit bort händelsen skulle användarna kunna strömma ditt arkiverade innehåll som en video på begäran så länge du inte tar bort tillgången. En tillgång kan inte tas bort om den används av en händelse. Händelsen måste tas bort först. 

Om du vill hantera dina till gångar väljer du **inställning** och klickar på **till gångar**.

![Tillgångar](./media/media-services-portal-passthrough-get-started/media-services-assets.png)

## <a name="next-step"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
