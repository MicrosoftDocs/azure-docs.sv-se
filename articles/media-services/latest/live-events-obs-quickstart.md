---
title: Skapa en Live-ström med ONLINEBANKSYSTEM Studio
description: Lär dig hur du skapar en Azure Media Services Live Stream med hjälp av portalen och ONLINEBANKSYSTEM Studio
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/20/2021
ms.openlocfilehash: d52affbdc4dc433c40be687f2e56afae4bcf4c2a
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104949935"
---
# <a name="create-an-azure-media-services-live-stream-with-obs"></a>Skapa en Azure Media Services Live Stream med ONLINEBANKSYSTEM

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här snabb starten hjälper dig att skapa en Media Services Live-händelse med hjälp av Azure Portal och sändning med Open Television Studio (ONLINEBANKSYSTEM). Det förutsätter att du har en Azure-prenumeration och har skapat ett Media Services-konto.

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

   ![Meny alternativ för strömnings slut punkter.](media/live-events-obs-quickstart/streaming-endpoints.png)
1. Om statusen för direkt uppspelnings slut punkten stoppas väljer du den. Det här steget tar dig till sidan för slut punkten.
1. Välj **Starta**.

   ![Start-knappen för slut punkten för direkt uppspelning.](media/live-events-obs-quickstart/start.png)

## <a name="set-up-an-azure-media-services-live-stream"></a>Konfigurera en Azure Media Services Live Stream

1. Gå till Azure Media Services kontot i portalen och välj sedan **Live streaming** från **Media Services** listan.

   ![Länk till direkt uppspelning.](media/live-events-obs-quickstart/select-live-streaming.png)
1. Välj **Lägg till Live-händelse** för att skapa en ny direkt uppspelnings händelse.

   ![Lägg till ikonen Live-händelse.](media/live-events-obs-quickstart/add-live-event.png)
1. Ange ett namn för den nya händelsen, till exempel *TestLiveEvent*, i rutan **Live-händelseloggen** .

   ![Rutan Live-händelse namn.](media/live-events-obs-quickstart/live-event-name.png)
1. Ange en valfri beskrivning av händelsen i rutan **Beskrivning** .
1. Välj alternativet genom **strömning – ingen moln kodning** .

   ![Alternativ för moln kodning.](media/live-events-obs-quickstart/cloud-encoding.png)
1. Välj alternativet **RTMP** .
1. Se till att alternativet **Nej** är markerat för att **starta direkt sändningen** för att undvika att faktureras för direkt sändningen innan det är klart. (Faktureringen påbörjas när Live-händelsen startas.)

   ![Starta Live Event-alternativet.](media/live-events-obs-quickstart/start-live-event-no.png)
1. Granska inställningarna genom att klicka på knappen **Granska + skapa** .
1. Välj knappen **skapa** för att skapa en Live-händelse. Sedan återgår du till listan över direktsända händelser.
1. Välj länken till den Live-händelse som du skapade. Observera att din händelse har stoppats.
1. Se till att sidan är öppen i webbläsaren. Vi kommer tillbaka till den senare.

## <a name="set-up-a-live-stream-by-using-obs-studio"></a>Konfigurera en Live-ström med hjälp av ONLINEBANKSYSTEM Studio

ONLINEBANKSYSTEM börjar med en standard-scen men utan att några indata har valts.

   ![ONLINEBANKSYSTEM standard skärm](media/live-events-obs-quickstart/live-event-obs-default-screen.png)

### <a name="add-a-video-source"></a>Lägg till en video källa

1. Från panelen **källor** väljer du ikonen **Lägg till** för att välja en ny käll enhet. Menyn **källor** öppnas.

1. Välj **video avbildnings enhet** från menyn käll enhet. Menyn för att **skapa/välja källa** öppnas.

   ![ONLINEBANKSYSTEM källor-menyn med video enheten vald.](media/live-events-obs-quickstart/live-event-obs-video-device-menu.png)

1. Välj alternativ knappen **Lägg till befintlig** och välj sedan **OK**. Menyn **Egenskaper för video enhet** öppnas.

   ![ONLINEBANKSYSTEM ny video källa-meny med Lägg till befintlig vald.](media/live-events-obs-quickstart/live-event-obs-new-video-source.png)

