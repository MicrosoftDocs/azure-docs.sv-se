---
title: Azure IoT Edge Security Manager – Azure IoT Edge
description: Hanterar IoT Edge enhetens säkerhets virtualiseringsprodukter från och integriteten hos säkerhets tjänsterna.
services: iot-edge
keywords: säkerhet, säkert element, enklaven, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: ed73c114c015ce38fefa02d2b71e27e8f85dca39
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028680"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IoT Edge Security Manager

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge Security Manager är en välbegränsad säkerhets kärna för att skydda IoT Edge-enheten och alla dess komponenter genom att göra en abstraktion av den säkra Silicon-maskinvaran. Security Manager är fokus punkten för säkerhets härdning och ger teknisk integrerings plats för OEM (Original Equipment Manufacturer).

![Azure IoT Edge Security Manager](media/edge-security-manager/iot-edge-security-manager.png)

IoT Edge Security Manager syftar till att skydda IoT Edges enhetens integritet och alla program som är inbyggda. Säkerhets hanteraren övergår till förtroende från underliggande maskin varu rot för betrodd maskin vara (om tillgängligt) för att starta IoT Edge körning och övervaka pågående åtgärder.  IoT Edge Security Manager är program vara som arbetar tillsammans med säker Silicon-maskinvara (där det är tillgängligt) för att tillhandahålla högsta möjliga säkerhets garanti.  

Ansvars områdena för IoT Edge Security Manager är, men är inte begränsade till:

* Skyddad och uppmätt start av den Azure IoT Edge enheten.
* Enhets identitets etablering och över gång av förtroende i tillämpliga fall.
* Vara värd för och skydda enhets komponenter i moln tjänster som enhets etablerings tjänsten.
* Etablera IoT Edge moduler med unika identiteter på ett säkert sätt.
* Gatekeeper till enhetens maskin varu rot genom Notary-tjänster.
* Övervaka integriteten för IoT Edge åtgärder vid körning.

IoT Edge Security Manager innehåller tre komponenter:

* IoT Edge Security daemon.
* Plattforms abstraktions lager för maskin varu säkerhet (HSM PAL).
* Valfri men hög Rekommenderad maskinvaru-eller HSM-rot.

## <a name="the-iot-edge-security-daemon"></a>IoT Edge Security daemon

IoT Edge Security daemon ansvarar för de logiska åtgärderna i IoT Edge Security Manager. Den representerar en betydande del av den betrodda dator basen för den IoT Edge enheten.

### <a name="design-principles"></a>Designprinciper

IoT Edge Security daemon följer två grundläggande principer: maximera drifts integriteten och minimera överdriven storlek och omsättning.

#### <a name="maximize-operational-integrity"></a>Maximera drifts integritet

IoT Edge Security daemon fungerar med högsta möjliga integritet i försvars förmågan hos alla eventuella rot-och maskin varu förtroenden. Med korrekt integrering är roten av Trust-maskinvara och övervakar säkerhets demon statiskt och vid körning för att motstå manipulering.

Fysisk åtkomst är alltid ett hot mot IoT-enheter. Förtroende för maskin varu roten spelar en viktig roll i att skydda integriteten för IoT Edge Security daemon.  Maskin varu roten i förtroendet levereras i två sorter:

* skydda element för att skydda känslig information som hemligheter och kryptografiska nycklar.
* skydda enclaves för att skydda hemligheter som nycklar och känsliga arbets belastningar som mätning och fakturering.

Det finns två typer av körnings miljöer som kan använda maskin varu roten:

* Standard miljön eller den avancerade körnings miljön (REE) som förlitar sig på användningen av säkra element för att skydda känslig information.
* Den betrodda körnings miljön (TEE) som förlitar sig på användningen av säker enklaven-teknik för att skydda känslig information och ge skydd till program körning.

