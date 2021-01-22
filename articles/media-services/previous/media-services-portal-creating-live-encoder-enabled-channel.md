---
title: Utför direkt uppspelning med Azure Media Services för att skapa strömmar med flera bit hastigheter med Azure Portal | Microsoft Docs
description: De här självstudierna visar dig stegen för att skapa en kanal som tar emot en direktsänd dataström med enkel bithastighet och kodar den till en dataström med flera bithastigheter med hjälp av Azure-portalen.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 504f74c2-3103-42a0-897b-9ff52f279e23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: inhenkel
ms.openlocfilehash: a02f1fd873c0578bb4ec3a7788a06b10eebec7bd
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695483"
---
# <a name="perform-live-streaming-using-media-services-to-create-multi-bitrate-streams-with-azure-portal"></a>Utföra Direktsänd strömning med Media Services för att skapa strömmar med flera bit hastigheter med Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
> * [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
> * [REST-API](/rest/api/media/operations/channel)
> 

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Den här självstudien visar dig stegen för att skapa en **kanal** som tar emot en  direktsänd dataström med enkel bithastighet och kodar den till en dataström med multibithastighet.

Mer konceptinformation relaterad till kanaler som är aktiverade för Live Encoding finns i [Direktsänd strömning med Azure Media Services för att skapa dataströmmar i multibithastighet](media-services-manage-live-encoder-enabled-channels.md).

## <a name="common-live-streaming-scenario"></a>Vanligt scenario för direktsänd strömning

Följande steg är allmänna steg som ingår i att skapa vanliga program för direktsänd strömning.

> [!NOTE]
> Den rekommenderade maximala längden för en direktsänd händelse är för närvarande 8 timmar. Kontakta amshelp@microsoft.com om du behöver köra en kanal under en längre tidsperiod.

1. Anslut en videokamera till en dator. <br/>För installations tips kan du läsa mer i installations [programmet för enkel och bärbar händelse]( https://link.medium.com/KNTtiN6IeT).

    Om du inte har åtkomst till en kamera kan du använda verktyg som t. ex. [Wirecast för multistream](media-services-configure-wirecast-live-encoder.md) för att generera en live-feed från en videofil.
1. Starta och konfigurera en lokal livekodare som kan mata ut en dataström med enkel bithastighet i något av följande protokoll: RTMP eller Smooth Streaming. Mer information finns i [Support och direktsända kodare för Azure Media Services RTMP](https://go.microsoft.com/fwlink/?LinkId=532824). <br/>Ta också en titt på den här bloggen: [produktion av Direktsänd strömning med onlinebanksystem](https://link.medium.com/ttuwHpaJeT).

    Det här steget kan också utföras när du har skapat din kanal.
1. Skapa och starta en kanal.
1. Hämta kanalens infognings-URL.

    Infognings-URL:en används av livekodaren för att skicka dataströmmen till kanalen.
1. Hämta kanalens förhandsgransknings-URL.

    Använd denna URL för att kontrollera att din kanal tar emot den direktsända dataströmmen korrekt.
1. Skapa en händelse/ett program (som också kommer att skapa en tillgång).
1. Publicera händelsen (som skapar en OnDemand-positionerare för den associerade tillgången).
1. Starta händelsen när du är redo att påbörja strömning och arkivering.
1. Som alternativ kan den direktsända kodaren få signal om att starta en annons. Annonsen infogas i utdataströmmen.
1. Stoppa händelsen när du vill stoppa strömningen och arkiveringen av händelsen.
1. Ta bort händelsen (och ta eventuellt bort tillgången).

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att kunna genomföra självstudien.

* Du behöver ett Azure-konto för att genomföra kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter.
  Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* Ett Media Services-konto. Mer information om att skapa ett Media Services-konto finns i [Skapa konto](media-services-portal-create-account.md).
* En webbkamera och en kodare som kan skicka en direktsänd dataström i enkel bithastighet.

## <a name="create-a-channel"></a>Skapa en kanal

1. I [Azure-portalen](https://portal.azure.com/) klickar du på Media Services och sedan på namnet för Media Services-kontot.
2. Välj **Liveuppspelning**.
3. Välj **Skapa anpassad**. Det här alternativet gör att du kan skapa en kanal som är aktiverad för Live Encoding.

    ![Skapa en kanal](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel.png)
4. Klicka på **Inställningar**.

   1. Välj kanaltypen **Live Encoding**. Den här typen anger att du vill skapa en kanal som är aktiverad för Live Encoding. Det innebär att den inkommande dataströmmen i enkel bithastighet skickas till kanalen och kodas till en dataström med multibithastighet med hjälp av livekodarens angivna inställningar. Mer information finns i [Direktsänd strömning med Azure Media Services för att skapa dataströmmar i multibithastighet](media-services-manage-live-encoder-enabled-channels.md). Klicka på OK.
   2. Ange ett kanalnamn.
   3. Klicka på OK längst ned på sidan.
5. Välj fliken **Mata in**.

   1. På den här sidan kan du välja ett strömningsprotokoll. För kanaltypen **Live Encoding** är de giltiga protokollalternativen:

      * Fragmenterad MP4 med enkel bithastighet (Smooth Streaming)
      * RTMP med enkel bithastighet

        Mer detaljerad information om varje protokoll finns i [Direktsänd strömning med Azure Media Services för att skapa dataströmmar i multibithastighet](media-services-manage-live-encoder-enabled-channels.md).

        Du kan inte ändra protokollalternativ när kanalen eller dess associerade händelse/program körs. Om du behöver olika protokoll får du skapa separata kanaler för varje strömningsprotokoll.  
   2. Du kan använda IP-begränsning på inmatningen.

       Du kan definiera de IP-adresser som får mata in en video på den här kanalen. Tillåtna IP-adresser kan anges antingen som en enskild IP-adress (t. ex. 10.0.0.1), ett IP-intervall med en IP-adress och en CIDR-nätmask (t. ex. 10.0.0.1/22) eller ett IP-intervall med en IP-adress och en punktavgränsad decimal näts mask (t. ex. 10.0.0.1 (255.255.252.0)).

       Om inga IP-adresser har angetts och det saknas regeldefinitioner kommer ingen IP-adress att tillåtas. Skapa en regel för att tillåta IP-adresser och ange 0.0.0.0/0.
6. På fliken **Förhandsvisning** appliceras IP-begränsning på förhandsgranskningen.
7. På fliken **Kodning** anges den förinställda kodningen.

    För närvarande är den enda förinställda systeminställningen som du kan välja **Default 720p**. Öppna ett Microsoft supportärende om du vill ange en förinställd anpassning. Ange därefter namnet på den förinställning som skapats för dig.

> [!NOTE]
> För närvarande kan kanalstarten ta upp till 30 minuter. Kanalåterställning kan ta upp till 5 minuter.

När du har skapat kanalen kan du klicka på kanalen och välja **Inställningar** där du kan visa dina kanalkonfigurationer.

Mer information finns i [Direktsänd strömning med Azure Media Services för att skapa dataströmmar i multibithastighet](media-services-manage-live-encoder-enabled-channels.md).

## <a name="get-ingest-urls"></a>Hämta infognings-URL:er

När kanalen har skapats kan du få infognings-URL:er som du tillhandahåller till livekodaren. Kodaren använder dessa URL:er för att mata in en direktsänd dataström.

![URL för inmatning](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)

## <a name="create-and-manage-events"></a>Skapa och hantera händelser

### <a name="overview"></a>Översikt

En kanal är associerad med händelser och program som gör att du kan styra publicering och lagring av segment i en direktsänd dataström. Kanaler hanterar händelser/program. Relationen mellan kanal och program liknar den för traditionella media där en kanal har en konstant ström av innehåll och ett program är begränsat till en viss tidsinställd händelse på kanalen.

Du kan ange det antal timmar som du vill behålla inspelat innehåll för händelsen genom att ställa in längden för **Arkivfönster**. Det här värdet kan anges från minst 5 minuter till högst 25 timmar. Även arkivfönstrets längd påverkar den maximala tid som klienter kan söka bakåt i tiden från den aktuella direktsända positionen. Händelser kan köras under den angivna tidsperioden men innehåll som understiger fönsterlängden ignoreras kontinuerligt. Värdet för den här egenskapen avgör också hur länge klientmanifesten kan växa.

Varje händelse är associerad till en tillgång. För att publicera händelsen måste du skapa en OnDemand-lokaliserare för den associerade tillgången. Med den här lokaliseraren kan du skapa en strömnings-URL som du kan tillhandahålla till dina klienter.

En kanal har stöd för upp till tre händelser som körs samtidigt så du kan skapa flera arkiv för samma inkommande dataström. På så sätt kan du publicera och arkivera olika delar av en händelse efter behov. Ditt verksamhetsbehov kan till exempel vara att arkivera 6 timmar av en händelse, men bara sända 10 minuter. För att åstadkomma detta måste du skapa två händelser som körs samtidigt. En händelse ställs in för att arkivera 6 timmar av händelsen, men programmet publiceras inte. Den andra händelsen ställs in för att arkivera i 10 minuter och det här programmet publiceras.

Du bör inte återanvända befintliga program för nya händelser. Skapa och starta istället ett nytt program för varje händelse.

Starta en händelse eller ett program när du är redo att påbörja strömning och arkivering. Stoppa händelsen när du vill stoppa strömningen och arkiveringen av händelsen.

Om du vill ta bort arkiverat innehåll, stoppar du och tar bort händelsen och tar sedan bort associerade tillgången. En tillgång kan inte tas bort om den används av händelsen. Händelsen måste tas bort först.

Även efter att du stoppat och tagit bort händelsen skulle användarna kunna strömma ditt arkiverade innehåll som en video på begäran så länge du inte tar bort tillgången.

Om du vill behålla det arkiverade innehållet, men inte att det ska vara tillgängligt för strömning, tar du bort strömningslokaliseraren.

### <a name="createstartstop-events"></a>Skapa/Starta/Stoppa händelser

När dataströmmen väl flödar till kanalen kan du påbörja strömningshändelsen genom att skapa en tillgång, ett program och en strömningspositionerare. Detta arkiverar dataströmmen och gör den tillgänglig för visning via strömningsslutpunkten.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**.

Det finns två sätt att starta en händelse:

1. På **Kanal**-sidan trycker du på **Live-händelse** för att lägga till en ny händelse.

    Ange: händelsens namn, tillgångsnamn, arkivfönster och krypteringsalternativ.

    ![Skapa program](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)

    Om du markerat **Publicera denna live-händelse nu** skapas händelsens PUBLICERINGS-URL:ER.

    Du kan trycka på **Start** när du är redo att strömma händelsen.

    När du startat händelsen kan du trycka på **Titta på** för att börja spela upp innehållet.
2. Du kan också använda en genväg och trycka på knappen **Go Live** på **Kanal**-sidan. Detta skapar en standardtillgång, ett program och en strömningspositionerare.

    Händelsen heter **standard** och arkivfönstret har angetts till 8 timmar.

Du kan titta på den publicerade händelsen från sidan **Live-händelse**.

Om du klickar på **Off Air**, stoppas alla live-händelser.

## <a name="watch-the-event"></a>Titta på händelsen

För att titta på händelsen klickar du på **Titta på** i Azure-portalen eller kopierar strömnings-URL:en och använder en valfri spelare.

![Skapad](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-play-event.png)

Live-händelser konverterar automatiskt händelser till innehåll-på-begäran när de stoppas.

## <a name="clean-up"></a>Rensa

Följ stegen nedan om du är klar med strömningen av händelser och vill rensa de resurser som etablerades tidigare.

* Stoppa sändningen av dataströmmen från kodaren.
* Stoppa kanalen. När kanalen har stoppats medför den inga avgifter. När du vill starta den igen har den samma infognings-URL så att du inte behöver konfigurera om din kodare.
* Du kan avbryta din strömningsslutpunkt om du inte vill fortsätta att tillhandahålla arkivet för din direktsända händelse som en strömning på begäran. När kanalen har stoppats medför den inga avgifter.

## <a name="view-archived-content"></a>Visa arkiverat innehåll

Även efter att du stoppat och tagit bort händelsen skulle användarna kunna strömma ditt arkiverade innehåll som en video på begäran så länge du inte tar bort tillgången.

> [!WARNING]
> En till gång **bör inte** tas bort om den används av en händelse. händelsen måste tas bort först.

Om du vill hantera dina till gångar väljer du **inställning** och klickar på **till gångar**.

![Tillgångar](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-assets.png)

## <a name="considerations"></a>Överväganden

* Den rekommenderade maximala längden för en direktsänd händelse är för närvarande 8 timmar. Kontakta amshelp@microsoft.com om du behöver köra en kanal under en längre tidsperiod.
* Kontrollera att slutpunkten för direktuppspelning som du vill spela upp innehåll från har tillståndet **Körs**.

## <a name="next-steps"></a>Nästa steg

Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
