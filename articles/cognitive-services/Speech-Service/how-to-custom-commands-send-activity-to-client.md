---
title: Skicka Anpassade kommandon aktivitet till klientprogram
titleSuffix: Azure Cognitive Services
description: I den här artikeln får du lära dig hur du skickar aktivitet från ett Anpassade kommandon-program till ett klientprogram som kör Speech SDK.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 52e0b750f02044afafe233a76e4f43755d9ed303
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725106"
---
# <a name="send-custom-commands-activity-to-client-application"></a>Skicka Anpassade kommandon aktivitet till klientprogram

I den här artikeln får du lära dig hur du skickar aktivitet från ett Anpassade kommandon-program till ett klientprogram som kör Speech SDK.

Du utför följande uppgifter:

- Definiera och skicka en anpassad JSON-nyttolast från ditt Anpassade kommandon program
- Ta emot och visualisera det anpassade JSON-nyttolastinnehållet från ett C# UWP Speech SDK-klientprogram

## <a name="prerequisites"></a>Förutsättningar
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) eller senare. Den här guiden använder Visual Studio 2019
> * En Azure-prenumerationsnyckel för Speech-tjänsten: [Hämta en kostnadsfritt](overview.md#try-the-speech-service-for-free) eller skapa den i [Azure-portalen](https://portal.azure.com)
> * En tidigare [skapad app för Anpassade kommandon](quickstart-custom-commands-application.md)
> * En Speech SDK-aktiverad klientapp: [Så här integrerar du med ett klientprogram med hjälp av Speech SDK](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-send-activity-to-client"></a>Konfigurera skicka aktivitet till klient 
1. Öppna Anpassade kommandon som du skapade tidigare
1. Välj **turnOnOff-kommandot,** välj **ConfirmationResponse** under slutföranderegel och välj sedan Lägg **till en åtgärd**
1. Under **Ny åtgärdstyp väljer** du Skicka aktivitet till **klient**
1. Kopiera JSON nedan till **aktivitetsinnehåll**
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
1. Klicka **på** Spara för att skapa en ny regel med åtgärden Skicka **aktivitet, träna** och **publicera** ändringen

   > [!div class="mx-imgBorder"]
   > ![Regel för slutförande av skicka aktivitet](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>Integrera med klientprogram

I [How-to: Setup client application with Speech SDK (Preview) (Konfigurera](./how-to-custom-commands-setup-speech-sdk.md)klientprogram med Speech SDK (förhandsversion) skapade du ett UWP-klientprogram med Speech SDK som hanterade kommandon som `turn on the tv` , `turn off the fan` . När vissa visuella objekt har lagts till kan du se resultatet av dessa kommandon.

I Lägg till märkta rutor med text som anger **på** eller **av** lägger du till följande XML-block i StackPanel till `MainPage.xaml` .

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

### <a name="add-reference-libraries"></a>Lägga till referensbibliotek

Eftersom du har skapat en JSON-nyttolast måste du lägga till en [referens till JSON.NET](https://www.newtonsoft.com/json) för att hantera deserialisering.

1. Rätt klient för din lösning.
1. Välj **Hantera NuGet-paket för lösningen** och välj **Bläddra** 
1. Om du redanNewtonsoft.js **på** kontrollerar du att versionen är minst 12.0.3. Om inte går du till **Hantera NuGet-paket för lösning –** uppdateringar ochNewtonsoft.jspå **för** att uppdatera den. Den här guiden använder version 12.0.3.

    > [!div class="mx-imgBorder"]
    > ![Skicka aktivitetsnyttolast](media/custom-commands/send-activity-to-client-json-nuget.png)

1. Kontrollera också att NuGet-paketet **Microsoft.NETCore.UniversalWindowsPlatform** är minst 6.2.10. Den här guiden använder version 6.2.10.

I MainPage.xaml.cs lägger du till

```C#
using Newtonsoft.Json; 
using Windows.ApplicationModel.Core;
using Windows.UI.Core;
```

### <a name="handle-the-received-payload"></a>Hantera den mottagna nyttolasten

I `InitializeDialogServiceConnector` ersätter `ActivityReceived` du händelsehanteraren med följande kod. Den ändrade händelsehanteraren extraherar nyttolasten från aktiviteten och ändrar det `ActivityReceived` visuella tillståndet för tv:n respektive fläkten.

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
1. Det visuella tillståndet för tv:n bör ändras till "på"
   > [!div class="mx-imgBorder"]
   > ![Skärmbild som visar att det visuella tillståndet för T V nu är på.](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gör så här: Konfigurera webbslutpunkter](./how-to-custom-commands-setup-web-endpoints.md)
