---
title: Mata in telemetri från IoT Hub
titleSuffix: Azure Digital Twins
description: Se hur du matar in meddelanden för telemetri från IoT Hub.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 9/15/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2fd0d9d2b6e80d54bdd45b7a13fab7bfa33841c9
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889475"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Mata in IoT Hub telemetri i Azure Digitals, dubbla

Digitala Azure-enheter drivs med data från IoT-enheter och andra källor. En gemensam källa för enhets data som ska användas i Azure Digitals dubbla är [IoT Hub](../iot-hub/about-iot-hub.md).

Processen för att mata in data i Azure Digitals, är att konfigurera en extern beräknings resurs, till exempel en funktion som görs med hjälp av [Azure Functions](../azure-functions/functions-overview.md). Funktionen tar emot data och använder DigitalTwins- [API: er](/rest/api/digital-twins/dataplane/twins) för att ange egenskaper eller brand släcknings händelser på [digitala dubbla](concepts-twins-graph.md) . 

Det här dokumentet vägleder dig genom processen för att skriva en funktion som kan mata in telemetri från IoT Hub.

## <a name="prerequisites"></a>Förutsättningar

Innan du fortsätter med det här exemplet måste du konfigurera följande resurser som krav:
* **En IoT-hubb**. Anvisningar finns i avsnittet *skapa en IoT Hub* i [den här IoT Hub snabb](../iot-hub/quickstart-send-telemetry-cli.md)starten.
* **En digital Azure-instans** som tar emot din enhets telemetri. Anvisningar finns i anvisningar [*: Konfigurera en digital Azure-instans och autentisering*](./how-to-set-up-instance-portal.md).

### <a name="example-telemetry-scenario"></a>Exempel scenario för telemetri

Den här instruktionen beskriver hur du skickar meddelanden från IoT Hub till Azures digitala dubbla, med hjälp av en funktion i Azure. Det finns många möjliga konfigurationer och matchnings strategier som du kan använda för att skicka meddelanden, men exemplet för den här artikeln innehåller följande delar:
* En termostat-enhet i IoT Hub, med ett känt enhets-ID
* En digital enhet som representerar enheten, med ett matchande ID

> [!NOTE]
> I det här exemplet används en enkel ID-matchning mellan enhets-ID: t och motsvarande digitala dubbla ID, men det är möjligt att ge mer sofistikerade mappningar från enheten till dess dubbla (till exempel med en mappnings tabell).

När en händelse för att utföra en termostat skickas av den enheten, bearbetar en funktion telemetri och egenskapen *temperatur* för den digitala enheten bör uppdateras. Det här scenariot beskrivs i ett diagram nedan:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Ett diagram som visar ett flödes diagram. I diagrammet skickar en IoT Hub-enhet temperatur telemetri via IoT Hub till en funktion i Azure, som uppdaterar en temperatur egenskap på en enhet i en digital i Azure Digitals." border="false":::

## <a name="add-a-model-and-twin"></a>Lägg till en modell och tvilling

I det här avsnittet ska du konfigurera en [digital](concepts-twins-graph.md) enhet i Azure Digitals, som representerar termostat-enheten och som kommer att uppdateras med information från IoT Hub.

Om du vill skapa en termostat-typ måste du först ladda upp termostat- [modellen](concepts-models.md) till din instans, som beskriver egenskaperna för en termostat och kommer att användas senare för att skapa den dubbla. 

Modellen ser ut så här:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Kör följande Azure CLI-kommando om du vill **överföra den här modellen till en-instansen**, som överför modellen ovan som inline-JSON. Du kan köra kommandot i [Azure Cloud Shell](/cloud-shell/overview.md) i webbläsaren eller på din dator om du har CLI [installerat lokalt](/cli/azure/install-azure-cli).

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

Sedan måste du **skapa en dubbel med den här modellen**. Använd följande kommando för att skapa en termostat med namnet **thermostat67** och ange 0,0 som första temperatur värde.

