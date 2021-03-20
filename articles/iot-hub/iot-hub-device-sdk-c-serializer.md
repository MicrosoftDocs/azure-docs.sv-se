---
title: Azure IoT-enhetens SDK för C-serialiserare | Microsoft Docs
description: Så här använder du serialiseraren i Azure IoT Device SDK för C för att skapa enhets program som kommunicerar med en IoT-hubb.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 09/06/2016
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: f52d1d1c5f264550076688d5e25e110de230eff4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92152228"
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>Azure IoT-enhetens SDK för C – mer om serialiserare

Den första artikeln i serien introducerade [introduktionen till Azure IoT-enhetens SDK för C](iot-hub-device-sdk-c-intro.md). Nästa artikel innehöll en mer detaljerad beskrivning av [Azure IoT-enhetens SDK för C--IoTHubClient](iot-hub-device-sdk-c-iothubclient.md). I den här artikeln slutförs täckningen av SDK genom att tillhandahålla en mer detaljerad beskrivning av den återstående komponenten: **serialiserar** bibliotek.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Den inledande artikeln beskriver hur du använder **serialiserar** -biblioteket för att skicka händelser till och ta emot meddelanden från IoT Hub. I den här artikeln utökar vi diskussionen genom att tillhandahålla en mer fullständig förklaring av hur dina data modelleras med hjälp av makro språket för **serialiseraren** . Artikeln innehåller också mer information om hur biblioteket serialiserar meddelanden (och i vissa fall hur du kan styra Serialiseringens beteende). Vi beskriver också vissa parametrar som du kan ändra som bestämmer storleken på de modeller som du skapar.

Slutligen kommer artikeln att gå vidare till vissa avsnitt som beskrivs i föregående artiklar, till exempel meddelande-och egenskaps hantering. Som vi lär dig fungerar dessa funktioner på samma sätt med hjälp av **serialiserar** -biblioteket som i **IoTHubClient** -biblioteket.

Allt som beskrivs i den här artikeln baseras på SDK-exemplen för **serialiseraren** . Om du vill följa med, se simplesample- **\_ AMQP** och **simplesample \_ http-** program som ingår i Azure IoT-enhetens SDK för C.

