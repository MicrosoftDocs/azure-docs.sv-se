---
title: Förstå Azure-språket för IoT Hub frågor | Microsoft Docs
description: Guide för utvecklare – Beskrivning av SQL-liknande IoT Hub frågespråk som används för att hämta information om enhet/modul-dubbla och jobb från IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: robinsh
ms.custom: devx-track-csharp
ms.openlocfilehash: cae2bcb1a3302814a426fa0cb2dfb36ba1b013fa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218374"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>IoT Hub-frågespråk för enhet och modultvillingar, jobb och meddelanderedigering

IoT Hub är ett kraftfullt SQL-liknande språk för att hämta information om [enhetens dubblare](iot-hub-devguide-device-twins.md), [modulens dubblare](iot-hub-devguide-module-twins.md), [jobb](iot-hub-devguide-jobs.md)och [meddelanderoutning.](iot-hub-devguide-messages-d2c.md) Den här artikeln visar:

* En introduktion till huvud funktionerna i IoT Hub frågespråk och
* En detaljerad beskrivning av språket. Information om frågespråket för meddelanderoutning finns i [frågor i](../iot-hub/iot-hub-devguide-routing-query-syntax.md)meddelanderoutning.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>Enhet och modul, dubbla frågor

[Enheten är uppflätad](iot-hub-devguide-device-twins.md) och [modulerna](iot-hub-devguide-module-twins.md) kan innehålla godtyckliga JSON-objekt som både Taggar och egenskaper. Med IoT Hub kan du fråga enhetens dubblare och modul sammanflätade som ett enda JSON-dokument med all dubbel information.

Anta till exempel att din IoT Hub-enhet har följande struktur (modulin skulle vara precis med ytterligare en moduleId):

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "tags": {
        "location": {
            "region": "US",
            "plant": "Redmond43"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300
            },
            "$metadata": {
            ...
            },
            "$version": 4
        },
        "reported": {
            "connectivity": {
                "type": "cellular"
            },
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300,
                "status": "Success"
            },
            "$metadata": {
            ...
            },
            "$version": 7
        }
    }
}
```

### <a name="device-twin-queries"></a>Enhets dubbla frågor

IoT Hub visar att enheten är uppflätad som en dokument samling som kallas **enheter**. Följande fråga hämtar till exempel hela enhets uppsättningen:

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Azure IoT-SDK](iot-hub-devguide-sdks.md) : er stöder växling av stora resultat.

Med IoT Hub kan du hämta enhets filter med valfria villkor. Om du till exempel vill ta emot enheten på flera **platser där taggen location. region** har angetts till **oss** använder du följande fråga:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

Booleska operatorer och aritmetiska jämförelser stöds också. För att till exempel hämta enheter som finns i USA och som kon figurer ATS för att skicka telemetri mindre än varje minut, använder du följande fråga:

```sql
SELECT * FROM devices
  WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

Som bekvämlighet är det också möjligt att använda matriskonstanter med operatorerna **in** och **nom** (inte in). Om du till exempel vill hämta en enhet som rapporterar WiFi eller kabelansluten anslutning använder du följande fråga:

```sql
SELECT * FROM devices
  WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

Det är ofta nödvändigt att identifiera alla enheter som innehåller en speciell egenskap. IoT Hub stöder funktionen `is_defined()` för detta ändamål. Om du till exempel vill hämta dubbla enheter som definierar `connectivity` egenskapen använder du följande fråga:

```SQL
SELECT * FROM devices
  WHERE is_defined(properties.reported.connectivity)
```

Se avsnittet [WHERE-sats](iot-hub-devguide-query-language.md#where-clause) för en fullständig referens till filtrerings funktionerna.

Gruppering och agg regeringar stöds också. Om du till exempel vill hitta antalet enheter i varje konfigurations status för telemetri använder du följande fråga:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
  FROM devices
  GROUP BY properties.reported.telemetryConfig.status
```

Den här grupp frågan returnerar ett resultat som liknar följande exempel:

```json
[
    {
        "numberOfDevices": 3,
        "status": "Success"
    },
    {
        "numberOfDevices": 2,
        "status": "Pending"
    },
    {
        "numberOfDevices": 1,
        "status": "Error"
    }
]
```

