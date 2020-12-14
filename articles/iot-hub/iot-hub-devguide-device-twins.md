---
title: Förstå hur Azure IoT Hub enheten är dubbla | Microsoft Docs
description: Utvecklings guide – Använd enheten för att synkronisera tillstånds-och konfigurations data mellan IoT Hub och dina enheter
author: nehsin
manager: philmea
ms.author: nehsin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom:
- mqtt
- 'Role: Cloud Development'
ms.openlocfilehash: b83faecb16ac09a47a0ade25474f7a5b3ecd4296
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400936"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Förstå och Använd enheten dubbla i IoT Hub

*Enhets dubbla* är JSON-dokument som lagrar information om enhets tillstånd, inklusive metadata, konfigurationer och villkor. Azure IoT Hub lagrar en enhetstvilling för varje enhet som du ansluter till IoT Hub. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här artikeln beskrivs:

* Enhetens struktur:: *taggar*, *önskade* och *rapporterade egenskaper*.
* De åtgärder som enhets appar och Server delar kan utföra på enheten är dubbla.

Använd enheten:

* Lagra enhetsspecifika metadata i molnet. Till exempel distributions platsen för en Vending-dator.

* Rapportera aktuell statusinformation, till exempel tillgängliga funktioner och villkor från din enhets app. Till exempel är en enhet ansluten till din IoT-hubb via mobil nät eller WiFi.

* Synkronisera tillstånd för långvariga arbets flöden mellan enhets app och backend-app. Till exempel, när Server delen för lösningen anger den nya versionen av inbyggd program vara som ska installeras och Device-appen rapporterar de olika stegen i uppdaterings processen.

* Fråga om metadata, konfiguration eller tillstånd för enheten.

Information om hur du använder rapporterade egenskaper, meddelanden från enhet till moln eller fil uppladdning finns i [rikt linjer för kommunikation mellan enheter och moln](iot-hub-devguide-d2c-guidance.md) .

Information om hur du använder önskade egenskaper, direkta metoder eller meddelanden från moln till enhet finns i [rikt linjer för kommunikation från moln till enhet](iot-hub-devguide-c2d-guidance.md) .

## <a name="device-twins"></a>Enhets dubbla

Enheten är kopplad till lagrings enhets information som:

* Enhets-och backend-slut kan användas för att synkronisera enhets villkor och-konfiguration.

* Lösningens Server del kan användas för att fråga efter och fokusera på tids krävande åtgärder.

Livs cykeln för en enhet med dubbla är kopplad till motsvarande [enhets identitet](iot-hub-devguide-identity-registry.md). Enhetens dubblare skapas och tas bort implicit när en enhets identitet skapas eller tas bort i IoT Hub.

En enhet är dubbla är ett JSON-dokument som innehåller:

* **Taggar**. Ett avsnitt i JSON-dokumentet som server delen av lösningen kan läsa från och skriva till. Taggarna är inte synliga för enhets program.

* **Önskade egenskaper**. Används tillsammans med rapporterade egenskaper för att synkronisera enhets konfiguration eller-villkor. Lösningens Server del kan ange önskade egenskaper och appen kan läsa dem. Device-appen kan också ta emot aviseringar om ändringar i önskade egenskaper.

* **Rapporterade egenskaper**. Används tillsammans med önskade egenskaper för att synkronisera enhetens konfiguration eller villkor. Device-appen kan ange rapporterade egenskaper och Server delen för lösningen kan läsa och fråga dem.

* **Egenskaper för enhets identitet**. Roten av enhetens dubbla JSON-dokument innehåller skrivskyddade egenskaper från motsvarande enhets identitet som lagras i [identitets registret](iot-hub-devguide-identity-registry.md). Egenskaper `connectionStateUpdatedTime` och tas `generationId` inte med.

![Skärm bild av enhetens dubbla egenskaper](./media/iot-hub-devguide-device-twins/twin.png)

I följande exempel visas ett enhets dubbla JSON-dokument:

