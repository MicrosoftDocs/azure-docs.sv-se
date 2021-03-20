---
title: Azure IoT-enhetens SDK för C-IoTHubClient | Microsoft Docs
description: Så här använder du IoTHubClient-biblioteket i Azure IoT-enhetens SDK för C för att skapa enhets program som kommunicerar med en IoT-hubb.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: f33521dd9110d7ba6ee84650345b38c8c6a4950b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92149141"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Azure IoT-enhetens SDK för C – mer om IoTHubClient

[Azure IoT-enhetens SDK för c](iot-hub-device-sdk-c-intro.md) är den första artikeln i den här serien som presenterar **Azure IoT-enhetens SDK för c**. Den artikeln förklaras att det finns två arkitektur lager i SDK. I bas är **IoTHubClient** -biblioteket som direkt hanterar kommunikation med IoT Hub. Det finns också ett **serialiserande** bibliotek som bygger ovanpå det för att tillhandahålla serialiserings tjänster. I den här artikeln ger vi ytterligare information om **IoTHubClient** -biblioteket.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Föregående artikel som beskriver hur du använder **IoTHubClient** -biblioteket för att skicka händelser till IoT Hub och ta emot meddelanden. Den här artikeln utökar diskussionen genom att förklara hur du bättre hanterar *när* du skickar och tar emot data, så att du kan introducera **API: erna på lägre nivå**. Vi förklarar också hur du kopplar egenskaper till händelser (och hämtar dem från meddelanden) med egenskaps hanterings funktionerna i **IoTHubClient** -biblioteket. Slutligen ger vi ytterligare förklaringar av olika sätt att hantera meddelanden som tas emot från IoT Hub.

Artikeln avslutas genom att omfatta ett par olika ämnen, inklusive mer information om autentiseringsuppgifter för enheten och hur du ändrar beteendet för **IoTHubClient** via konfigurations alternativ.

Vi använder **IoTHubClient** SDK-exemplen för att förklara dessa ämnen. Om du vill följa med i **iothub \_ \_ -klient exemplet \_ http-** och iothub-klient exempel program som ingår i Azure IoT-enhetens SDK för C. allt som beskrivs i följande avsnitt visas i exemplen. **\_ \_ \_**

