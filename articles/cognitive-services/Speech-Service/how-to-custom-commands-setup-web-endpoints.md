---
title: Konfigurera webbslutpunkter
titleSuffix: Azure Cognitive Services
description: konfigurera webbslutpunkter för Anpassade kommandon
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 5f1d5318140dd14c5024e8dd3ad0def0afc7f378
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725916"
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
> * En Anpassade kommandon -app (se [Skapa en röstassistent med Anpassade kommandon](quickstart-custom-commands-application.md))
> * En Speech SDK-aktiverad klientapp (se [Integrera med ett klientprogram med hjälp av Speech SDK)](how-to-custom-commands-setup-speech-sdk.md)

## <a name="deploy-an-external-web-endpoint-using-azure-function-app"></a>Distribuera en extern webbslutpunkt med hjälp av Azure Function-appen

I den här självstudien behöver du en HTTP-slutpunkt som upprätthåller tillstånd för alla enheter som du har ställt in i kommandot **TurnOnOff** för ditt Anpassade kommandon program.

Om du redan har en webbslutpunkt som du vill anropa går du vidare till [nästa avsnitt.](#setup-web-endpoints-in-custom-commands) Nästa avsnitt innehåller också information om en standardvärdwebbslutpunkt som du kan använda om du vill hoppa över det här avsnittet.

### <a name="input-format-of-azure-function"></a>Indataformat för Azure-funktion

Därefter distribuerar du en slutpunkt med hjälp [av Azure Functions](../../azure-functions/index.yml).
Följande är formatet för en Anpassade kommandon händelse som skickas till din Azure-funktion. Använd den här informationen när du skriver azure-funktionsappen.

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

    
I följande tabell beskrivs nyckelattributen för dessa indata:
        
| Attribut | Förklaring |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | Den unika identifieraren för konversationen. Observera att detta ID kan genereras av klientappen. |
| **currentCommand** | Kommandot som för närvarande är aktivt i konversationen. |
| **Namn** | Namnet på kommandot. Attributet `parameters` är en karta med de aktuella värdena för parametrarna. |
| **currentGlobalParameters** | En karta som `parameters` , men som används för globala parametrar. |


För **Azure-funktionen DeviceState** ser ett exempel Anpassade kommandon händelse ut så här. Detta fungerar som **indata** för funktionsappen.
    
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

### <a name="azure-function-output-for-a-custom-command-app"></a>Azure Function-utdata för en anpassad kommandoapp

Om utdata från din Azure-funktion används av Anpassade kommandon bör de visas i följande format. Mer [information finns i Uppdatera ett kommando från en](./how-to-custom-commands-update-command-from-web-endpoint.md) webbslutpunkt.

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

### <a name="azure-function-output-for-a-client-application"></a>Azure Function-utdata för ett klientprogram

Om utdata från din Azure-funktion används av ett klientprogram kan utdata ha den form som klientprogrammet kräver.

För vår **DeviceState-slutpunkt** används utdata från din Azure-funktion av ett klientprogram i stället för Anpassade kommandon program. Exempel på utdata från Azure-funktionen bör se ut så här:
    
```json
{
  "TV": "on",
  "Fan": "off"
}
``` 

Dessa utdata ska skrivas till en extern lagringsplats så att du kan upprätthålla enheternas tillstånd. Det externa lagringstillståndet används i [avsnittet Integrera med klientprogram](#integrate-with-client-application) nedan.


### <a name="deploy-azure-function"></a>Distribuera en Azure-funktion

Vi tillhandahåller ett exempel som du kan konfigurera och distribuera som en Azure Functions app. Följ dessa steg om du vill skapa ett lagringskonto för vårt exempel.
 
1. Skapa tabellagring för att spara enhetstillståndet. I Azure Portal skapar du en ny resurs av typen **Lagringskonto med** namnet **devicestate**.
1. Kopiera värdet **för Anslutningssträng** från **devicestate -> Access Keys**. Du måste lägga till den här stränghemligheten i den nedladdade funktionsappskoden.
1. Ladda ned [funktionsappens exempelkod](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/main/custom-commands/quick-start).
1. Öppna den nedladdade lösningen i Visual Studio 2019. I **Connections.jspå** **ersätter STORAGE_ACCOUNT_SECRET_CONNECTION_STRING** med hemligheten från steg 2.
1.  Ladda ned **DeviceStateAzureFunction-koden.**

Följ dessa steg om du Azure Functions distribuera exempelappen.

1. [Distribuera](../../azure-functions/index.yml) Azure Functions appen.
1. Vänta tills distributionen har lyckats och gå till den distribuerade resursen på Azure Portal. 
1. Välj **Funktioner** i den vänstra rutan och välj sedan **DeviceState.**
1.  I det nya fönstret väljer du **Kod + test och** sedan Hämta **funktions-URL.**
 
## <a name="setup-web-endpoints-in-custom-commands"></a>Konfigurera webbslutpunkter i Anpassade kommandon

Nu ska vi koppla ihop Azure-funktionen med det Anpassade kommandon programmet.
I det här avsnittet använder du en befintlig **DeviceState-standardslutpunkt.** Om du har skapat en egen webbslutpunkt med hjälp av Azure Function eller på annat sätt använder du den i stället för standardvärdet `https://webendpointexample.azurewebsites.net/api/DeviceState` .

1. Öppna programmet Anpassade kommandon som du skapade tidigare.
1. Gå till **Webbslutpunkter och** klicka på **Ny webbslutpunkt.**

   > [!div class="mx-imgBorder"]
   > ![Ny webbslutpunkt](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Inställning | Föreslaget värde | Beskrivning |
   | ------- | --------------- | ----------- |
   | Name | UpdateDeviceState | Namnet på webbslutpunkten. |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | URL-adressen till den slutpunkt som du vill att din anpassade kommandoapp ska kommunicera med. |
   | Metod | POST | Tillåtna interaktioner (till exempel GET, POST) med din slutpunkt.|
   | Sidhuvuden | Nyckel: app, Värde: använd de första 8 siffrorna i ditt applicationId | De rubrikparametrar som ska ingå i begärandets rubrik.|

    > [!NOTE]
    > - Exempelwebbslutpunkten som [skapas med Azure Functions](../../azure-functions/index.yml), som är ansluten till databasen som sparar enhetens tillstånd för TV och fläkt.
    > - Den föreslagna rubriken behövs bara för slutpunktsexempel.
    > - För att se till att värdet för rubriken är unikt i vår exempelslutpunkt tar du de första 8 siffrorna i ditt **applicationId**.
    > - I verkligheten kan webbslutpunkten vara slutpunkten för [den IOT-hubb](../../iot-hub/about-iot-hub.md) som hanterar dina enheter.

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
- Spara, träna och testa när du får ett svar.
   > [!div class="mx-imgBorder"]
   > ![Skärmbild som visar svaret Vid lyckad.](media/custom-commands/setup-web-endpoint-on-success-response.png)
- Ta bort en av frågeparametrarna vid felsvar, spara, träna om och testa.
   > [!div class="mx-imgBorder"]
   > ![Anropa webbslutpunkter vid lyckad åtgärd](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integrera med klientprogram

I [Skicka Anpassade kommandon till klientprogram lade](./how-to-custom-commands-send-activity-to-client.md)du till åtgärden Skicka aktivitet **till** klient. Aktiviteten skickas till klientprogrammet oavsett om åtgärden **Anropa webbslutpunkt** har slutförts eller inte.
Normalt vill du dock bara skicka aktivitet till klientprogrammet när anropet till webbslutpunkten lyckas. I det här exemplet sker det när enhetens status har uppdaterats.

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
Nu skickar du bara aktivitet till klienten när begäran till webbslutpunkten lyckas.

### <a name="create-visuals-for-syncing-device-state"></a>Skapa visuella objekt som synkroniserar enhetstillstånd

Lägg till följande XML i `MainPage.xaml` ovanför **blocket EnableMicrophoneButton.**

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Synkronisera enhetstillstånd

I `MainPage.xaml.cs` lägger du till referensen `using Windows.Web.Http;`. Lägg till följande kod i klassen `MainPage`. Den här metoden skickar en GET-begäran till slutpunktsexemplet och extraherar det aktuella enhetsläget för appen. Se till att ändra `<your_app_name>` till det du använde i rubriken i **webbslutpunkten** för anpassat kommando.

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

## <a name="try-it-out"></a>Prova

1. Starta programmet.
1. Klicka på Synkronisera enhetstillstånd.\
Om du testade appen med `turn on tv` i föregående avsnitt visas TV-programmet som **på**.
    > [!div class="mx-imgBorder"]
    > ![Synkronisera enhetstillstånd](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Välj **Aktivera mikrofon.**
1. Välj **knappen** Prata.
1. Säg `turn on the fan` . Fläktens visuella tillstånd bör ändras till **på**.
    > [!div class="mx-imgBorder"]
    > ![Sätt på fläkten](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Exportera Anpassade kommandon program som en fjärrfärdighet](./how-to-custom-commands-integrate-remote-skills.md)
