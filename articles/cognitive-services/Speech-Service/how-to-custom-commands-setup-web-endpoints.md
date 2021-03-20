---
title: Konfigurera webbslutpunkter (förhandsversion)
titleSuffix: Azure Cognitive Services
description: konfigurera webbslutpunkter för anpassade kommandon
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 6f2dfdbb5833b34441b4abba7359ad70c4717d1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98602163"
---
# <a name="set-up-web-endpoints"></a>Konfigurera webbslutpunkter

I den här artikeln får du lära dig att ställa in webbslutpunkter i programmet Anpassade kommandon, där du kan göra HTTP-begäranden från ett klientprogram. Du kommer att utföra följande uppgifter:

- Konfigurera webbslutpunkter i programmet Anpassade kommandon
- Anropa webbslutpunkter i programmet Anpassade kommandon
- Ta emot webbslutpunkternas svar
- Integrera webbslutpunkternas svar i en anpassad JSON-nyttolast, samt skicka och visualisera det från ett Speech SDK-klientprogram för C# UWP

## <a name="prerequisites"></a>Förutsättningar
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * En Azure-prenumerationsnyckel för Speech-tjänsten: [Hämta en kostnadsfritt](overview.md#try-the-speech-service-for-free) eller skapa den i [Azure-portalen](https://portal.azure.com)
> * En tidigare [skapad app för Anpassade kommandon](quickstart-custom-commands-application.md)
> * En Speech SDK-aktiverad klientapp: [Instruktion: Avsluta aktivitet i klientprogram](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-web-endpoints"></a>Konfigurera webbslutpunkter

1. Öppna programmet Anpassade kommandon som du skapade tidigare.
1. Gå till ”Webbslutpunkter” och klicka på ”Ny webbslutpunkt”.

   > [!div class="mx-imgBorder"]
   > ![Ny webbslutpunkt](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Inställning | Föreslaget värde | Beskrivning |
   | ------- | --------------- | ----------- |
   | Name | UpdateDeviceState | Namnet på webbslutpunkten. |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | URL-adressen till den slutpunkt som du vill att din anpassade kommandoapp ska kommunicera med. |
   | Metod | POST | Tillåtna interaktioner (till exempel GET, POST) med din slutpunkt.|
   | Sidhuvuden | Nyckel: app, Värde: använd de första 8 siffrorna i ditt applicationId | De rubrikparametrar som ska ingå i begärandets rubrik.|

    > [!NOTE]
    > - Exemplet på webbslutpunkten som skapades med [Azure Function](../../azure-functions/index.yml), som kopplas ihop med databasen som sparar enhetens tillstånd för tv och fläkt
    > - Den föreslagna rubriken behövs bara i slutpunktsexemplet
    > - För att värdet för rubriken ska vara unikt i slutpunktsexemplet, använder du de första 8 siffrorna i ditt applicationId
    > - I verkligheten kan webbslutpunkten vara slutpunkten för [IoT-hubben](../../iot-hub/about-iot-hub.md) som hanterar dina enheter

1. Klicka på **Spara**.

## <a name="call-web-endpoints"></a>Anropa webbslutpunkter

1. Gå till kommandot **TurnOnOff**, välj **ConfirmationResponse** under slutföranderegeln och välj sedan **Lägg till en åtgärd**.
1. Under **Ny åtgärdstyp** väljer du **Anropa webbslutpunkt**
1. I **Redigera åtgärd – Slutpunkter** väljer du **UpdateDeviceState**, som är webbslutpunkten vi skapade nyss.  
1. I **Konfiguration** anger du följande värden:
   > [!div class="mx-imgBorder"]
   > ![Anropa åtgärdsparametrar för webbslutpunkter](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | Inställning | Föreslaget värde | Beskrivning |
   | ------- | --------------- | ----------- |
   | Slutpunkter | UpdateDeviceState | Den webbslutpunkt som du vill anropa med åtgärden. |
   | Frågeparametrar | item={SubjectDevice}&&value={OnOff} | Frågeparametrarna som ska läggas till i webbslutpunktens URL.  |
   | Brödtextinnehåll | E.t. | Brödtextinnehållet i begärandet. |

    > [!NOTE]
    > - De föreslagna frågeparametrarna behövs bara i slutpunktsexemplet

1. I **Vid lyckad åtgärd – Åtgärd att utföra** väljer du **Skicka talsvar**.

    I **Enkelt redigeringsprogram** anger du `{SubjectDevice} is {OnOff}`.

   > [!div class="mx-imgBorder"]
   > ![Skärm bild som visar skärmen vid lyckad åtgärd för att köra skärmen.](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | Inställning | Föreslaget värde | Beskrivning |
   | ------- | --------------- | ----------- |
   | Åtgärd att utföra | Skicka talsvar | Åtgärd att utföra om begäran till webbslutpunkten lyckas |

   > [!NOTE]
   > - Du kan också få direkt åtkomst till fälten i http-svaret med hjälp av `{YourWebEndpointName.FieldName}`. Exempelvis: `{UpdateDeviceState.TV}`

1. I **Vid fel – Åtgärd att utföra** väljer du **Skicka talsvar**

    I **Enkelt redigeringsprogram** anger du `Sorry, {WebEndpointErrorMessage}`.

   > [!div class="mx-imgBorder"]
   > ![Anropa webbslutpunkter vid fel](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | Inställning | Föreslaget värde | Beskrivning |
   | ------- | --------------- | ----------- |
   | Åtgärd att utföra | Skicka talsvar | Åtgärd att utföra om begäran till webbslutpunkten misslyckas |

   > [!NOTE]
   > - `{WebEndpointErrorMessage}` är valfritt. Du kan ta bort den om du inte vill visa något felmeddelande.
   > - I vårt slutpunktsexempel skickar vi tillbaka http-svar med detaljerade felmeddelanden om vanliga fel som t.ex. saknade rubrikparametrar.

### <a name="try-it-out-in-test-portal"></a>Testa i testportalen
- Svaret Vid lyckad åtgärd\
Spara, träna och testa
   > [!div class="mx-imgBorder"]
   > ![Skärm bild som visar svaret på lyckad åtgärd.](media/custom-commands/setup-web-endpoint-on-success-response.png)
- Svaret Vid fel\
Ta bort någon av frågeparametrarna, spara, träna om och testa
   > [!div class="mx-imgBorder"]
   > ![Anropa webbslutpunkter vid lyckad åtgärd](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integrera med klientprogram

I [Instruktioner: Skicka aktivitet till klientprogram (förhandsversion)](./how-to-custom-commands-send-activity-to-client.md), lade du till åtgärden **Skicka aktivitet till klient**. Aktiviteten skickas till klientprogrammet oavsett om åtgärden **Anropa webbslutpunkt** har slutförts eller inte.
I de flesta fall brukar man dock vanligtvis enbart vilja skicka aktiviteten till klientprogrammet om anropet till webbslutpunkten lyckas. I det här exemplet sker det när enhetens status har uppdaterats.

1. Ta bort åtgärden **Skicka aktivitet till klient** som du tidigare lade till.
1. Redigera anrop till webbslutpunkt:
    1. I **Konfiguration** kontrollerar du att **Frågeparametrar** är `item={SubjectDevice}&&value={OnOff}`
    1. I **Vid lyckad åtgärd** ändrar du **Åtgärd att utföra** till **Skicka aktivitet till klient**
    1. Kopiera JSON-filen nedan till **Aktivitetsinnehåll**
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
Nu ska du enbart skicka aktiviteten till klienten när begärandet till webbslutpunkten lyckas.

### <a name="create-visuals-for-syncing-device-state"></a>Skapa visuella objekt som synkroniserar enhetstillstånd
Lägg till följande XML i `MainPage.xaml` ovanför blocket `"EnableMicrophoneButton"`.

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Synkronisera enhetstillstånd

I `MainPage.xaml.cs` lägger du till referensen `using Windows.Web.Http;`. Lägg till följande kod i klassen `MainPage`. Den här metoden skickar en GET-begäran till slutpunktsexemplet och extraherar det aktuella enhetsläget för appen. Kom ihåg att ändra `<your_app_name>` till det som du använde i det anpassade kommandots **rubrik** i webbslutpunkten

```C#
private async void SyncDeviceState_ButtonClicked(object sender, RoutedEventArgs e)
{
    //Create an HTTP client object
    var httpClient = new HttpClient();

    //Add a user-agent header to the GET request.
    var your_app_name = "<your-app-name>";

    Uri endpoint = new Uri("https://webendpointexample.azurewebsites.net/api/DeviceState");
    var requestMessage = new HttpRequestMessage(HttpMethod.Get, endpoint);
    requestMessage.Headers.Add("app", $"{your_app_name}");

    try
    {
        //Send the GET request
        var httpResponse = await httpClient.SendRequestAsync(requestMessage);
        httpResponse.EnsureSuccessStatusCode();
        var httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
        dynamic deviceState = JsonConvert.DeserializeObject(httpResponseBody);
        var TVState = deviceState.TV.ToString();
        var FanState = deviceState.Fan.ToString();
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
            CoreDispatcherPriority.Normal,
            () =>
            {
                State_TV.Text = TVState;
                State_Fan.Text = FanState;
            });
    }
    catch (Exception ex)
    {
        NotifyUser(
            $"Unable to sync device status: {ex.Message}");
    }
}
```

## <a name="try-it-out"></a>Prova nu

1. Starta programmet
1. Klicka på Synkronisera enhetstillstånd.\
Om du testade appen med `turn on tv` i föregående avsnitt, ser du att tv-programmen visas som ”på”.
    > [!div class="mx-imgBorder"]
    > ![Synkronisera enhetstillstånd](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Välj Aktivera mikrofon
1. Välj knappen Prata
1. Säg `turn on the fan`
1. Fläktens status bör ändras till ”på”
    > [!div class="mx-imgBorder"]
    > ![Sätt på fläkten](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Exportera anpassade kommandon program som en fjärrskicklighet](./how-to-custom-commands-integrate-remote-skills.md)

