---
title: Översikt över Azure percept-säkerhet
description: Läs mer om Azure percept Security
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: template-concept
ms.openlocfilehash: 93884fb87f87651054ffff0a04c4910de634a5eb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567652"
---
# <a name="azure-percept-security-overview"></a>Översikt över Azure percept-säkerhet

Azure percept-enheter har utformats med en maskin varu rot med förtroende. Den här inbyggda säkerheten hjälper till att skydda härlednings data och sekretess känsliga sensorer som kameror och mikrofoner och aktiverar enhetsautentisering och auktorisering för Azure percept Studio-tjänster.

> [!NOTE]
> Azure percept DK är licensierat för användning i utvecklings-och test miljöer.

## <a name="devices"></a>Enheter

### <a name="azure-percept-dk"></a>Azure percept DK

Azure percept DK innehåller en Trusted Platform Module (TPM) version 2,0, som kan användas för att ansluta enheten till Azure Device Provisioning-tjänster (DPS) med ytterligare säkerhet. TPM är en ISO-standard som är bransch standard från Trusted Computing Group. Kolla in [Trusted Computing Group webbplats](https://trustedcomputinggroup.org/resource/tpm-library-specification/) för mer information om den fullständiga TPM 2,0-specifikationen eller ISO/IEC 11889-specifikationen. Mer information om hur DPS kan etablera enheter på ett säkert sätt finns i [Azure-IoT Hub Device Provisioning service – TPM-attestering](../iot-dps/concepts-tpm-attestation.md).

### <a name="azure-percept-system-on-modules-soms"></a>Azure percept system-on-modules (SoMs)

Azure percept vision system-on-Module (som) och Azure percept-ljudet som båda innehåller en mikrostyrenhet-enhet (MCU) för att skydda åtkomsten till de inbäddade AI-sensorer. Vid varje start autentiserar den inbyggda program varan och godkänner AI-acceleratorn med Azure percept Studio-tjänster med hjälp av MCU-arkitekturen för enhets identifierare. TÄRNINGarna fungerar genom att dela upp start i lager och skapa unika enhets hemligheter (UDS) för varje lager och konfiguration. Om en annan kod eller konfiguration startas när som helst i kedjan, är hemligheterna annorlunda. Du kan läsa mer om TÄRNINGarna i [spec-arbetsgruppen](https://trustedcomputinggroup.org/work-groups/dice-architectures/). Information om hur du konfigurerar åtkomst till Azure percept Studio och de tjänster som krävs finns i artikeln om [att konfigurera brand väggar för Azure PERCEPT DK](concept-security-configuration.md).

Azure percept-enheter använder maskin varu roten i förtroende för att skydda den inbyggda program varan. Start-ROM säkerställer integriteten för den inbyggda program varan mellan ROM-och OS-inläsaren, som i sin tur säkerställer integriteten hos de andra program komponenterna och skapar en förtroende kedja.

## <a name="services"></a>Tjänster

### <a name="iot-edge"></a>IoT Edge

Azure percept DK ansluter till Azure percept Studio med ytterligare säkerhet och andra Azure-tjänster som använder Transport Layer Security-protokollet (TLS). Azure percept DK är en Azure IoT Edge-aktiverad enhet. IoT Edge runtime är en samling program som omvandlar en enhet till en IoT Edge-enhet. IoT Edges körnings komponenter gör det möjligt för IoT Edge enheter att ta emot kod som ska köras vid gränsen och förmedla resultatet. Azure percept DK använder Docker-behållare för att isolera IoT Edge arbets belastningar från värd operativ systemet och Edge-aktiverade program. Mer information om säkerhets ramverket Azure IoT Edge finns i [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

### <a name="device-update-for-iot-hub"></a>Enhets uppdatering för IoT Hub

Enhets uppdatering för IoT Hub möjliggör säkrare, skalbar och pålitlig uppdatering via luften som ger förnybar säkerhet till Azure percept-enheter. Det ger omfattande hanterings kontroller och kompatibilitet med hjälp av insikter. Azure percept DK innehåller en förintegrerad enhets uppdaterings lösning som tillhandahåller elastisk uppdatering (A/B) från inbyggd program vara till OS-lager.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om brand Väggs konfigurationer och säkerhets rekommendationer](concept-security-configuration.md)

> [!div class="nextstepaction"]
> [Köp en Azure percept DK från Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
