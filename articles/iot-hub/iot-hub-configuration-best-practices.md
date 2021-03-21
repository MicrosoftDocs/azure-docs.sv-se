---
title: Metod tips för enhets konfiguration för Azure IoT Hub | Microsoft Docs
description: Lär dig mer om metod tips för att använda automatisk enhets hantering för att minimera repetitiva och komplexa uppgifter som används för att hantera IoT-enheter i stor skala
author: robinsh
ms.author: robinsh
ms.date: 06/28/2019
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 8a39c2b06ca8a0f852891acb60ba199fc2c6db5c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96024137"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Metod tips för enhets konfiguration i en IoT-lösning

Automatisk enhets hantering i Azure IoT Hub automatiserar många repetitiva och komplexa aktiviteter för att hantera stora enhets flottor över hela livs cykeln. Den här artikeln definierar många av de bästa metoderna för de olika rollerna som används för att utveckla och driva en IoT-lösning.

* **IoT-maskin varu tillverkare/Integrator:** Tillverkare av IoT-maskinvara, integrerar maskin vara från olika tillverkare eller leverantörer som tillhandahåller maskin vara för en IoT-distribution som tillverkas eller integreras av andra leverantörer. Ingår i utveckling och integrering av inbyggd program vara, inbäddade operativ system och inbäddad program vara.

* **IoT Solution Developer:** Utvecklingen av en IoT-lösning utförs vanligt vis av en lösnings utvecklare. Den här utvecklaren kan vara en del av ett internt team eller en System Integrator som är specialiserad i denna aktivitet. IoT Solution Developer kan utveckla olika komponenter i IoT-lösningen från grunden, integrera olika komponenter med standard eller öppen källkod eller anpassa en [IoT Solution Accelerator](../iot-accelerators/index.yml).

* **IoT-lösnings operator:** När IoT-lösningen har distribuerats kräver den långsiktiga åtgärder, övervakning, uppgraderingar och underhåll. Dessa uppgifter kan utföras av ett internt team som består av informations teknik specialister, maskin varu åtgärder och underhålls team och domän specialister som övervakar rätt beteende för den övergripande IoT-infrastrukturen.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Förstå automatisk enhets hantering för att konfigurera IoT-enheter i stor skala

Automatisk enhets hantering omfattar de många fördelarna med [enheten](iot-hub-devguide-device-twins.md) , tillsammans med [modulernas dubblare](iot-hub-devguide-module-twins.md) , för att synkronisera önskade och rapporterade tillstånd mellan molnet och enheterna. [Automatisk enhets konfiguration](./iot-hub-automatic-device-management.md) uppdaterar automatiskt stora uppsättningar av dubbla och sammanfattande förlopp och efterlevnad. Följande avancerade steg beskriver hur automatisk enhets hantering utvecklas och används:

* **IoT Hardware Manufacturer/Integrator** implementerar enhets hanterings funktioner i ett inbäddat program med hjälp av [enheten](iot-hub-devguide-device-twins.md). Dessa funktioner kan omfatta uppdateringar av inbyggd program vara, program varu installation och uppdatering samt inställningar för hantering av inställningar.

* **IoT Solution Developer** implementerar hanterings lagret för enhets hanterings åtgärder med hjälp av [enhets](iot-hub-devguide-device-twins.md) -och [Automatisk enhets konfiguration](./iot-hub-automatic-device-management.md). Lösningen bör innehålla definiera ett operatörs gränssnitt för att utföra enhets hanterings aktiviteter.

* **IoT-lösningens operator** använder IoT-lösningen för att utföra enhets hanterings uppgifter, särskilt för att gruppera enheter, initiera konfigurations ändringar som uppdateringar av inbyggd program vara, övervaka förloppet och felsöka problem som uppstår.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT-maskin varu tillverkare/Integrator

Följande är metod tips för maskin varu tillverkare och integratorer som hanterar inbäddad program varu utveckling:

* **Implementera [enhets dubbla](iot-hub-devguide-device-twins.md):** Med enhets dubbla är det möjligt att synkronisera önskad konfiguration från molnet och för att rapportera aktuella konfigurations-och enhets egenskaper. Det bästa sättet att implementera enhets dubbla i inbäddade program är via [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks): er. Enhets uppflätade passar bäst för konfigurationen eftersom de:

    * Stöd för dubbelriktad kommunikation.
    * Tillåt både anslutna och frånkopplade enhets tillstånd.
    * Följ principen om eventuell konsekvens.
    * Är helt enhetsmetadata i molnet.

* **Strukturera enheten med enhets hantering:** Enhetens måste vara strukturerad så att egenskaperna för enhets hantering grupperas logiskt i avsnitt. Detta gör att konfigurations ändringar kan isoleras utan att andra delar av den dubbla. Du kan till exempel skapa ett avsnitt i önskade egenskaper för inbyggd program vara, ett annat avsnitt för program vara och ett tredje avsnitt för nätverks inställningar. 

* **Rapportera enhets attribut som är användbara för enhets hantering:** Attribut som fysisk enhets märke och modell, inbyggd program vara, operativ system, serie nummer och andra identifierare är användbara för rapportering och som parametrar för konfigurations ändringar för mål.

* **Definiera huvud tillstånd för rapporterings status och förlopp:** De högsta nivå tillstånden bör räknas upp så att de kan rapporteras till operatören. Till exempel kan en uppdatering av inbyggd program vara rapportera status som aktuell, hämtning, tillämpning, pågående och fel. Definiera ytterligare fält för mer information om varje tillstånd.

