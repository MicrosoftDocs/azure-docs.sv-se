---
title: Översikt över Azure percept-säkerhet
description: Läs mer om Azure percept Security
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 9575c0aec4a1b45f9099bff1dc4209c63529ddf6
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025551"
---
# <a name="azure-percept-security-overview"></a>Översikt över Azure percept-säkerhet

Azure percept DK-enheter är utformade med en maskin varu rot med förtroende: ytterligare inbyggd säkerhet på varje enhet. Det hjälper till att skydda integritets känsliga sensorer som kameror och mikrofoner, data härlednings data och autentisering av enheter och auktorisering för Azure percept Studio-tjänster.

> [!NOTE]
> Azure percept DK är licensierat för användning i utvecklings-och test miljöer.

## <a name="devices"></a>Enheter

### <a name="azure-percept-dk"></a>Azure percept DK

Azure percept DK innehåller en Trusted Platform Module (TPM) version 2,0 som kan användas för att ansluta enheten till Azure Device Provisioning-tjänster med ytterligare säkerhet. TPM är en ISO-standard som är bransch standard från Trusted Computing Group och du kan läsa mer om TPM i den [fullständiga tpm 2,0-specifikationen](https://trustedcomputinggroup.org/resource/tpm-library-specification/) eller ISO/IEC 11889-specifikationen. Mer information om hur DPS kan etablera enheter på ett säkert sätt finns i [Azure-IoT Hub Device Provisioning service – TPM-attestering](../iot-dps/concepts-tpm-attestation.md).

### <a name="azure-percept-system-on-module-som"></a>Azure percept-system för modul (som)

Azure percept DK vision-aktiverat system på modul (som) och ljud tillbehöret Azure percept som båda innehåller en Micro Controller-enhet (MCU) för att skydda åtkomsten till de inbäddade AI-sensorer. Vid varje start autentiserar den inbyggda program varan och godkänner AI-acceleratorn med Azure percept Studio-tjänster med hjälp av MCU-arkitekturen för enhets identifierare. TÄRNINGarna fungerar genom att dela upp start i lager och skapa hemligheter som är unika för varje lager och konfiguration baserat på en unik enhets hemlighet (UDS). Om en annan kod eller konfiguration startas, när som helst i kedjan, är hemligheterna annorlunda. Du kan läsa mer om TÄRNINGarna i [spec-arbetsgruppen](https://trustedcomputinggroup.org/work-groups/dice-architectures/). För att konfigurera åtkomst till Azure percept Studio och nödvändiga tjänster, se **Konfigurera brand väggar för Azure PERCEPT DK** nedan.

Azure percept-enheter använder maskin varu rot förtroende för att skydda inbyggd program vara. Start-ROM säkerställer integriteten för den inbyggda program varan mellan ROM-och OS-inläsaren, vilket i sin tur säkerställer integriteten hos de andra program komponenterna som skapar en förtroende kedja.

## <a name="services"></a>Tjänster

### <a name="iot-edge"></a>IoT Edge

Azure percept DK ansluter till Azure percept Studio med ytterligare säkerhet och andra Azure-tjänster som använder Transport Layer Security-protokollet (TLS). Azure percept DK är en Azure IoT Edge aktive rad enhet. IoT Edge runtime är en samling program som omvandlar en enhet till en IoT Edge-enhet. IoT Edges körnings komponenter gör det möjligt för IoT Edge enheter att ta emot kod som ska köras vid gränsen och förmedla resultatet. Azure percept DK använder Docker-behållare för att isolera IoT Edge arbets belastningar från värd operativ systemet och Edge-aktiverade program. Mer information om säkerhets ramverket Azure IoT Edge finns i [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

### <a name="device-update-for-iot-hub"></a>Enhets uppdatering för IoT Hub

Enhets uppdatering för IoT Hub möjliggör säkrare, skalbar och pålitlig uppdatering via luften som ger förnybar säkerhet till Azure percept-enheter. Det ger omfattande hanterings kontroller och kompatibilitet med hjälp av insikter. Azure percept DK innehåller en förintegrerad enhets uppdaterings lösning som tillhandahåller elastisk uppdatering (A/B) från inbyggd program vara till OS-lager.

<!---I think the below topics need to be somewhere else, (i.e. not on the main page)
--->

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Konfigurera brand väggar för Azure percept DK

Om nätverks konfigurationen kräver att du uttryckligen tillåter anslutningar från Azure percept DK-enheter kan du läsa igenom följande lista över komponenter.

Den här check listan är en start punkt för brand Väggs regler:

|URL (* = jokertecken) |Utgående TCP-portar|    Användning|
|-------------------|------------------|---------|
|*. auth.azureperceptdk.azure.net|   443|    Azure DK som autentisering och auktorisering|
|*. auth.projectsantacruz.azure.net| 443|    Azure DK som autentisering och auktorisering|

Granska dessutom listan över anslutningar som [används av Azure IoT Edge](../iot-edge/production-checklist.md#allow-connections-from-iot-edge-devices).

<!---
## Additional Recommendations for Deployment to Production

Azure Percept DK offers a great variety of security capabilities out of the box. In addition to those powerful security features included in the current release, Microsoft also suggests the following guidelines when considering production deployments:

- Strong physical protection of the device itself
- Ensuring data at rest encryption is enabled
- Continuously monitoring the device posture and quickly responding to alerts
- Limiting the number of administrators who have access to the device
--->


## <a name="next-steps"></a>Nästa steg

Lär dig mer om tillgängliga [Azure PERCEPT AI-modeller](./overview-ai-models.md).