Du hittar [**Azure IoT-enhetens SDK för c**](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen och visar information om API: et i [C API-referensen](/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-modeling-language"></a>Modell språket

[Azure IoT-enhetens SDK för c](iot-hub-device-sdk-c-intro.md) -artikeln i den här serien introducerade **Azure IoT-enhetens SDK för c** -modellerings språk genom exemplet som finns i **simplesample \_ AMQP** -programmet:

```C
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Som du kan se baseras modell språket på C-makron. Du börjar alltid med att inleda din definition med **BEGIN \_ namespace** och slutar alltid med **End \_ namespace**. Det är vanligt att namnge namn området för ditt företag eller, som i det här exemplet, det projekt som du arbetar med.

Det som går inuti namn området är modell definitioner. I det här fallet finns det en enda modell för en anemometer. När du har fått en gång till modellen kan du namnge allt, men vanligt vis används modellen för den enhet eller typ av data som du vill byta till IoT Hub.  

Modeller innehåller en definition av de händelser som du kan komma in på att IoT Hub ( *data*) samt de meddelanden som du kan ta emot från IoT Hub ( *åtgärderna*). Som du kan se från exemplet har händelser en typ och ett namn. åtgärder har ett namn och valfria parametrar (var och en med en typ).

Vad som inte visas i det här exemplet är ytterligare data typer som stöds av SDK: n. Vi tar den härnäst.

> [!NOTE]
> IoT Hub refererar till de data som en enhet skickar till den som *händelser*, medan modell språket refererar till den som *data* (definieras med **WITH_DATA**). På samma sätt refererar IoT Hub till de data som du skickar till enheter som *meddelanden*, medan modellerings språket refererar till den som *åtgärder* (definieras med **WITH_ACTION**). Tänk på att dessa villkor kan användas utbytbart i den här artikeln.
> 
> 

## <a name="supported-data-types"></a>Datatyper som stöds

Följande data typer stöds i modeller som har skapats med **serialiserar** -biblioteket:

| Typ | Beskrivning |
| --- | --- |
| double |Double precision flytt ALS nummer |
| int |32-bitars heltal |
| flyt |flytt ALS nummer med enkel precision |
| long |långt heltal |
| Int8 \_ t |8-bitars heltal |
| Int16 \_ t |16-bitars heltal |
| Int32 \_ t |32-bitars heltal |
| Int64 \_ t |64-bitars heltal |
| boolesk |boolean |
| ASCII- \_ tecken \_ PTR |ASCII-sträng |
| \_ \_ tids \_ förskjutning för EDM-datum |datum/tid för förskjutning |
| EDM- \_ GUID |GUID |
| EDM- \_ binär |binary |
| DEKLARERA \_ struct |komplex data typ |

Vi börjar med den senaste data typen. Med **Declare- \_ struct** kan du definiera komplexa data typer, som är grupperingar av andra primitiva typer. Med dessa grupperingar kan vi definiera en modell som ser ut så här:

```C
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

Vår modell innehåller en enskild data händelse av typen **TestType**. **TestType** är en komplex typ som innehåller flera medlemmar, som sammanvisar de primitiva typer som stöds av språket för **serialiserade** modeller.

Med en modell som detta kan vi skriva kod för att skicka data till IoT Hub som visas på följande sätt:

```C
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

I princip tilldelar vi ett värde till varje medlem i **test** strukturen och anropar sedan **SendAsync** för att skicka **test** data händelsen till molnet. **SendAsync** är en hjälp funktion som skickar en enskild data händelse till IoT Hub:

```C
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
    unsigned char* destination;
    size_t destinationSize;
    if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
    {
        // null terminate the string
        char* destinationAsString = (char*)malloc(destinationSize + 1);
        if (destinationAsString != NULL)
        {
            memcpy(destinationAsString, destination, destinationSize);
            destinationAsString[destinationSize] = '\0';
            IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
            if (messageHandle != NULL)
            {
                IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

                IoTHubMessage_Destroy(messageHandle);
            }
            free(destinationAsString);
        }
        free(destination);
    }
}
```

Den här funktionen serialiserar den aktuella data händelsen och skickar den till IoT Hub med hjälp av **IoTHubClient \_ SendEventAsync**. Detta är samma kod som beskrivs i föregående artiklar (**SendAsync** kapslar in logiken i en praktisk funktion).

En annan hjälp funktion som används i föregående kod är **GetDateTimeOffset**. Den här funktionen omvandlar den aktuella tiden till ett värde av typen **EDM- \_ datum och \_ tids \_ förskjutning**:

```C
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
    struct tm newTime;
    gmtime_s(&newTime, &time);
    EDM_DATE_TIME_OFFSET dateTimeOffset;
    dateTimeOffset.dateTime = newTime;
    dateTimeOffset.fractionalSecond = 0;
    dateTimeOffset.hasFractionalSecond = 0;
    dateTimeOffset.hasTimeZone = 0;
    dateTimeOffset.timeZoneHour = 0;
    dateTimeOffset.timeZoneMinute = 0;
    return dateTimeOffset;
}
```

Om du kör den här koden skickas följande meddelande till IoT Hub:

```C
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Observera att serialiseringen är i JSON, vilket är det format som genereras av **serialiserar** -biblioteket. Observera också att varje medlem i det serialiserade JSON-objektet matchar medlemmarna i **TestType** som vi definierade i vår modell. Värdena matchar också exakt de som används i koden. Observera dock att binära data är base64-kodade: "AQID" är base64-kodningen för {0x01, protokollnumret 0x02, 0x03}.

Det här exemplet visar fördelen med att använda **serialiseraren** – det gör att vi kan skicka JSON till molnet utan att behöva hantera serialisering i vårt program. Allt vi behöver bekymra dig om är att ställa in värdena för data händelser i vår modell och sedan anropa enkla API: er för att skicka dessa händelser till molnet.