Du hittar [**Azure IoT-enhetens SDK för c**](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen och visar information om API: et i [C API-referensen](/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-lower-level-apis"></a>API: er på lägre nivå

I föregående artikel beskrivs den grundläggande åtgärden i **IotHubClient** inom kontexten för AMQP- **programmet \_ iothub \_ client \_** . Till exempel förklaras hur du initierar biblioteket med hjälp av den här koden.

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Det beskrivs också hur du skickar händelser med hjälp av det här funktions anropet.

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

Artikeln beskriver också hur du tar emot meddelanden genom att registrera en motringnings funktion.

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

Artikeln visade också hur du kostnads fritt resurser använder kod som följande.

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

Det finns funktioner för var och en av dessa API: er:

* IoTHubClient \_ lla \_ CreateFromConnectionString
* IoTHubClient \_ lla \_ SendEventAsync
* IoTHubClient \_ lla \_ SetMessageCallback
* IoTHubClient \_ lla att \_ förstöra

Dessa funktioner alla inkluderar **lla** i API-namnet. Andra delar **av** namnet, parametrarna för var och en av dessa funktioner är identiska med de övriga motsvarigheterna till varandra. Beteendet för dessa funktioner är dock annorlunda på ett viktigt sätt.

När du anropar **IoTHubClient \_ CreateFromConnectionString** skapar de underliggande biblioteken en ny tråd som körs i bakgrunden. Den här tråden skickar händelser till och tar emot meddelanden från IoT Hub. Ingen sådan tråd skapas när du arbetar med **lla** API: er. Att skapa en bakgrunds tråd är en bekvämlighet för utvecklaren. Du behöver inte bekymra dig om att uttryckligen skicka händelser och ta emot meddelanden från IoT Hub – det sker automatiskt i bakgrunden. Dessutom ger **lla** API: er en uttrycklig kontroll över kommunikationen med IoT Hub, om du behöver den.

För att förstå det här konceptet bättre, ska vi titta på ett exempel:

När du anropar **IoTHubClient \_ SendEventAsync** placerar du händelsen i en buffert. Den bakgrunds tråd som skapas när du anropar **IoTHubClient \_ CreateFromConnectionString** övervakar hela tiden den här bufferten och skickar alla data som den innehåller för att IoT Hub. Detta sker i bakgrunden på samma gång som huvud tråden utför annat arbete.

När du registrerar en callback-funktion för meddelanden med hjälp av **IoTHubClient \_ SetMessageCallback**, instrueras du till SDK att låta bakgrunds tråden anropa motringningsfunktionen när ett meddelande tas emot, oberoende av huvud tråden.

**Lla** API: er skapar inte en bakgrunds tråd. I stället måste ett nytt API anropas för att explicit skicka och ta emot data från IoT Hub. Detta visas i följande exempel.

**Iothub \_ \_ -klientens \_ http** -program som ingår i SDK visar de lågnivå-API: erna. I det exemplet skickar vi händelser till IoT Hub med kod som följande:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

De tre första raderna skapar meddelandet och den sista raden skickar händelsen. Som tidigare nämnts innebär det att när händelsen skickades innebär det att data bara placeras i en buffert. Inget överförs i nätverket när vi kallar **IoTHubClient \_ lla \_ SendEventAsync**. Du måste anropa **IoTHubClient \_ lla \_ DoWork**, som i det här exemplet, om du vill att data ska intränga till IoT Hub:

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Den här koden (från **iothub \_ -klientens \_ exempel \_ -http-** program) anropar upprepade gånger **IoTHubClient \_ lla \_ DoWork**. Varje tid **IoTHubClient \_ lla \_ DoWork** anropas vissa händelser från bufferten till IoT Hub och det hämtar ett köade meddelande som skickas till enheten. Det sistnämnda fallet innebär att om vi har registrerat en callback-funktion för meddelanden, anropas återanropet (förutsatt att alla meddelanden är köade). Vi skulle ha registrerat en sådan callback-funktion med kod som följande:

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

Anledningen till att **IoTHubClient \_ lla \_ DoWork** ofta anropas i en loop är att varje tidpunkt det kallas, skickar *vissa* buffrade händelser till IoT Hub och hämtar *Nästa* meddelande i kö för enheten. Varje anrop garanterar inte att skicka alla buffrade händelser eller hämta alla köade meddelanden. Om du vill skicka alla händelser i bufferten och sedan fortsätta med annan bearbetning kan du ersätta den här slingan med kod som följande:

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Den här koden anropar **IoTHubClient \_ lla \_ DoWork** tills alla händelser i bufferten har skickats till IoT Hub. Observera att detta inte också innebär att alla köade meddelanden har tagits emot. En del av orsaken till detta är att kontrollen av "alla"-meddelanden inte är lika deterministisk som en åtgärd. Vad händer om du hämtar alla meddelanden, men ett annat skickas till enheten direkt efter? Ett bättre sätt att hantera med det är med programmerad tids gräns. Till exempel kan funktionen motringning för meddelande återställa en timer varje gång den startas. Du kan sedan skriva logiken för att fortsätta bearbeta om till exempel inga meddelanden har tagits emot under de senaste *X* sekunderna.

När du är klar med ingångs händelser och tar emot meddelanden, måste du anropa motsvarande funktion för att rensa resurser.

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

I princip finns det bara en uppsättning API: er för att skicka och ta emot data med en bakgrunds tråd och en annan uppsättning API: er som gör samma sak utan bakgrunds tråd. Många utvecklare kan föredra de icke-lla API: erna, men de lågnivå-API: erna är användbara när utvecklaren vill ha explicit kontroll över nätverks överföringarna. Till exempel kan vissa enheter samla in data över tid och bara inträngande händelser vid angivna intervall (till exempel en gång i timmen eller en gång per dag). De lågnivå-API: erna ger dig möjligheten att explicit styra när du skickar och tar emot data från IoT Hub. Andra föredrar att enkelt göra det enklare att tillhandahålla API: er på lägre nivå. Allt händer i huvud tråden i stället för ett arbete som sker i bakgrunden.

Oavsett vilken modell du väljer, måste du se till att du är konsekvent med vilka API: er som du använder. Om du börjar genom att anropa **IoTHubClient \_ lla \_ CreateFromConnectionString**, se till att du bara använder motsvarande lågnivå-API: er för alla uppföljnings arbeten:

* IoTHubClient \_ lla \_ SendEventAsync
* IoTHubClient \_ lla \_ SetMessageCallback
* IoTHubClient \_ lla att \_ förstöra
* IoTHubClient \_ lla \_ DoWork

Det motsatta är också sant. Om du börjar med **IoTHubClient \_ CreateFromConnectionString** kan du använda de icke-lla API: erna för ytterligare bearbetning.

I Azure IoT-enhetens SDK för C, se **iothub \_ -klientens \_ exempel \_ -http-** program för ett komplett exempel på de lågnivå-API: erna. **Iothub- \_ \_ \_ AMQP** program kan refereras till i ett fullständigt exempel på icke-lla API: er.

## <a name="property-handling"></a>Egenskaps hantering

Så långt som vi har beskrivit att skicka data, har vi refererat till bröd texten i meddelandet. Ta till exempel den här koden:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Det här exemplet skickar ett meddelande till IoT Hub med texten "Hello World". Men IoT Hub också tillåta att egenskaper bifogas till varje meddelande. Egenskaper är namn/värde-par som kan kopplas till meddelandet. Vi kan till exempel ändra föregående kod för att koppla en egenskap till meddelandet:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Vi börjar med att anropa **IoTHubMessage- \_ Egenskaper** och skickar det till meddelandet. Vad vi får tillbaka är en **referens \_ till karta** som gör att vi kan börja lägga till egenskaper. Den senare åstadkommer du genom att anropa **Map- \_ AddOrUpdate**, som tar en referens till ett kart \_ handtag, egenskaps namnet och egenskap svärdet. Med detta API kan vi lägga till så många egenskaper som vi vill.

När händelsen läses från **Event Hubs** kan mottagaren räkna upp egenskaperna och hämta motsvarande värden. I .NET kan du till exempel åstadkomma detta genom att öppna [egenskaps samlingen på EventData-objektet](/dotnet/api/microsoft.servicebus.messaging.eventdata).

I föregående exempel kopplar vi egenskaper till en händelse som vi skickar till IoT Hub. Egenskaper kan också bifogas till meddelanden som tas emot från IoT Hub. Om vi vill hämta egenskaper från ett meddelande kan vi använda kod som följande i funktionen motringning av meddelande:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

Anropet till **IoTHubMessage- \_ Egenskaper** Returnerar referensen för referensen till **kartan \_** . Därefter skickar vi en referens till **Map- \_ GetInternals** för att få en referens till en matris med namn/värde-par (samt antalet egenskaper). I det här läget är det enkelt att räkna upp egenskaperna för att komma till de värden som vi vill ha.

Du behöver inte använda egenskaper i ditt program. Men om du behöver ange dem för händelser eller hämta dem från meddelanden gör **IoTHubClient** -biblioteket det enkelt.

## <a name="message-handling"></a>Meddelandehantering

Som tidigare nämnts, när meddelanden tas emot från IoT Hub **IoTHubClient** -biblioteket, svarar genom att anropa en registrerad callback-funktion. Det finns en retur parameter för den här funktionen som förtjänar ytterligare förklaring. Här är ett utdrag av motringningsfunktionen i **iothub \_ -klientens exempel program för \_ \_ http-** exempel:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Observera att RETUR typen är **IOTHUBMESSAGE \_ dispositions \_ resultat** och i det här fallet returnerar vi **IOTHUBMESSAGE \_ Accepted**. Det finns andra värden som kan returneras från den här funktionen som ändrar hur **IoTHubClient** -biblioteket reagerar på återanrop till meddelande. Här är alternativen.

* **IOTHUBMESSAGE \_ GODKÄND** – meddelandet har bearbetats. **IoTHubClient** -biblioteket kommer inte att anropa motringningsfunktionen igen med samma meddelande.

* **IOTHUBMESSAGE \_ Avvisad** – meddelandet bearbetades inte och det finns ingen önskan att göra det i framtiden. **IoTHubClient** -biblioteket ska inte anropa callback-funktionen igen med samma meddelande.

* **IOTHUBMESSAGE \_ Övergivna** – meddelandet bearbetades inte korrekt, men **IoTHubClient** -biblioteket bör anropa motringningsfunktionen igen med samma meddelande.

För de första två retur koderna skickar **IoTHubClient** -biblioteket ett meddelande till IoT Hub som anger att meddelandet ska tas bort från enhets kön och inte levereras igen. Netto resultatet är detsamma (meddelandet tas bort från enhets kön), men om meddelandet har accepterats eller avvisats fortfarande har registrerats.  Att spela upp den här skillnaden är användbart för att skicka meddelandet som kan lyssna efter feedback och ta reda på om en enhet har accepterat eller avvisat ett visst meddelande.

I det senaste fallet skickas ett meddelande även till IoT Hub, men det anger att meddelandet ska skickas igen. Normalt kommer du att överge ett meddelande om du stöter på ett fel, men vill försöka bearbeta meddelandet igen. Att avvisa ett meddelande är däremot lämpligt när du stöter på ett oåterkalleligt fel (eller om du bara bestämmer dig för att inte fortsätta att bearbeta meddelandet).

Du måste i alla fall vara medveten om de olika retur koderna så att du kan använda det beteende du vill ha i **IoTHubClient** -biblioteket.

## <a name="alternate-device-credentials"></a>Alternativa autentiseringsuppgifter för enhet

Som tidigare nämnts är det första du ska göra när du arbetar med **IoTHubClient** -biblioteket för att hämta en **IOTHUB \_ klient \_ referens** med ett anrop, till exempel följande:

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Argumenten till **IoTHubClient \_ CreateFromConnectionString** är enhets anslutnings strängen och en parameter som anger det protokoll som vi använder för att kommunicera med IoT Hub. Enhets anslutnings strängen har ett format som visas på följande sätt:

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Det finns fyra delar av informationen i den här strängen: IoT Hub namn, IoT Hub suffix, enhets-ID och delad åtkomst nyckel. Du får det fullständigt kvalificerade domän namnet (FQDN) för en IoT-hubb när du skapar din IoT Hub-instans i Azure Portal – detta ger IoT Hub-namnet (den första delen av FQDN) och IoT Hub-suffixet (resten av FQDN). Du får enhets-ID och den delade åtkomst nyckeln när du registrerar din enhet med IoT Hub (enligt beskrivningen i [föregående artikel](iot-hub-device-sdk-c-intro.md)).

**IoTHubClient \_ CreateFromConnectionString** ger dig ett sätt att initiera biblioteket. Om du vill kan du skapa en ny **\_ klient \_ referens för IOTHUB** med hjälp av dessa enskilda parametrar i stället för enhets anslutnings strängen. Detta uppnås med följande kod:

```C
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Detta utför samma sak som **IoTHubClient \_ CreateFromConnectionString**.

Det kan förefalla självklart att du vill använda **IoTHubClient \_ CreateFromConnectionString** snarare än den här mer utförliga metoden för initiering. Tänk dock på att när du registrerar en enhet i IoT Hub det du får ett enhets-ID och en enhets nyckel (inte en anslutnings sträng). *Device Explorer* SDK-verktyget som introducerades i [föregående artikel](iot-hub-device-sdk-c-intro.md) använder bibliotek i **Azure IoT service SDK** för att skapa enhets anslutnings strängen från enhets-ID, enhets nyckel och IoT Hub värdnamn. Det kan vara bättre att anropa **IoTHubClient \_ ll- \_ create** eftersom det sparar en anslutnings sträng. Använd den metod som är praktisk.

## <a name="configuration-options"></a>Konfigurationsalternativ

Hittills beskrivet allt om hur **IoTHubClient** -biblioteket fungerar reflekterar dess standard beteende. Det finns dock några alternativ som du kan ange för att ändra hur biblioteket fungerar. Detta åstadkommer du genom att använda **IoTHubClient \_ lla \_ SetOption** -API: et. Ta det här exemplet:

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Det finns ett par alternativ som ofta används:

* **SetBatching** (bool) – om **värdet är true** skickas data som skickas till IoT Hub i batchar. Om **värdet är false** skickas meddelanden individuellt. Standardvärdet är **false**. Batching över AMQP/AMQP-WS, samt att lägga till system egenskaper på D2C-meddelanden, stöds.

* **Tids gräns** (osignerad int) – det här värdet representeras i millisekunder. Om det tar längre tid att skicka en HTTPS-begäran eller ta emot ett svar tar det längre tid att ansluta till anslutnings tiden.

Batch-alternativet är viktigt. Som standard tar biblioteket ingående händelser individuellt (en enda händelse är vad du skickar till **IoTHubClient \_ lla \_ SendEventAsync**). Om batch-alternativet är **Sant** samlar biblioteket in så många händelser som möjligt från bufferten (upp till den maximala meddelande storlek som IoT Hub accepterar).  Händelse batchen skickas till IoT Hub i ett enda HTTPS-anrop (de enskilda händelserna paketeras i en JSON-matris). Att aktivera batchbearbetning ger normalt stora prestanda vinster eftersom du minskar antalet nätverks fördröjningar. Det minskar också avsevärt bandbredden eftersom du skickar en uppsättning med HTTPS-huvuden med en händelse grupp i stället för en uppsättning huvuden för varje enskild händelse. Om du inte har en annan anledning att göra det, vanligt vis vill du aktivera batching.

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskriver i detalj hur **IoTHubClient** -biblioteket fungerar i **Azure IoT-enhetens SDK för C**. Med den här informationen bör du ha en god förståelse för funktionerna i **IoTHubClient** -biblioteket. Den andra artikeln i serien är [Azure IoT-enhets-SDK för C-serialiserare](iot-hub-device-sdk-c-serializer.md), vilket ger liknande information i **serialiserar** -biblioteket.

Mer information om hur du utvecklar för IoT Hub finns i [Azure IoT SDK](iot-hub-devguide-sdks.md): er.

För att ytterligare utforska funktionerna i IoT Hub, se [distribuera AI till gräns enheter med Azure IoT Edge](../iot-edge/quickstart-linux.md).