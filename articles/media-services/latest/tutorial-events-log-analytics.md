---
title: Lagra Media Services händelser i Azure Log Analytics
description: Lär dig hur du lagrar Azure Media Services händelser i Azure Log Analytics.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: inhenkel
ms.openlocfilehash: 8e3b9636dfb900fa0e22bfea8bcd2d0c7edf0241
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596713"
---
# <a name="tutorial-store-azure-media-services-events-in-azure-log-analytics"></a>Självstudie: lagra Azure Media Services händelser i Azure Log Analytics

## <a name="azure-media-services-events"></a>Azure Media Services händelser

Azure Media Services v3 genererar händelser på [Azure Event Grid](monitoring/media-services-event-schemas.md). Du kan prenumerera på händelser på många sätt och lagra dem i data lager. I den här självstudien kommer du att prenumerera på Media Services händelser med hjälp av ett [logg program flöde](https://azure.microsoft.com/services/logic-apps/). Logic app utlöses för varje händelse och lagrar händelsens innehåll i Azure Log Analytics. När händelserna finns i Azure Log Analytics kan du använda andra Azure-tjänster för att skapa en instrument panel, övervakare och avisering för dessa händelser, men vi kommer inte att se den i den här självstudien.

> [!NOTE]
> Det skulle vara bra om du redan är bekant med att använda FFmpeg som din lokala kodare.  Annars är det OK. Kommando raden och anvisningarna för att strömma en video finns nedan.

Du lär dig hur du:

> [!div class="checklist"]
> * Skapa ett program flöde utan kod logik
> * Prenumerera på Azure Media Services händelse ämnen
> * Analysera händelser och lagra till Azure Log Analytics
> * Fråga händelser från Azure Log Analytics

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

> * En [Azure-prenumeration](how-to-set-azure-subscription.md)
> * Ett [Media Services](create-account-howto.md) konto och en resurs grupp.
> * En installation av [ffmpeg](https://ffmpeg.org/download.html) för ditt operativ system.
> * En [Log Analytics](../../azure-monitor/logs/quick-create-workspace.md) arbets yta

## <a name="subscribe-to-a-media-services-event-with-logic-app"></a>Prenumerera på en Media Services-händelse med Logic app

1. I Azure Portal, om du inte redan har gjort det, skapar du en [Log Analytics](../../azure-monitor/logs/quick-create-workspace.md) arbets yta. Du behöver arbetsyte-ID: t och en av nycklarna, så se till att webbläsarfönstret är öppet. Öppna sedan portalen på en annan flik eller i ett fönster.

1. Navigera till ditt Azure Media Services konto och välj **händelser**. Då visas alla metoder för att prenumerera på Azure Media Services händelser.
    > [!div class="mx-imgBorder"]
    > ![Azure Media Services Portal](media/tutorial-events-log-analytics/select-events-01a.png)

1. Välj **Logic Apps ikonen** för att skapa en logisk app. Nu öppnas Logic Apps designer där du kan skapa flödet för att avbilda händelserna och skicka dem till Log Analytics. 
    > [!div class="mx-imgBorder"]
    > ![Skapa Logic app](media/tutorial-events-log-analytics/select-logic-app-02.png)

1. Välj **ikonen +**, Välj den klient som du vill använda och välj sedan logga in. En Microsoft-inloggnings tolk visas.
    > [!div class="mx-imgBorder"]
    > ![Anslut till Azure Event Grid ](media/tutorial-events-log-analytics/select-event-add-grid-03.png)
 ![ Välj klienten](media/tutorial-events-log-analytics/select-tenant-03a.png)

1. Välj **Fortsätt** om du vill prenumerera på Media Services händelser.
    > [!div class="mx-imgBorder"]
    > ![Ansluten till Azure Event Grid](media/tutorial-events-log-analytics/select-continue-04.png)

1. Leta upp "Microsoft. Media. Media Services" i listan **resurs typ** .
    > [!div class="mx-imgBorder"]
    >![Azure Media Services resurs händelser](media/tutorial-events-log-analytics/locate-azure-media-services-events-05.png)

1. Välj **objektet händelse typ**. Det kommer att finnas en lista över alla händelser Azure Media Servicess. Du kan välja de händelser som du vill spåra. Du kan lägga till flera händelse typer. (Senare kan du göra en liten ändring i Logic app-flödet för att lagra varje händelse typ i en separat Log Analytics logg och sprida händelse typ namnet till Log Analytics-loggnamn dynamiskt.)
    > [!div class="mx-imgBorder"]
    > ![Händelse typ för Azure Media Services](media/tutorial-events-log-analytics/select-azure-media-services-event-type-06.png)

1. Välj **Spara som**.

1. Ge din Logic app ett namn.  Resurs gruppen är markerad som standard. Lämna de andra inställningarna på samma sätt som de är och välj sedan **skapa**.  Du kommer tillbaka till Azures start sida.
    > [!div class="mx-imgBorder"]
    > ![Namngivnings gränssnitt för Logic app](media/tutorial-events-log-analytics/give-logic-app-name-06a.png)

## <a name="create-an-action"></a>Skapa en åtgärd

Nu när du prenumererar på händelse (r) skapar du en åtgärd.

1. Om portalen har gått tillbaka till Start skärmen går du tillbaka till den Logic-app som du nyss skapade genom att söka i alla resurser efter appens namn.

1. Välj din app och välj sedan **Logic App Designer**. Design fönstret öppnas.

1. Välj **+ nytt steg**.

1. Eftersom du vill skicka händelserna till Azure Log Analytics-tjänsten söker du efter "Azure Log Analytics" och väljer "Azure Log Analytics data Collector".
    > [!div class="mx-imgBorder"]
    > ![Datainsamlare för Azure Log Analytics](media/tutorial-events-log-analytics/select-azure-log-analytics-data-collector-07.png)

1. Om du vill ansluta till arbets ytan Log Analytics behöver du arbets ytans ID och en agent nyckel. Öppna Azure Portal på en ny flik eller i ett nytt fönster, navigera till arbets ytan Log Analytics som du skapade innan du börjar den här självstudien.
    > [!div class="mx-imgBorder"]
    > ![ID för Azure Log Analytics-arbetsytan](media/tutorial-events-log-analytics/log-analytics-workspace-id-08.png)

1. Leta upp **agent hantering** på den vänstra menyn och välj den. Då visas de agent nycklar som har genererats.
    > [!div class="mx-imgBorder"]
    > ![Hantering av Azure Log Analytics-agenter](media/tutorial-events-log-analytics/select-agents-management-09.png)

1. Kopiera *arbetsyte-ID: t*.
    > [!div class="mx-imgBorder"]
    > ![Kopiera arbetsyte-ID](media/tutorial-events-log-analytics/copy-workspace-id.png)

1. I den andra webbläsare-fliken eller-fönstret under Azure Log Analytics data Collector väljer du **skicka data**, ge din anslutning ett namn och klistrar in *arbetsyte-ID* : t i fältet **arbetsyte-ID** .

1. Gå tillbaka till fliken Arbetsyteläsare eller fönstret och kopiera *arbets ytans nyckel*.
    > [!div class="mx-imgBorder"]
    > ![Agent hantering primär nyckel](media/tutorial-events-log-analytics/agents-management-primary-key-10.png)

1. I den andra webbläsarens flik eller fönster klistrar du in *arbetsyte nyckeln* i nyckel fältet för **arbets ytan** .

1. Välj **Skapa**. Nu ska du skapa JSON-frågans brödtext och det anpassade logg namnet.

1. Välj fältet **JSON-begär ande brödtext** .  En länk för att **lägga till dynamiskt innehåll** visas.

1. Välj **Lägg till dynamiskt innehåll** och välj sedan **ämne**.

1. Gör samma sak för **anpassat logg namn**.
    > [!div class="mx-imgBorder"]
    > ![Ämne valt](media/tutorial-events-log-analytics/topic-selected.png)

1. Välj **kodvyn** för Logic app. Leta efter indata och Log-Type rader.
    > [!div class="mx-imgBorder"]
    > ![Kodvyn för två rader](media/tutorial-events-log-analytics/code-view-two-lines.png)

1. Ändra `body` värdet från `"@triggerBody()?['topic']"` till `"@{triggerBody()}"` . Detta används för att parsa hela meddelandet för att Log Analytics.

1. Ändra `Log-Type` från `"@triggerBody()?['topic']"` till `"@replace(triggerBody()?['eventType'],'.','')"` . (Detta ersätter "." eftersom dessa inte är tillåtna i Log Analytics logg namn.)
    > [!div class="mx-imgBorder"]
    > ![Logic app-JSON efter ändring](media/tutorial-events-log-analytics/changed-lines.png)

1. Välj **Spara**.

1. Om du vill verifiera väljer du **Logic Apps designer**.
    > [!div class="mx-imgBorder"]
    > ![Verifiera bröd text-och funktions steg](media/tutorial-events-log-analytics/verify-changes-to-json.png)

1. När du granskar alla resurser i resurs gruppen kommer det att finnas en Logic app och två Logic app API-kopplingar i listan, en för händelserna och en för Log Analytics. Mer information om Event Grid Systems ämnen finns i [avsnittet Event Grid system](../../event-grid/system-topics.md).
    > [!div class="mx-imgBorder"]
    > ![Visa alla nya resurser i resurs gruppen](media/tutorial-events-log-analytics/contoso-rg-listing.png)

## <a name="test"></a>Testa

Du kan testa hur det fungerar genom att skapa en direkt sändnings händelse i Azure Media Services. Skapa en RTMP Live-händelse och Använd ffmpeg för att skicka en "Live"-ström baserat på en. mp4-exempel fil. Hämta RTMP-inmatnings-URL: en när händelsen har skapats.

1. Välj **Live streaming** från ditt Media Services-konto.
    > [!div class="mx-imgBorder"]
    > ![Skapa en Azure Media Services Live-händelse](media/tutorial-events-log-analytics/live-event.png)

1. Välj **Lägg till Live-händelse**.

1. Ange ett namn i fältet **Live-händelseloggen** . (Fältet **Beskrivning** är valfritt.)

1. Välj **standard** moln kodning.

1. Välj **standard-720p** för kodnings för inställningen.

1. Välj **RTMP** -indataports protokoll.

1. Välj **Ja** för den permanenta ingångs-URL: en.

1. Välj **Ja** om du vill starta händelsen när händelsen skapas.

    > [!WARNING]
    > Faktureringen startar om du väljer Ja.  Om du vill vänta med att starta strömmen tills du är först *innan* du börjar strömma med ffmpeg väljer du **Nej** och kom ihåg att starta Live-händelsen och sedan.

    > [!div class="mx-imgBorder"]
    > ![Inställningar för Live-händelse](media/tutorial-events-log-analytics/live-event-settings.png)

1. Välj **Jag har alla rättigheter för att använda kryss rutan innehåll/fil...** .

1. Välj **Granska + skapa**.

1. Granska inställningarna och välj sedan **skapa**.  Live Event-listan visas och Live Events-URL: en visas.

1. Kopiera inmatnings- **URL:** en till Urklipp.

1. Välj **Live-händelsen** i listan för att se Producer-vyn.

### <a name="stream-with-ffmpeg-cli"></a>Stream med FFmpeg CLI

1. Använd följande kommando rad.

    ```AzureCLI
    ffmpeg -i <localpathtovideo> -map 0 -c:v libx264 -c:a copy -f flv <ingestURL>/mystream
    ```

1. Ändra till den inmatnings- `<ingestURL>` URL som du kopierade till Urklipp.
1. Ändra `<localpathtovideo>` till den lokala sökvägen till filen som du vill strömma från ffmpeg.
1. Lägg till ett unikt namn i slutet, till exempel `mystream` .
1. Justera kommando raden så att den återspeglar test käll filen och andra systemvariabler.
1. Kör kommandot. Efter ett par sekunder ska spelaren starta direkt uppspelningen. (Uppdatera spelaren om videon inte visas automatiskt.)

    > [!div class="mx-imgBorder"]
    > ![Verifiera korrekt video inmatning i Producer-spelare](media/tutorial-events-log-analytics/live-event-producer-view.png)

## <a name="verify-the-events"></a>Verifiera händelserna

Med Live Stream avger Azure Media Services flera händelser som utlöser Logic app-flödet. Kontrol lera genom att navigera till Logic-appen och avgöra om det finns några utlösare som utlöses av händelserna från Media Services.

1. Gå till översikts sidan för Logic app. du bör se jobben "kör historik" som har slutförts.
    > [!div class="mx-imgBorder"]
    > ![Verifiera lyckad jobb körning i Logic app](media/tutorial-events-log-analytics/run-history.png)

1. Välj ett lyckat jobb. Information om jobbet under körningen visas.
1. Välj **skicka data** för att expandera den. I det här fallet `MicrosoftMediaLiveEventEncoderConnected` visar händelsen att den har samlats in samt den parsade texten. Det här är vad som skickas till Azure Log Analytics-arbetsytan.
    > [!div class="mx-imgBorder"]
    > ![Se skicka data](media/tutorial-events-log-analytics/verify-send-data.png)

## <a name="verify-the-logs"></a>Verifiera loggarna

1. Navigera till Log Analytics arbets yta som du skapade tidigare.

1. Välj **loggar**.
1. Stäng popup-exempel frågor.
1. Det kommer att finnas en lista med anpassade loggar. Välj nedpilen för att expandera den. Där visas händelse namnet `MicrosoftMediaLiveEventEncoderConnected` .
1. Välj händelse namnet för att expandera det.
1. När du väljer ikonen "ögon" visas en förhands granskning av frågeresultatet.
1. Välj **se i Frågeredigeraren** och markera objektet under **TimeGenerated UTC** list för att expandera det och Visa rå data.

![Se detaljerad händelse utdata i Log Analytics](media/tutorial-events-log-analytics/raw-data.png)

## <a name="delete-resources"></a>Ta bort resurser

Om du inte vill fortsätta att använda de resurser som du skapade under den här självstudien kontrollerar du att du tar bort alla resurser i resurs gruppen eller att du kommer att fortsätta att debiteras.

## <a name="next-steps"></a>Nästa steg

Du kan skapa olika frågor och spara dem. Dessa kan läggas till på [Azure-instrumentpanelen](../../azure-monitor/visualize/tutorial-logs-dashboards.md).