```json
{
    "deviceId": "devA",
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

I rotobjektet finns enhetens identitets egenskaper och behållar objekt för `tags` och båda `reported` egenskaperna och `desired` . `properties`Behållaren innehåller skrivskyddade element ( `$metadata` , `$etag` och `$version` ) som beskrivs i avsnitten [enhets dubbla metadata](iot-hub-devguide-device-twins.md#device-twin-metadata) och [optimistisk samtidighet](iot-hub-devguide-device-twins.md#optimistic-concurrency) .

### <a name="reported-property-example"></a>Exempel på rapporterad egenskap

I föregående exempel innehåller enheten dubbla en `batteryLevel` egenskap som rapporteras av enhetens app. Den här egenskapen gör det möjligt att fråga och använda enheter baserat på den senaste rapporterade batteri nivån. Andra exempel är enhets funktioner för enhets program rapportering eller anslutnings alternativ.

> [!NOTE]
> Rapporterade egenskaper fören klar scenarier där lösningens Server del är intresse rad av det sista kända värdet för en egenskap. Använd [enhets-till-moln-meddelanden](iot-hub-devguide-messages-d2c.md) om lösningens Server del behöver bearbeta telemetri i form av sekvenser med tidsstämplade händelser, t. ex. tids serier.

### <a name="desired-property-example"></a>Exempel på önskad egenskap

I föregående exempel `telemetryConfig` används enhetens dubbla önskade och rapporterade egenskaper av lösningens Server del och Device-appen för att synkronisera telemetri-konfigurationen för den här enheten. Exempel:

1. Server delen för lösningen anger önskad egenskap med det önskade konfiguration svärdet. Här är den del av dokumentet med önskad egenskaps uppsättning:

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. Enhets appen meddelas om ändringen omedelbart om den är ansluten eller vid första åter anslutning. Device-appen rapporterar sedan den uppdaterade konfigurationen (eller ett fel tillstånd med hjälp av `status` egenskapen). Här är den del av de rapporterade egenskaperna:

   ```json
   "reported": {
       "telemetryConfig": {
           "sendFrequency": "5m",
           "status": "success"
       }
       ...
   }
   ```

3. Lösningens Server del kan spåra resultatet av konfigurations åtgärden på många enheter genom att [fråga](iot-hub-devguide-query-language.md) enheten i flera enheter.

> [!NOTE]
> Föregående kodfragment är exempel, optimerade för läsbarhet, av ett sätt att koda en enhets konfiguration och dess status. IoT Hub tillhandahåller inte ett visst schema för enheten med dubbla önskade och rapporterade egenskaper i enheten.
> 

Du kan använda dubbla för att synkronisera långvariga åtgärder, till exempel uppdateringar av inbyggd program vara. Mer information om hur du använder egenskaper för att synkronisera och spåra en tids krävande åtgärd mellan enheter finns i [använda önskade egenskaper för att konfigurera enheter](tutorial-device-twins.md).

## <a name="back-end-operations"></a>Server dels åtgärder

Lösningens Server del fungerar på enheten med dubbla med hjälp av följande atomiska åtgärder, exponeras via HTTPS:

* **Hämta enhetens dubbla efter ID**. Den här åtgärden returnerar enhetens dubbla dokument, inklusive Taggar och önskade och rapporterat system egenskaper.

* **Delvis uppdatering av enhets enhet** Den här åtgärden gör att lösningens Server del kan användas för att delvis uppdatera taggarna eller önskade egenskaper i en enhet. Den partiella uppdateringen uttrycks som ett JSON-dokument som lägger till eller uppdaterar en egenskap. Egenskaperna har angetts till `null` tas bort. I följande exempel skapas en ny önskad egenskap med värde `{"newProperty": "newValue"}` , vilket skriver över det befintliga värdet för `existingProperty` med `"otherNewValue"` och tar bort `otherOldProperty` . Inga andra ändringar har gjorts i befintliga önskade egenskaper eller Taggar:

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

* **Få dubbla meddelanden**. Den här åtgärden gör att lösnings Server delen får ett meddelande när den dubbla ändras. För att göra det måste IoT-lösningen skapa en väg och ange data källan som lika med *twinChangeEvents*. Som standard finns inga sådana vägar i förväg, så inga dubbla meddelanden skickas. Om ändrings frekvensen är för hög, eller av andra orsaker, t. ex. interna problem, kan IoT Hub bara skicka ett meddelande som innehåller alla ändringar. Om ditt program behöver tillförlitlig granskning och loggning av alla mellanliggande tillstånd bör du därför använda meddelanden från enheten till molnet. Det dubbla aviserings meddelandet innehåller egenskaper och brödtext.

  - Egenskaper

    | Namn | Värde |
    | --- | --- |
    $content-typ | application/json |
    $iothub-enqueuedtime |  Tid när meddelandet skickades |
    $iothub-meddelande källa | twinChangeEvents |
    $content kodning | UTF-8 |
    deviceId | ID för enheten |
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

Alla föregående åtgärder har stöd för [optimistisk samtidighet](iot-hub-devguide-device-twins.md#optimistic-concurrency) och kräver **ServiceConnect** -behörighet, enligt definitionen i [kontrol lera åtkomst till IoT Hub](iot-hub-devguide-security.md).

Förutom dessa åtgärder kan lösningens Server del:

* Fråga enheten till varandra med hjälp av SQL-like [IoT Hub frågespråk](iot-hub-devguide-query-language.md).

* Utför åtgärder på stora enhets uppsättningar med [jobb](iot-hub-devguide-jobs.md).

## <a name="device-operations"></a>Enhets åtgärder

Enhetens app fungerar på enheten med dubbla med följande atomiska åtgärder:

* **Hämta enhet dubbla**. Den här åtgärden returnerar enhetens dubbla dokument (inklusive önskade och rapporterade system egenskaper) för den anslutna enheten. (Taggar är inte synliga för enhets program.)

* **Delvis uppdatera rapporterade egenskaper**. Den här åtgärden aktiverar den partiella uppdateringen av de rapporterade egenskaperna för den anslutna enheten. Den här åtgärden använder samma JSON-uppdaterings format som lösningens Server del använder för att få en del uppdatering av önskade egenskaper.

* **Observera önskade egenskaper**. Den aktuella anslutna enheten kan välja att få ett meddelande om uppdateringar av önskade egenskaper när de sker. Enheten får samma typ av uppdatering (delvis eller fullständig ersättning) som körs av Server delen för lösningen.

Alla föregående åtgärder kräver **DeviceConnect** -behörighet, enligt definitionen i [kontrol lera åtkomst till IoT Hub](iot-hub-devguide-security.md).

SDK: er för [Azure IoT-enheter](iot-hub-devguide-sdks.md) gör det enkelt att använda föregående åtgärder från många olika språk och plattformar. Mer information om IoT Hub primitiver för önskade synkronisering av egenskaper finns i [enhets åter anslutnings flöde](iot-hub-devguide-device-twins.md#device-reconnection-flow).

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

## <a name="device-twin-size"></a>Enhetens dubbla storlek

IoT Hub tillämpar en storleks gräns på 8 KB för värdet av och `tags` en storlek på 32 kB som är begränsad till värdet för `properties/desired` och `properties/reported` . Dessa summor är exklusivt för skrivskyddade element som `$etag` , `$version` och `$metadata/$lastUpdated` .

Den dubbla storleken beräknas på följande sätt:

* För varje egenskap i JSON-dokumentet IoT Hub ackumulerade beräkningar och lägger till längden på egenskapens nyckel och värde.

* Egenskaps nycklar betraktas som UTF8-kodade strängar.

* Enkla egenskaps värden betraktas som UTF8-kodade strängar, numeriska värden (8 byte) eller booleska värden (4 byte).

* Storleken på UTF8-kodade strängar beräknas genom att räkna alla tecken, förutom UNICODE-kontrolltecken (segmenten C0 och C1).

* Komplexa egenskaps värden (kapslade objekt) beräknas baserat på den sammanlagda storleken på de egenskaps nycklar och egenskaps värden som de innehåller.

IoT Hub avvisar alla åtgärder som skulle öka storleken på `tags` , `properties/desired` eller `properties/reported` dokumenten över gränsen.

## <a name="device-twin-metadata"></a>Enhetens dubbla metadata

IoT Hub behåller tidsstämpeln för den senaste uppdateringen för varje JSON-objekt i enhetens dubbla önskade och rapporterade egenskaper. Tidsstämplar anges i UTC och kodas i [iso8601](https://en.wikipedia.org/wiki/ISO_8601) -format `YYYY-MM-DDTHH:MM:SS.mmmZ` .

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
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
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

Enhetens dubbla önskade och rapporterade egenskaper har inte ETags, men har ett `$version` värde som garanterar att det ökar. På samma sätt som en ETag, kan versionen användas av uppdaterings parten för att tvinga fram konsekvens av uppdateringar. Till exempel en enhets app för en rapporterad egenskap eller lösningens Server del för en önskad egenskap.

Versioner är också användbara när en iakttagit agent (t. ex. enhets appen som underrättar önskade egenskaper) måste stämma av races mellan resultatet av en Hämta-åtgärd och ett uppdaterings meddelande. [Avsnittet flöde för enhets åter anslutning](iot-hub-devguide-device-twins.md#device-reconnection-flow) innehåller mer information.

## <a name="device-reconnection-flow"></a>Flöde för åter anslutning av enhet

IoT Hub bevarar inte önskade egenskaper uppdaterings meddelanden för frånkopplade enheter. Det följer på att en enhet som ansluter måste hämta ett fullständigt önskade egenskaps dokument, förutom att prenumerera på uppdaterings meddelanden. Med tanke på möjligheten att races mellan uppdaterings meddelanden och fullständig hämtning måste följande flöde vara säkerställt:

1. Enhets appen ansluter till en IoT-hubb.
2. Enhets app prenumererar på önskade egenskaper uppdatera meddelanden.
3. Enhets appen hämtar det fullständiga dokumentet för önskade egenskaper.

Device-appen kan ignorera alla meddelanden med `$version` mindre eller samma version av det fullständiga hämtade dokumentet. Den här metoden är möjlig eftersom IoT Hub garanterar att versioner alltid ökar.

> [!NOTE]
> Den här logiken är redan implementerad i SDK: er för [Azure IoT-enheter](iot-hub-devguide-sdks.md). Den här beskrivningen är användbar endast om enhets appen inte kan använda någon av Azure IoT-enhetens SDK: er och måste program mera MQTT-gränssnittet direkt.
> 

## <a name="additional-reference-material"></a>Ytterligare referens material

Andra referens ämnen i IoT Hub Developer Guide är:

* I artikeln [IoT Hub slut punkter](iot-hub-devguide-endpoints.md) beskrivs de olika slut punkter som varje IoT-hubb visar för körnings-och hanterings åtgärder.

* I artikeln [begränsning och kvoter](iot-hub-devguide-quotas-throttling.md) beskrivs de kvoter som gäller för den IoT Hub tjänsten och begränsnings beteendet som kan förväntas när du använder tjänsten.

* Artikeln [Azure IoT-enhet och tjänst-SDK](iot-hub-devguide-sdks.md) : er innehåller de olika språk-SDK: er som du kan använda när du utvecklar både enhets-och tjänst program som samverkar med IoT Hub.

* I artikeln [IoT Hub frågespråk för enhets-och skriv-, jobb-och meddelande cirkulations](iot-hub-devguide-query-language.md) artikeln beskrivs det IoT Hub frågespråk som du kan använda för att hämta information från IoT Hub om din enhets dubblare och jobb.

* I artikeln [IoT Hub MQTT support](iot-hub-mqtt-support.md) finns mer information om IoT Hub-stöd för MQTT-protokollet.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig mer om enheten, kan du vara intresse rad av följande avsnitt om IoT Hub Developer-Guide:

* [Förstå och Använd modul dubbla i IoT Hub](iot-hub-devguide-module-twins.md)
* [Anropa en direkt metod på en enhet](iot-hub-devguide-direct-methods.md)
* [Schemalägga jobb på flera enheter](iot-hub-devguide-jobs.md)

Om du vill testa några av de begrepp som beskrivs i den här artikeln kan du läsa följande IoT Hub Självstudier:

* [Så här använder du enheten med dubbla](iot-hub-node-node-twin-getstarted.md)
* [Använda enhetens dubbla egenskaper](tutorial-device-twins.md)
* [Enhetshantering med Azure IoT-verktyg för VS Code](iot-hub-device-management-iot-toolkit.md)