1. I list rutan **enhet** väljer du de video ingångar som du vill använda för sändningen. Lämna resten av inställningarna separat för tillfället och välj **OK**. Indatakällan kommer att läggas till i panelen **källor** och vyn videoinspelning visas i **förhands gransknings** avsnittet.

   ![ONLINEBANKSYSTEM kamera inställningar](media/live-events-obs-quickstart/live-event-surface-camera.png)

### <a name="add-an-audio-source"></a>Lägg till en ljud källa

1. Från panelen **källor** väljer du ikonen **Lägg till** för att välja en ny käll enhet. Menyn käll enhet öppnas.

1. Välj **ljud** inspelnings insamling från menyn käll enhet. Menyn för att **skapa/välja källa** öppnas.

   ![Menyn ONLINEBANKSYSTEM-källor med ljud enheten vald.](media/live-events-obs-quickstart/live-event-obs-audio-device-menu.png)

1. Välj alternativ knappen **Lägg till befintlig** och välj sedan **OK**. Menyn **Egenskaper för inspelning av ljud** inspelning öppnas.

   ![ONLINEBANKSYSTEM ljud källa med Lägg till befintlig vald.](media/live-events-obs-quickstart/live-event-obs-new-audio-source.png)

1. I list rutan **enhet** väljer du den ljud inspelnings enhet som du vill använda för sändningen. Lämna resten av inställningarna separat för tillfället och välj OK. Ljud inspelnings enheten kommer att läggas till i panelen ljud mixning.

   ![List Rute lista för ONLINEBANKSYSTEM ljud enhets val](media/live-events-obs-quickstart/live-event-select-audio-device.png)

### <a name="set-up-streaming-and-advanced-encoding-settings-in-obs"></a>Konfigurera inställningar för strömning och avancerad kodning i ONLINEBANKSYSTEM

I nästa procedur går du tillbaka till Azure Media Services i webbläsaren för att kopiera inmatnings-URL: en för att komma in i inställningarna för utdata:

1. På sidan Azure Media Services i portalen väljer du **Starta** för att starta händelsen Live Stream. (Faktureringen startar nu.)

   ![Start ikon.](media/live-events-obs-quickstart/start.png)
1. Ange att **RTMP** ska växla till **rtmps**.
1. I rutan **inmatad URL** kopierar du webb adressen till Urklipp.

   ![Inmatad URL.](media/live-events-obs-quickstart/input-url.png)

1. Växla till ONLINEBANKSYSTEM-programmet.

1. Välj knappen **Inställningar** på panelen **kontroller** . Inställningarna är öppna.

   ![ONLINEBANKSYSTEM kontroll panelen med valda inställningar.](media/live-events-obs-quickstart/live-event-obs-settings.png)

1. Välj **Stream** på menyn **Inställningar** .

1. I list rutan **tjänst** väljer du Visa alla och väljer sedan **Anpassad...**.

1. I fältet **Server** klistrar du in den RTMP-URL som du kopierade till Urklipp.

1. Ange något i fältet **Stream Key** .  Det spelar ingen roll, men det måste ha ett värde.

    ![ONLINEBANKSYSTEM Stream-inställningar.](media/live-events-obs-quickstart/live-event-obs-stream-settings.png)

1. Välj **utdata** på menyn **Inställningar** .

1. Välj List rutan **utmatnings läge** överst på sidan och välj **Avancerat** för att få åtkomst till alla tillgängliga Encoder-inställningar.

1. Välj fliken **strömning** för att konfigurera kodaren.

1. Välj rätt kodare för systemet.  Om maskin varan stöder GPU-acceleration väljer du från NVIDIA **NVENC** h. 264 eller Intel **QuickSync** H. 264. Om systemet inte har en GPU som stöds väljer du alternativet **x264** Software Encoder.

#### <a name="x264-encoder-settings"></a>X264-inställningar för kodare

1. Om du har valt alternativet kodning av **x264** väljer du rutan **skala om utdata** . Välj antingen 1920x1080 om du använder en Premium Live-händelse i Media Services eller 1280x720 om du använder en vanlig (720P) Live-händelse.  Om du använder en direkt sändnings händelse kan du välja vilken tillgänglig lösning som helst.