Med den här informationen kan vi definiera modeller som innehåller intervallet av data typer som stöds, inklusive komplexa typer (vi kan till och med använda komplexa typer i andra komplexa typer). Den serialiserade JSON som genereras av exemplet ovan ger dock en viktig punkt. *Hur* vi skickar data med **serialiserar** biblioteket bestämmer exakt hur JSON-filen bildas. Den specifika punkten är det vi ska se härnäst.

## <a name="more-about-serialization"></a>Mer om serialisering

I föregående avsnitt beskrivs ett exempel på utdata som genereras av **serialiseraren** . I det här avsnittet förklarar vi hur biblioteket serialiserar data och hur du kan styra beteendet med hjälp av API: erna för serialisering.

Vi samarbetar med en ny modell som baseras på en termostat för att gå vidare med diskussionen om serialisering. Börja med att ge en bakgrund på det scenario vi försöker åtgärda.

Vi vill modellera en termostat som mäter temperatur och fuktighet. Varje data del kommer att skickas till IoT Hub olika. Som standard tar termostat ingress en temperatur händelse var 2: e minut; en fuktighets händelse inträffar en gång var 15: e minut. När någon händelse har inträffat måste den innehålla en tidsstämpel som visar den tid då motsvarande temperatur eller fuktighet mättes.

Med tanke på det här scenariot demonstrerar vi två olika sätt att modellera data, och vi förklarar den effekt som modelleringen har på den serialiserade utmatningen.

### <a name="model-1"></a>Modell 1

Här är den första versionen av en modell som stöder det tidigare scenariot:

```C
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

Observera att modellen innehåller två data händelser: **temperatur** och **fuktighet**. Till skillnad från tidigare exempel är typen av varje händelse en struktur som definieras med **Declare- \_ struct**. **TemperatureEvent** omfattar en temperatur mätning och en tidsstämpel. **HumidityEvent** innehåller en fuktighets mätning och en tidsstämpel. Den här modellen ger oss ett naturligt sätt att modellera data för det scenario som beskrivs ovan. När vi skickar en händelse till molnet skickar vi antingen ett temperatur-/tidsstämpel-eller tids stämplings-/tidsstämpel-par.

Vi kan skicka en temperatur händelse till molnet med hjälp av kod som följande:

```C
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Vi använder hårdkodade värden för temperatur och fuktighet i exempel koden, men tänk på att vi faktiskt hämtar dessa värden genom att sampla motsvarande sensorer på termostat.

I koden ovan används **GetDateTimeOffset** -hjälpen som introducerades tidigare. Av orsaker som kommer att rensas senare, separerar koden uttryckligen uppgiften att serialisera och skicka händelsen. Föregående kod serialiserar temperatur händelsen i en buffert. Sedan är **SendMessage** en hjälp funktion (ingår i **simplesample \_ AMQP**) som skickar händelsen till IoT Hub:

```C
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

Den här koden är en delmängd av **SendAsync** -hjälpen som beskrivs i föregående avsnitt, så vi går inte över den igen.

När vi kör föregående kod för att skicka temperatur händelsen skickas denna serialiserade form av händelsen till IoT Hub:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Vi skickar en temperatur som är av typen **TemperatureEvent** och den strukturen innehåller en **temperatur** och en **tids** medlem. Detta återspeglas direkt i serialiserade data.

På samma sätt kan vi skicka en fuktighets händelse med den här koden:

```C
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Det serialiserade formuläret som skickas till IoT Hub visas på följande sätt:

```C
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Detta är som förväntat.

Med den här modellen kan du föreställa dig hur du enkelt kan lägga till ytterligare händelser. Du definierar fler strukturer med hjälp av **Declare \_ struct** och inkluderar motsvarande händelse i modellen med hjälp **av \_ data**.

Nu ska vi ändra modellen så att den innehåller samma data men med en annan struktur.

### <a name="model-2"></a>Modell 2

Överväg den här alternativa modellen till en ovan:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

I det här fallet har vi eliminerat **deklarerar \_** vi åtgärds-makron och definierar bara data objekt från vårt scenario med enkla typer från modellerings språket.

För tillfället ska du ignorera **tids** händelsen. Det här är koden för att ingressa **temperatur**:

```C
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Den här koden skickar följande serialiserade händelse till IoT Hub:

