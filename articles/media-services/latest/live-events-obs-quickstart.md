---
title: Skapa en Live-ström med ONLINEBANKSYSTEM Studio
description: Lär dig hur du skapar en Azure Media Services Live Stream med hjälp av portalen och ONLINEBANKSYSTEM Studio
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 08/31/2020
ms.openlocfilehash: 83f072b67f5aa162137a55d2b311dccf0daf7f53
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98956080"
---
# <a name="create-an-azure-media-services-live-stream-with-obs"></a>Skapa en Azure Media Services Live Stream med ONLINEBANKSYSTEM

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här snabb starten hjälper dig att skapa en Azure Media Services Live Stream med hjälp av Azure Portal och öppna sändning Studio (ONLINEBANKSYSTEM). Det förutsätter att du har en Azure-prenumeration och har skapat ett Media Services-konto.

I den här snabb starten tar vi upp:

- Konfigurera en lokal kodare med ONLINEBANKSYSTEM.
- Konfigurera en Live-dataström.
- Konfigurera real tids data Ströms utdata.
- Kör en standard slut punkt för direkt uppspelning.
- Använd Azure Media Player för att Visa Live Stream och utdata på begäran.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Öppna webbläsaren och gå till [Microsoft Azure-portalen](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.

## <a name="set-up-an-on-premises-encoder-by-using-obs"></a>Konfigurera en lokal kodare med hjälp av ONLINEBANKSYSTEM

1. Hämta och installera ONLINEBANKSYSTEM för ditt operativ system på [webbplatsen öppna program vara för program vara](https://obsproject.com/).
1. Starta programmet och låt det vara öppet.

## <a name="run-the-default-streaming-endpoint"></a>Kör standard slut punkten för direkt uppspelning

1. Välj **strömnings slut punkter** i Media Services listan.

   ![Meny alternativ för strömnings slut punkter](media/live-events-obs-quickstart/streaming-endpoints.png)
1. Om statusen för direkt uppspelnings slut punkten stoppas väljer du den. Det här steget tar dig till sidan för slut punkten.
1. Välj **Starta**.

   ![Start-knappen för slut punkten för direkt uppspelning](media/live-events-obs-quickstart/start.png)

## <a name="set-up-an-azure-media-services-live-stream"></a>Konfigurera en Azure Media Services Live Stream

1. Gå till Azure Media Services kontot i portalen och välj sedan **Live streaming** från **Media Services** listan.

   ![Länk till Live Streaming](media/live-events-obs-quickstart/select-live-streaming.png)
1. Välj **Lägg till Live-händelse** för att skapa en ny direkt uppspelnings händelse.

   ![Lägg till ikonen Live-händelse](media/live-events-obs-quickstart/add-live-event.png)
1. Ange ett namn för den nya händelsen, till exempel *TestLiveEvent*, i rutan **Live-händelseloggen** .

   ![Rutan Live-händelse namn](media/live-events-obs-quickstart/live-event-name.png)
1. Ange en valfri beskrivning av händelsen i rutan **Beskrivning** .
1. Välj alternativet genom **strömning – ingen moln kodning** .

   ![Alternativ för moln kodning](media/live-events-obs-quickstart/cloud-encoding.png)
1. Välj alternativet **RTMP** .
1. Se till att alternativet **Nej** är markerat för att **starta direkt sändningen** för att undvika att faktureras för direkt sändningen innan det är klart. (Faktureringen påbörjas när Live-händelsen startas.)

   ![Alternativet Starta Live-händelse](media/live-events-obs-quickstart/start-live-event-no.png)
1. Granska inställningarna genom att klicka på knappen **Granska + skapa** .
1. Välj knappen **skapa** för att skapa en Live-händelse. Sedan återgår du till listan över direktsända händelser.
1. Välj länken till den Live-händelse som du nyss skapade. Observera att din händelse har stoppats.
1. Se till att sidan är öppen i webbläsaren. Vi kommer tillbaka till den senare.

## <a name="set-up-a-live-stream-by-using-obs-studio"></a>Konfigurera en Live-ström med hjälp av ONLINEBANKSYSTEM Studio

ONLINEBANKSYSTEM börjar med en standard-scen men utan att några indata har valts.

   ![ONLINEBANKSYSTEM standard skärm](media/live-events-obs-quickstart/live-event-obs-default-screen.png)

### <a name="add-a-video-source"></a>Lägg till en video källa

1. Från panelen **källor** klickar du på ikonen **Lägg till** för att välja en ny käll enhet. Menyn **källor** öppnas.

1. Välj **video avbildnings enhet** från menyn käll enhet. Menyn för att **skapa/välja källa** öppnas.

   ![Menyn ONLINEBANKSYSTEM-källor med video enhet vald](media/live-events-obs-quickstart/live-event-obs-video-device-menu.png)

1. Välj alternativ knappen **Lägg till befintlig** och klicka sedan på **OK**. Menyn **Egenskaper för video enhet** öppnas.

   ![ONLINEBANKSYSTEM ny video källa-meny med Lägg till befintlig vald](media/live-events-obs-quickstart/live-event-obs-new-video-source.png)

1. I list rutan **enhet** väljer du de video ingångar som du vill använda för sändningen. Lämna resten av inställningarna separat för tillfället och klicka på **OK**. Indatakällan kommer att läggas till i panelen **källor** och vyn videoinspelning visas i **förhands gransknings** avsnittet.

   ![ONLINEBANKSYSTEM kamera inställningar](media/live-events-obs-quickstart/live-event-surface-camera.png)

### <a name="add-an-audio-source"></a>Lägg till en ljud källa

1. Från panelen **källor** klickar du på ikonen **Lägg till** för att välja en ny käll enhet. Menyn käll enhet öppnas.

1. Välj **ljud** inspelnings insamling från menyn käll enhet. Menyn för att **skapa/välja källa** öppnas.

   ![Menyn ONLINEBANKSYSTEM-källor med ljud enheten vald](media/live-events-obs-quickstart/live-event-obs-audio-device-menu.png)

1. Välj alternativ knappen **Lägg till befintlig** och klicka sedan på **OK**. Menyn **Egenskaper för inspelning av ljud** inspelning öppnas.

   ![ONLINEBANKSYSTEM ljud källa med Lägg till befintlig vald ](media/live-events-obs-quickstart/live-event-obs-new-audio-source.png)

1. I list rutan **enhet** väljer du den ljud inspelnings enhet som du vill använda för sändningen. Lämna resten av inställningarna separat för tillfället och klicka på OK. Ljud inspelnings enheten kommer att läggas till i panelen ljud mixning.

   ![List Rute lista för ONLINEBANKSYSTEM ljud enhets val](media/live-events-obs-quickstart/live-event-select-audio-device.png)

### <a name="set-up-streaming-in-obs"></a>Konfigurera strömning i ONLINEBANKSYSTEM

I nästa procedur går du tillbaka till Azure Media Services i webbläsaren för att kopiera inmatnings-URL: en för att komma in i inställningarna för utdata:

1. På sidan Azure Media Services i portalen väljer du **Starta** för att starta händelsen Live Stream. (Faktureringen startar nu.)

   ![Start ikon](media/live-events-obs-quickstart/start.png)
1. Ange att **RTMP** ska växla till **rtmps**.
1. I rutan **inmatad URL** kopierar du webb adressen till Urklipp.

   ![Inmatad URL](media/live-events-obs-quickstart/input-url.png)

1. Växla till ONLINEBANKSYSTEM-programmet.

1. Klicka på knappen **Inställningar** på panelen **kontroller** . Inställningarna är öppna.

   ![ONLINEBANKSYSTEM kontroll panelen med inställningar valt](media/live-events-obs-quickstart/live-event-obs-settings.png)

1. Välj **Stream** på menyn **Inställningar** .

1. I list rutan **tjänst** väljer du Visa alla och väljer sedan **Anpassad...**.

1. I fältet **Server** klistrar du in den RTMP-URL som du kopierade till Urklipp.

1. Ange något i fältet **Stream Key** .  Det spelar ingen roll, men det måste ha ett värde.

    ![ONLINEBANKSYSTEM Stream-inställningar](media/live-events-obs-quickstart/live-event-obs-stream-settings.png)

1. Välj **utdata** på menyn **Inställningar** .

1. Ange *2* i fältet **intervall för nyckel** fält. Detta anger fragment längden till 2 sekunder. Använd värdet 1 sekund för leverans av låg latens.

1. Valfritt: Ange för **inställningen för CPU-användning** till *veryfast* om du använder en dator som har låg bearbetnings kraft. Alternativt kan du ange kbps till något lägre om det finns oönskade nätverks villkor.

   ![ONLINEBANKSYSTEM-inställningar för utdata](media/live-events-obs-quickstart/live-event-obs-advanced-output-settings.png)

1. Lämna resten av inställningarna oförändrade och klicka på **OK**.

### <a name="start-streaming"></a>Starta direkt uppspelning

1. I panelen **kontroller** klickar du på **starta direkt uppspelning**.

    ![ONLINEBANKSYSTEM för att starta direkt uppspelning](media/live-events-obs-quickstart/live-event-obs-start-streaming.png)

2. Växla till skärmen Azure Media Services Live-händelse i webbläsaren och klicka på länken **load The Player** . Nu bör du se din data ström i Preview Player.

## <a name="set-up-outputs"></a>Konfigurera utdata

Den här delen konfigurerar dina utdata och gör att du kan spara en inspelning av din Live Stream.  

> [!NOTE]
> För att du ska kunna strömma dessa utdata måste slut punkten för direkt uppspelning köras. Mer information finns i avsnittet senare [köra standard slut punkten för direkt uppspelning](#run-the-default-streaming-endpoint) .

1. Välj länken **skapa utdata** under **utdata** i video visnings programmet.
1. Om du vill kan du redigera namnet på utdata i rutan **namn** så att det blir mer användarvänligt, så det är enkelt att hitta dem senare.

   ![Utmatnings namn ruta](media/live-events-wirecast-quickstart/output-name.png)
1. Lämna bara resten av rutorna för tillfället.
1. Välj **Nästa** för att lägga till en strömmande positionerare.
1. Ändra namnet på lokaliseraren till något mer användarvänligt, om du vill.

   ![Namn Box för lokaliserare](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Lämna allt annat på den här skärmen separat för tillfället.
1. Välj **Skapa**.

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>Spela upp utdata-sändningen med hjälp av Azure Media Player

1. Kopiera den strömmande URL: en under **utdata** Videos pelaren.
1. Öppna [Azure Media Player demonstration](https://ampdemo.azureedge.net/azuremediaplayer.html)i en webbläsare.
1. Klistra in URL-adressen för strömning i rutan **URL** för Azure Media Player.
1. Välj knappen **Uppdatera spelare** .
1. Klicka på ikonen **spela upp** på videon om du vill se din real tids ström.

## <a name="stop-the-broadcast"></a>Stoppa sändningen

När du tror att du har strömmat tillräckligt med innehåll, stoppa sändningen.

1. I portalen väljer du **stoppa**.

1. I ONLINEBANKSYSTEM väljer du knappen **stoppa direkt uppspelning** på **kontroll** panelen. Det här steget avbryter sändningen från ONLINEBANKSYSTEM.

## <a name="play-the-on-demand-output-by-using-azure-media-player"></a>Spela upp utdata på begäran genom att använda Azure Media Player

De utdata som du har skapat är nu tillgängliga för strömning på begäran så länge som slut punkten för direkt uppspelning körs.

1. Gå till Media Services listan och välj **till gångar**.
1. Hitta de händelse utdata som du skapade tidigare och välj länken till till gången. Sidan till gångens utdata öppnas.
1. Kopiera strömnings-URL: en under Videos pelaren för till gången.
1. Gå tillbaka till Azure Media Player i webbläsaren och klistra in URL-adressen för strömning i rutan URL.
1. Välj **uppdaterings spelaren**.
1. Välj **uppspelnings** ikonen på videon om du vill visa en till gång på begäran.

## <a name="clean-up-resources"></a>Rensa resurser

> [!IMPORTANT]
> Stoppa tjänsterna! När du har slutfört stegen i den här snabb starten måste du stoppa Live-händelsen och slut punkten för direkt uppspelning, annars debiteras du för den tid som de fortfarande körs. Om du vill stoppa direkt sändningen går du till [stoppa sändnings](#stop-the-broadcast) proceduren, steg 2 och 3.

Stoppa slut punkten för direkt uppspelning:

1. I listan Media Services väljer du **slut punkter för direkt uppspelning**.
2. Välj den standard slut punkt för direkt uppspelning som du startade tidigare. I det här steget öppnas sidan slut punkt.
3. Välj **stoppa**.

> [!TIP]
> Om du inte vill behålla till gångarna från den här händelsen måste du ta bort dem så att du inte debiteras för lagring.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Live-händelser och Live-utdata i Media Services](./live-events-outputs-concept.md)