I det här exemplet har tre enheter rapporterat att konfigurationen har slutförts, två använder fortfarande konfigurationen och ett fel rapporteras.

Med projektions frågor kan utvecklare bara returnera de egenskaper som de bryr sig om. För att till exempel hämta den senaste aktivitets tiden för alla frånkopplade enheter använder du följande fråga:

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>Modul, dubbla frågor

Fråga om modulernas dubblare liknar fråga på enheten, men med en annan samling/namnrymd; i stället för från **enheter** frågar du från **enheter. modules**:

```sql
SELECT * FROM devices.modules
```

Vi tillåter inte koppling mellan enheterna och enheterna. modules-samlingar. Om du vill att fråge modulen ska ha flera enheter, gör du det baserat på taggar. Den här frågan returnerar all modul dubbla på alla enheter med genomsöknings status:

```sql
SELECT * FROM devices.modules WHERE properties.reported.status = 'scanning'
```

Den här frågan returnerar alla moduler med skannings status, men endast på den angivna del mängden av enheter:

```sql
SELECT * FROM devices.modules
  WHERE properties.reported.status = 'scanning'
  AND deviceId IN ['device1', 'device2']
```

### <a name="c-example"></a>C#-exempel

Fråge funktionen exponeras av [C#-tjänst-SDK: n](iot-hub-devguide-sdks.md) i klassen **RegistryManager** .

Här är ett exempel på en enkel fråga:

```csharp
var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
while (query.HasMoreResults)
{
    var page = await query.GetNextAsTwinAsync();
    foreach (var twin in page)
    {
        // do work on twin object
    }
}
```

**Frågespråket** instansieras med en sid storlek (upp till 100). Sedan hämtas flera sidor genom att anropa **GetNextAsTwinAsync** -metoderna flera gånger.

Objektet fråga visar flera **Nästa** värden, beroende på vilket avserialiserings alternativ som krävs av frågan. Till exempel enhets-eller jobb objekt, eller vanlig JSON när du använder projektioner.

### <a name="nodejs-example"></a>Node.js exempel

Fråge funktionen exponeras av [Azure IoT service SDK för Node.js](iot-hub-devguide-sdks.md) i **Registry** -objektet.

Här är ett exempel på en enkel fråga:

```javascript
var query = registry.createQuery('SELECT * FROM devices', 100);
var onResults = function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        // Do something with the results
        results.forEach(function(twin) {
            console.log(twin.deviceId);
        });

        if (query.hasMoreResults) {
            query.nextAsTwin(onResults);
        }
    }
};
query.nextAsTwin(onResults);
```

**Frågespråket** instansieras med en sid storlek (upp till 100). Sedan hämtas flera sidor genom att **nextAsTwin** -metoden anropas flera gånger.

Objektet fråga visar flera **Nästa** värden, beroende på vilket avserialiserings alternativ som krävs av frågan. Till exempel enhets-eller jobb objekt, eller vanlig JSON när du använder projektioner.

### <a name="limitations"></a>Begränsningar

> [!IMPORTANT]
> Frågeresultaten kan ha några minuters fördröjning i förhållande till de senaste värdena i enheten är dubbla. Om en fråga om en enskild enhet är uppflätad med ID, använder du funktionen [Hämta dubbla REST API](/java/api/com.microsoft.azure.sdk.iot.device.devicetwin). Detta API returnerar alltid de senaste värdena och har högre begränsnings gränser. Du kan utfärda REST API direkt eller använda motsvarande funktioner i en av [Azure IoT Hub service SDK: erna](iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks).

För närvarande stöds jämförelser bara mellan primitiva typer (inga objekt), till exempel `... WHERE properties.desired.config = properties.reported.config` endast om dessa egenskaper har primitiva värden.

## <a name="get-started-with-jobs-queries"></a>Kom igång med jobb frågor

[Jobb](iot-hub-devguide-jobs.md) är ett sätt att köra åtgärder på enhets uppsättningar. Varje enhet, som innehåller information om de jobb som den ingår i en samling som kallas **jobb**.

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        ...
    },
    "properties": {
        ...
    },
    "jobs": [
        {
            "deviceId": "myDeviceId",
            "jobId": "myJobId",
            "jobType": "scheduleTwinUpdate",
            "status": "completed",
            "startTimeUtc": "2016-09-29T18:18:52.7418462",
            "endTimeUtc": "2016-09-29T18:20:52.7418462",
            "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
            "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
            "outcome": {
                "deviceMethodResponse": null
            }
        },
        ...
    ]
}
```

För närvarande är den här samlingen frågad som **Devices.Jobs** i IoT Hub frågespråk.

> [!IMPORTANT]
> För närvarande returneras inte jobb-egenskapen när en fråga om enheten är uppkopplad. Det vill säga frågor som innehåller "från enheter". Det går bara att komma åt jobb egenskapen direkt med frågor som använder `FROM devices.jobs` .
>
>

Om du till exempel vill hämta alla jobb (tidigare och schemalagda) som påverkar en enskild enhet kan du använda följande fråga:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
```