```C
{"Temperature":75}
```

Och koden för att skicka händelsen fuktighet visas på följande sätt:

```C
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Den här koden skickar den till IoT Hub:

```C
{"Humidity":45}
```

Så långt finns det fortfarande inga överraskningar. Nu ska vi ändra hur vi använder det SERIELLa makrot.

Det går att använda flera data händelser som argument i det **seriella** makrot. På så sätt kan vi serialisera **temperatur** -och **fuktighets** händelser tillsammans och skicka dem till IoT Hub i ett samtal:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Du kan gissa att resultatet av den här koden är att två data händelser skickas till IoT Hub:

[{"Temperatur": 75}, {"fuktighet": 45}]

Med andra ord kan du tro att den här koden är densamma som att skicka **temperatur** och **fuktighet** separat. Det är bara en bekvämlighet att skicka båda händelserna för att **serialiseras** i samma anrop. Men det är inte fallet. I stället skickar koden ovan denna enstaka data händelse till IoT Hub:

{"Temperatur": 75, "fuktighet": 45}

Detta kan verka konstigt eftersom vår modell definierar **temperatur** och **fuktighet** som två *separata* händelser:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Ännu mer till den här punkten modellerade vi inte dessa händelser där **temperatur** och **fuktighet** är i samma struktur:

```C
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Om vi använde den här modellen skulle det vara lättare att förstå hur **temperatur** och **fuktighet** skickas i samma serialiserade meddelande. Det kan dock vara uppenbart att det fungerar på det sättet när du skickar båda data händelserna till att **serialiseras** med hjälp av modell 2.

Det här beteendet är enklare att förstå om du känner till de antaganden som **serialiserar** biblioteket gör. För att göra detta kan vi gå tillbaka till vår modell:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Tänk på den här modellen i objektorienterade termer. I det här fallet utformar vi en fysisk enhet (en termostat) och enheten inkluderar attribut som **temperatur** och **fuktighet**.

Vi kan skicka hela statusen för vår modell med kod som följande:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Förutsatt att värdena för temperatur, fuktighet och tid anges visas en händelse som detta skickat till IoT Hub:

```C
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Ibland kanske du bara vill skicka *vissa* egenskaper för modellen till molnet (Detta gäller särskilt om din modell innehåller ett stort antal data händelser). Det är praktiskt att bara skicka en delmängd data händelser, t. ex. i vårt tidigare exempel:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Detta genererar exakt samma serialiserade händelse som om vi hade definierat en **TemperatureEvent** med en **temperatur** och en **tids** medlem, precis som vi gjorde med modell 1. I det här fallet kunde vi generera exakt samma serialiserade händelse genom att använda en annan modell (modell 2) eftersom vi anropade **serialisering** på ett annat sätt.

Den viktiga punkten är att om du skickar flera data händelser som ska **serialiseras** antar det att varje händelse är en egenskap i ett enda JSON-objekt.

Den bästa metoden beror på dig och hur du tycker om din modell. Om du skickar "händelser" till molnet och varje händelse innehåller en definierad uppsättning egenskaper, är den första metoden mycket obegriplig. I så fall kan du använda **deklarera \_ struct** för att definiera strukturen för varje händelse och sedan ta med dem i din modell med **data makrot med \_ data** . Sedan skickar du varje händelse som vi gjorde i det första exemplet ovan. I den här metoden skickar du bara en enskild data händelse till **serialiseraren**.

Om du tycker om din modell i ett objektorienterat sätt kan den andra metoden passa dig. I det här fallet är de element som definieras med hjälp av **med \_ data** "egenskaper" för ditt objekt. Du skickar olika delmängd av händelser så att de kan **serialiseras** som du vill, beroende på hur mycket av ditt "objekt"-tillstånd som du vill skicka till molnet.

Nether-metoden är rätt eller fel. Du behöver bara känna till hur **serialiserar** biblioteket fungerar och välja den modellerings metod som passar dina behov bäst.

## <a name="message-handling"></a>Meddelandehantering

Hittills har den här artikeln endast diskuterat att skicka händelser till IoT Hub och har inte adresserat emot meddelanden. Orsaken till detta är att det som vi behöver veta om att ta emot meddelanden i stort sett har beskrivits i artikeln [Azure IoT-enhetens SDK för C](iot-hub-device-sdk-c-intro.md). Kom ihåg från artikeln att du bearbetar meddelanden genom att registrera en motringnings funktion för meddelanden:

```C
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Sedan skriver du callback-funktionen som anropas när ett meddelande tas emot:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
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

