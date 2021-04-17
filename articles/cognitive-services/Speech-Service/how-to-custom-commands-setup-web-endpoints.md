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
ms.openlocfilehash: 95f27827950c5ed38caa1f83ede266afb57a1697
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515642"
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

## <a name="deploy-an-external-web-endpoint-using-azure-function-app"></a>Distribuera en extern webbslutpunkt med hjälp av Azure-funktionsappen

* För den här självstudien behöver du en HTTP-slutpunkt som upprätthåller tillstånd för alla enheter som du har ställt in i **kommandot TurnOnOff** för ditt anpassade kommandoprogram.

* Om du redan har en webbslutpunkt som du vill anropa går du vidare till [nästa avsnitt.](#setup-web-endpoints-in-custom-commands) I nästa avsnitt har vi också försedt dig med en standardvärdwebbslutpunkt som du kan använda om du vill hoppa över det här avsnittet.

### <a name="input-format-of-azure-function"></a>Indataformat för Azure Function
* Därefter distribuerar du en slutpunkt med hjälp [av Azure Functions](../../azure-functions/index.yml).
Följande är det allmänna formatet för en Anpassade kommandon händelse som skickas till din Azure-funktion. Använd den här informationen när du skriver funktionsappen.

    ```json
    {
      "conversationId": "string",
      "currentCommand": {
        "name": "string",
        "parameters": {
          "SomeParameterName": "string",
          "SomeOtherParameterName": "string"
        }
      },
      "currentGlobalParameters": {
          "SomeGlobalParameterName": "string",
          "SomeOtherGlobalParameterName": "string"
      }
    }
    ```

    
* Nu ska vi gå igenom nyckelattributen för dessa indata:
        
    | Attribut | Förklaring |
    | ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
    | **conversationId** | Den unika identifieraren för konversationen. Observera att detta ID kan genereras från klientappen. |
    | **currentCommand** | Kommandot som för närvarande är aktivt i konversationen. |
    | **Namn** | Namnet på kommandot. Attributet `parameters` är en karta med de aktuella värdena för parametrarna. |
    | **currentGlobalParameters** | En karta som `parameters` , men som används för globala parametrar. |


* För **Azure-funktionen DeviceState** ser ett exempel Anpassade kommandon händelse ut så här. Detta fungerar som **indata** till funktionsappen.
    
    ```json
    {
      "conversationId": "someConversationId",
      "currentCommand": {
        "name": "TurnOnOff",
        "parameters": {
          "item": "tv",
          "value": "on"
        }
      }
    }
    ```

### <a name="output-format-of-azure-function"></a>Utdataformat för Azure Function

#### <a name="output-consumed-by-a-custom-commands--application"></a>Utdata som används av Anpassade kommandon program
I det här fallet kan du ange att utdataformatet måste följa följande format. Följ [Uppdatera ett kommando från en webbslutpunkt](./how-to-custom-commands-update-command-from-web-endpoint.md) för mer information.

```json
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

#### <a name="output-consumed-by-a-client-application"></a>Utdata som används av ett klientprogram
I det här fallet kan du ange utdataformatet så att det passar din klients behov.
* För vår **DeviceState-slutpunkt** används utdata från Azure-funktionen av ett klientprogram i stället för Anpassade kommandon program. Exempel **på** utdata från Azure-funktionen bör se ut så här:
    
    ```json
    {
      "TV": "on",
      "Fan": "off"
    }
    ``` 

*  Dessutom bör dessa utdata skrivas till en extern lagring, så att du kan upprätthålla enheternas tillstånd. Det externa lagringstillståndet används i [avsnittet Integrera med klientprogram.](#integrate-with-client-application)


### <a name="host-azure-function"></a>Värd för Azure-funktion

1. Skapa ett tabellagringskonto för att spara enhetstillståndet.
    1. Gå till Azure Portal skapa en ny resurs av typen **Lagringskonto** med namnet **devicestate**.
        1. Kopiera värdet **Anslutningssträng** från **devicestate -> Access Keys**.
        1. Du måste lägga till den här strängen i den nedladdade funktionsappskoden.
    1. Ladda ned [funktionsappens exempelkod](https://aka.ms/speech/cc-function-app-sample).
    1. Öppna den nedladdade lösningen i VS 2019. I filen **Connections.jspå** ersätter **STORAGE_ACCOUNT_SECRET_CONNECTION_STRING** värdet till den kopierade hemligheten från *steg ett*.
1.  Ladda ned **DeviceStateAzureFunction-koden.**
1. [Distribuera](../../azure-functions/index.yml) Functions-appen till Azure.
    
    1.  Vänta tills distributionen har lyckats och gå till den distribuerade resursen på Azure Portal. 
    1. Välj **Funktioner** i den vänstra rutan och välj sedan **DeviceState.**
    1.  I det nya fönstret väljer du **Kod + test och** sedan Hämta **funktions-URL.**
 
## <a name="setup-web-endpoints-in-custom-commands"></a>Konfigurera webbslutpunkter i Anpassade kommandon
Nu ska vi koppla ihop Azure-funktionen med det Anpassade kommandon programmet.
I det här avsnittet använder du en befintlig **devicestate-standardslutpunkt.** Om du har skapat en egen webbslutpunkt med hjälp av Azure-funktionen eller på annat sätt använder du den i stället för standardvärdet https://webendpointexample.azurewebsites.net/api/DeviceState .

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
    > - Exempelwebbslutpunkten som skapats med hjälp av [Azure-funktionen](../../azure-functions/index.yml), som ansluter till databasen som sparar enhetens tillstånd för TV och fläkt
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
   > ![Skärmbild som visar skärmen Vid lyckad åtgärd – Åtgärd att köra.](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

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
   > ![Skärmbild som visar svaret Vid lyckad.](media/custom-commands/setup-web-endpoint-on-success-response.png)
- Svaret Vid fel\
Ta bort någon av frågeparametrarna, spara, träna om och testa
   > [!div class="mx-imgBorder"]
   > ![Anropa webbslutpunkter vid lyckad åtgärd](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integrera med klientprogram

I [How-to: Send activity to client application](./how-to-custom-commands-send-activity-to-client.md)(Så här gör du: Skicka aktivitet till klientprogram) lade du till åtgärden Skicka aktivitet **till** klient. Aktiviteten skickas till klientprogrammet oavsett om åtgärden **Anropa webbslutpunkt** har slutförts eller inte.
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
> [Exportera Anpassade kommandon program som en fjärrfärdighet](./how-to-custom-commands-integrate-remote-skills.md)

