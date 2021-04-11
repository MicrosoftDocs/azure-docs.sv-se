---
title: Skapa en Azure Media Services Live Stream
description: Lär dig hur du skapar en Azure Media Services Live Stream med hjälp av portalen och Wirecast
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 08/31/2020
ms.openlocfilehash: de3b3f4b9e871ab95e60f4bb2393e86770069544
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106123655"
---
# <a name="create-an-azure-media-services-live-stream"></a>Skapa en Azure Media Services Live Stream

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här snabb starten hjälper dig att skapa en Azure Media Services Live Stream med hjälp av Azure Portal-och Wirecast. Det förutsätter att du har en Azure-prenumeration och har skapat ett Media Services-konto.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Öppna webbläsaren och gå till [Microsoft Azure-portalen](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.

I den här snabb starten tar vi upp:

- Konfigurera en lokal kodare med en kostnads fri utvärderings version av multistream-Wirecast.
- Konfigurera en Live-dataström.
- Konfigurera real tids data Ströms utdata.
- Kör en standard slut punkt för direkt uppspelning.
- Använd Azure Media Player för att Visa Live Stream och utdata på begäran.

För att det ska vara enkelt, använder vi en kodning för att Azure Media Services i Wirecast, genom strömnings moln kodning och RTMP.

## <a name="set-up-an-on-premises-encoder-by-using-wirecast"></a>Konfigurera en lokal kodare med hjälp av Wirecast

1. Ladda ned och installera Wirecast för ditt operativ system på den [fjärrströms webbplatsen](https://www.telestream.net).
1. Starta programmet och Använd din favorit-e-postadress för att registrera produkten. Se till att programmet är öppet.
1. Verifiera din e-postadress i e-postmeddelandet som du får. Sedan startar programmet den kostnads fria utvärderings versionen.
1. Rekommenderat: titta på video självstudien på skärmen öppna program.

## <a name="set-up-an-azure-media-services-live-stream"></a>Konfigurera en Azure Media Services Live Stream

1. Gå till Azure Media Services kontot i portalen och välj sedan **Live streaming** från **Media Services** listan.

   ![Länk till Live Streaming](media/live-events-wirecast-quickstart/select-live-streaming.png)
1. Välj **Lägg till Live-händelse** för att skapa en ny direkt uppspelnings händelse.

   ![Lägg till ikonen Live-händelse](media/live-events-wirecast-quickstart/add-live-event.png)
1. Ange ett namn för den nya händelsen, till exempel *TestLiveEvent*, i rutan **Live-händelseloggen** .

   ![Rutan Live-händelse namn](media/live-events-wirecast-quickstart/live-event-name.png)
1. Ange en valfri beskrivning av händelsen i rutan **Beskrivning** .
1. Välj alternativet genom **strömning – ingen moln kodning** .

   ![Alternativ för moln kodning](media/live-events-wirecast-quickstart/cloud-encoding.png)
1. Välj alternativet **RTMP** .
1. Se till att alternativet **Nej** är markerat för att **starta direkt sändningen** för att undvika att faktureras för direkt sändningen innan det är klart. (Faktureringen påbörjas när Live-händelsen startas.)

   ![Alternativet Starta Live-händelse](media/live-events-wirecast-quickstart/start-live-event-no.png)
1. Granska inställningarna genom att klicka på knappen **Granska + skapa** .
1. Välj knappen **skapa** för att skapa en Live-händelse. Sedan återgår du till listan över direktsända händelser.
1. Välj länken till den Live-händelse som du nyss skapade. Observera att din händelse har stoppats.
1. Se till att sidan är öppen i webbläsaren. Vi kommer tillbaka till den senare.

## <a name="set-up-a-live-stream-by-using-wirecast-studio"></a>Konfigurera en Live-ström med hjälp av Wirecast Studio

1. I Wirecast-programmet väljer du **Skapa tomt dokument** från huvud menyn och väljer sedan **Fortsätt**.

   ![Wirecast Start skärm](media/live-events-wirecast-quickstart/open-empty-document.png)
1. Hovra över det första lagret i **Wirecast lager** -ytan.  Välj ikonen **Lägg till** som visas och välj den video indata som du vill strömma.

   ![Wirecast Lägg till ikon](media/live-events-wirecast-quickstart/add-icon.png)

   Dialog rutan **Master Layer 1** öppnas.
1. Välj **video insamling** på menyn och välj sedan den kamera som du vill använda.

   ![Förhands gransknings yta för videoinspelning](media/live-events-wirecast-quickstart/video-shot-selection.png)

   Vyn från kameran visas i förhands gransknings avsnittet.
1. Hovra över det andra lagret i **Wirecast lager** -ytan. Välj ikonen **Lägg till** som visas och välj den ljud inspelning som du vill strömma. Dialog rutan **Master Layer 2** öppnas.
1. Välj **ljud inspelning** på menyn och välj sedan den ljud inspelning som du vill använda.

   ![Indata för ljud inspelning](media/live-events-wirecast-quickstart/audio-shot-select.png)
1. Från huvud menyn väljer du **Inställningar för utdata**. Dialog rutan **Välj ett mål för utgående utdata** visas.
1. Välj **Azure Media Services** i list rutan **mål** . Inställningen utdata för Azure Media Services fyller automatiskt i *de flesta* av inställningarna för utdata.

   ![Skärmen Wirecast Output Settings](media/live-events-wirecast-quickstart/azure-media-services.png)


I nästa procedur går du tillbaka till Azure Media Services i webbläsaren för att kopiera inmatnings-URL: en för att komma in i inställningarna för utdata:

1. På sidan Azure Media Services i portalen väljer du **Starta** för att starta händelsen Live Stream. (Faktureringen startar nu.)

   ![Start ikon](media/live-events-wirecast-quickstart/start.png)
2. Ange säker **/** osäker växling **till osäkert.** I det här steget anges protokollet till RTMP i stället för RTMP.
3. I rutan **inmatad URL** kopierar du webb adressen till Urklipp.

   ![Inmatad URL](media/live-events-wirecast-quickstart/input-url.png)
4. Växla till Wirecast **-** programmet och klistra in **URL: en för indata** i rutan adress i inställningarna för utdata.

   ![Wirecast-ingångs-URL](media/live-events-wirecast-quickstart/input-url-wirecast.png)
5. Välj **OK**.

## <a name="set-up-outputs"></a>Konfigurera utdata

Den här delen konfigurerar dina utdata och gör att du kan spara en inspelning av din Live Stream.  

> [!NOTE]
> För att du ska kunna strömma dessa utdata måste slut punkten för direkt uppspelning köras. Mer information finns i avsnittet senare [köra standard slut punkten för direkt uppspelning](#run-the-default-streaming-endpoint) .

1. Välj länken **skapa utdata** under **utdata** i video visnings programmet.
1. Om du vill kan du redigera namnet på utdata i rutan **namn** om du vill ha något mer användarvänligt, så att det är enkelt att hitta senare.
   
   ![Utmatnings namn ruta](media/live-events-wirecast-quickstart/output-name.png)
1. Lämna bara resten av rutorna för tillfället.
1. Välj **Nästa** för att lägga till en strömmande positionerare.
1. Ändra namnet på lokaliseraren till något mer användarvänligt, om du vill.
   
   ![Namn Box för lokaliserare](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Lämna allt annat på den här skärmen separat för tillfället.
1. Välj **Skapa**.

## <a name="start-the-broadcast"></a>Starta sändningen

1. I Wirecast väljer du **utdata**  >  **Starta/stoppa sändning**  >  **Start Azure Media Services: Azure Media Services** från huvud menyn.

   ![Starta meny alternativ för sändning](media/live-events-wirecast-quickstart/start-broadcast.png)

   När strömmen har skickats till direkt sändningen visas **Live** -fönstret i Wirecast i Videos pelaren på sidan live event i Azure Media Services.

1. Välj knappen **gå** under förhands gransknings fönstret för att starta sändningen av video och ljud som du har valt för Wirecast-skiktet.

   ![Wirecast go-knapp](media/live-events-wirecast-quickstart/go-button.png)

   > [!TIP]
   > Om det uppstår ett fel kan du prova att läsa in spelaren igen genom att välja länken **load Player** ovanför spelaren.

## <a name="run-the-default-streaming-endpoint"></a>Kör standard slut punkten för direkt uppspelning

1. Välj **strömnings slut punkter** i Media Services listan.

   ![Meny alternativ för strömnings slut punkter](media/live-events-wirecast-quickstart/streaming-endpoints.png)
1. Om statusen för direkt uppspelnings slut punkten stoppas väljer du den. Det här steget tar dig till sidan för slut punkten.
1. Välj **Starta**.
   
   ![Start-knappen för slut punkten för direkt uppspelning](media/live-events-wirecast-quickstart/start.png)

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>Spela upp utdata-sändningen med hjälp av Azure Media Player

1. Kopiera den strömmande URL: en under **utdata** Videos pelaren.
1. Öppna [Azure Media Player demonstration](https://ampdemo.azureedge.net/azuremediaplayer.html)i en webbläsare.
1. Klistra in URL-adressen för strömning i rutan **URL** för Azure Media Player.
1. Välj knappen **Uppdatera spelare** .
1. Klicka på ikonen **spela upp** på videon om du vill se din real tids ström.

## <a name="stop-the-broadcast"></a>Stoppa sändningen

När du tror att du har strömmat tillräckligt med innehåll, stoppa sändningen.

1. I Wirecast väljer du **sändnings** knappen. Det här steget avbryter sändningen från Wirecast.
1. I portalen väljer du **stoppa**. Sedan får du ett varnings meddelande om att den aktiva strömmen kommer att stoppas, men utdata blir nu en till gång på begäran.
1. Välj **stoppa** i varnings meddelandet. Azure Media Player visar nu ett fel, eftersom direkt strömmen inte längre är tillgänglig.

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
> [Live-händelser och Live-utdata i Media Services](./live-event-outputs-concept.md)
