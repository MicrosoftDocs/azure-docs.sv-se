---
title: Utöka Azure-IoT Central med anpassade regler och meddelanden | Microsoft Docs
description: Som en lösnings utvecklare konfigurerar du ett IoT Central program för att skicka e-postaviseringar när en enhet slutar skicka telemetri. Den här lösningen använder Azure Stream Analytics, Azure Functions och SendGrid.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc, devx-track-csharp
manager: philmea
ms.openlocfilehash: c79367ca8cf9e4a4884c829c675d794b2e734737
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98220274"
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

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

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

### <a name="sendgrid-account"></a>SendGrid-konto

Använd [Azure Portal för att skapa ett SendGrid-konto](https://portal.azure.com/#create/Sendgrid.sendgrid) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Namn    | Välj ditt SendGrid-konto namn |
| Lösenord | Skapa ett lösen ord |
| Prenumeration | Din prenumeration |
| Resursgrupp | DetectStoppedDevices |
| Prisnivå | F1 Kostnadsfri |
| Kontaktinformation | Fyll i nödvändig information |

När du har skapat alla nödvändiga resurser ser **DetectStoppedDevices** -resurs gruppen ut som följande skärm bild:

![Identifiera resurs gruppen stoppade enheter](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

Du kan konfigurera ett IoT Central program för att kontinuerligt exportera telemetri till en Event Hub. I det här avsnittet skapar du en händelsehubben som tar emot telemetri från ditt IoT Central-program. Händelsehubben ger telemetri till din Stream Analytics jobb för bearbetning.

1. I Azure Portal navigerar du till Event Hubs namn området och väljer **+ Event Hub**.
1. Namnge Event Hub- **centralexport** och välj **skapa**.

Event Hubs namn området ser ut som på följande skärm bild:

![Event Hubs-namnområde](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>Hämta SendGrid API-nyckel

Din Function-app behöver en SendGrid API-nyckel för att skicka e-postmeddelanden. Så här skapar du en SendGrid API-nyckel:

1. I Azure Portal navigerar du till ditt SendGrid-konto. Välj sedan **Hantera** för att komma åt ditt SendGrid-konto.
1. I ditt SendGrid-konto väljer du **Inställningar** och sedan **API-nycklar**. Välj **skapa API-nyckel**:

    ![Skapa SendGrid API-nyckel](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. På sidan **skapa API-nyckel** skapar du en nyckel med namnet **AzureFunctionAccess** med **fullständig** behörighet.
1. Anteckna API-nyckeln, du behöver den när du konfigurerar din Function-app.

## <a name="define-the-function"></a>Definiera funktionen

Den här lösningen använder en Azure Functions app för att skicka ett e-postmeddelande när Stream Analytics jobbet identifierar en stoppad enhet. Så här skapar du en Function-app:

1. I Azure Portal navigerar du till **App Service** -instansen i resurs gruppen **DetectStoppedDevices** .
1. Välj **+** om du vill skapa en ny funktion.
1. På sidan **Välj en utvecklings miljö** väljer du **i portalen** och väljer sedan **Fortsätt**.
1. På sidan **skapa en funktion** väljer du **webhook + API** och väljer sedan **skapa**.

Portalen skapar en standard funktion som kallas **HttpTrigger1**:

![Standard funktion för HTTP-utlösare](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Konfigurera funktions bindningar

Om du vill skicka e-postmeddelanden med SendGrid måste du konfigurera bindningarna för din funktion på följande sätt:

1. Välj **integrera**, Välj utdata **http ($Return)** och välj sedan **ta bort**.
1. Välj **+ nya utdata**, Välj **SendGrid** och välj sedan **Välj**. Välj **Installera** för att installera SendGrid-tillägget.
1. När installationen är klar väljer du **Använd funktions retur värde**. Lägg till en giltig **adress för att** ta emot e-postaviseringar.  Lägg till en giltig **från-adress** som ska användas som e-postavsändaren.
1. Välj **ny** bredvid **SendGrid API Key app Setting**. Ange **SendGridAPIKey** som nyckel och den SendGrid API-nyckel som du antecknade tidigare som värde. Välj sedan **Skapa**.
1. Välj **Spara** för att spara SendGrid-bindningarna för din funktion.

De integrerande inställningarna ser ut som på följande skärm bild:

![Function-programintegrationer](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>Lägg till funktions koden

Om du vill implementera din funktion lägger du till C#-koden för att parsa inkommande HTTP-begäran och skicka e-postmeddelanden enligt följande:

1. Välj funktionen **HttpTrigger1** i din Function-app och Ersätt C#-koden med följande kod:

    ```csharp
    #r "Newtonsoft.Json"
    #r "..\bin\SendGrid.dll"

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

    Du kan se ett fel meddelande tills du har sparat den nya koden.

1. Spara funktionen genom att välja **Spara** .

### <a name="test-the-function-works"></a>Testa funktionen fungerar

Om du vill testa funktionen i portalen väljer du först **loggar** längst ned i kod redigeraren. Välj sedan **testa** till höger om kod redigeraren. Använd följande JSON som **begär ande text**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

Funktions logg meddelanden visas på panelen **loggar** :

![Funktions loggens utdata](media/howto-create-custom-rules/function-app-logs.png)

Efter några minuter får e-postadressen ett e **-postmeddelande med** följande innehåll:

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

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Konfigurera export i IoT Central

På webbplatsen [Azure IoT Central Application Manager](https://aka.ms/iotcentral) navigerar du till det IoT Central program som du skapade från contoso-mallen. I det här avsnittet konfigurerar du programmet för att strömma Telemetrin från dess simulerade enheter till händelsehubben. Konfigurera exporten:

1. Gå till sidan **data export** , Välj **+ ny** och sedan **Azure Event Hubs**.
1. Använd följande inställningar för att konfigurera exporten och välj sedan **Spara**:

    | Inställning | Värde |
    | ------- | ----- |
    | Visningsnamn | Exportera till Event Hubs |
    | Enabled | På |
    | Event Hubs-namnområde | Namnet på Event Hubs namn området |
    | Händelsehubb | centralexport |
    | Mått | På |
    | Egenskaper | Av |
    | Enhetsmallar | Av |

![Konfiguration av kontinuerlig data export](media/howto-create-custom-rules/cde-configuration.png)

Vänta tills export status är **igång** innan du fortsätter.

## <a name="test"></a>Testa

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