För enheter som använder säker enclaves som maskin varu rot bör känslig logik i IoT Edge Security daemon finnas inuti enklaven.  Icke-känsliga delar av Security daemon kan ligga utanför TEE.  I samtliga fall bör de ursprungliga design tillverkarna (ODM) och OEM-tillverkare (Original Equipment Manufacturer) utöka förtroendet från sin HSM för att mäta och försvara integriteten hos IoT Edge Security daemon vid start och körning.

#### <a name="minimize-bloat-and-churn"></a>Minimera överdriven storlek och omsättning

En annan kärn princip för IoT Edge Security daemon är att minimera omsättningen.  För den högsta förtroende nivån kan IoT Edge säkerhetsdaemonen vara nära kopplad till enhetens maskin varu rot och arbeta som inbyggd kod.  Det är vanligt att de här typerna av Realizations uppdaterar daemon-programvaran via maskin varu rotens säkra uppdaterings vägar (till skillnad från operativ system som tillhandahålls uppdaterings mekanismer), vilket kan vara svårt i vissa scenarier.  Även om säkerhets förnyelse rekommenderas för IoT-enheter, kan omfattande uppdaterings krav eller stora uppdaterings nytto laster expandera hot ytan på många sätt.  Exempel på detta är att hoppa över uppdateringar för att maximera drifts tillgängligheten eller roten av tillförlitlig maskin vara för att kunna bearbeta stora uppdaterings nytto laster.  Därför är designen av IoT Edge Security daemon koncis för att hålla utrymmes-och Trusted Computings-basen små och minimera uppdaterings kraven.

### <a name="architecture-of-iot-edge-security-daemon"></a>Arkitektur för IoT Edge Security daemon

![Azure IoT Edge Security daemon](media/edge-security-manager/iot-edge-security-daemon.png)

IoT Edge Security daemon drar nytta av alla tillgängliga maskin varu roten för förtroende teknik för säkerhets härdning.  Det ger också till gång till en Split-World-åtgärd mellan en standard-/Rich körnings miljö (REE) och en TEE (Trusted Execution Environment) när maskin varu teknikerna erbjuder betrodda körnings miljöer. Rollbaserade gränssnitt gör det möjligt för huvud komponenterna i IoT Edge att garantera integriteten hos IoT Edges enheten och dess åtgärder.

#### <a name="cloud-interface"></a>Cloud Interface

Cloud Interface gör det möjligt för IoT Edge Security daemon att komma åt moln tjänster som till exempel molnet till enhets säkerhet som säkerhets förnyelse.  Till exempel använder IoT Edge Security daemon för närvarande det här gränssnittet för att komma åt Azure IoT Hub [Device Provisioning-tjänsten](../iot-dps/index.yml) för livs cykel hantering av enhetens identitet.  

#### <a name="management-api"></a>Hanterings-API

IoT Edge Security daemon erbjuder ett hanterings-API, som anropas av IoT Edge-agenten när en IoT Edge-modul skapas/startas/stoppas/tas bort. Security daemon lagrar "registreringar" för alla aktiva moduler. Dessa registreringar mappar en moduls identitet till vissa egenskaper i modulen. I exemplen ingår exempelvis process identifieraren (PID) för processen som körs i behållaren och hashen för Docker-behållarens innehåll.

Dessa egenskaper används av arbets belastnings-API: et (beskrivs nedan) för att kontrol lera att anroparen har behörighet för en åtgärd.

Hanterings-API: et är ett privilegie rad API som endast kan anropas från den IoT Edge agenten.  Eftersom IoT Edge säkerhetsdaemonen startar och startar IoT Edge-agenten, verifierar den att IoT Edge agenten inte har manipulerats och kan sedan skapa en implicit registrering för IoT Edge agenten. Samma attesterings process som används för arbets belastnings-API: t begränsar också åtkomsten till hanterings-API: t till endast den IoT Edge agenten.

#### <a name="container-api"></a>Container-API

Behållar-API: et samverkar med det behållar system som används för modul hantering, som Moby eller Docker.

