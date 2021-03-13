---
title: 'Begrepp: mappa mallar i Azure IoT Connector för FHIR (för hands version) i Azure API för FHIR'
description: Lär dig hur du skapar två typer av mappnings mallar i Azure IoT Connector för FHIR (för hands version). Enhets mappnings mal len omvandlar enhets data till ett normaliserat schema. FHIR-mappnings mal len transformerar ett normaliserat meddelande till en FHIR-baserad observations resurs.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: punagpal
ms.openlocfilehash: 581afbb5cec166f0ef5048b6ecc89f8ff95fd794
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020687"
---
# <a name="azure-iot-connector-for-fhir-preview-mapping-templates"></a>Azure IoT-anslutningsprogram för FHIR (förhandsversion) – mappningsmall
Den här artikeln beskriver hur du konfigurerar Azure IoT Connector för resurser för snabb hälso samverkan (FHIR&#174;) * med hjälp av mappnings-mallar.

Azure IoT-anslutningen för FHIR kräver två typer av JSON-baserade mappnings-mallar. Den första typen, **enhets mappningen** är ansvarig för att mappa enhetens nytto laster som skickas till `devicedata` slut punkten för Azure Event Hub. Den extraherar typer, enhets identifierare, datum/tid för mätning och mått värden. Den andra typen, **FHIR-mappning**, styr mappningen för FHIR-resursen. Det gör det möjligt att konfigurera längden på observations perioden, FHIR-datatypen som används för att lagra värdena och terminologins kod (er). 

Mappnings mallarna består av ett JSON-dokument baserat på deras typ. Dessa JSON-dokument läggs sedan till i din Azure IoT-anslutning för FHIR via Azure Portal. Enhets mappnings dokumentet läggs till via sidan **Konfigurera enhets mappning** och FHIR mappnings dokument via sidan **Konfigurera FHIR-mappning** .

> [!NOTE]
> Mappar mallar lagras i en underliggande blob-lagring och läses in från BLOB per beräknings körning. När de har uppdaterats bör de börja gälla omedelbart. 

## <a name="device-mapping"></a>Enhets mappning
Enhets mappningen innehåller kart funktioner för att extrahera enhets innehåll i ett gemensamt format för ytterligare utvärdering. Varje meddelande som tas emot utvärderas mot alla mallar. Med den här metoden kan ett enskilt inkommande meddelande projiceras till flera utgående meddelanden, som senare mappas till olika observationer i FHIR. Resultatet är ett normaliserat data objekt som representerar värdet eller värdena som parsas av mallarna. Den normaliserade data modellen har några nödvändiga egenskaper som måste hittas och extraheras:

| Egenskap | Beskrivning |
| - | - |
|**Typ**|Namnet/typen som klassificerar måttet. Det här värdet används för att binda till den obligatoriska mappnings mal len för FHIR.  Flera mallar kan skrivas ut till samma typ, så att du kan mappa olika representationer över flera enheter till samma vanliga utdata.|
|**OccurenceTimeUtc**|Tiden då mätningen utfördes.|
|**DeviceId**|Identifieraren för enheten. Det här värdet ska matcha en identifierare på den enhets resurs som finns på mål FHIR-servern.|
 |**Egenskaper**|Extrahera minst en egenskap så att värdet kan sparas i observations resursen som skapats.  Egenskaper är en samling nyckel värdes par som extraheras under normalisering.|

Nedan visas ett konceptuellt exempel på vad som händer under normalisering.

![Normaliserings exempel](media/concepts-iot-mapping-templates/normalization-example.png)

Innehålls nytto lasten är ett Azure Event Hub-meddelande, som består av tre delar: brödtext, egenskaper och SystemProperties. `Body`Är en byte mat ris som representerar en UTF-8-kodad sträng. Under utvärdering av mall konverteras byte mat ris automatiskt till strängvärdet. `Properties` är en nyckel värde samling som kan användas av meddelandets skapare. `SystemProperties` är också en nyckel värde samling som reserver ATS av Azure Event Hub-ramverket och poster som fylls i automatiskt.

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2019-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```

### <a name="mapping-with-json-path"></a>Mappning med JSON-sökväg
De tre mallarna för enhets innehåll som stöds idag förlitar sig på JSON-sökväg för att både matcha den nödvändiga mallen och extraherade värden. Mer information om JSON-sökvägen hittar du [här](https://goessner.net/articles/JsonPath/). Alla tre mallarna använder [JSON .net-implementering](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) för att matcha JSON Path-uttryck.

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate
JsonPathContentTemplate tillåter matchning av och extrahering av värden från ett Event Hub-meddelande med hjälp av JSON-sökväg.

| Egenskap | Beskrivning |<div style="width:150px">Exempel</div>
| --- | --- | --- 
|**Typnamn**|Typen som ska associeras med mått som matchar mallen.|`heartrate`
|**TypeMatchExpression**|JSON-sökvägar som utvärderas mot händelsens nytto Last. Om det finns en matchande JToken, betraktas mallen som en matchning. Alla efterföljande uttryck utvärderas mot den extraherade JToken som matchas här.|`$..[?(@heartRate)]`
|**TimestampExpression**|JSON-sökvägar för att extrahera tidsstämpel-värdet för mätningens OccurenceTimeUtc.|`$.endDate`
|**DeviceIdExpression**|JSON Path-uttryck för att extrahera enhets identifieraren.|`$.deviceId`
|**PatientIdExpression**|*Valfritt*: JSON-sökvägar för att extrahera patient-ID.|`$.patientId`
|**EncounterIdExpression**|*Valfritt*: JSON-sökvägar för att extrahera identifieraren.|`$.encounterId`
|**Värden []. Värdets namn**|Namnet som ska associeras med värdet som extraheras av det efterföljande uttrycket. Används för att binda det obligatoriska värdet/komponenten i mappnings mal len för FHIR. |`hr`
|**Värden []. ValueExpression**|JSON Path-uttrycket som krävs för att extrahera det nödvändiga värdet.|`$.heartRate`
|**Värden []. Kunna**|Kräver att värdet finns i nytto lasten.  Om inget värde hittas genereras inte en mätning och en InvalidOperationException kommer att genereras.|`true`

##### <a name="examples"></a>Exempel
---
**Puls pris**

*Meddelande*
```json
{
    "Body": {
        "heartRate": "78",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Mall*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Blod tryck**

*Meddelande*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Mall*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@systolic && @diastolic)]",
    "deviceIdExpression": "$.deviceid",
    "timestampExpression": "$.endDate",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
---

**Projicera flera mått från ett enda meddelande**

*Meddelande*
```json
{
    "Body": {
        "heartRate": "78",
        "steps": "2",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Mall 1*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
*Mall 2*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.steps",
                "valueName": "steps"
            }
        ]
    }
}
```
---

**Projicera flera mått från matrisen i ett meddelande**

*Meddelande*
```json
{
    "Body": [
        {
            "heartRate": "78",
            "endDate": "2019-02-01T22:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "81",
            "endDate": "2019-02-01T23:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "72",
            "endDate": "2019-02-01T24:46:01.8750000Z",
            "deviceId": "device123"
        }
    ],
    "Properties": {},
    "SystemProperties": {}
}
```
*Mall*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

#### <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate

IotJsonPathContentTemplate liknar JsonPathContentTemplate, förutom att DeviceIdExpression och TimestampExpression inte krävs.

Antagande när du använder den här mallen är de meddelanden som utvärderas med hjälp av [azure IoT Hub enhets-SDK](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) : er eller  [Exportera data (bakåtkompatibelt)](../../iot-central/core/howto-export-data-legacy.md) funktionen i [Azure IoT Central](../../iot-central/core/overview-iot-central.md). När du använder dessa SDK: er är enhets identiteten (förutsatt att enhets identifieraren från Azure IoT Hub/Central har registrerats som en identifierare för en enhets resurs på mål FHIR-servern) och tidsstämpeln för meddelandet är känd. Om du använder Azure IoT Hub-enhets-SDK: er men använder anpassade egenskaper i meddelande texten för enhets identiteten eller tidsstämpeln för mätning, kan du fortfarande använda JsonPathContentTemplate.

*Obs: när du använder IotJsonPathContentTemplate bör TypeMatchExpression matcha hela meddelandet som en JToken. Se exemplen nedan.* 
##### <a name="examples"></a>Exempel
---
**Puls pris**

*Meddelande*
```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*Mall*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.Body.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Blod tryck**

*Meddelande*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*Mall*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@Body.systolic && @Body.diastolic)]",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.Body.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.Body.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```

#### <a name="iotcentraljsonpathcontenttemplate"></a>IotCentralJsonPathContentTemplate

IotCentralJsonPathContentTemplate kräver inte heller DeviceIdExpression och TimestampExpression, och används när meddelanden som utvärderas skickas via funktionen [Exportera data](../../iot-central/core/howto-export-data.md) i [Azure IoT Central](../../iot-central/core/overview-iot-central.md). När du använder den här funktionen registreras enhets identiteten (förutsatt att enhets identifieraren från Azure IoT Central är registrerad som en identifierare för en enhets resurs på mål FHIR-servern) och tidsstämpeln för meddelandet är känd. Om du använder Azure IoT Centrals data export funktion men använder anpassade egenskaper i meddelande texten för enhets identiteten eller tidsstämpeln för mätning, kan du fortfarande använda JsonPathContentTemplate.

*Obs: när du använder IotCentralJsonPathContentTemplate bör TypeMatchExpression matcha hela meddelandet som en JToken. Se exemplen nedan.* 
##### <a name="examples"></a>Exempel
---
**Puls pris**

*Meddelande*
```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "HeartRate": "88",
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
*Mall*
```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@telemetry.HeartRate)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.HeartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Blod tryck**