1. Ange **bit hastigheten** till var som helst mellan 1500 kbps och 4000 kbit/s. Vi rekommenderar 2500 kbit/s om du använder en standard kodning av Live-händelser vid 720P. Om du använder ett 1080P Premium live-evenemang rekommenderas 4000 kbit/s. Du kanske vill justera bit hastigheten baserat på tillgängliga processor funktioner och bandbredd i nätverket för att uppnå önskad kvalitets inställning.

1. Ange *2* i fältet **intervall för nyckel** fält. Värdet anger nyckel bilds intervallet till 2 sekunder, vilket styr den slutliga storleken på de fragment som levereras över HLS eller tank streck från Media Services. Ange aldrig ett värde för nyckel intervallet som är högre än 4 sekunder.  Om du ser hög latens vid sändning bör du alltid markera eller informera dina program användare att alltid ange värdet till 2 sekunder. När du försöker uppnå en leverans med lägre latens kan du välja att ange det här värdet till så lågt som 1 sekund.

1. Valfritt: Ange för inställningen för CPU-användning till **veryfast** och kör några experiment för att se om din lokala processor kan hantera kombinationen av bit hastighet och för inställning med tillräckligt med ledigt utrymme. Försök att undvika inställningar som skulle resultera i en genomsnittlig CPU som är högre än 80% för att undvika eventuella problem under direkt uppspelning. För att förbättra kvaliteten kan du testa med **snabbare** och **snabba** förinställda inställningar tills du når dina CPU-begränsningar.

   ![Inställningar för ONLINEBANKSYSTEM x264-kodare](media/live-events-obs-quickstart/live-event-obs-x264-settings.png)

1. Lämna resten av inställningarna oförändrade och välj **OK**.

#### <a name="nvidia-nvenc-encoder-settings"></a>Inställningar för NVIDIA NVENC-kodare

1. Om du har valt alternativet **NVENC** GPU encoding, kontrollerar du rutan **skala om utdata** och väljer antingen 1920x1080 om du använder en Premium live-händelse i Media Services eller 1280x720 om du använder en standard (720p) Live-händelse. Om du använder en direkt sändnings händelse kan du välja vilken tillgänglig lösning som helst.

1. Ange **frekvens kontrollen** till CBR för kontroll av konstant bit hastighet.

1. Ange **bit hastigheten** mellan 1500 och 4000 kbit/s. Vi rekommenderar 2500 kbit/s om du använder en standard kodning av Live-händelser vid 720P. Om du använder ett 1080P Premium live-evenemang rekommenderas 4000 kbit/s. Du kan välja att justera detta utifrån tillgängliga processor funktioner och bandbredd i nätverket för att uppnå önskad kvalitets inställning.

1. Ange ett värde för **nyckel intervallet** till 2 sekunder som anges ovan under x264-alternativen. Överstiger inte 4 sekunder, eftersom detta kan påverka svars tiden för din Live-sändning avsevärt.

1. Ange för **inställningen** låg latens, Low-Latency prestanda eller Low-Latency kvalitet beroende på processorns hastighet på den lokala datorn. Experimentera med de här inställningarna för att uppnå bästa möjliga balans mellan kvalitet och processor användning på din egen maskin vara.

1. Ställ in **profilen** på "Main" eller "hög" om du använder en kraftfullare maskin varu konfiguration.

1. Lämna **sökningen** omarkerad. Om du har en mycket kraftfull dator kan du kontrol lera detta.

1. Lämna den **visuella justeringen Psycho** omarkerad. Om du har en mycket kraftfull dator kan du kontrol lera detta.

1. Ange **GPU** till 0 för att automatiskt avgöra vilka GPU: er som ska allokeras. Om du vill kan du begränsa GPU-användningen.

1. Ange **Max B-ramar** till 2

   ![ONLINEBANKSYSTEM NVidia NVidia NVENC GPU Encoder-inställningar.](media/live-events-obs-quickstart/live-event-obs-nvidia-settings.png)

#### <a name="intel-quicksync-encoder-settings"></a>Inställningar för Intel QuickSync-kodare