## <a name="iot-solution-developer"></a>IoT Solution-utvecklare

Följande är metod tips för IoT Solution-utvecklare som bygger system baserade på Azure:

* **Implementera [enhets dubbla](iot-hub-devguide-device-twins.md):** Med enhets dubbla är det möjligt att synkronisera önskad konfiguration från molnet och för att rapportera aktuella konfigurations-och enhets egenskaper. Det bästa sättet att implementera enhets dubbla i moln lösnings program är via [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks): er. Enhets uppflätade passar bäst för konfigurationen eftersom de:

    * Stöd för dubbelriktad kommunikation.
    * Tillåt både anslutna och frånkopplade enhets tillstånd.
    * Följ principen om eventuell konsekvens.
    * Är helt enhetsmetadata i molnet.

* **Organisera enheter med enhets dubbla Taggar:** Lösningen ska tillåta operatören att definiera kvalitets ringar eller andra uppsättningar av enheter baserat på olika distributions strategier, till exempel Kanarie. Enhets organisation kan implementeras i din lösning med hjälp av enhetens dubbla Taggar och [frågor](iot-hub-devguide-query-language.md). Enhets organisation är nödvändig för att göra det möjligt att återställa konfigurationen på ett säkert och korrekt sätt.

* **Implementera [Automatisk enhets konfiguration](./iot-hub-automatic-device-management.md):** Automatisk enhets konfiguration distribuerar och övervakar konfigurations ändringar till stora uppsättningar IoT-enheter via enhets dubbla.

   Automatisk enhets konfiguration mål uppsättningar av enheten är dubbla via **mål villkoret,** som är en fråga på enheten med dubbla taggar eller rapporterade egenskaper. **Mål innehållet** är en uppsättning av önskade egenskaper som anges inom den riktade enheten. Mål innehållet ska justeras med enhetens dubbla struktur som definieras av IoT Hardware Manufacturer/Integrator. **Måtten** är frågor på enhetens dubbla rapporterade egenskaper och bör också anpassas efter enhetens dubbla struktur som definieras av IoT Hardware Manufacturer/Integrator.

   Automatisk enhets konfiguration körs för första gången strax efter att konfigurationen har skapats och sedan fem minuters intervall. De kan också dra nytta av IoT Hub utföra enhets dubbla åtgärder med en hastighet som aldrig överskrider [begränsnings gränserna](iot-hub-devguide-quotas-throttling.md) för enhetens dubbla läsningar och uppdateringar.

* **Använd [enhets etablerings tjänsten](../iot-dps/how-to-manage-enrollments.md):** lösnings utvecklare bör använda enhets etablerings tjänsten för att tilldela enheter dubbla taggar till nya enheter, så att de konfigureras automatiskt av **automatiska enhets konfigurationer** som är riktade mot varandra med den taggen. 

## <a name="iot-solution-operator"></a>IoT Solution-operator

Följande är metod tips för IoT Solution-operatörer som använder en IoT-lösning som bygger på Azure:

* **Organisera enheter för hantering:** IoT-lösningen bör definiera eller tillåta att kvalitets ringar eller andra enhets uppsättningar skapas baserat på olika distributions strategier, till exempel Kanarie. Enhets uppsättningarna används för att distribuera konfigurations ändringar och utföra andra hanterings åtgärder för enhets hantering.

* **Utför konfigurations ändringar med hjälp av en**  fördelad sammanslagning:  En stegvis distribution är en övergripande process där en operatör distribuerar ändringar till en större uppsättning IoT-enheter. Målet är att göra ändringar gradvis för att minska risken för att minska storleken på stora förändringar.  Operatören bör använda lösningens gränssnitt för att skapa en [Automatisk enhets konfiguration](./iot-hub-automatic-device-management.md) och mål villkoret bör rikta en första uppsättning enheter (till exempel en Kanarie grupp). Operatören bör sedan validera konfigurations ändringen i den första uppsättningen enheter.

   När verifieringen är klar uppdaterar operatören automatisk enhets konfiguration för att inkludera en större uppsättning enheter. Operatören bör också ställa in prioriteten för konfigurationen så att den är högre än andra konfigurationer som för närvarande är riktade till dessa enheter. Distributionen kan övervakas med hjälp av de mått som rapporteras av den automatiska enhets konfigurationen.

* **Utför återställningar i händelse av fel eller fel konfiguration:**  En automatisk enhets konfiguration som orsakar fel eller fel konfiguration kan återställas genom att ändra **mål villkor** så att enheterna inte längre uppfyller mål villkoret. Se till att en annan automatisk enhets konfiguration med lägre prioritet fortfarande är avsedd för enheterna. Kontrol lera att återställningen lyckades genom att visa måtten: den återställda konfigurationen ska inte längre Visa status för enheter som inte är riktade till, och den andra konfigurationens mått bör nu innehålla antal för de enheter som fortfarande är riktade.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om hur du implementerar enheten i en [förståelse och använder enheten i IoT Hub](iot-hub-devguide-device-twins.md).

* Gå igenom stegen för att skapa, uppdatera eller ta bort en automatisk enhets konfiguration i [Konfigurera och övervaka IoT-enheter i stor skala](./iot-hub-automatic-device-management.md).

* Implementera uppdaterings mönster för inbyggd program vara med enhets-och automatiska enhets konfiguration i [Självstudier: implementera en uppdaterings process för enhetens inbyggda program](tutorial-firmware-update.md).