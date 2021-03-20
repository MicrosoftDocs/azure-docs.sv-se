---
title: Skicka anpassade kommandon-aktivitet till klient program
titleSuffix: Azure Cognitive Services
description: 'I den här artikeln får du lära dig hur du skickar en aktivitet från ett program med anpassade kommandon till ett klient program som kör tal-SDK: n.'
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 52a4dbc4ff01515af8cd7d2503877184a09f7e64
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94566103"
---
# <a name="send-custom-commands-activity-to-client-application"></a>Skicka anpassade kommandon-aktivitet till klient program

I den här artikeln får du lära dig hur du skickar en aktivitet från ett program med anpassade kommandon till ett klient program som kör tal-SDK: n.

Du utför följande aktiviteter:

- Definiera och skicka en anpassad JSON-nyttolast från programmet för anpassade kommandon
- Ta emot och visualisera det anpassade JSON-nyttolasten från ett C# UWP-program för tal-SDK

## <a name="prerequisites"></a>Förutsättningar
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) eller senare. Den här guiden använder Visual Studio 2019
> * En Azure-prenumerationsnyckel för Speech-tjänsten: [Hämta en kostnadsfritt](overview.md#try-the-speech-service-for-free) eller skapa den i [Azure-portalen](https://portal.azure.com)
> * En tidigare [skapad app för Anpassade kommandon](quickstart-custom-commands-application.md)
> * En klient app för tal-SDK som är aktive rad: [så här integrerar du med ett klient program med hjälp av tal-SDK](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-send-activity-to-client"></a>Konfigurera skicka aktivitet till klient 
1. Öppna programmet anpassade kommandon som du skapade tidigare
1. Välj kommandot **TurnOnOff** , Välj **ConfirmationResponse** under regel för slut för ande och välj sedan **Lägg till en åtgärd**
1. Under **ny åtgärd-typ** väljer **du skicka aktivitet till klient**
1. Kopiera JSON nedan till **aktivitets innehåll**
   ```json
   {
      "type": "event",
      "name": "UpdateDeviceState",
      "value": {
        "state": "{OnOff}",
        "device": "{SubjectDevice}"
      }
    }
   ```
1. Klicka på **Spara** för att skapa en ny regel med åtgärden skicka aktivitet, **träna** och **publicera** ändringen

   > [!div class="mx-imgBorder"]
   > ![Skicka regel för slut för ande av aktivitet](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>Integrera med klientprogram

I [anvisningar: Konfigurera klient program med Speech SDK (för hands version)](./how-to-custom-commands-setup-speech-sdk.md)har du skapat ett UWP-klientprogram med tal-SDK som hanterade kommandon som `turn on the tv` , `turn off the fan` . Med vissa visuella objekt tillagda kan du se resultatet av dessa kommandon.

Om du vill lägga till etiketterade rutor med text som visar **på** eller **av**, lägger du till följande XML-block med StackPanel i `MainPage.xaml` .

```xml
<StackPanel Orientation="Vertical" H......>
......
</StackPanel>
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
<MediaElement ....../>
```

### <a name="add-reference-libraries"></a>Lägg till referens bibliotek

Eftersom du har skapat en JSON-nyttolast måste du lägga till en referens i [JSON.net](https://www.newtonsoft.com/json) -biblioteket för att hantera deserialisering.

1. Rätt-klient din lösning.
1. Välj **Hantera NuGet-paket för lösningen**, Välj **Bläddra** 
1. Om du redan har installerat **Newtonsoft.jspå** kontrollerar du att dess version är minst 12.0.3. Om inte går du till **Hantera NuGet-paket för lösning – uppdateringar**, sök efter **Newtonsoft.jspå** för att uppdatera den. Den här guiden använder version 12.0.3.

    > [!div class="mx-imgBorder"]
    > ![Skicka aktivitetens nytto Last](media/custom-commands/send-activity-to-client-json-nuget.png)

1. Kontrol lera också att NuGet-paketet **Microsoft. NetCore. UniversalWindowsPlatform** är minst 6.2.10. Den här guiden använder version 6.2.10.

I MainPage. XAML. CS, lägger du till

```C#
using Newtonsoft.Json; 
using Windows.ApplicationModel.Core;
using Windows.UI.Core;
```

### <a name="handle-the-received-payload"></a>Hantera mottagna nytto Last

I `InitializeDialogServiceConnector` ersätter du `ActivityReceived` händelse hanteraren med följande kod. Den modifierade `ActivityReceived` händelse hanteraren kommer att extrahera nytto lasten från aktiviteten och ändra TV-eller fläktens visuella status.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.value?.state != null ? activity.value.state.ToString() : string.Empty;
        var device = activity?.value?.device != null ? activity.value.device.ToString() : string.Empty;

        if (state.Equals("on") || state.Equals("off"))
        {
            switch (device)
            {
                case "tv":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_TV.Text = state; });
                    break;
                case "fan":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_Fan.Text = state; });
                    break;
                default:
                    NotifyUser($"Received request to set unsupported device {device} to {state}");
                    break;
            }
        }
        else { 
            NotifyUser($"Received request to set unsupported state {state}");
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Prova nu

1. Starta programmet
1. Välj Aktivera mikrofon
1. Välj knappen Prata
1. Säg `turn on the tv`
1. TV-apparatens visuella tillstånd ska ändras till "på"
   > [!div class="mx-imgBorder"]
   > ![Skärm bild som visar att det visuella läget för T V är nu på.](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gör så här: konfigurera webb slut punkter (förhands granskning)](./how-to-custom-commands-setup-web-endpoints.md)