1. Om du har valt alternativet för GPU-kodning för Intel- **QuickSync** , markerar du kryss rutan **skala om utdata** och väljer antingen 1920x1080 om du använder en Premium live-händelse i Media Services eller 1280x720 om du använder en standard (720p) Live-händelse. Om du använder en direkt sändnings händelse kan du välja vilken tillgänglig lösning som helst.

1. Ange **mål användningen** som "bal anse rad" eller justera efter behov baserat på CPU och KOMBINERAd GPU-belastning. Justera efter behov och experimentera för att uppnå en 80% maximal processor användning i genomsnitt med den kvalitet som maskin varan kan producera. Om du använder mer begränsad maskin vara kan du testa med "fast" eller släppa till "mycket snabb" om du har prestanda problem.

1. Ställ in **profilen** på "Main" eller "hög" om du använder en kraftfullare maskin varu konfiguration.

1. Ange ett värde för **nyckel intervallet** till 2 sekunder som anges ovan under x264-alternativen. Överstiger inte 4 sekunder, eftersom detta kan påverka svars tiden för din Live-sändning avsevärt.

1. Ange **frekvens kontrollen** till CBR för kontroll av konstant bit hastighet.

1. Ange **bit hastigheten** var som helst mellan 1500 och 4000 kbit/s.  Vi rekommenderar 2500 kbit/s om du använder en standard kodning av Live-händelser vid 720P. Om du använder ett 1080P Premium live-evenemang rekommenderas 4000 kbit/s. Du kan välja att justera detta utifrån tillgängliga processor funktioner och bandbredd i nätverket för att uppnå önskad kvalitets inställning.

1. Ange **svars tiden** till "låg".

1. Ange **B-ramarna** till 2.

1. Lämna de **förbättrade video förbättringarna** avmarkerade.

   ![ONLINEBANKSYSTEM inställningar för Intel QuickSync GPU-kodare.](media/live-events-obs-quickstart/live-event-obs-intel-settings.png)

### <a name="set-audio-settings"></a>Ange ljud inställningar

I nästa steg ska du justera inställningarna för ljud kodning.

1. Välj fliken utdata->ljud i inställningar.

1. Ange **ljud bit hastighet** för spår 1 till 128 kbit/s.

   ![ONLINEBANKSYSTEM inställningar för ljud bit hastighet.](media/live-events-obs-quickstart/live-event-obs-audio-output-panel.png)

1. Välj fliken ljud i inställningar.

1. Ange **samplings frekvensen** till 44,1 kHz.

   ![Inställningar för ljud samplings frekvens i ONLINEBANKSYSTEM.](media/live-events-obs-quickstart/live-event-obs-audio-sample-rate-settings.png)

### <a name="start-streaming"></a>Starta direkt uppspelning

1. I panelen **kontroller** klickar du på **starta direkt uppspelning**.

    ![ONLINEBANKSYSTEM för att starta direkt uppspelning.](media/live-events-obs-quickstart/live-event-obs-start-streaming.png)

2. Växla till skärmen Azure Media Services Live-händelse i webbläsaren och klicka på länken **load The Player** . Nu bör du se din data ström i Preview Player.

## <a name="set-up-outputs"></a>Konfigurera utdata

Den här delen konfigurerar dina utdata och gör att du kan spara en inspelning av din Live Stream.  

> [!NOTE]
> För att du ska kunna strömma dessa utdata måste slut punkten för direkt uppspelning köras. Mer information finns i avsnittet senare [köra standard slut punkten för direkt uppspelning](#run-the-default-streaming-endpoint) .

1. Välj länken **skapa utdata** under **utdata** i video visnings programmet.
1. Om du vill kan du redigera namnet på utdata i rutan **namn** så att det blir mer användarvänligt, så det är enkelt att hitta dem senare.

   ![Utmatnings namn ruta.](media/live-events-wirecast-quickstart/output-name.png)
1. Lämna bara resten av rutorna för tillfället.
1. Välj **Nästa** för att lägga till en strömmande positionerare.
1. Ändra namnet på lokaliseraren till något mer användarvänligt, om du vill.

   ![Namn rutan för lokaliseraren.](media/live-events-wirecast-quickstart/live-event-locator.png)
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
