---
title: Utöka Azure IoT Central med anpassade regler och meddelanden | Microsoft Docs
description: Som lösningsutvecklare konfigurerar du ett IoT Central för att skicka e-postaviseringar när en enhet slutar skicka telemetri. Den här lösningen använder Azure Stream Analytics, Azure Functions och SendGrid.
author: philmea
ms.author: philmea
ms.date: 02/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc, devx-track-csharp
manager: philmea
ms.openlocfilehash: a65d9dbaed4d197c2e0843e73ff3f45b8678017e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864225"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Utöka Azure IoT Central med anpassade regler med hjälp av Stream Analytics, Azure Functions och SendGrid

Den här guiden visar hur du som lösningsutvecklare utökar ditt IoT Central med anpassade regler och meddelanden. Exemplet visar hur du skickar ett meddelande till en operatör när en enhet slutar skicka telemetri. Lösningen använder en [Azure Stream Analytics](../../stream-analytics/index.yml) för att identifiera när en enhet har slutat skicka telemetri. I Stream Analytics-jobbet används [Azure Functions för att](../../azure-functions/index.yml) skicka e-postmeddelanden med [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

Den här guiden visar hur du utökar IoT Central utöver vad den redan kan göra med de inbyggda reglerna och åtgärderna.

I den här guiden får du lära dig att:

* Strömma telemetri från ett IoT Central program med hjälp av *kontinuerlig dataexport*.
* Skapa en Stream Analytics som identifierar när en enhet har slutat skicka data.
* Skicka ett e-postmeddelande med hjälp Azure Functions- och SendGrid-tjänsterna.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här instruktionerna behöver du en aktiv Azure-prenumeration.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

### <a name="iot-central-application"></a>IoT Central program

Skapa ett IoT Central-program på [Azure IoT Central programhanterarens](https://aka.ms/iotcentral) webbplats med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Prisplan | Standard |
| Programmall | Analys i butik – villkorsövervakning |
| Programnamn | Acceptera standardinställningen eller välj ditt eget namn |
| URL | Acceptera standardinställningen eller välj ett eget unikt URL-prefix |
| Katalog | Din Azure Active Directory klient |
| Azure-prenumeration | Din Azure-prenumeration |
| Region | Din närmaste region |

Exemplen och skärmbilderna i den här artikeln använder **USA** region. Välj en plats nära dig och se till att du skapar alla dina resurser i samma region.

Den här programmallen innehåller två simulerade termostatenheter som skickar telemetri.

### <a name="resource-group"></a>Resursgrupp

Använd Azure Portal [för att skapa en resursgrupp med](https://portal.azure.com/#create/Microsoft.ResourceGroup) namnet **DetectStoppedDevices** som ska innehålla de andra resurser som du skapar. Skapa dina Azure-resurser på samma plats som ditt IoT Central program.

### <a name="event-hubs-namespace"></a>Event Hubs-namnområde

Använd Azure Portal [för att skapa Event Hubs ett namnområde](https://portal.azure.com/#create/Microsoft.EventHub) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Namn    | Välj namnområdesnamn |
| Prisnivå | Grundläggande |
| Prenumeration | Din prenumeration |
| Resursgrupp | DetectStoppedDevices |
| Plats | USA, östra |
| Genomflödesenheter | 1 |

### <a name="stream-analytics-job"></a>Stream Analytics-jobb

Använd Azure Portal [för att skapa Stream Analytics jobb](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob)  med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Namn    | Välj jobbnamn |
| Prenumeration | Din prenumeration |
| Resursgrupp | DetectStoppedDevices |
| Plats | USA, östra |
| Värdmiljö | Moln |
| Strömningsenheter | 3 |

### <a name="function-app"></a>Funktionsapp

Använd Azure Portal [för att skapa en funktionsapp](https://portal.azure.com/#create/Microsoft.FunctionApp) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Appnamn    | Välj funktionsappens namn |
| Prenumeration | Din prenumeration |
| Resursgrupp | DetectStoppedDevices |
| Operativsystem | Windows |
| Värdplan | Förbrukningsplan |
| Plats | USA, östra |
| Körningsstack | .NET |
| Storage | Skapa ny |

### <a name="sendgrid-account-and-api-keys"></a>SendGrid-konto och API-nycklar

Om du inte har ett Sendgrid-konto kan du skapa ett [kostnadsfritt konto](https://app.sendgrid.com/) innan du börjar.

1. Från Sendgrid-instrumentpanelens inställningar på den vänstra menyn väljer du **API-nycklar.**
1. Klicka **på Skapa API-nyckel.**
1. Ge den nya API-nyckeln **namnet AzureFunctionAccess.**
1. Klicka **på Skapa & Visa**.

    :::image type="content" source="media/howto-create-custom-rules/sendgrid-api-keys.png" alt-text="Skärmbild av create SendGrid API key (Skapa SendGrid API-nyckel).":::

Därefter får du en API-nyckel. Spara den här strängen för senare användning.

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

Du kan konfigurera ett IoT Central att kontinuerligt exportera telemetri till en händelsehubb. I det här avsnittet skapar du en händelsehubb för att ta emot telemetri från IoT Central program. Händelsehubben levererar telemetrin till ditt Stream Analytics för bearbetning.

1. I den Azure Portal navigerar du till Event Hubs namnområdet och väljer **+ Händelsehubb.**
1. Namnge händelsehubben **centralexportera** och välj **Skapa**.

Ditt Event Hubs ser ut som på följande skärmbild: 

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

Funktionsloggmeddelandena visas på **panelen Loggar:**

```:::image type="content" source="media/howto-create-custom-rules/function-app-logs.png" alt-text="Function log output":::

After a few minutes, the **To** email address receives an email with the following content:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Lägga Stream Analytics fråga

Den här lösningen använder Stream Analytics för att identifiera när en enhet slutar skicka telemetri i mer än 120 sekunder. Frågan använder telemetrin från händelsehubben som indata. Jobbet skickar frågeresultatet till funktionsappen. I det här avsnittet konfigurerar du Stream Analytics jobb:

1. I den Azure Portal navigerar du till Stream Analytics jobb, under **Jobbtopologi** väljer du **Indata,** väljer + Lägg till **strömindata** och väljer sedan **Händelsehubb.**
1. Använd informationen i följande tabell för att konfigurera indata med hjälp av den händelsehubb som du skapade tidigare och välj sedan **Spara:**

    | Inställning | Värde |
    | ------- | ----- |
    | Inmatat alias | centraltelemetry |
    | Prenumeration | Din prenumeration |
    | Namnområde för händelsehubb | Event Hub-namnområdet |
    | Namn på händelsehubb | Använd befintlig – **centralexport** |

1. Under **Jobbtopologi** väljer du **Utdata,** **väljer + Lägg till** och sedan Azure **Function**.
1. Använd informationen i följande tabell för att konfigurera utdata och välj sedan **Spara**:

    | Inställning | Värde |
    | ------- | ----- |
    | Utdataalias | emailnotification |
    | Prenumeration | Din prenumeration |
    | Funktionsapp | Din funktionsapp |
    | Funktion  | HttpTrigger1 |

1. Under **Jobbtopologi** väljer du **Fråga** och ersätter den befintliga frågan med följande SQL:

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
1. Om du vill Stream Analytics jobbet väljer **du Översikt,** **sedan Starta,** **sedan Nu** och **sedan Starta:**

    :::image type="content" source="media/howto-create-custom-rules/stream-analytics.png" alt-text="Skärmbild av Stream Analytics.":::

## <a name="configure-export-in-iot-central"></a>Konfigurera export i IoT Central 

På Azure IoT Central [programhanterarens webbplats](https://aka.ms/iotcentral) går du till IoT Central som du skapade.

I det här avsnittet konfigurerar du programmet för att strömma telemetrin från dess simulerade enheter till din händelsehubb. Så här konfigurerar du exporten:

1. Gå till **sidan Dataexport,** välj **+ Ny** och välj sedan **Azure Event Hubs**.
1. Använd följande inställningar för att konfigurera exporten och välj sedan **Spara:** 

    | Inställning | Värde |
    | ------- | ----- |
    | Visningsnamn | Exportera till Event Hubs |
    | Enabled | På |
    | Typ av data som ska exporteras | Telemetri |
    | Berikande | Ange önskad nyckel/värde för hur du vill att exporterade data ska ordnas | 
    | Mål | Skapa ny och ange information för var data ska exporteras |

    :::image type="content" source="media/howto-create-custom-rules/cde-configuration.png" alt-text="Skärmbild av Dataexport.":::

Vänta tills exportstatusen **är Körs** innan du fortsätter.

## <a name="test"></a>Test

Om du vill testa lösningen kan du inaktivera den kontinuerliga dataexporten från IoT Central till simulerade stoppade enheter:

1. I ditt IoT Central program navigerar du till **sidan Dataexport** och väljer **konfigurationen Exportera för Event Hubs** exportera.
1. Ställ **in Aktiverad** på **Av** och välj **Spara.**
1. Efter minst två minuter får **till-e-postadressen** ett eller flera e-postmeddelanden som ser ut som följande exempelinnehåll:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Tidy up (Tidy Up)

Om du vill hålla ordning på och undvika onödiga kostnader tar du bort resursgruppen **DetectStoppedDevices** i Azure Portal.

Du kan ta bort IoT Central från **sidan Hantering** i programmet.

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig att:

* Strömma telemetri från ett IoT Central program med hjälp av *kontinuerlig dataexport*.
* Skapa en Stream Analytics som identifierar när en enhet har slutat skicka data.
* Skicka ett e-postmeddelande med hjälp Azure Functions- och SendGrid-tjänsterna.

Nu när du vet hur du skapar anpassade regler och meddelanden föreslår vi att du går vidare med att lära dig hur du [utökar Azure IoT Central med anpassad analys.](howto-create-custom-analytics.md)
