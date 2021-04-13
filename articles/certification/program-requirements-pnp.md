---
title: Krav för IoT Plug and Play-certifiering
description: Krav för IoT Plug and Play-certifierings program
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: IoT Plug and Play Certification Requirements
ms.service: certification
ms.openlocfilehash: b26fab6f8b92e3cb996f545f1f6201d32b1eaced
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310520"
---
# <a name="iot-plug-and-play-certification-requirements"></a>Krav för IoT Plug and Play-certifiering

Det här dokumentet beskriver de specifika enhets funktioner som visas i Azure IoT-katalogen. En funktion är ett bevarat enhets attribut som kan vara program varu implementering eller kombination av program-och maskin varu implementeringar.

## <a name="program-purpose"></a>Program syfte

IoT Plug and Play Preview gör det möjligt för lösnings byggare att integrera smarta enheter med sina lösningar utan manuell konfiguration. I IoT Plug and Play är en enhets modell som en enhet använder för att annonsera sina funktioner till ett IoT Plug and Play-aktiverat program. Den här modellen är strukturerad som en uppsättning element: telemetri, egenskaper och kommandon.

Att garantera IoT Plug and Play-certifiering är:

1.  Definierade enhets modeller och gränssnitt är kompatibla med det  [digitala, dubbla definitions språket](https://github.com/Azure/opendigitaltwins-dtdl)  
2.  Säker etablering och enkel överföring av ID-omfånget ägarskap i enhets etablerings tjänster
3.  Enkel integrering med Azure IoT-baserade lösningar med hjälp av [digitala dubbla API: er](../iot-pnp/concepts-digital-twin.md)  : Azure IoT Hub och Azure IoT Central
4.  Verifierad produkt-sanningen på certifierade enheter

## <a name="requirements"></a>Krav

**Kunna Enhet till moln: Syftet med testet är att se till att enheter som skickar telemetri fungerar med IoT Hub**

| **Namn**                | IoTPnP. D2C                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Tillgänglighet för mål** | Tillgängligt nu                                                |
| **Gäller för**          | Löv enhet/gräns enhet                                      |
| **Operativsystem**                  | Oberoende                                                     |
| **Validerings typ**     | Automatiserad                                                    |
| **Signaturverifiering**          | Enheten måste skicka eventuella telemetri scheman till IoT Hub. Microsoft tillhandahåller [Portal arbets flödet](https://certify.azure.com) för att köra testerna. Enhet till moln (krävs): **1.** Verifierar att enheten kan skicka meddelande till AICS-hanterade IoT Hub **2.** Användaren måste ange antal och frekvens för meddelanden. **3.** AICS validerar Telemetrin tas emot av Hub-instansen |
| **Resurser**           | [Certifierings steg](./overview.md) (har alla ytterligare resurser) |

**Kunna DPS: Syftet med testet är att kontrol lera att enheten implementerar och stöder IoT Hub Device Provisioning Service med en av de tre metoderna för attestering**

| **Namn**                | IoTPnP. DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Tillgänglighet för mål** | Tillgängligt nu                                                |
| **Gäller för**          | Valfri enhet                                                   |
| **Operativsystem**                  | Oberoende                                                     |
| **Validerings typ**     | Automatiserad                                                    |
| **Signaturverifiering**          | Enheten måste implementera enkel överföring av DPS-ID omfångets ägarskap utan att behöva kompilera om den inbäddade koden. Microsoft tillhandahåller ett [Portal arbets flöde](https://certify.azure.com) för att köra testerna för att kontrol lera att enheten har stöd för DPS **1.** Användaren måste välja en av attesterings metoderna (X. 509, TPM och SAS-nyckel) **2.** Beroende på attesterings metoden måste användaren vidta motsvarande åtgärd, till exempel **a)** Ladda upp X. 509-certifikat till AICS Managed DPS **b)** implementera SAS-nyckel eller bekräftelse nyckel i enheten |
| **Resurser**           | **a)** [Översikt över enhets etablerings tjänsten](../iot-dps/about-iot-dps.md), **b)** [exempel på KONFIGURATIONS fil för överföring av DPS-ID omfånget](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview-pnp/serializer/samples/devicetwin_simplesample) |

**Kunna DTDL v2: Syftet med testet för att säkerställa att definierade enhets modeller och gränssnitt är kompatibla med det digitala dubbla definitions språket v2.**                                                              

| **Namn**                | IoTPnP.DTDL                                                  |
| ----------------------- | ------------------------------------------------------------ |
| **Tillgänglighet för mål** | Tillgängligt nu                                                |
| **Gäller för**          | Valfri enhet                                                   |
| **Operativsystem**                  | Oberoende                                                     |
| **Validerings typ**     | Automatiserad                                                    |
| **Signaturverifiering**          | [Portal arbets flödet](https://certify.azure.com) verifierar: **1.** Meddelande om modell-ID och se till att enheten är ansluten med antingen MQTT eller MQTT över WebSockets Protocol **2.** Modeller är kompatibla med DTDL v2 **3.** Telemetri, egenskaper och kommandon implementeras korrekt och interagerar mellan IoT Hub digital enhet och enhet, dubbla på enheten |
| **Resurser**           | [Offentlig för hands version uppdatera uppdateringar](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |

**Kunna Enhets modeller publiceras i den offentliga modell databasen**

| **Namn**                | IoTPnP.ModelRepo                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Tillgänglighet för mål** | Tillgängligt nu                                                |
| **Gäller för**          | Valfri enhet                                                   |
| **Operativsystem**                  | Oberoende                                                     |
| **Validerings typ**     | Automatiserad                                                    |
| **Signaturverifiering**          | Alla enhets modeller måste publiceras i den offentliga lagrings platsen. Enhets modeller löses via modeller som är tillgängliga i den offentliga lagrings platsen **1.** Användaren måste publicera modellerna manuellt till den offentliga lagrings platsen innan du skickar in för certifieringen. **2.** Observera att när modellerna har publicerats är det oföränderligt. Vi rekommenderar starkt att du bara publicerar när modeller och inbäddad enhets kod har slutförts. * 1 * 1 användaren måste kontakta Microsoft Support för att återkalla modellerna när den har publicerats till modell lagring **3.** [Portal arbets flödet](https://certify.azure.com) kontrollerar om det finns modeller i det offentliga lagret när enheten är ansluten till certifikat tjänsten |
| **Resurser**           | [Modelldatabas](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |

**Kunna Fysisk enhets validering med GSG**

| **Namn**                                  | IoTPnP.Physicaldevice                                      |
| ----------------------------------------- | ------------------------------------------------------------ |
| **Tillgänglighet för mål**                   | Tillgängligt nu                                                |
| **Gäller för**                            | Valfri enhet                                                   |
| **Operativsystem**                                    | Oberoende                                                     |
| **Validerings typ**                       | Manuell                                                       |
| **Signaturverifiering**                            | Partner måste engagera sig hos Microsoft Contact ( [iotcert@microsoft.com](mailto:iotcert@microsoft.com) ) för att kunna utföra ytterligare verifieringar på den fysiska enheten. På grund av COVID-19-situationen undersöker vi olika sätt att utföra fysisk enhets verifiering utan att leverera enheten till Microsoft. |
| **Resurser**                             | Information finns senare                                 |
| **Azure rekommenderas**       | Ej tillämpligt    |

**[IF Implemented] Enhets informations gränssnitt: Syftet med testet är att verifiera att enhets informations gränssnittet är korrekt implementerat i enhets koden**

| **Namn**                | IoTPnP.DeviceInfoInterface                                   |
| ----------------------- | ------------------------------------------------------------ |
| **Tillgänglighet för mål** | Tillgängligt nu                                                |
| **Gäller för**          | Valfri enhet                                                   |
| **Operativsystem**                  | Oberoende                                                     |
| **Validerings typ**     | Automatiserad                                                    |
| **Signaturverifiering**          | [Portal arbets flöde](https://certify.azure.com) verifierar enhets koden implementerar enhets informations gränssnittet **1.** Kontrollerar att värdena genereras av enhets koden till IoT Hub **2.** Kontrollerar att gränssnittet har implementerats i DCM (den här implementeringen kommer att ändras i DTDL v2) **3.** Kontroll egenskaperna är inte skrivbara (skrivskyddade) **4.** Kontrollerar schema typen är sträng och/eller lång och inte null |
| **Resurser**           | [Microsoft-definierat gränssnitt](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |
| **Azure rekommenderas**  | Ej tillämpligt                                                          |

**[IF Implemented] Moln till enhet: Syftet med testet är att se till att meddelanden kan skickas från molnet till enheter**

| **Namn**                | IoTPnP. C2D                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Tillgänglighet för mål** | Tillgängligt nu                                                |
| **Gäller för**          | Löv enhet/gräns enhet                                      |
| **Operativsystem**                  | Oberoende                                                     |
| **Validerings typ**     | Automatiserad                                                    |
| **Signaturverifiering**          | Enheten måste kunna molnet till enhets meddelanden från IoT Hub. Microsoft tillhandahåller [Portal arbets flödet](https://certify.azure.com) för att utföra dessa tester. Moln till enhet (om det implementeras): **1.** Verifierar att enheten kan ta emot meddelande från IoT Hub **2.** AICS skickar ett slumpmässigt meddelande och validerar via meddelande ACK från enheten |
| **Resurser**           | **1.** [certifierings steg](./overview.md) (har alla ytterligare resurser), **2.** [Skicka molnet till enhets meddelanden från en IoT Hub](../iot-hub/iot-hub-devguide-messages-c2d.md) |

**[IF Implemented] Direkta metoder: Syftet med testet är att se till att enheterna fungerar med IoT Hub och stöder direkta metoder**

| **Namn**                | IoTPnP.DirectMethods                                     |
| ----------------------- | ------------------------------------------------------------ |
| **Tillgänglighet för mål** | Tillgängligt nu                                                |
| **Gäller för**          | Löv enhet/gräns enhet                                      |
| **Operativsystem**                  | Oberoende                                                     |
| **Validerings typ**     | Automatiserad                                                    |
| **Signaturverifiering**          | Enheten måste kunna ta emot och svara på kommando begär Anden från IoT Hub. Microsoft tillhandahåller [Portal arbets flödet](https://certify.azure.com) för att köra testerna. Direkta metoder (om de implementeras): **1.** Användaren måste ange metod nytto lasten för Direct-metoden. **2.** AICS verifierar att angiven begäran om nytto Last skickas från hubben och ACK-meddelandet som tas emot av enheten |
| **Resurser**           | **1.** [certifierings steg](./overview.md) (har alla ytterligare resurser), **2.** [Förstå direkta metoder från IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md) |

**[IF Implemented] Enhetens dubbla egenskap: Syftet med testet är att se till att enheter som skickar telemetri fungerar med IoT Hub och har stöd för vissa av de IoT Hub funktionerna, till exempel direkta metoder och enhetens dubbla egenskap**

| **Namn**                | IoTPnP.DeviceTwin                                        |
| ----------------------- | ------------------------------------------------------------ |
| **Tillgänglighet för mål** | Tillgängligt nu                                                |
| **Gäller för**          | Löv enhet/gräns enhet                                      |
| **Operativsystem**                  | Oberoende                                                     |
| **Validerings typ**     | Automatiserad                                                    |
| **Signaturverifiering**          | Enheten måste skicka eventuella telemetri scheman till IoT Hub. Microsoft tillhandahåller [Portal arbets flödet](https://certify.azure.com) för att köra testerna. Enhetens dubbla egenskap (om implementeras): **1.** AICS verifierar egenskapen läsa/skrivbar i enhet, dubbel JSON **2.** Användaren måste ange den JSON-nyttolast som ska ändras **3.** AICS verifierar de angivna önskade egenskaperna som skickas från IoT Hub och ACK-meddelande som tagits emot av enheten |
| **Resurser**           | **1.** [certifierings steg](./overview.md) (har alla ytterligare resurser), **2.** [Använd enheten tillsammans med IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) |