Observera hur den här frågan tillhandahåller enhetsspecifika status (och eventuellt det direkta metod svaret) för varje jobb som returneras.

Det går också att filtrera med valfria booleska villkor för alla objekt egenskaper i **Devices.Jobs** -samlingen.

För att till exempel hämta alla slutförda enhets dubbla uppdaterings jobb som har skapats efter den 2016 september för en speciell enhet, Använd följande fråga:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

Du kan också hämta resultatet av varje enhet i ett enda jobb.

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Begränsningar

För närvarande stöder inte frågor på **Devices.Jobs** :

* Prognoser är därför bara `SELECT *` möjliga.
* Villkor som refererar till enheten, tillsammans med jobb egenskaper (se föregående avsnitt).
* Utföra agg regeringar, t. ex. Count, AVG, Group by.

## <a name="basics-of-an-iot-hub-query"></a>Grunderna i en IoT Hub fråga

Varje IoT Hub fråga består av SELECT-och FROM-satser, med valfria WHERE-och GROUP BY-satser. Varje fråga körs på en samling JSON-dokument, t. ex. enheten. FROM-satsen anger den dokument samling som ska upprepas på (**enheter**, **enheter. moduler** eller **Devices.Jobs**). Sedan används filtret i WHERE-satsen. Med agg regeringar grupperas resultatet av det här steget som anges i GROUP BY-satsen. För varje grupp genereras en rad som anges i SELECT-satsen.

```sql
SELECT <select_list>
  FROM <from_specification>
  [WHERE <filter_condition>]
  [GROUP BY <group_specification>]
```

## <a name="from-clause"></a>FROM-satsen

From **<from_specification>** -satsen kan bara anta tre värden: **från enheter** för att fråga enheten till varandra, **från enheter. moduler** till fråge modul, dubbla eller **från Devices.Jobs** för att söka efter jobb per enhets information.

## <a name="where-clause"></a>WHERE-satsen

Satsen **WHERE <filter_condition>** är valfri. Det anger ett eller flera villkor som JSON-dokumenten i från-samlingen måste uppfylla för att inkluderas som en del av resultatet. Alla JSON-dokument måste utvärdera de angivna villkoren till "true" för att inkluderas i resultatet.

