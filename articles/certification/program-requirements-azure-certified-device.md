---
title: Krav för Azure-certifierad enhet
description: Krav för Azure Certified Device-program
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Azure Certified Device Certification Requirements
ms.service: certification
ms.openlocfilehash: 948fe25da8468e887693fe8c9f75f675dfbea858
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969406"
---
# <a name="azure-certified-device-requirements"></a>Krav för Azure-certifierad enhet 
(kallades tidigare IoT Hub)

Det här dokumentet beskriver de specifika enhets funktioner som visas i Azure-certifierad enhets katalog. En funktion är ett bevarat enhets attribut som kan vara program varu implementering eller kombination av program-och maskin varu implementeringar. 

## <a name="program-purpose"></a>Program syfte

Microsoft fören klar IoT och Azure-certifierad enhets certifiering är ett bas linje certifierings program för att se till att alla enhets typer tillhandahålls till Azure IoT Hub på ett säkert sätt.

Ett löfte om Azure-certifierad enhets certifiering är:

1. Telemetri för enhets stöd som fungerar med IoT Hub
2.  Enhets support IoT Hub Device Provisioning Service (DPS) för säker etablering till Azure IoT Hub
3.  Enheten stöder enkel indata från mål överföring av DPS-ID utan att användaren behöver kompilera om inbäddad kod.
4.  Du kan också validera andra element som molnet till enhets meddelanden, direkta metoder och enhets dubbla 

## <a name="requirements"></a>Krav

**Kunna Enhet till moln: Syftet med testet är att se till att enheter som skickar telemetri fungerar med IoT Hub**

| **Namn**                | AzureCertified. D2C                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Tillgänglighet för mål** | Tillgängligt nu                                                |
| **Gäller för**          | Löv enhet/gräns enhet                                      |
| **Operativsystem**                  | Oberoende                                                     |
| **Validerings typ**     | Automatiserad                                                    |
| **Signaturverifiering**          | Enheten måste skicka eventuella telemetri scheman till IoT Hub. Microsoft tillhandahåller [Portal arbets flödet](https://certify.azure.come) för att köra testerna. Enhet till moln (krävs): **1.** Verifierar att enheten kan skicka meddelande till AICS-hanterade IoT Hub **2.** Användaren måste ange antal och frekvens för meddelanden. **3.** AICS validerar Telemetrin tas emot av Hub-instansen |
| **Resurser**           | [Certifierings steg](./overview.md) (har alla ytterligare resurser) |

**Kunna DPS: Syftet med testet är att kontrol lera att enheten implementerar och stöder IoT Hub Device Provisioning Service med en av de tre metoderna för attestering**

| **Namn**                | AzureCertified. DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Tillgänglighet för mål** | Ny                                                          |
| **Gäller för**          | Valfri enhet                                                   |
| **Operativsystem**                  | Oberoende                                                     |
| **Validerings typ**     | Automatiserad                                                    |
| **Signaturverifiering**          | Enheten har stöd för enkel ineffekt av mål för DPS-ID-omfattning utan att behöva kompilera om den inbäddade koden. Microsoft tillhandahåller ett [Portal arbets flöde](https://certify.azure.com) för att köra testerna för att kontrol lera att enheten har stöd för DPS **1.** Användaren måste välja en av attesterings metoderna (X. 509, TPM och SAS-nyckel) **2.** Beroende på attesterings metoden måste användaren vidta motsvarande åtgärd, till exempel **a)** Ladda upp X. 509-certifikat till AICS Managed DPS **b)** implementera SAS-nyckel eller bekräftelse nyckel i enheten |
| **Resurser**           | [Översikt över enhets etablerings tjänsten](../iot-dps/about-iot-dps.md) |

**[IF Implemented] Moln till enhet: Syftet med testet är att se till att meddelanden kan skickas från molnet till enheter**                                                              

| **Namn**                | AzureCertified. C2D                                                  |
| ----------------------- | ------------------------------------------------------------ |
| **Tillgänglighet för mål** | Tillgängligt nu                                            |
| **Gäller för**          | Löv enhet/gräns enhet                                                   |
| **Operativsystem**                  | Oberoende                                                     |
| **Validerings typ**     | Automatiserad                                                    |
| **Signaturverifiering**          | Enheten måste kunna molnet till enhets meddelanden från IoT Hub. Microsoft tillhandahåller [Portal arbets flödet](https://certify.azure.com) för att utföra dessa tester. Moln till enhet (om det implementeras): **1.** Verifierar att enheten kan ta emot meddelande från IoT Hub **2.** AICS skickar ett slumpmässigt meddelande och validerar via meddelande ACK från enheten  |
| **Resurser**           | **a)** [certifierings steg](./overview.md) (har alla ytterligare resurser) **b)** [Skicka molnet till enhets meddelanden från en IoT Hub](../iot-hub/iot-hub-devguide-messages-c2d.md) |

**[IF Implemented] Direkta metoder: Syftet med testet är att se till att enheterna fungerar med IoT Hub och stöder direkta metoder**

| **Namn**                | AzureCertified.DirectMethods                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Tillgänglighet för mål** | Tillgängligt nu                                            |
| **Gäller för**          | Löv enhet/gräns enhet                                                   |
| **Operativsystem**                  | Oberoende                                                     |
| **Validerings typ**     | Automatiserad                                                    |
| **Signaturverifiering**          | Enheten måste kunna ta emot och svara på kommando begär Anden från IoT Hub. Microsoft tillhandahåller [Portal arbets flödet](https://certify.azure.com) för att köra testerna. Direkta metoder (om de implementeras) **1.** Användaren måste ange metod nytto lasten för Direct-metoden. **2.** AICS verifierar att angiven begäran om nytto Last skickas från hubben och ACK-meddelandet som tas emot av enheten |
| **Resurser**           | **a)** [certifierings steg](./overview.md) (har alla ytterligare resurser) **b)** [förstå direkta metoder från IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md) |

**[IF Implemented] Enhetens dubbla egenskap: Syftet med testet är att se till att enheter som skickar telemetri fungerar med IoT Hub och har stöd för vissa av de IoT Hub funktionerna, till exempel direkta metoder och enhetens dubbla egenskap**

| **Namn**                                  | AzureCertified.DeviceTwin                                      |
| ----------------------------------------- | ------------------------------------------------------------ |
| **Tillgänglighet för mål**                   | Tillgängligt nu                                            |
| **Gäller för**                            | Löv enhet/gräns enhet                                                   |
| **Operativsystem**                                    | Oberoende                                                     |
| **Validerings typ**                       | Automatiserad                                                       |
| **Signaturverifiering**                            | Enheten måste skicka eventuella telemetri scheman till IoT Hub. Microsoft tillhandahåller [Portal arbets flödet](https://certify.azure.com) för att köra testerna. Enhetens dubbla egenskap (om den implementeras) **1.** AICS verifierar egenskapen läsa/skrivbar i enhet, dubbel JSON **2.** Användaren måste ange den JSON-nyttolast som ska ändras **3.** AICS verifierar de angivna önskade egenskaperna som skickas från IoT Hub och ACK-meddelande som tagits emot av enheten |
| **Resurser**                             | **a)** [certifierings steg](./overview.md) (har alla ytterligare resurser) **b)** [Använd enheten tillsammans med IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) |