*Meddelande*
```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "BloodPressure": {
            "Diastolic": "87",
            "Systolic": "123"
        }
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
*Mall*
```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "bloodPressure",
        "typeMatchExpression": "$..[?(@telemetry.BloodPressure.Diastolic && @telemetry.BloodPressure.Systolic)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Diastolic",
                "valueName": "bp_diastolic"
            },
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Systolic",
                "valueName": "bp_systolic"
            }
        ]
    }
}
```

## <a name="fhir-mapping"></a>FHIR-mappning
När enhets innehållet har extraherats till en normaliserad modell samlas data in och grupperas enligt enhets-ID, mätnings typ och tids period. Utdata från den här grupperingen skickas för konvertering till en FHIR-resurs ([observation](https://www.hl7.org/fhir/observation.html) för närvarande). Här styr du hur data mappas till en FHIR-observation i FHIR. Bör en observation skapas för en tidpunkt eller under en period på en timme? Vilka koder ska läggas till i observationen? Ska värdet representeras som [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) eller en [kvantitet](https://www.hl7.org/fhir/datatypes.html#Quantity)? Dessa data typer är alla alternativ för FHIR mappning av konfigurations kontroller.

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate
CodeValueFhirTemplate är för närvarande den enda mall som stöds i FHIR-mappningen för tillfället.  Det gör att du kan definiera koder, den effektiva perioden och observations värdet. Flera värde typer stöds: [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData), [CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept)och [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity). Tillsammans med dessa konfigurerbara värden hanteras identifieraren för observations resursen och länkas till rätt enhet och patient resurser automatiskt.

| Egenskap | Beskrivning 
| --- | ---
|**Typnamn**| Den typ av mått som mallen ska bindas till. Det bör finnas minst en enhets mappnings mall som matar ut den här typen.
|**PeriodInterval**|Den tids period som observationen skapas ska representera. De värden som stöds är 0 (en instans), 60 (en timme), 1440 (per dag).
|**Kategori**|Ett valfritt antal [CodeableConcepts](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept) som klassificerar typen av observationer som skapats.
|**Koder**|En eller flera [kodningar](http://hl7.org/fhir/datatypes-definitions.html#coding) som ska användas för observationer som skapats.
|**Koder []. Rikt**|Koden för [kodningen](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Koder []. Säker**|Systemet för [kodning](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Koder []. Hur**|Visningen av [kodningen](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Värde**|Det värde som ska extraheras och återges i observationen. Mer information finns i [Value Type templates](#valuetypes).
|**Komponenter**|*Valfritt:* En eller flera komponenter som ska skapas i observationen.
|**Komponenter []. Koder**|En eller flera [kodningar](http://hl7.org/fhir/datatypes-definitions.html#coding) som ska tillämpas på komponenten.
|**Komponenter []. Värde**|Värdet som ska extraheras och återges i komponenten. Mer information finns i [Value Type templates](#valuetypes).

### <a name="value-type-templates"></a>Mallar för värde typer <a name="valuetypes"></a>
Nedan visas de mallar för aktuell Värdetyp som stöds. I framtiden kan ytterligare mallar läggas till.
#### <a name="sampleddata"></a>SampledData
Representerar data typen [SampledData](http://hl7.org/fhir/datatypes.html#SampledData) FHIR. Observations mätningar skrivs till en värde ström som startar vid en tidpunkt och ökar framåt med den angivna perioden. Om det inte finns något värde `E` skrivs ett till data strömmen. Om perioden är sådan att två fler värden upptar samma position i data strömmen används det senaste värdet. Samma logik används när en observation med SampledData uppdateras.

| Egenskap | Beskrivning 
| --- | ---
|**DefaultPeriod**|Standard perioden i millisekunder som ska användas. 
|**Enhet**|Enheten som ska anges för SampledData ursprung. 

#### <a name="quantity"></a>Kvantitet
Representerar data typen [kvantitet](http://hl7.org/fhir/datatypes.html#Quantity) FHIR. Om det finns fler än ett värde i gruppen används bara det första värdet. När det nya värdet anländer som mappar till samma observation kommer det gamla värdet att skrivas över.

| Egenskap | Beskrivning 
| --- | --- 
|**Enhet**| Enhets representation.
|**Kod**| Kodad form för enheten.
|**System**| System som definierar det kodade enhets formuläret.

### <a name="codeableconcept"></a>CodeableConcept
Representerar data typen [CodeableConcept](http://hl7.org/fhir/datatypes.html#CodeableConcept) FHIR. Det faktiska värdet används inte.

| Egenskap | Beskrivning 
| --- | --- 
|**Text**|Oformaterad text representation. 
|**Koder**|En eller flera [kodningar](http://hl7.org/fhir/datatypes-definitions.html#coding) som ska användas för observationer som skapats.
|**Koder []. Rikt**|Koden för [kodningen](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Koder []. Säker**|Systemet för [kodning](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Koder []. Hur**|Visningen av [kodningen](http://hl7.org/fhir/datatypes-definitions.html#coding).

### <a name="examples"></a>Exempel
**Hjärta – SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "8867-4",
                "system": "http://loinc.org",
                "display": "Heart rate"
            }
        ],
        "periodInterval": 60,
        "typeName": "heartrate",
        "value": {
            "defaultPeriod": 5000,
            "unit": "count/min",
            "valueName": "hr",
            "valueType": "SampledData"
        }
    }
}
```
---
**Steg – SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "55423-8",
                "system": "http://loinc.org",
                "display": "Number of steps"
            }
        ],        
        "periodInterval": 60,
        "typeName": "stepsCount",
        "value": {
            "defaultPeriod": 5000,
            "unit": "",
            "valueName": "steps",
            "valueType": "SampledData"
        }
    }
}
```
---
**Blod tryck – SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 60,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "SampledData"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "SampledData"
                }
            }
        ]
    }
}
```
---
**Blod tryck – kvantitet**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 0,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "Quantity"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "Quantity"
                }
            }
        ]
    }
}
```
---
**Enheten har tagits bort – CodeableConcept**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "deviceEvent",
                "system": "https://www.mydevice.com/v1",
                "display": "Device Event"
            }
        ],
        "periodInterval": 0,
        "typeName": "deviceRemoved",
        "value": {
            "text": "Device Removed",
            "codes": [
                {
                    "code": "deviceRemoved",
                    "system": "https://www.mydevice.com/v1",
                    "display": "Device Removed"
                }
            ],
            "valueName": "deviceRemoved",
            "valueType": "CodeableConcept"
        }
    }
}
```
---

## <a name="next-steps"></a>Nästa steg

Ta en titt på vanliga frågor om Azure IoT Connector för FHIR (för hands version).

>[!div class="nextstepaction"]
>[Vanliga frågor och svar om Azure IoT Connector för FHIR](fhir-faq.md)

* I Azure Portal kallas Azure IoT Connector för FHIR IoT Connector (för hands version). FHIR är ett registrerat varumärke som tillhör HL7 och används med behörigheten för HL7.