De tillåtna villkoren beskrivs i avsnitts [uttryck och villkor](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="select-clause"></a>SELECT-satsen

**>välj <select_list** är obligatorisk och anger vilka värden som hämtas från frågan. Den anger de JSON-värden som ska användas för att generera nya JSON-objekt.
För varje element i den filtrerade (och alternativt grupperade) delmängd av från-samlingen genererar projektion-fasen ett nytt JSON-objekt. Det här objektet konstrueras med de värden som anges i SELECT-satsen.

Följande är grammatiken i SELECT-satsen:

```
SELECT [TOP <max number>] <projection list>

<projection_list> ::=
    '*'
    | <projection_element> AS alias [, <projection_element> AS alias]+

<projection_element> :==
    attribute_name
    | <projection_element> '.' attribute_name
    | <aggregate>

<aggregate> :==
    count()
    | avg(<projection_element>)
    | sum(<projection_element>)
    | min(<projection_element>)
    | max(<projection_element>)
```

**Attribute_name** refererar till en egenskap i JSON-dokumentet i från-samlingen. Några exempel på SELECT-satser finns i avsnittet komma igång med enhets dubbla frågor.

För närvarande stöds inte markerings satser som skiljer sig från **Select*** i mängd frågor på enheten.

## <a name="group-by-clause"></a>GROUP BY-sats

Satsen **Group by <group_specification>** är ett valfritt steg som körs efter filtret som anges i WHERE-satsen, och innan projektionen som anges i SELECT. Den grupperar dokument baserat på värdet för ett attribut. Dessa grupper används för att generera sammanställda värden som anges i SELECT-satsen.

Ett exempel på en fråga som använder GROUP BY är:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

Den formella syntaxen för GROUP BY är:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name** refererar till en egenskap i JSON-dokumentet i från-samlingen.

GROUP BY-satsen stöds för närvarande bara när du frågar enheten.

> [!IMPORTANT]
> Termen `group` behandlas för närvarande som ett särskilt nyckelord i frågor. Om du använder `group` som egenskaps namn kan du välja att omge det med dubbla hakparenteser för att undvika fel, t. ex. `SELECT * FROM devices WHERE tags.[[group]].name = 'some_value'` .
>

## <a name="expressions-and-conditions"></a>Uttryck och villkor

Ett *uttryck* på hög nivå:

* Utvärderar till en instans av en JSON-typ (till exempel boolesk, siffra, sträng, matris eller objekt).
* Definieras genom att ändra data som kommer från enhets-JSON-dokumentet och konstanter med inbyggda operatorer och funktioner.

*Villkor* är uttryck som utvärderas till ett booleskt värde. En annan konstant än Boolean **True** anses vara **falskt**. Den här regeln inkluderar **Null**, **Odefinierad**, alla objekt eller Array-instanser, valfri sträng och booleska **falskt**.

Syntaxen för uttryck är:

```
<expression> ::=
    <constant> |
    attribute_name |
    <function_call> |
    <expression> binary_operator <expression> |
    <create_array_expression> |
    '(' <expression> ')'

<function_call> ::=
    <function_name> '(' expression ')'

<constant> ::=
    <undefined_constant>
    | <null_constant>
    | <number_constant>
    | <string_constant>
    | <array_constant>

<undefined_constant> ::= undefined
<null_constant> ::= null
<number_constant> ::= decimal_literal | hexadecimal_literal
<string_constant> ::= string_literal
<array_constant> ::= '[' <constant> [, <constant>]+ ']'
```

För att förstå vad varje symbol i uttrycks syntaxen står för, se följande tabell:

| Symbol | Definition |
| --- | --- |
| attribute_name | Alla egenskaper i JSON-dokumentet i **från** -samlingen. |
| binary_operator | En binär operator som anges i avsnittet [operatorer](#operators) . |
| function_name| Alla funktioner som anges i avsnittet [Functions](#functions) . |
| decimal_literal |Ett växel uttryck uttryckt i Decimal form. |
| hexadecimal_literal |Ett tal som uttrycks av strängen 0x följt av en sträng med hexadecimala siffror. |
| string_literal |Sträng litteraler är Unicode-strängar som representeras av en sekvens med noll eller flera Unicode-tecken eller escape-sekvenser. Sträng litteraler omges av enkla citat tecken eller dubbla citat tecken. Tillåtna Escape: `\'` ,, `\"` `\\` , `\uXXXX` för Unicode-tecken som definieras av 4 hexadecimala siffror. |

### <a name="operators"></a>Operatorer

Följande operatorer stöds:

| Familj | Operatorer |
| --- | --- |
| Aritmetisk |+, -, *, /, % |
| Logiskt |AND, OR, NOT (och, eller, inte) |
| Jämförelse |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Functions

Vid frågor om dubbla och jobb är den enda funktioner som stöds:

| Funktion | Beskrivning |
| -------- | ----------- |
| IS_DEFINED (egenskap) | Returnerar ett booleskt värde som anger om egenskapen har tilldelats ett värde (inklusive `null` ). |

I villkor för flöden stöds följande matematiska funktioner:

| Funktion | Beskrivning |
| -------- | ----------- |
| ABS (x) | Returnerar det absoluta (positiva) värdet för det angivna numeriska uttrycket. |
| EXP (x) | Returnerar exponent svärdet för det angivna numeriska uttrycket (e ^ x). |
| STRÖM (x, y) | Returnerar värdet för det angivna uttrycket till den angivna exponenten (x ^ y).|
| FYRKANT (x)    | Returnerar kvadraten för det angivna numeriska värdet. |
| TAK (x) | Returnerar det minsta heltalsvärdet som är större än eller lika med det angivna numeriska uttrycket. |
| BASYTA (x) | Returnerar det största heltalsvärdet som är mindre än eller lika med det angivna numeriska uttrycket. |
| TECKEN (x) | Returnerar det positiva (+ 1), noll (0) eller negativa (-1) tecknet för det angivna numeriska uttrycket.|
| SQRT (x) | Returnerar kvadratroten för det angivna numeriska värdet. |

I villkor för flöden stöds följande typ kontroll och data typs funktioner:

| Funktion | Beskrivning |
| -------- | ----------- |
| AS_NUMBER | Konverterar Indatasträngen till ett tal. `noop` Om indatatypen är ett tal. `Undefined` om strängen inte representerar ett tal.|
| IS_ARRAY | Returnerar ett booleskt värde som anger om typen för det angivna uttrycket är en matris. |
| IS_BOOL | Returnerar ett booleskt värde som anger om typen för det angivna uttrycket är ett booleskt värde. |
| IS_DEFINED | Returnerar ett booleskt värde som anger huruvida egenskapen har tilldelats ett värde. Detta stöds endast när värdet är en primitiv typ. Primitiva typer är sträng, boolesk, numerisk eller `null` . DateTime, objekt typer och matriser stöds inte. |
| IS_NULL | Returnerar ett booleskt värde som anger om typen för det angivna uttrycket är null. |
| IS_NUMBER | Returnerar ett booleskt värde som anger om typen för det angivna uttrycket är ett tal. |
| IS_OBJECT | Returnerar ett booleskt värde som anger om typen för det angivna uttrycket är ett JSON-objekt. |
| IS_PRIMITIVE | Returnerar ett booleskt värde som anger om typen för det angivna uttrycket är en primitiv (sträng, boolesk, numerisk eller `null` ). |
| IS_STRING | Returnerar ett booleskt värde som anger om typen för det angivna uttrycket är en sträng. |

I villkor för flöden stöds följande sträng funktioner:

| Funktion | Beskrivning |
| -------- | ----------- |
| CONCAt (x, y,...) | Returnerar en sträng som är resultatet av en sammanfogning av två eller fler strängvärden. |
| LÄNGD (x) | Returnerar antalet tecken i det angivna sträng uttrycket.|
| LÄGRE (x) | Returnerar ett stränguttryck efter att teckendata med versaler har konverterats till gemener. |
| ÖVRE (x) | Returnerar ett stränguttryck efter att teckendata med gemener har konverterats till versaler. |
| Del sträng (sträng, start [, längd]) | Returnerar en del av ett sträng uttryck som börjar vid den angivna tecken noll-baserade positionen och fortsätter till den angivna längden, eller till slutet av strängen. |
| INDEX_OF (sträng, fragment) | Returnerar startpositionen för den första förekomsten av det andra stränguttrycket i det första angivna stränguttrycket eller -1 om strängen inte hittas.|
| STARTS_WITH (x, y) | Returnerar ett booleskt värde som anger om det första sträng uttrycket börjar med det andra. |
| ENDS_WITH (x, y) | Returnerar ett booleskt värde som anger om det första sträng uttrycket slutar med det andra. |
| INNEHÅLLER (x, y) | Returnerar ett booleskt värde som anger huruvida det första stränguttrycket innehåller det andra. |

## <a name="next-steps"></a>Nästa steg

Lär dig hur du kör frågor i dina appar med hjälp av [Azure IoT SDK](iot-hub-devguide-sdks.md): er.