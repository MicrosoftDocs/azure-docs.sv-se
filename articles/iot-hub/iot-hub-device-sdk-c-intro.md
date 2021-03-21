---
title: Azure IoT-enhetens SDK för C | Microsoft Docs
description: Kom igång med Azure IoT-enhetens SDK för C och lär dig hur du skapar enhets appar som kommunicerar med en IoT-hubb.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 17b31e365e311b97e322828927827f40733313a6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97588836"
---
# <a name="azure-iot-device-sdk-for-c"></a>Azure IoT-enhetens SDK för C

**Azure IoT Device SDK** är en uppsättning bibliotek som är utformade för att förenkla processen att skicka meddelanden till och ta emot meddelanden från **Azure IoT Hub** -tjänsten. Det finns olika varianter av SDK, som är riktade till en specifik plattform, men den här artikeln beskriver **Azure IoT-enhetens SDK för C**.

> [!NOTE]
> Embedded C SDK är ett alternativ för begränsade enheter som stöder BYON-metoden (ta med din egen nätverks lösning). IoT-utvecklare har frihet att ta MQTT-klienten, TLS och socket som de väljer för att skapa en enhets lösning. [Läs mer om Embedded C SDK](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Azure IoT-enhetens SDK för C är skriven i ANSI C (C99) för att maximera portabiliteten. Den här funktionen gör det lättare för biblioteken att fungera på flera plattformar och enheter, särskilt där det är en prioritet att minimera disk-och minnes utrymmet.

Det finns ett brett utbud av plattformar där SDK har testats (se [Azure-certifierad för IoT-katalogen](https://catalog.azureiotsolutions.com/) för mer information). Även om den här artikeln innehåller en genom gång av exempel kod som körs på Windows-plattformen, är koden som beskrivs i den här artikeln identisk i intervallet med plattformar som stöds.

I följande video presenteras en översikt över Azure IoT SDK för C:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

I den här artikeln beskrivs arkitekturen i Azure IoT-enhetens SDK för C. Den visar hur du initierar enhets biblioteket, skickar data till IoT Hub och tar emot meddelanden från det. Informationen i den här artikeln bör vara tillräckligt för att komma igång med SDK, men innehåller även pekare till ytterligare information om biblioteken.

## <a name="sdk-architecture"></a>SDK-arkitektur

Du hittar [**Azure IoT-enhetens SDK för c**](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen och visar information om API: et i [C API-referensen](/azure/iot-hub/iot-c-sdk-ref/).

Den senaste versionen av biblioteken finns i **huvud** grenen för lagrings platsen:

  ![Skärm bild av lagrings platsens huvud gren](./media/iot-hub-device-sdk-c-intro/RepoMasterBranch.png)

* Kärn implementeringen av SDK finns i **iothub \_** som innehåller implementeringen av det lägsta API-lagret i SDK: **IoTHubClient** -biblioteket. **IoTHubClient** -biblioteket innehåller API: er som implementerar RAW-meddelanden för att skicka meddelanden till IoT Hub och ta emot meddelanden från IoT Hub. När du använder det här biblioteket ansvarar du för att implementera serialisering av meddelanden, men annan information om hur du kommunicerar med IoT Hub hanteras åt dig.

* Mappen **serialiserare** innehåller hjälp funktioner och exempel som visar hur du kan serialisera data innan du skickar dem till Azure IoT Hub med hjälp av klient biblioteket. Användningen av serialiseraren är inte obligatorisk och tillhandahålls som en bekvämlighet. Om du vill använda **serialiserar** -biblioteket definierar du en modell som anger de data som ska skickas till IoT Hub och de meddelanden som du förväntar dig att ta emot från den. När modellen har definierats ger SDK en API-yta som gör det enkelt att arbeta med enhet-till-moln-och moln-till-enhet-meddelanden utan att behöva oroa dig om serialiserings informationen. Biblioteket är beroende av andra bibliotek med öppen källkod som implementerar transport med hjälp av protokoll som MQTT och AMQP.

* **IoTHubClient** -biblioteket är beroende av andra bibliotek med öppen källkod:

  * [Azure C Shared Utility](https://github.com/Azure/azure-c-shared-utility) -biblioteket, som innehåller vanliga funktioner för grundläggande uppgifter (till exempel strängar, list MANIPULATION och IO) som krävs i flera Azure-relaterade C SDK: er.

  * [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) -biblioteket, som är en implementering av AMQP som är optimerade för resurs begränsade enheter, är en implementering på klient sidan av.

  * [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) -biblioteket, som är ett bibliotek för generell användning som IMPLEMENTERAr MQTT-protokollet och är optimerat för resurs begränsade enheter.

Det är lättare att förstå användningen av dessa bibliotek genom att titta på exempel kod. I följande avsnitt får du stegvisa anvisningar genom flera av de exempel program som ingår i SDK: n. Den här genom gången bör ge dig en bättre känsla för de olika funktionerna i hanterings lagren i SDK och en introduktion till hur API: erna fungerar.

## <a name="before-you-run-the-samples"></a>Innan du kör exemplen

Innan du kan köra exemplen i Azure IoT-enhetens SDK för C måste du [skapa en instans av tjänsten IoT Hub](iot-hub-create-through-portal.md) i din Azure-prenumeration. Utför sedan följande uppgifter:

* Förbereda utvecklingsmiljön
* Hämta autentiseringsuppgifter för enheten.

### <a name="prepare-your-development-environment"></a>Förbereda utvecklingsmiljön

Paket tillhandahålls för vanliga plattformar (till exempel NuGet för Windows eller apt_get för Debian och Ubuntu) och exemplen använder dessa paket när de är tillgängliga. I vissa fall måste du kompilera SDK för eller på din enhet. Om du behöver kompilera SDK: n, se [förbereda utvecklings miljön](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) i GitHub-lagringsplatsen.

Hämta exempel koden genom att ladda ned en kopia av SDK från GitHub. Hämta din kopia av källan från **huvud** grenen i GitHub- [lagringsplatsen](https://github.com/Azure/azure-iot-sdk-c).


### <a name="obtain-the-device-credentials"></a>Hämta autentiseringsuppgifter för enheten

Nu när du har exempel käll koden är det som följer att hämta en uppsättning autentiseringsuppgifter för enheten. För att en enhet ska kunna få åtkomst till en IoT-hubb måste du först lägga till enheten i IoT Hub Identity-registret. När du lägger till din enhet får du en uppsättning autentiseringsuppgifter för enheten som du behöver för att enheten ska kunna ansluta till IoT-hubben. Exempel programmen som diskuteras i nästa avsnitt förväntar sig dessa autentiseringsuppgifter i form av en **anslutnings sträng för enheten**.

Det finns flera verktyg med öppen källkod som hjälper dig att hantera din IoT Hub.

* Ett Windows-program som heter [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer).

* Ett Visual Studio Code-tillägg mellan plattformar som kallas [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

* En python CLI med mellan plattformar [som kallas IoT-tillägg för Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

I den här självstudien används det grafiska verktyget *Device Explorer* . Du kan använda *Azure IoT-verktyg för vs Code* om du utvecklar i vs Code. Du kan också använda *IoT-tillägget för Azure CLI 2,0* -verktyget om du föredrar att använda ett CLI-verktyg.

Device Explorer-verktyget använder Azure IoT-tjänstebibliotek för att utföra olika funktioner på IoT Hub, inklusive att lägga till enheter. Om du använder Device Explorer-verktyget för att lägga till en enhet får du en anslutnings sträng för enheten. Du behöver den här anslutnings strängen för att köra exempel programmen.

Om du inte är bekant med verktyget enhets Utforskaren beskriver följande procedur hur du använder det för att lägga till en enhet och hämta en anslutnings sträng för enheten.

1. Information om hur du installerar verktyget Device Explorer finns i [så här använder du Device Explorer för IoT Hub enheter](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/).

1. När du kör programmet visas följande gränssnitt:

   ![Device Explorer dubbla skärm bild](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinConfigTab.png)

1. Ange din **IoT Hub anslutnings sträng** i det första fältet och klicka på **Uppdatera**. Det här steget konfigurerar verktyget så att det kan kommunicera med IoT Hub. 

Du hittar **anslutnings strängen** under **IoT Hub service**  >  **Settings**  >  iothubowner för **delad åtkomst princip**  >  .

1. När IoT Hub anslutnings strängen har kon figurer ATS klickar du på fliken **hantering** :

   ![Skärm bild av Device Explorer dubbla/hantering](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab.png)

På den här fliken hanterar du de enheter som är registrerade i IoT Hub.

1. Du skapar en enhet genom att klicka på knappen **skapa** . En dialog ruta visas med en uppsättning i förväg ifyllda nycklar (primär och sekundär). Ange ett **enhets-ID** och klicka sedan på **skapa**.

   ![Skärm bild för skapa enhet](./media/iot-hub-device-sdk-c-intro/CreateDevice.png)

1. När enheten har skapats uppdateras enhets listan med alla registrerade enheter, inklusive den som du nyss skapade. Om du högerklickar på den nya enheten visas den här menyn:

   ![Device Explorer dubbla resultat av snabb klickning](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab_RightClick.png)

1. Om du väljer **Kopiera anslutnings sträng för den valda enheten** kopieras enhets anslutnings strängen till Urklipp. Behåll en kopia av enhets anslutnings strängen. Du behöver den när du kör exempel programmen som beskrivs i följande avsnitt.

När du har slutfört stegen ovan är du redo att börja köra kod. De flesta exempel har en konstant överst i huvud käll filen som gör att du kan ange en anslutnings sträng. Till exempel visas motsvarande rad från **iothub_client \_ exempel \_ iothub_convenience_sample** programmet på följande sätt.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>Använda IoTHubClient-biblioteket

I **iothub- \_ klientens** mapp i [Azure-IoT-SDK-c-](https://github.com/azure/azure-iot-sdk-c) lagringsplatsen finns det en **exempel** -mapp som innehåller ett program med namnet **iothub \_ client \_ Sample \_ MQTT**.

Windows-versionen av iothub_convenience_sample programmet för **iothub_client \_ exempel \_** innehåller följande Visual Studio-lösning:

  ![Visual Studio-Solution Explorer](./media/iot-hub-device-sdk-c-intro/iothub-client-sample-mqtt.png)

> [!NOTE]
> Om Visual Studio ber dig att nå projektet till den senaste versionen godkänner du uppmaningen.

Den här lösningen innehåller ett enda projekt. Det finns fyra NuGet-paket installerade i den här lösningen:

* Microsoft. Azure. C. SharedUtility
* Microsoft. Azure. IoTHub. MqttTransport
* Microsoft. Azure. IoTHub. IoTHubClient
* Microsoft. Azure. umqtt

Du behöver alltid paketet **Microsoft. Azure. C. SharedUtility** när du arbetar med SDK: n. I det här exemplet används MQTT-protokollet, och därför måste du inkludera paketen **Microsoft. Azure. umqtt** och **Microsoft. Azure. IoTHub. MqttTransport** (det finns motsvarande paket för AMQP och https). Eftersom exemplet använder **IoTHubClient** -biblioteket måste du även inkludera paketet **Microsoft. Azure. IoTHub. IoTHubClient** i din lösning.

Du kan hitta implementeringen för exempel programmet i iothub_client- **\_ exempel \_ iothub_convenience_sample** käll filen.

I följande steg används det här exempel programmet för att vägleda dig genom vad som krävs för att använda **IoTHubClient** -biblioteket.

### <a name="initialize-the-library"></a>Initiera biblioteket

> [!NOTE]
> Innan du börjar arbeta med biblioteken kan du behöva utföra en viss plattformsspecifik initiering. Om du till exempel planerar att använda AMQP på Linux måste du initiera OpenSSL-biblioteket. Exemplen i [GitHub-lagringsplatsen](https://github.com/Azure/azure-iot-sdk-c) anropar verktygets funktions **plattform \_ init** när klienten startar och anropar **Platform \_ deinit** -funktionen innan den avslutas. Dessa funktioner deklareras i huvud filen Platform. h. Undersök definitionerna för dessa funktioner för mål plattformen i [lagrings platsen](https://github.com/Azure/azure-iot-sdk-c) för att avgöra om du behöver ta med valfri plattformsspecifik initierings kod i klienten.

Börja arbeta med biblioteken genom att först allokera en IoT Hub klient referens:

```c
if ((iotHubClientHandle = 
  IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol)) == NULL)
{
    (void)printf("ERROR: iotHubClientHandle is NULL!\r\n");
}
else
{
    ...
```

Du skickar en kopia av enhets anslutnings strängen som du fick från verktyget Device Explorer till den här funktionen. Du anger också det kommunikations protokoll som ska användas. I det här exemplet används MQTT, men AMQP och HTTPS är också alternativ.

När du har en giltig **\_ klient \_ referens för IOTHUB** kan du börja anropa API: erna för att skicka och ta emot meddelanden till och från IoT Hub.

### <a name="send-messages"></a>Skicka meddelanden

Exempel programmet skapar en slinga för att skicka meddelanden till IoT Hub. Följande kodfragment:

- Skapar ett meddelande.
- Lägger till en egenskap i meddelandet.
- Skickar ett meddelande.

Börja med att skapa ett meddelande:

```c
size_t iterator = 0;
do
{
    if (iterator < MESSAGE_COUNT)
    {
        sprintf_s(msgText, sizeof(msgText), "{\"deviceId\":\"myFirstDevice\",\"windSpeed\":%.2f}", avgWindSpeed + (rand() % 4 + 2));
        if ((messages[iterator].messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText))) == NULL)
        {
            (void)printf("ERROR: iotHubMessageHandle is NULL!\r\n");
        }
        else
        {
            messages[iterator].messageTrackingId = iterator;
            MAP_HANDLE propMap = IoTHubMessage_Properties(messages[iterator].messageHandle);
            (void)sprintf_s(propText, sizeof(propText), "PropMsg_%zu", iterator);
            if (Map_AddOrUpdate(propMap, "PropName", propText) != MAP_OK)
            {
                (void)printf("ERROR: Map_AddOrUpdate Failed!\r\n");
            }

            if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messages[iterator].messageHandle, SendConfirmationCallback, &messages[iterator]) != IOTHUB_CLIENT_OK)
            {
                (void)printf("ERROR: IoTHubClient_LL_SendEventAsync..........FAILED!\r\n");
            }
            else
            {
                (void)printf("IoTHubClient_LL_SendEventAsync accepted message [%d] for transmission to IoT Hub.\r\n", (int)iterator);
            }
        }
    }
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1);

    iterator++;
} while (g_continueRunning);
```

Varje gång du skickar ett meddelande anger du en referens till en callback-funktion som anropas när data skickas. I det här exemplet kallas callback-funktionen **SendConfirmationCallback**. Följande fragment visar denna motringnings funktion:

```c
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %zu with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Anteckna anropet till IoTHubMessage-funktionen för att **\_ förstöra** när du är klar med meddelandet. Den här funktionen frigör de resurser som allokeras när du skapade meddelandet.

### <a name="receive-messages"></a>Ta emot meddelanden

Det är en asynkron åtgärd att ta emot ett meddelande. Först registrerar du återanropet för att anropa när enheten tar emot ett meddelande:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext) != IOTHUB_CLIENT_OK)
{
    (void)printf("ERROR: IoTHubClient_LL_SetMessageCallback..........FAILED!\r\n");
}
else
{
    (void)printf("IoTHubClient_LL_SetMessageCallback...successful.\r\n");
    ...
```

Den sista parametern är en annullerad pekare till vad du vill. I exemplet är det en pekare till ett heltal, men det kan vara en pekare till en mer komplex data struktur. Med den här parametern kan motringningsfunktionen fungera på delat tillstånd med anroparen för den här funktionen.

När enheten tar emot ett meddelande anropas den registrerade callback-funktionen. Denna motringnings funktion hämtar:

* Meddelande-ID och korrelations-ID från meddelandet.
* Meddelandets innehåll.
* Eventuella anpassade egenskaper från meddelandet.

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    MAP_HANDLE mapProperties;
    const char* messageId;
    const char* correlationId;

    // Message properties
    if ((messageId = IoTHubMessage_GetMessageId(message)) == NULL)
    {
        messageId = "<null>";
    }

    if ((correlationId = IoTHubMessage_GetCorrelationId(message)) == NULL)
    {
        correlationId = "<null>";
    }

    // Message content
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        (void)printf("unable to retrieve the message data\r\n");
    }
    else
    {
        (void)printf("Received Message [%d]\r\n Message ID: %s\r\n Correlation ID: %s\r\n Data: <<<%.*s>>> & Size=%d\r\n", *counter, messageId, correlationId, (int)size, buffer, (int)size);
        // If we receive the work 'quit' then we stop running
        if (size == (strlen("quit") * sizeof(char)) && memcmp(buffer, "quit", size) == 0)
        {
            g_continueRunning = false;
        }
    }

    // Retrieve properties from the message
    mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                size_t index;

                printf(" Message Properties:\r\n");
                for (index = 0; index < propertyCount; index++)
                {
                    (void)printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                (void)printf("\r\n");
            }
        }
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Använd funktionen **IoTHubMessage \_ GetByteArray** för att hämta meddelandet, som i det här exemplet är en sträng.

### <a name="uninitialize-the-library"></a>Avinitiera biblioteket

När du är klar med att skicka händelser och ta emot meddelanden kan du avinitiera IoT-biblioteket. Det gör du genom att utfärda följande funktions anrop:

```c
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Det här anropet frigör de resurser som tidigare allokerats av funktionen **IoTHubClient \_ CreateFromConnectionString** .

Som du kan se är det enkelt att skicka och ta emot meddelanden med **IoTHubClient** -biblioteket. Biblioteket hanterar information om hur du kommunicerar med IoT Hub, inklusive vilket protokoll som ska användas (från utvecklarens perspektiv är detta ett enkelt konfigurations alternativ).

**IoTHubClient** -biblioteket ger också exakt kontroll över hur du kan serialisera de data som enheten skickar till IoT Hub. I vissa fall är den här kontroll nivån en fördel, men i andra är det en implementerings information som du inte vill ska vara berörd. I så fall kan du överväga att använda **serialiseraren** , som beskrivs i nästa avsnitt.

## <a name="use-the-serializer-library"></a>Använd serialiserar biblioteket

Det konceptuella biblioteket i **serialiseraren** finns ovanpå **IoTHubClient** -biblioteket i SDK: n. Den använder **IoTHubClient** -biblioteket för den underliggande kommunikationen med IoT Hub, men den lägger till modellerings funktioner som tar bort belastningen för att hantera meddelande serialisering från utvecklaren. Hur det här biblioteket fungerar bäst visas i ett exempel.

I mappen **serialiserare** i [Azure-IoT-SDK-c-lagringsplatsen](https://github.com/Azure/azure-iot-sdk-c)är en **exempel** -mapp som innehåller ett program som heter **simplesample \_ MQTT**. Windows-versionen av det här exemplet innehåller följande Visual Studio-lösning:

  ![Visual Studio-lösning för MQTT-exempel](./media/iot-hub-device-sdk-c-intro/simplesample_mqtt.png)

> [!NOTE]
> Om Visual Studio ber dig att nå projektet till den senaste versionen godkänner du uppmaningen.

Som i föregående exempel innehåller detta ett antal NuGet-paket:

* Microsoft. Azure. C. SharedUtility
* Microsoft. Azure. IoTHub. MqttTransport
* Microsoft. Azure. IoTHub. IoTHubClient
* Microsoft. Azure. IoTHub. serialiserare
* Microsoft. Azure. umqtt

Du har sett de flesta av de här paketen i föregående exempel, men **Microsoft. Azure. IoTHub. Serialization** är nytt. Det här paketet krävs när du använder **serialiserar** -biblioteket.

Du hittar implementeringen av exempel programmet i **\_ \_ iothub_convenience_samples filen iothub_client samples** .

I följande avsnitt får du stegvisa anvisningar genom de viktigaste delarna i det här exemplet.

### <a name="initialize-the-library"></a>Initiera biblioteket

Om du vill börja arbeta med **serialiserar** -biblioteket anropar du initierings-API: erna:

```c
if (serializer_init(NULL) != SERIALIZER_OK)
{
    (void)printf("Failed on serializer_init\r\n");
}
else
{
    IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol);
    srand((unsigned int)time(NULL));
    int avgWindSpeed = 10;

    if (iotHubClientHandle == NULL)
    {
        (void)printf("Failed on IoTHubClient_LL_Create\r\n");
    }
    else
    {
        ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
        if (myWeather == NULL)
        {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
        }
        else
        {
...
```

Anropet till funktionen **Serialization \_ init** är ett engångs anrop och initierar det underliggande biblioteket. Sedan anropar du funktionen **IoTHubClient \_ lla \_ CreateFromConnectionString** , som är samma API som i exemplet på **IoTHubClient** . Det här anropet anger enhets anslutnings strängen (detta anrop är också där du väljer det protokoll som du vill använda). Det här exemplet använder MQTT som transport, men kan använda AMQP eller HTTPS.

Anropa slutligen funktionen för att **skapa \_ modell \_ instans** . **WeatherStation** är namn området för modellen och **ContosoAnemometer** är namnet på modellen. När modell instansen har skapats kan du använda den för att börja skicka och ta emot meddelanden. Det är dock viktigt att förstå vad en modell är.

### <a name="define-the-model"></a>Definiera modellen

En modell i **serialiseraren** definierar de meddelanden som enheten kan skicka till IoT Hub och meddelandena, som kallas *åtgärder* i det modellerings språk som den kan ta emot. Du definierar en modell med en uppsättning C-makron som i **iothub_client \_ exempel \_ iothub_convenience_sample** exempel program:

```c
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(int, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Makrona **BEGIN \_ namespace** och **End \_ namespace** tar båda ut namn området för modellen som ett argument. Det förväntas att allt mellan dessa makron är definitionen av din modell eller dina modeller och de data strukturer som modeller använder.

I det här exemplet finns det en enda modell som heter **ContosoAnemometer**. Den här modellen definierar två data typer som din enhet kan skicka till IoT Hub: **DeviceID** och **WindSpeed**. Den definierar också tre åtgärder (meddelanden) som enheten kan ta emot: **TurnFanOn**, **TurnFanOff** och **SetAirResistance**. Varje data element har en typ och varje åtgärd har ett namn (och eventuellt en uppsättning parametrar).

De data och åtgärder som definierats i modellen definierar en API-yta som du kan använda för att skicka meddelanden till IoT Hub och svara på meddelanden som skickas till enheten. Användning av den här modellen går bra att förstå genom ett exempel.

### <a name="send-messages"></a>Skicka meddelanden

Modellen definierar de data som du kan skicka till IoT Hub. I det här exemplet innebär det ett av de två data objekt som definieras med hjälp av **WITH_DATA** makrot. Det finns flera steg som krävs för att skicka **DeviceID** -och **WindSpeed** -värden till en IoT-hubb. Det första är att ange de data som du vill skicka:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

Den modell som du definierade tidigare gör det möjligt att ange värden genom att ställa in medlemmar i en **struktur**. Serialisera sedan det meddelande som du vill skicka:

```c
unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, myWeather->DeviceId, myWeather->WindSpeed) != CODEFIRST_OK)
{
    (void)printf("Failed to serialize\r\n");
}
else
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
    free(destination);
}
```

Den här koden serialiserar enhet till molnet till en buffert (refereras av **målet**). Koden anropar sedan funktionen **SendMessage** för att skicka meddelandet till IoT Hub:

```c
static void sendMessage(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle == NULL)
    {
        printf("unable to create a new IoTHubMessage\r\n");
    }
    else
    {
        if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }
        IoTHubMessage_Destroy(messageHandle);
    }
    messageTrackingId++;
}
```

Den andra till sista parametern för **IoTHubClient \_ lla \_ SendEventAsync** är en referens till en callback-funktion som anropas när data har skickats. Här är motringningsfunktionen i exemplet:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

Den andra parametern är en pekare till användar kontexten. samma pekare skickades till **IoTHubClient \_ lla \_ SendEventAsync**. I det här fallet är kontexten en enkel räknare, men det kan vara vad du vill.

Det är allt du behöver för att skicka meddelanden från enheten till molnet. Det enda som återstår att ta emot är att ta emot meddelanden.

### <a name="receive-messages"></a>Ta emot meddelanden

Att ta emot ett meddelande fungerar på liknande sätt som meddelanden fungerar i **IoTHubClient** -biblioteket. Först registrerar du en motringnings funktion för meddelanden:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, 
  IoTHubMessage, myWeather) != IOTHUB_CLIENT_OK)
{
    printf("unable to IoTHubClient_SetMessageCallback\r\n");
}
else
{
...
```

Sedan skriver du callback-funktionen som anropas när ett meddelande tas emot:

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = IOTHUBMESSAGE_ABANDONED;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = IOTHUBMESSAGE_ABANDONED;
        }
        else
        {
            (void)memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

Den här koden är ett exempel på samma sak för alla lösningar. Den här funktionen tar emot meddelandet och tar hand om att vidarebefordra det till lämplig funktion via **\_ kommandot** anropa för att köra. Funktionen som anropas i den här punkten beror på definitionen av åtgärderna i din modell.

När du definierar en åtgärd i modellen måste du implementera en funktion som anropas när enheten tar emot motsvarande meddelande. Om din modell till exempel definierar den här åtgärden:

```c
WITH_ACTION(SetAirResistance, int, Position)
```

Definiera en funktion med den här signaturen:

```c
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Observera hur namnet på funktionen matchar namnet på åtgärden i modellen och att parametrarna för funktionen matchar de parametrar som har angetts för åtgärden. Den första parametern krävs alltid och innehåller en pekare till instansen av din modell.

När enheten tar emot ett meddelande som matchar den här signaturen anropas motsvarande funktion. Därför är det inte allt från att behöva inkludera standard koden från **IoTHubMessage** och det är bara en fråga om att definiera en enkel funktion för varje åtgärd som definierats i din modell.

### <a name="uninitialize-the-library"></a>Avinitiera biblioteket

När du är klar med att skicka data och ta emot meddelanden kan du avinitiera IoT-biblioteket:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Var och en av dessa tre funktioner justeras med de tre initierings funktioner som beskrivs ovan. Genom att anropa dessa API: er ser du till att du kostnads fritt tidigare allokerade resurser

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs grunderna för att använda biblioteken i **Azure IoT-enhetens SDK för C**. Du får tillräckligt med information för att förstå vad som ingår i SDK, dess arkitektur och hur du kommer igång med Windows-exemplen. Nästa artikel fortsätter beskrivningen av SDK genom [att förklara mer om IoTHubClient-biblioteket](iot-hub-device-sdk-c-iothubclient.md).

Mer information om hur du utvecklar för IoT Hub finns i [Azure IoT SDK](iot-hub-devguide-sdks.md): er.

För att ytterligare utforska funktionerna i IoT Hub, se:

* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/quickstart-linux.md)