#### <a name="workload-api"></a>API för arbets belastning

Arbets belastnings-API: et är tillgängligt för alla moduler. Det ger bevis på identitet, antingen som en HSM-rotad signerad token eller ett X509-certifikat och motsvarande förtroende paket till en modul. Trust-paketet innehåller CA-certifikat för alla andra servrar som modulerna ska ha förtroende för.

IoT Edge Security daemon använder en attesterings process för att skydda detta API. När en modul anropar det här API: et försöker säkerhets demon att hitta en registrering för identiteten. Om det lyckas används egenskaperna för registreringen för att mäta modulen. Om resultatet av mätnings processen matchar registreringen genereras ett nytt identitets bevis. Motsvarande CA-certifikat (förtroende bunt) returneras till modulen.  Modulen använder det här certifikatet för att ansluta till IoT Hub, andra moduler eller starta en server. När den signerade token eller certifikatet närmar sig upphör att gälla, är det ansvaret för modulen att begära ett nytt certifikat.

### <a name="integration-and-maintenance"></a>Integrering och underhåll

Microsoft underhåller huvud kod basen för [IoT Edge Security daemon på GitHub](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Installation och uppdateringar

Installation och uppdateringar av IoT Edge Security daemon hanteras via operativ systemets pakethanteringssystem. IoT Edge enheter med förtroende för maskin varu roten bör ge ytterligare härdning av daemonens integritet genom att hantera dess livs cykel genom hanterings systemen för säker start och uppdateringar. Enhets tillverkarna bör utforska dessa vägar baserat på deras respektive enhets funktioner.

#### <a name="versioning"></a>Versionshantering

IoT Edge körningen spårar och rapporterar versionen av IoT Edge Security daemon. Versionen rapporteras som *runtime. Platform. version* -attributet för den rapporterade egenskapen för den IoT Edge agent-modulen.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Plattforms abstraktions lager för maskin varu säkerhet (HSM PAL)

HSM PAL sammanfattar all rot för betrodd maskin vara för att isolera utvecklaren eller användaren av IoT Edge från deras komplexitet.  Den innehåller en kombination av procedurer för application programming interface (API) och kommunikation mellan domäner, till exempel kommunikation mellan en standard körnings miljö och en säker enklaven.  Den faktiska implementeringen av HSM PAL är beroende av den specifika säkra maskin vara som används. Den befintliga funktionen möjliggör användning av praktiskt taget all säker Silicon-maskinvara.

## <a name="secure-silicon-root-of-trust-hardware"></a>Säker Silicon-rot för betrodd maskin vara

Säker Silicon är nödvändig för att fästa förtroende i IoT Edge enhetens maskin vara.  Säker kisel ingår i sort till att inkludera Trusted Platform Module (TPM), Embedded Secure element (eSE), ARM TrustZone, Intel SGX och anpassade säkra Silicon-tekniker.  Användning av säker kisel roten av förtroende i enheter rekommenderas med tanke på de hot som är kopplade till IoT-enheters fysiska tillgänglighet.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>Integrering och underhåll av IoT Edge Security Manager

IoT Edge Security Manager syftar till att identifiera och isolera de komponenter som skyddar Azure IoT Edge plattformens säkerhet och integritet för anpassad härdning. Tredje part, som enhets tillverkare, bör använda anpassade säkerhetsfunktioner som är tillgängliga med enhetens maskin vara.  

Lär dig hur du kan skydda Azure IoT Security Manager med Trusted Platform Module (TPM) med program vara eller virtuella TPM: er:  

Skapa och etablera en [IoT Edge enhet med en virtuell TPM på en virtuell Linux-dator](how-to-auto-provision-simulated-device-linux.md).

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Skapa och etablera en [IoT Edge enhet med en simulerad TPM i Windows](how-to-auto-provision-simulated-device-windows.md).
:::moniker-end

## <a name="next-steps"></a>Nästa steg

Läs bloggen om [att skydda den intelligenta gränsen](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).
