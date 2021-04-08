---
title: Lär dig om dubbla Azure IoT Hub-moduler | Microsoft Docs
description: Utvecklings guide – använda modulen för att synkronisera tillstånds-och konfigurations data mellan IoT Hub och dina enheter
author: nehsin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: nehsin
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 6e728eaf8335a102e38a3b4b07ab5e504d452294
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91996463"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Förstå och Använd modul dubbla i IoT Hub

I den här artikeln förutsätter vi att du har läst [förstå och använder enheten dubbla i IoT Hub](iot-hub-devguide-device-twins.md) först. I IoT Hub kan du under varje enhets identitet skapa upp till 50 modul identiteter. Varje moduls identitet genererar implicit en modul med dubbla. Precis som enhets dubbla, är modulerna sammanflätade JSON-dokument som lagrar information om modulens tillstånd, inklusive metadata, konfigurationer och villkor. Azure IoT Hub hanterar en modul som är sammanflätad för varje modul som du ansluter till IoT Hub. 

På enhets sidan kan du med IoT Hub enhets-SDK: er skapa moduler där var och en öppnar en oberoende anslutning till IoT Hub. Med den här funktionen kan du använda separata namn rymder för olika komponenter på enheten. Du kan till exempel ha en Vending-dator som har tre olika sensorer. Varje sensor styrs av olika avdelningar i företaget. Du kan skapa en modul för varje sensor. På så sätt kan varje avdelning bara skicka jobb eller direkta metoder till den sensor som de styr, undvika konflikter och användar fel.

 Modul identitet och modul dubbla ger samma funktioner som enhets identitet och enhet, men med en bättre precision. Med den här bättre precisionen kan enheter, till exempel operativ systembaserade enheter eller inbyggda enheter hantera flera komponenter, för att isolera konfiguration och villkor för var och en av dessa komponenter. Modulens identitet och modul ger en hanterings uppdelning av problem när du arbetar med IoT-enheter som har modulära program varu komponenter. Vi strävar efter att stödja alla enheter med dubbla funktioner i modul dubbla nivåer enligt modul, med en allmän tillgänglighet. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här artikeln beskrivs:

* Strukturen för modulerna "dubbla: *taggar*, *önskade* och *rapporterade egenskaper*.
* Åtgärderna som moduler och Server delar kan utföra i modulen är dubbla.

Information om hur du använder rapporterade egenskaper, meddelanden från enhet till moln eller fil uppladdning finns i [rikt linjer för kommunikation mellan enheter och moln](iot-hub-devguide-d2c-guidance.md) .

Information om hur du använder önskade egenskaper, direkta metoder eller meddelanden från moln till enhet finns i [rikt linjer för kommunikation från moln till enhet](iot-hub-devguide-c2d-guidance.md) .

## <a name="module-twins"></a>Modul, dubbla

Modul sammanflätar information om Store-modul:

* Moduler på enheten och IoT Hub kan använda för att synkronisera villkor och konfiguration för modulen.

* Lösningens Server del kan användas för att fråga efter och fokusera på tids krävande åtgärder.

Livs cykeln för en modul med dubbla är kopplad till motsvarande [modul identitet](iot-hub-devguide-identity-registry.md). Modulerna skapas implicit och tas bort när en moduls identitet skapas eller tas bort i IoT Hub.

En modul dubbla är ett JSON-dokument som innehåller:

* **Taggar**. Ett avsnitt i JSON-dokumentet som server delen av lösningen kan läsa från och skriva till. Taggarna är inte synliga för moduler på enheten. Taggar har angetts för fråge syfte.

* **Önskade egenskaper**. Används tillsammans med rapporterade egenskaper för att synkronisera konfiguration eller villkor för modulen. Server delen för lösningen kan ange önskade egenskaper och modulens app kan läsa dem. Module-appen kan också ta emot aviseringar om ändringar i önskade egenskaper.

* **Rapporterade egenskaper**. Används tillsammans med önskade egenskaper för att synkronisera konfiguration eller villkor för modulen. Module-appen kan ange rapporterade egenskaper och Server delen för lösningen kan läsa och fråga dem.

* **Modulens identitets egenskaper**. Roten i modulens dubbla JSON-dokument innehåller skrivskyddade egenskaper från motsvarande modul identitet lagrad i [identitets registret](iot-hub-devguide-identity-registry.md).

![Arkitektur framställning av enhetens dubbla](./media/iot-hub-devguide-device-twins/module-twin.jpg)

I följande exempel visas ett moduls dubbla JSON-dokument:

```json
{
    "deviceId": "devA",
    "moduleId": "moduleA",
    "etag": "AAAAAAAAAAc=", 
    "status": "enabled",
    "statusReason": "provisioned",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "connected",
    "lastActivityTime": "2015-02-30T16:24:48.789Z",
    "cloudToDeviceMessageCount": 0, 
    "authenticationType": "sas",
    "x509Thumbprint": {     
        "primaryThumbprint": null, 
        "secondaryThumbprint": null 
    }, 
    "version": 2, 
    "tags": {
        "$etag": "123",
        "deploymentLocation": {
            "building": "43",
            "floor": "1"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata" : {...},
            "$version": 1
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

I rotobjektet finns modulens identitets egenskaper och behållar objekt för `tags` och båda `reported` egenskaperna och `desired` . `properties`Behållaren innehåller skrivskyddade element ( `$metadata` , `$etag` och `$version` ) som beskrivs i avsnittet om [modulens dubbla metadata](iot-hub-devguide-module-twins.md#module-twin-metadata) och [optimistisk concurrency](iot-hub-devguide-device-twins.md#optimistic-concurrency) .

### <a name="reported-property-example"></a>Exempel på rapporterad egenskap

I det föregående exemplet innehåller modul två en `batteryLevel` egenskap som rapporteras av modulens app. Den här egenskapen gör det möjligt att fråga och använda moduler baserat på den senaste rapporterade batteri nivån. Andra exempel är funktioner för modulens app repor ting module eller anslutnings alternativ.

> [!NOTE]
> Rapporterade egenskaper fören klar scenarier där lösningens Server del är intresse rad av det sista kända värdet för en egenskap. Använd [enhets-till-moln-meddelanden](iot-hub-devguide-messages-d2c.md) om lösningens Server del måste bearbeta telemetri i form av sekvenser med tidsstämplade händelser, t. ex. tids serier.

### <a name="desired-property-example"></a>Exempel på önskad egenskap

I föregående exempel `telemetryConfig` används modulens dubbla önskade och rapporterade egenskaper av lösningens Server del och modulens app för att synkronisera telemetri-konfigurationen för den här modulen. Exempel:

1. Server delen för lösningen anger önskad egenskap med det önskade konfiguration svärdet. Här är den del av dokumentet med önskad egenskaps uppsättning:

    ```json
    ...
    "desired": {
        "telemetryConfig": {
            "sendFrequency": "5m"
        },
        ...
    },
    ...
    ```

2. Module-appen meddelas om ändringen omedelbart om den är ansluten, eller vid första åter anslutning. Module-appen rapporterar sedan den uppdaterade konfigurationen (eller ett fel tillstånd med hjälp av `status` egenskapen). Här är den del av de rapporterade egenskaperna:

    ```json
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ```

3. Lösningens Server del kan spåra resultatet av konfigurations åtgärden över flera moduler, genom att [fråga](iot-hub-devguide-query-language.md) modulerna åt.

> [!NOTE]
> Föregående kodfragment är exempel, optimerade för läsbarhet, av ett sätt att koda en moduls konfiguration och dess status. IoT Hub tillhandahåller inte ett särskilt schema för modulen och rapporterade egenskaper i modulen.
> 
> 

## <a name="back-end-operations"></a>Server dels åtgärder
Lösningens Server del fungerar i modulen dubbla med hjälp av följande atomiska åtgärder som exponeras via HTTPS:

* **Hämta modul dubbla efter ID**. Den här åtgärden returnerar modulens dubbla dokument, inklusive Taggar och önskade och rapporterade system egenskaper.

* **Delvis uppdaterad modul, dubbel**. Den här åtgärden gör att lösningens Server del kan användas för att delvis uppdatera taggarna eller önskade egenskaper i en modul. Den partiella uppdateringen uttrycks som ett JSON-dokument som lägger till eller uppdaterar en egenskap. Egenskaperna har angetts till `null` tas bort. I följande exempel skapas en ny önskad egenskap med värde `{"newProperty": "newValue"}` , vilket skriver över det befintliga värdet för `existingProperty` med `"otherNewValue"` och tar bort `otherOldProperty` . Inga andra ändringar har gjorts i befintliga önskade egenskaper eller Taggar:

    ```json
    {
        "properties": {
            "desired": {
                "newProperty": {
                    "nestedProperty": "newValue"
                },
                "existingProperty": "otherNewValue",
                "otherOldProperty": null
            }
        }
    }
    ```

* **Ersätt önskade egenskaper**. Den här åtgärden gör att Server delen av lösningen helt skriver över alla befintliga önskade egenskaper och ersätter ett nytt JSON-dokument för `properties/desired` .

* **Ersätt Taggar**. Den här åtgärden gör att Server delen av lösningen fullständigt skriver över alla befintliga taggar och ersätter ett nytt JSON-dokument för `tags` .

* **Få dubbla meddelanden**. Den här åtgärden gör att lösnings Server delen får ett meddelande när den dubbla ändras. För att göra det måste IoT-lösningen skapa en väg och ange data källan som lika med *twinChangeEvents*. Som standard skickas inga dubbla meddelanden, det vill säga inga sådana vägar. Om ändrings frekvensen är för hög, eller av andra orsaker, t. ex. interna problem, kan IoT Hub bara skicka ett meddelande som innehåller alla ändringar. Om ditt program behöver tillförlitlig granskning och loggning av alla mellanliggande tillstånd bör du därför använda meddelanden från enheten till molnet. Det dubbla aviserings meddelandet innehåller egenskaper och brödtext.

  - Egenskaper

    | Name | Värde |
    | --- | --- |
    $content-typ | application/json |
    $iothub-enqueuedtime |  Tid när meddelandet skickades |
    $iothub-meddelande källa | twinChangeEvents |
    $content kodning | UTF-8 |
    deviceId | ID för enheten |
    moduleId | ID för modulen |
    hubName | Namn på IoT Hub |
    operationTimestamp | [Iso8601](https://en.wikipedia.org/wiki/ISO_8601) tidsstämpel för åtgärd |
    iothub – meddelande schema | twinChangeNotification |
    opType | "replaceTwin" eller "updateTwin" |

    Meddelande system egenskaper föregås av `$` symbolen.

  - Brödtext
        
    Det här avsnittet innehåller alla dubbla ändringar i JSON-format. Den använder samma format som en korrigering, med skillnaden att den kan innehålla alla dubbla avsnitt: taggar, egenskaper. rapporterade, egenskaper. önskade och innehåller $metadata element. Exempel:

    ```json
    {
      "properties": {
          "desired": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          },
          "reported": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          }
      }
    }
    ```

Alla föregående åtgärder har stöd för [optimistisk samtidighet](iot-hub-devguide-device-twins.md#optimistic-concurrency) och kräver **ServiceConnect** -behörighet, enligt definitionen i artikeln [kontrol lera åtkomst till IoT Hub](iot-hub-devguide-security.md) .

Förutom dessa åtgärder kan lösningens Server del skicka frågor till modulen med SQL-liknande [IoT Hub frågespråk](iot-hub-devguide-query-language.md).

## <a name="module-operations"></a>Modul åtgärder

Modulens App körs i modulen dubbla med följande atomiska åtgärder:

* **Hämta modul dubbla**. Den här åtgärden returnerar modulens dubbla dokument (inklusive Taggar och önskade och rapporterade system egenskaper) för den aktuella anslutna modulen.

* **Delvis uppdatera rapporterade egenskaper**. Den här åtgärden aktiverar den partiella uppdateringen av de rapporterade egenskaperna för den för tillfället anslutna modulen. Den här åtgärden använder samma JSON-uppdaterings format som lösningens Server del använder för att få en del uppdatering av önskade egenskaper.

* **Observera önskade egenskaper**. Den aktuella anslutna modulen kan välja att bli meddelad om uppdateringar av önskade egenskaper när de sker. Modulen får samma typ av uppdatering (delvis eller fullständig ersättning) som körs av Server delen för lösningen.

Alla föregående åtgärder kräver **ModuleConnect** -behörighet, enligt definitionen i artikeln [kontrol lera åtkomst till IoT Hub](iot-hub-devguide-security.md) .

SDK: er för [Azure IoT-enheter](iot-hub-devguide-sdks.md) gör det enkelt att använda föregående åtgärder från många olika språk och plattformar.

## <a name="tags-and-properties-format"></a>Format för taggar och egenskaper

Taggar, önskade egenskaper och rapporterade egenskaper är JSON-objekt med följande begränsningar:

* **Nycklar**: alla nycklar i JSON-objekt är UTF-8-kodade, SKIFT läges känsliga och upp till 1 KB. Tillåtna tecken utesluter UNICODE-kontrolltecken (segment C0 och C1) och `.` , `$` , och SP.

* **Värden**: alla värden i JSON-objekt kan vara av följande JSON-typer: Boolean, Number, String, Object. Matriser stöds också.

    * Heltal kan ha ett lägsta värde på-4503599627370496 och ett högsta värde på 4503599627370495.

    * Sträng värden är UTF-8-kodade och kan ha en maximal längd på 4 KB.

* **Djup**: det maximala djupet för JSON-objekt i taggar, önskade egenskaper och rapporterade egenskaper är 10. Till exempel är följande objekt giltigt:

   ```json
   {
       ...
       "tags": {
           "one": {
               "two": {
                   "three": {
                       "four": {
                           "five": {
                               "six": {
                                   "seven": {
                                       "eight": {
                                           "nine": {
                                               "ten": {
                                                   "property": "value"
                                               }
                                           }
                                       }
                                   }
                               }
                           }
                       }
                   }
               }
           }
       },
       ...
   }
   ```

## <a name="module-twin-size"></a>Modulens dubbla storlek

IoT Hub tillämpar en storleks gräns på 8 KB för värdet av och `tags` en storlek på 32 kB som är begränsad till värdet för `properties/desired` och `properties/reported` . Dessa summor är exklusivt för skrivskyddade element som `$etag` , `$version` och `$metadata/$lastUpdated` .

Den dubbla storleken beräknas på följande sätt:

* För varje egenskap i JSON-dokumentet IoT Hub ackumulerade beräkningar och lägger till längden på egenskapens nyckel och värde.

* Egenskaps nycklar betraktas som UTF8-kodade strängar.

* Enkla egenskaps värden betraktas som UTF8-kodade strängar, numeriska värden (8 byte) eller booleska värden (4 byte).

* Storleken på UTF8-kodade strängar beräknas genom att räkna alla tecken, förutom UNICODE-kontrolltecken (segmenten C0 och C1).

* Komplexa egenskaps värden (kapslade objekt) beräknas baserat på den sammanlagda storleken på de egenskaps nycklar och egenskaps värden som de innehåller.

IoT Hub avvisar alla åtgärder som skulle öka storleken på dokumenten över gränsen.

## <a name="module-twin-metadata"></a>Modul, dubbla metadata

IoT Hub behåller tidsstämpeln för den senaste uppdateringen för varje JSON-objekt i modulen, och rapporterade egenskaper. Tidsstämplar anges i UTC och kodas i [iso8601](https://en.wikipedia.org/wiki/ISO_8601) -format `YYYY-MM-DDTHH:MM:SS.mmmZ` .
Exempel:

```json
{
    ...
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$lastUpdated": "2016-03-30T16:24:48.789Z"
                },
                "$lastUpdated": "2016-03-30T16:24:48.789Z"
            },
            "$version": 23
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": "5m",
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                },
                "batteryLevel": {
                    "$lastUpdated": "2016-04-01T16:35:48.789Z"
                },
                "$lastUpdated": "2016-04-01T16:24:48.789Z"
            },
            "$version": 123
        }
    }
    ...
}
```

Den här informationen lagras på alla nivåer (inte bara löv till JSON-strukturen) för att bevara uppdateringar som tar bort objekt nycklar.

## <a name="optimistic-concurrency"></a>Optimistisk samtidighet

Taggar, önskade och rapporterade egenskaper alla stöder optimistisk samtidighet.
Taggar har en ETag, som per [RFC7232](https://tools.ietf.org/html/rfc7232), som representerar TAGGENs JSON-representation. Du kan använda ETags i villkorliga uppdaterings åtgärder från lösningens Server del för att säkerställa konsekvens.

Modulernas dubbla önskade och rapporterade egenskaper har inte ETags, men har ett `$version` värde som garanterar att det ökar. På samma sätt som en ETag, kan versionen användas av uppdaterings parten för att tvinga fram konsekvens av uppdateringar. Till exempel en modul-app för en rapporterad egenskap eller lösningens Server del för en önskad egenskap.

Versioner är också användbara när en iakttagit agent (t. ex. module-appen som underrättar önskade egenskaper) måste stämma av races mellan resultatet av en Hämta-åtgärd och ett uppdaterings meddelande. Avsnittet [enhets återkopplings flöde](iot-hub-devguide-device-twins.md#device-reconnection-flow) innehåller mer information. 

## <a name="next-steps"></a>Nästa steg

Om du vill testa några av de begrepp som beskrivs i den här artikeln kan du läsa följande IoT Hub Självstudier:

* [Kom igång med IoT Hub modulens identitet och modul dubbla med .NET-Server delen och .NET-enheten](iot-hub-csharp-csharp-module-twin-getstarted.md)