Den här implementeringen av **IoTHubMessage** anropar den speciella funktionen för varje åtgärd i din modell. Om din modell till exempel definierar den här åtgärden:

```C
WITH_ACTION(SetAirResistance, int, Position)
```

Du måste definiera en funktion med den här signaturen:

```C
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** anropas sedan när meddelandet skickas till enheten.

Det som vi inte har förklarat än är vad den serialiserade versionen av meddelandet ser ut. Vad ser det som om du vill skicka ett **SetAirResistance** -meddelande till din enhet?

Om du skickar ett meddelande till en enhet gör du det via Azure IoT service SDK. Du måste fortfarande veta vilken sträng som ska skickas för att anropa en viss åtgärd. Det allmänna formatet för att skicka ett meddelande visas på följande sätt:

```C
{"Name" : "", "Parameters" : "" }
```

Du skickar ett serialiserat JSON-objekt med två egenskaper: **namn** är namnet på åtgärden (meddelandet) och **parametrarna** innehåller parametrarna för den åtgärden.

Om du till exempel vill anropa **SetAirResistance** kan du skicka meddelandet till en enhet:

```C
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

Åtgärds namnet måste exakt matcha en åtgärd som definierats i din modell. Parameter namnen måste också matcha varandra. Observera också Skift läges känslighet. **Namn** och **parametrar** är alltid versaler. Se till att matcha Skift läget för ditt åtgärds namn och parametrar i din modell. I det här exemplet är åtgärds namnet "SetAirResistance" och inte "SetAirResistance".

De två andra åtgärderna **TurnFanOn** och **TurnFanOff** kan anropas genom att skicka dessa meddelanden till en enhet:

```C
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

I det här avsnittet beskrivs allt du behöver veta när du skickar händelser och tar emot meddelanden med **serialiserar** -biblioteket. Innan du fortsätter tar vi upp några parametrar som du kan konfigurera som styr hur stor din modell är.

## <a name="macro-configuration"></a>Makro konfiguration

Om du använder **serialiseraren** finns en viktig del av SDK för att vara medveten om i Azure-c-Shared Utility-biblioteket.

Om du har klonat Azure-IoT-SDK-c-lagringsplatsen från GitHub och utfärdat `git submodule update --init` kommandot kommer du att hitta det här delade verktygs biblioteket här:

```C
.\\c-utility
```

Om du inte har klonat biblioteket kan du hitta det [här](https://github.com/Azure/azure-c-shared-utility).

I biblioteket för delade verktyg hittar du följande mapp:

```C
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Den här mappen innehåller en Visual Studio-lösning som kallas **Macro \_ utils \_ h \_ Generator. SLN**:

  ![Skärm bild av Visual Studio-lösningen maco_utils_h_generator](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.png)

Programmet i den här lösningen genererar **makro filen \_ utils. h** . Det finns en standard- \_ fil för makro-utils. h som ingår i SDK. Med den här lösningen kan du ändra vissa parametrar och sedan återskapa rubrik filen baserat på dessa parametrar.

De två nyckel parametrarna som ska vara aktuella för är **nArithmetic** och **nMacroParameters**, som definieras i följande två rader i Macro \_ utils.tt:

```C
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>
```

Dessa värden är standard parametrarna som ingår i SDK: n. Varje parameter har följande betydelse:

* nMacroParameters – styr hur många parametrar du kan ha i en DEKLARERAd \_ modell makro definition.
* nArithmetic – styr det totala antalet medlemmar som tillåts i en modell.

Anledningen är att dessa parametrar är viktiga eftersom de styr hur stor din modell kan vara. Anta till exempel den här modell definitionen:

