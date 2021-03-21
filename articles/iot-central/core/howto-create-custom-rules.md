---
title: Utöka Azure-IoT Central med anpassade regler och meddelanden | Microsoft Docs
description: Som en lösnings utvecklare konfigurerar du ett IoT Central program för att skicka e-postaviseringar när en enhet slutar skicka telemetri. Den här lösningen använder Azure Stream Analytics, Azure Functions och SendGrid.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 02/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc, devx-track-csharp
manager: philmea
ms.openlocfilehash: 824308b66803d2dfa05383ff06ce97c48626619d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179348"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Utöka Azure IoT Central med anpassade regler med hjälp av Stream Analytics, Azure Functions och SendGrid

Den här instruktions guiden visar dig som lösnings utvecklare och hur du utökar ditt IoT Central program med anpassade regler och meddelanden. Exemplet visar hur du skickar ett meddelande till en operatör när en enhet slutar skicka telemetri. Lösningen använder en [Azure Stream Analytics](../../stream-analytics/index.yml) fråga för att identifiera när en enhet har slutat skicka telemetri. Stream Analytics jobbet använder [Azure Functions](../../azure-functions/index.yml) för att skicka e-postmeddelanden med hjälp av [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

Den här instruktions guiden visar hur du utökar IoT Central bortom det som redan kan utföras med de inbyggda reglerna och åtgärderna.

I den här instruktions guiden får du lära dig att:

* Strömma telemetri från ett IoT Central program med *kontinuerlig data export*.
* Skapa en Stream Analytics-fråga som identifierar när en enhet har slutat skicka data.
* Skicka ett e-postmeddelande med hjälp av Azure Functions-och SendGrid-tjänsterna.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här instruktions guiden behöver du en aktiv Azure-prenumeration.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

### <a name="iot-central-application"></a>IoT Central program

Skapa ett IoT Central-program på webbplatsen för [Azure IoT Central Application Manager](https://aka.ms/iotcentral) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Pris plan | Standard |
| Programmall | In-Store Analytics – villkors övervakning |
| Programnamn | Acceptera standardvärdet eller Välj ditt eget namn |
| URL | Acceptera standardvärdet eller Välj ditt eget unika URL-prefix |
| Katalog | Din Azure Active Directory klient |
| Azure-prenumeration | Din Azure-prenumeration |
| Region | Din närmaste region |

I exemplen och skärm bilderna i den här artikeln används den **USA** regionen. Välj en plats nära dig och se till att du skapar alla resurser i samma region.

Den här program mal len innehåller två simulerade termostat-enheter som skickar telemetri.

### <a name="resource-group"></a>Resursgrupp

Använd [Azure Portal för att skapa en resurs grupp](https://portal.azure.com/#create/Microsoft.ResourceGroup) med namnet **DetectStoppedDevices** som innehåller de andra resurser som du skapar. Skapa dina Azure-resurser på samma plats som ditt IoT Central-program.

### <a name="event-hubs-namespace"></a>Event Hubs-namnområde

Använd [Azure Portal för att skapa ett Event Hubs-namnområde](https://portal.azure.com/#create/Microsoft.EventHub) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Namn    | Välj namn på namn område |
| Prisnivå | Grundläggande |
| Prenumeration | Din prenumeration |
| Resursgrupp | DetectStoppedDevices |
| Plats | USA, östra |
| Genomflödesenheter | 1 |

### <a name="stream-analytics-job"></a>Stream Analytics-jobb

Använd [Azure Portal för att skapa ett Stream Analytics jobb](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob)  med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Namn    | Välj ditt jobb namn |
| Prenumeration | Din prenumeration |
| Resursgrupp | DetectStoppedDevices |
| Plats | USA, östra |
| Värdmiljö | Moln |
| Strömningsenheter | 3 |

### <a name="function-app"></a>Funktionsapp

Använd [Azure Portal för att skapa en Function-app](https://portal.azure.com/#create/Microsoft.FunctionApp) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Appnamn    | Välj namnet på din Function-app |
| Prenumeration | Din prenumeration |
| Resursgrupp | DetectStoppedDevices |
| Operativsystem | Windows |
| Värdplan | Förbrukningsplan |
| Plats | USA, östra |
| Körningsstack | .NET |
| Storage | Skapa ny |

### <a name="sendgrid-account-and-api-keys"></a>SendGrid-konto och API-nycklar

Om du inte har ett SendGrid-konto kan du skapa ett [kostnads fritt konto](https://app.sendgrid.com/) innan du börjar.

1. Välj **API-nycklar** på SendGrid instrument panels inställningar på den vänstra menyn.
1. Klicka på **skapa API-nyckel.**
1. Ge den nya API-nyckeln **AzureFunctionAccess.**
1. Klicka på **skapa & vy**.

    :::image type="content" source="media/howto-create-custom-rules/sendgrid-api-keys.png" alt-text="Skärm bild av API-nyckeln Create SendGrid.":::

Därefter får du en API-nyckel. Spara den här strängen för senare användning.

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

Du kan konfigurera ett IoT Central program för att kontinuerligt exportera telemetri till en Event Hub. I det här avsnittet skapar du en händelsehubben som tar emot telemetri från ditt IoT Central-program. Händelsehubben ger telemetri till din Stream Analytics jobb för bearbetning.

1. I Azure Portal navigerar du till Event Hubs namn området och väljer **+ Event Hub**.
1. Namnge Event Hub- **centralexport** och välj **skapa**.

Event Hubs namn området ser ut som på följande skärm bild: 

```:::image type="content" source="media/howto-create-custom-rules/event-hubs-namespace.png" alt-text="Screenshot of Event Hubs namespace." border="false":::

## Define the function

This solution uses an Azure Functions app to send an email notification when the Stream Analytics job detects a stopped device. To create your function app:

1. In the Azure portal, navigate to the **App Service** instance in the **DetectStoppedDevices** resource group.
1. Select **+** to create a new function.
1. Select **HTTP Trigger**.
1. Select **Add**.

    :::image type="content" source="media/howto-create-custom-rules/add-function.png" alt-text="Image of the Default HTTP trigger function"::: 

## Edit code for HTTP Trigger

The portal creates a default function called **HttpTrigger1**:

```:::image type="content" source="media/howto-create-custom-rules/default-function.png" alt-text="Screenshot of Edit HTTP trigger function.":::

1. Replace the C# code with the following code:

    ```csharp
    #r "Newtonsoft.Json"
    #r "SendGrid"
    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);  

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    You may see an error message until you save the new code.
1. Select **Save** to save the function.

## Add SendGrid Key

To add your SendGrid API Key, you need to add it to your **Function Keys** as follows:

1. Select **Function Keys**.
1. Choose **+ New Function Key**.
1. Enter the *Name* and *Value* of the API Key you created before.
1. Click **OK.**

    :::image type="content" source="media/howto-create-custom-rules/add-key.png" alt-text="Screenshot of Add Sangrid Key.":::


## Configure HttpTrigger function to use SendGrid

To send emails with SendGrid, you need to configure the bindings for your function as follows:

1. Select **Integrate**.
1. Choose **Add Output** under **HTTP ($return)**.
1. Select **Delete.**
1. Choose **+ New Output**.
1. For Binding Type, then choose **SendGrid**.
1. For SendGrid API Key Setting Type, click New.
1. Enter the *Name* and *Value* of your SendGrid API key.
1. Add the following information:

| Setting | Value |
| ------- | ----- |
| Message parameter name | Choose your name |
| To address | Choose the name of your To Address |
| From address | Choose the name of your From Address |
| Message subject | Enter your subject header |
| Message text | Enter the message from your integration |

1. Select **OK**.

    :::image type="content" source="media/howto-create-custom-rules/add-output.png" alt-text="Screenshot of Add SandGrid Output.":::


### Test the function works

To test the function in the portal, first choose **Logs** at the bottom of the code editor. Then choose **Test** to the right of the code editor. Use the following JSON as the **Request body**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

Funktions logg meddelanden visas på panelen **loggar** :

```:::image type="content" source="media/howto-create-custom-rules/function-app-logs.png" alt-text="Function log output":::

After a few minutes, the **To** email address receives an email with the following content:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Lägg till Stream Analytics fråga

I den här lösningen används en Stream Analytics fråga för att identifiera när en enhet slutar skicka telemetri i mer än 120 sekunder. Frågan använder Telemetrin från händelsehubben som indatatyp. Jobbet skickar frågeresultaten till Function-appen. I det här avsnittet konfigurerar du Stream Analytics jobbet:

1. I Azure Portal navigerar du till ditt Stream Analytics-jobb, under **jobb sto pol Ogin** väljer **indata**, väljer **+ Lägg till Stream-indata** och väljer sedan **Event Hub**.
1. Använd informationen i följande tabell för att konfigurera indata med händelsehubben som du skapade tidigare och välj sedan **Spara**:

    | Inställning | Värde |
    | ------- | ----- |
    | Inmatat alias | centraltelemetry |
    | Prenumeration | Din prenumeration |
    | Namnområde för händelsehubb | Ditt Event Hub-namnområde |
    | Namn på händelsehubb | Använd befintlig- **centralexport** |

1. Under **jobb sto pol Ogin** väljer du **utdata**, väljer **+ Lägg till** och väljer sedan **Azure Function**.
1. Använd informationen i följande tabell för att konfigurera utdata och välj sedan **Spara**:

    | Inställning | Värde |
    | ------- | ----- |
    | Utdataalias | emailnotification |
    | Prenumeration | Din prenumeration |
    | Funktionsapp | Din Function-app |
    | Funktion  | HttpTrigger1 |

1. Under **jobb sto pol Ogin** väljer du **fråga** och ersätter den befintliga frågan med följande SQL:

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. Välj **Spara**.
1. Starta Stream Analytics jobb genom att välja **Översikt**, sedan **Start** **och sedan** **Starta**:

    :::image type="content" source="media/howto-create-custom-rules/stream-analytics.png" alt-text="Skärm bild av Stream Analytics.":::

## <a name="configure-export-in-iot-central"></a>Konfigurera export i IoT Central 

På webbplatsen [Azure IoT Central Application Manager](https://aka.ms/iotcentral) navigerar du till det IoT Central program som du har skapat.

I det här avsnittet konfigurerar du programmet för att strömma Telemetrin från dess simulerade enheter till händelsehubben. Konfigurera exporten:

1. Gå till sidan **data export** , Välj **+ ny** och sedan **Azure Event Hubs**.
1. Använd följande inställningar för att konfigurera exporten och välj sedan **Spara**: 

    | Inställning | Värde |
    | ------- | ----- |
    | Visningsnamn | Exportera till Event Hubs |
    | Enabled | På |
    | Typ av data som ska exporteras | Telemetri |
    | Berikningar | Ange önskad nyckel/värde för hur du vill att exporterade data ska ordnas | 
    | Mål | Skapa ny och ange information om var data ska exporteras |

    :::image type="content" source="media/howto-create-custom-rules/cde-configuration.png" alt-text="Skärm bild av data exporten.":::

Vänta tills export status är **igång** innan du fortsätter.

## <a name="test"></a>Test

Om du vill testa lösningen kan du inaktivera kontinuerlig data export från IoT Central till simulerade stoppade enheter:

1. I ditt IoT Central program navigerar du till sidan **data export** och väljer export konfigurationen **export till Event Hubs** .
1. Ange **aktive rad** till **av** och välj **Spara**.
1. Efter minst två minuter tar e-postadressen emot ett eller flera e **-postmeddelanden som** ser ut som i följande exempel innehåll:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Städa upp

Ta bort resurs gruppen **DetectStoppedDevices** i Azure Portal för att städa upp efter den här instruktionen och undvika onödiga kostnader.

Du kan ta bort IoT Central-programmet från **hanterings** sidan i programmet.

## <a name="next-steps"></a>Nästa steg

I den här instruktions guiden har du lärt dig att:

* Strömma telemetri från ett IoT Central program med *kontinuerlig data export*.
* Skapa en Stream Analytics-fråga som identifierar när en enhet har slutat skicka data.
* Skicka ett e-postmeddelande med hjälp av Azure Functions-och SendGrid-tjänsterna.

Nu när du vet hur du skapar anpassade regler och meddelanden är det föreslagna nästa steg att lära dig hur du [utökar Azure-IoT Central med anpassad analys](howto-create-custom-analytics.md).
