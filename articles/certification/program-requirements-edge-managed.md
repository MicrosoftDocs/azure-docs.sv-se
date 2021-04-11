---
title: Krav för Edge-hanterad certifiering
description: Krav för Edge Managed Certification-program
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Managed Certification Requirements
ms.service: certification
ms.openlocfilehash: 744f81d0544a765f60793ece1aa539c6c37e39cd
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969401"
---
# <a name="azure-certification-edge-managed"></a>Azure Certificate Edge-hanterad 

Det här dokumentet beskriver de specifika enhets funktioner som visas i Azure-certifierad enhets katalog. En funktion är ett beskrivande enhets attribut som kan beskriva enheten. 

## <a name="program-purpose"></a>Program syfte

Edge-hanterad certifiering, en stegvis certifiering utöver den grundläggande Azure-certifierade enhetens certifiering. Edge-hanterade fokuserar på enhets hanterings standarder för Azure Connected-enheter och validerar IoT Edge kompatibilitet för modul distribution och hantering. (Tidigare identifierades det här programmet som IoT Edge certifierings program.) 

Edge-hanterad certifiering verifierar IoT Edge runtime-kompatibilitet för distribution och hantering av modulen. Det här programmet ger förtroende för hanteringen av Azure-anslutna IoT-enheter.

## <a name="requirements"></a>Krav

Den Edge-hanterade certifieringen kräver att alla krav från [Azure Certified-enhetens bas linje program](.\program-requirements-azure-certified-device.md).

**DPS: Syftet med testet är att kontrol lera att enheten implementerar och stöder IoT Hub Device Provisioning Service med en av de tre metoderna för attestering**

| **Namn**                | AzureReady. DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Tillgänglighet för mål** | Antändning (i för hands version)                                                |
| **Gäller för**          | Valfri enhet                                      |
| **Operativsystem**                  | Oberoende                                                     |
| **Validerings typ**     | Automatiserad                                                    |
| **Signaturverifiering**          | AICS verifierar enhets koden stöd för DPS. **1.** Användaren måste välja en av attesterings metoderna (X. 509, TPM och SAS-nyckel). **2.** Beroende på attesterings metoden måste användaren vidta motsvarande åtgärd, till exempel **a)** Ladda upp X. 509-certifikat till AICS Managed DPS **b)** implementera SAS-nyckel eller bekräftelse nyckel i enheten. **3.** Sedan klickar användaren på knappen Anslut för att ansluta till AICS-hanterade IoT Hub via DPS                                                    |
| **Resurser**           |                                                      |
| **Rekommenderad av Azure:**     | Ej tillämpligt                                                    |

## <a name="iot-edge"></a>IoT Edge

**Edge runtime finns: Syftet med testet är att se till att enheten innehåller IoT Edge Runtime ($edgehub och $edgeagent) fungerar korrekt.**

| **Namn**                | EdgeManaged.EdgeRT                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Tillgänglighet för mål** | Tillgängligt nu                                                          |
| **Gäller för**          | IoT Edge-enhet                                                   |
| **Operativsystem**                  | [1 och – nivå 2 OS](../iot-edge/support.md)                                                     |
| **Validerings typ**     | Automatiserad                                                    |
| **Signaturverifiering**          | AICS verifierar distributions möjligheten för den installerade IoT Edge RT. **1.** Användaren måste ange ett speciellt operativ system (OS som inte finns på listan över 1/2 accepteras inte) **2.** AICS genererar dess config. yaml och distribuerar den kanoniska [simulerade Temp-modulen](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-iot.simulated-temperature-sensor?tab=Overview) **3.** AICS verifierar att Docker-kompatibelt container Subsystem (Moby) är installerat på enhet **4.** Test resultatet bestäms baserat på lyckad distribution av den simulerade Temp-modulen och funktionerna i Docker Compatible container Subsystem                                                    |
| **Resurser**           | **a)** [AICS blogg](https://azure.microsoft.com/en-in/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/), **b)** [certifierings steg](./overview.md) (har alla ytterligare resurser), **c)** [krav](./program-requirements-azure-certified-device.md) |
| **Rekommenderad av Azure:**     | Ej tillämpligt                                                    |

### <a name="capability-template"></a>Funktions mal len:

**IoT Edge enkel installation: Syftet med testet är att se till att IoT Edge enheten är enkel att konfigurera och verifiera att IoT Edge runtime förinstalleras under valideringen av den fysiska enheten**

| **Namn**                | EdgeManaged.PhysicalDevice                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Tillgänglighet för mål** | Tillgängligt nu (väntar på att vara på grund av COVID-19)                                            |
| **Gäller för**          | IoT Edge-enhet                                                   |
| **Operativsystem**                  | [1 och – nivå 2 OS](../iot-edge/support.md)                                                     |
| **Validerings typ**     | Manuell/labb verifierad                                                    |
| **Signaturverifiering**          | OEM måste leverera den fysiska enheten till IoT-administration (HCL). HCL utför manuell verifiering på den fysiska enheten för att kontrol lera: **1.** EdgeRT använder Moby-undersystemet (tillåten omdistributions version). Inte Docker **2.** Välj den senaste Edge-modulen för att verifiera möjligheten att distribuera Edge.                                                     |
| **Resurser**           | **a)** [AICS blogg](https://azure.microsoft.com/en-in/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/), **b)** [krav](./program-requirements-azure-certified-device.md) för [certifiering](./overview.md) , **c)** |
| **Rekommenderad av Azure:**     | Ej tillämpligt                                                    |