```C
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Som tidigare nämnts är **deklarera \_ modell** bara ett C-makro. Namnen på modellen och **with \_ data** -instruktionen (ännu ett annat makro) är parametrar för **Declare- \_ modellen**. **nMacroParameters** definierar hur många parametrar som kan tas med i **Declare- \_ modellen**. Detta definierar effektivt hur många data händelse-och åtgärds deklarationer som du kan ha. Med standard gränsen på 124 innebär det att du kan definiera en modell med en kombination av om 60 åtgärder och data händelser. Om du försöker överskrida den här gränsen får du kompilator fel som ser ut ungefär så här:

  ![Skärm bild av fel i kompilatorn för makro parametrar](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.png)

Parametern **nArithmetic** är mer om det interna arbetet i makro språket än ditt program.  Det styr det totala antalet medlemmar som du kan ha i din modell, inklusive **DECLARE_STRUCT** makron. Om du börjar se kompilator fel som detta bör du försöka med att öka **nArithmetic**:

   ![Skärm bild av aritmetiska kompilator fel](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.png)

Om du vill ändra de här parametrarna ändrar du värdena i makrots \_ utils.tt-fil, kompilerar om Macro \_ -filen med \_ \_ SLN-generatorn och kör det kompilerade programmet. När du gör det skapas en ny \_ . h-fil för makron skapas och placeras i. \\ gemensamma \\ Inc-katalogen.

För att kunna använda den nya versionen av Macro \_ utils. h tar du bort **serialiseraren** NuGet-paketet från din lösning och i dess ställe ingår **serialiseraren** Visual Studio-projektet. Detta gör att din kod kan kompileras mot käll koden i serialiseraren. Detta inkluderar det uppdaterade makrots \_ utils. h. Om du vill göra detta för **simplesample- \_ AMQP** börjar du med att ta bort NuGet-paketet för serialiseraren från lösningen:

   ![Skärm bild av borttagning av NuGet-paketet för serialiserar-biblioteket](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.png)

Lägg sedan till det här projektet i din Visual Studio-lösning:

> .\\ c- \\ serialiseraren \\ build \\ Windows \\ serialiserare. vcxproj
> 
> 

När du är klar bör din lösning se ut så här:

   ![Skärm bild av simplesample_amqp Visual Studio-lösningen](media/iot-hub-device-sdk-c-serializer/05-serializer-project.png)

Nu när du kompilerar din lösning ingår det uppdaterade makrots \_ utils. h i binärfilen.

Observera att om du ökar värdena tillräckligt högt kan det överskrida kompilator gränserna. I det här läget är **nMacroParameters** den huvudsakliga parameter som ska vara berörd. C99-specifikationen anger att minst 127 parametrar tillåts i en makro definition. Microsoft compiler följer specifikationen exakt (och har en gräns på 127), så du kan inte öka **nMacroParameters** utöver standardvärdet. Andra kompilatorer kan göra det (till exempel GNU-kompilatorn stöder en högre gräns).

Hittills har vi täckt bara allt du behöver veta om hur man skriver kod med **serialiserar** -biblioteket. Innan du börjar kan vi gå tillbaka till några ämnen från föregående artiklar som du kanske undrar om.

## <a name="the-lower-level-apis"></a>API: er på lägre nivå
Det exempel program som den här artikeln fokuserar på är **simplesample \_ AMQP**. I det här exemplet används de högre (icke-**lla**) API: erna för att skicka händelser och ta emot meddelanden. Om du använder dessa API: er körs en bakgrunds tråd som tar hand om att skicka händelser och ta emot meddelanden. Du kan dock använda de lågnivå-API: erna för att eliminera den här bakgrunds tråden och ta direkt kontroll över när du skickar händelser eller tar emot meddelanden från molnet.

Som det beskrivs i [föregående artikel](iot-hub-device-sdk-c-iothubclient.md)finns en uppsättning funktioner som består av API: er på högre nivå:

* IoTHubClient \_ CreateFromConnectionString
* IoTHubClient \_ SendEventAsync
* IoTHubClient \_ SetMessageCallback
* IoTHubClient att \_ förstöra

Dessa API: er visas i **simplesample \_ AMQP**.

Det finns också en analog uppsättning med lågnivå-API: er.

* IoTHubClient \_ lla \_ CreateFromConnectionString
* IoTHubClient \_ lla \_ SendEventAsync
* IoTHubClient \_ lla \_ SetMessageCallback
* IoTHubClient \_ lla att \_ förstöra

Observera att de lågnivå-API: erna fungerar exakt på samma sätt som beskrivs i föregående artiklar. Du kan använda den första uppsättningen med API: er om du vill att en bakgrunds tråd ska hantera sändning av händelser och ta emot meddelanden. Du använder den andra uppsättningen API: er om du vill ha en direkt kontroll över när du skickar och tar emot data från IoT Hub. Antingen fungerar Uppsättnings-API: er lika bra med **serialiserar** -biblioteket.

Ett exempel på hur de lågnivå-API: erna används med **serialiserar** -biblioteket finns i **simplesample \_ http-** programmet.

## <a name="additional-topics"></a>Ytterligare information
Några andra avsnitt som är värda att nämnas är egenskaps hantering, med alternativa autentiseringsuppgifter för enhet och konfigurations alternativ. Detta är alla ämnen som beskrivs i [föregående artikel](iot-hub-device-sdk-c-iothubclient.md). Huvud punkten är att alla dessa funktioner fungerar på samma sätt med **serialiserar** -biblioteket som med **IoTHubClient** -biblioteket. Om du till exempel vill bifoga egenskaper till en händelse från din modell använder du **IoTHubMessage- \_ Egenskaper** och **mappar** \_ **AddorUpdate**, på samma sätt som tidigare beskrivits:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Huruvida händelsen genererades från **serialiserar** biblioteket eller skapats manuellt med **IoTHubClient** -biblioteket spelar ingen roll.

För de alternativa autentiseringsuppgifterna för enheten, **\_ \_ skapar IoTHubClient lla** både och **IoTHubClient \_ CreateFromConnectionString** för att allokera en **IOTHUB \_ klient \_ referens**.

Slutligen, om du använder **serialiseraren** , kan du ange konfigurations alternativ med **IoTHubClient \_ lla \_ SetOption** precis som du gjorde när du använde **IoTHubClient** -biblioteket.

En funktion som är unik för **serialiserar** -biblioteket är initierings-API: erna. Innan du kan börja arbeta med biblioteket måste du anropa **serialiseraren \_ init**:

```C
serializer_init(NULL);
```

Detta görs precis innan du anropar **IoTHubClient \_ CreateFromConnectionString**.

När du har arbetat med biblioteket är det sista anropet som du gör till **serialiseraren \_ deinit**:

```C
serializer_deinit();
```

Annars fungerar alla andra funktioner som anges ovan i **serialiserar** -biblioteket som i **IoTHubClient** -biblioteket. Mer information om något av dessa ämnen finns i [föregående artikel](iot-hub-device-sdk-c-iothubclient.md) i den här serien.

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskriver i detalj vilka unika aspekter av **serialiserar** -biblioteket som finns i **Azure IoT-enhetens SDK för C**. Med den information som tillhandahålls bör du ha en god förståelse för hur du använder modeller för att skicka händelser och ta emot meddelanden från IoT Hub.

Detta avslutar även serien i tre delar om hur du utvecklar program med **Azure IoT-enhetens SDK för C**. Detta bör vara tillräckligt med information för att inte bara komma igång utan ger dig en grundlig förståelse för hur API: erna fungerar. För ytterligare information finns det några exempel i SDK som inte beskrivs här. I annat fall är [Azure IoT SDK-dokumentationen](https://github.com/Azure/azure-iot-sdk-c) en lämplig resurs för ytterligare information.

Mer information om hur du utvecklar för IoT Hub finns i [Azure IoT SDK](iot-hub-devguide-sdks.md): er.

För att ytterligare utforska funktionerna i IoT Hub, se [distribuera AI till gräns enheter med Azure IoT Edge](../iot-edge/quickstart-linux.md).