```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

>[!NOTE]
> Om du använder Cloud Shell i PowerShell-miljön kan du behöva undanta citat tecken i de infogade JSON-fälten för att deras värden ska kunna parsas korrekt. Här följer kommandon för att ladda upp modellen och skapa den dubbla med den här ändringen:
>
> Överförings modell:
> ```azurecli-interactive
> az dt model create --models '{  \"@id\": \"dtmi:contosocom:DigitalTwins:Thermostat;1\",  \"@type\": \"Interface\",  \"@context\": \"dtmi:dtdl:context;2\",  \"contents\": [    {      \"@type\": \"Property\",      \"name\": \"Temperature\",      \"schema\": \"double\"    }  ]}' -n {digital_twins_instance_name}
> ```
>
> Skapa dubbla:
> ```azurecli-interactive
> az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{\"Temperature\": 0.0,}' --dt-name {digital_twins_instance_name}
> ```

När den dubbla har skapats, bör CLI-utdata från kommandot se ut ungefär så här:
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-a-function"></a>Skapa en funktion

I det här avsnittet ska du skapa en Azure-funktion för att få åtkomst till Azure Digital-meddelanden och uppdatering av dubblare baserat på IoT-telemetri som den tar emot. Följ stegen nedan för att skapa och publicera funktionen.

#### <a name="step-1-create-a-function-app-project"></a>Steg 1: skapa ett Function app-projekt

Skapa först ett nytt Function app-projekt i Visual Studio. Instruktioner för hur du gör detta finns i avsnittet [**skapa en Function-app i Visual Studio**](how-to-create-azure-function.md#create-a-function-app-in-visual-studio) i artikeln *instruktion: Konfigurera en funktion för bearbetning av data* .

#### <a name="step-2-fill-in-function-code"></a>Steg 2: Fyll i funktions koden

Lägg till följande paket i projektet:
* [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure. Identity](https://www.nuget.org/packages/Azure.Identity/)
* [Microsoft. Azure. WebJobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid/)

Byt namn på exempel funktionen *Function1. cs* som Visual Studio har genererat med det nya projektet till *IoTHubtoTwins. cs*. Ersätt koden i filen med följande kod:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs":::

Spara funktions koden.

#### <a name="step-3-publish-the-function-app-to-azure"></a>Steg 3: publicera Function-appen till Azure

Publicera projektet i en Function-app i Azure.

Instruktioner för hur du gör detta finns i avsnittet [**publicera Function-appen till Azure**](how-to-create-azure-function.md#publish-the-function-app-to-azure) med *instruktionen så här konfigurerar du en funktion för bearbetning av data* .

#### <a name="step-4-configure-the-function-app"></a>Steg 4: Konfigurera Function-appen

Tilldela sedan **en åtkomst roll** för funktionen och **Konfigurera program inställningarna** så att de kan komma åt din Azure Digital-instansen. Instruktioner för hur du gör detta finns i avsnittet [**Konfigurera säkerhets åtkomst för funktionen app**](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) för *instruktionen instruktion: Konfigurera en funktion för bearbetning av data* .

## <a name="connect-your-function-to-iot-hub"></a>Anslut din funktion till IoT Hub

I det här avsnittet ska du konfigurera din funktion som en händelse mål för enhets data för IoT Hub. Detta säkerställer att data från termostat-enheten i IoT Hub skickas till Azure-funktionen för bearbetning.

I [Azure Portal](https://portal.azure.com/)navigerar du till IoT Hub-instansen som du skapade i avsnittet [*krav*](#prerequisites) . Under **händelser** skapar du en prenumeration för din funktion.

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Skärm bild av Azure Portal som visar hur du lägger till en händelse prenumeration.":::

På sidan **Skapa händelse prenumeration** fyller du i fälten enligt följande:
  1. För **namn** väljer du det namn som du vill använda för händelse prenumerationen.
  2. För **händelse schema** väljer du _Event Grid schema_.
  3. I **namn på system ämne** väljer du det namn som du vill använda.
  1. För **att filtrera till händelse typer** väljer du kryss rutan _telemetri för enheter_ och avmarkerar andra händelse typer.
  1. I **typ av slut punkt** väljer du _Azure Function_.
  1. För **slut punkt** använder du länken _Välj en slut punkt_ för att välja vilken Azure-funktion som ska användas för slut punkten.
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="Skärm bild av Azure Portal för att skapa information om händelse prenumerationen":::

På sidan _Välj Azure-funktion_ som öppnas kontrollerar du eller fyller i informationen nedan.
 1. **Prenumeration**: din Azure-prenumeration.
 2. **Resurs grupp**: din resurs grupp.
 3. **Function-app**: namnet på din Function-app.
 4. **Fack**: _produktion_.
 5. **Funktion**: Välj funktionen från tidigare, *IoTHubtoTwins* från List rutan.

Spara informationen med knappen _Bekräfta val_ .            
      
:::image type="content" source="media/how-to-ingest-iot-hub-data/select-azure-function.png" alt-text="Skärm bild av Azure Portal för att välja funktionen.":::

Välj knappen _skapa_ för att skapa händelse prenumerationen.

## <a name="send-simulated-iot-data"></a>Skicka simulerade IoT-data

Testa din nya ingångs funktion genom att använda enhets simulatorn från [*självstudien: Anslut en lösning från slut punkt till slut punkt*](./tutorial-end-to-end.md). Den själv studie kursen drivs av ett exempel projekt som skrivits i C#. Exempel koden finns här: [Azure Digitals dubblare-exempel från slut punkt till slut punkt](/samples/azure-samples/digital-twins-samples/digital-twins-samples). Du kommer att använda **DeviceSimulator** -projektet på den lagrings platsen.

I självstudierna från slut punkt till slut punkt utför du följande steg:
1. [*Registrera den simulerade enheten med IoT Hub*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*Konfigurera och kör simuleringen*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Verifiera dina resultat

När du kör enhets simulatorn ovan ändras temperatur värdet för din digitala garn. I Azure CLI kör du följande kommando för att visa temperatur svärdet.

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

Dina utdata bör innehålla ett temperatur värde som detta:

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

Om du vill se värde ändringen kör du kommandot query upprepade gånger.

## <a name="next-steps"></a>Nästa steg

Läs om data ingress och utgånget med Azure Digitals:
* [*Koncept: integrering med andra tjänster*](concepts-integration.md)
