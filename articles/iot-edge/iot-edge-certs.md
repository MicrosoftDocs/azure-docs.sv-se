---
title: Certifikat för enhets säkerhet – Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge använder certifikat för att verifiera enheter, moduler och löv enheter och upprätta säkra anslutningar mellan dem.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: mqtt
ms.openlocfilehash: ffe2f2b7f94d546cdfe393170da2fd2ca6ac0149
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491001"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>Förstå hur Azure IoT Edge använder certifikat

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge certifikat används av modulerna och underordnade IoT-enheter för att verifiera identiteten och giltighet för modulen för [IoT Edge Hub](iot-edge-runtime.md#iot-edge-hub) -körning. Dessa kontroller aktiverar TLS (Transport Layer Security) säker anslutning mellan körning, moduler och IoT-enheter. Precis som IoT Hub behöver IoT Edge en säker och krypterad anslutning från IoT-underenheten (eller löv) och IoT Edge moduler. För att upprätta en säker TLS-anslutning visar IoT Edge Hub-modulen en server certifikat kedja för att ansluta klienter för att verifiera identiteten.

>[!NOTE]
>Den här artikeln pratar om de certifikat som används för att skydda anslutningar mellan olika komponenter på en IoT Edge enhet eller mellan en IoT Edge-enhet och eventuella löv enheter. Du kan också använda certifikat för att autentisera IoT Edge-enheten för att IoT Hub. Dessa certifikat för autentisering är olika och beskrivs inte i den här artikeln. Mer information om hur du autentiserar enheten med certifikat finns i [skapa och etablera en IoT Edge enhet med X. 509-certifikat](how-to-auto-provision-x509-certs.md).

I den här artikeln förklaras hur IoT Edge certifikat kan fungera i produktions-, utvecklings-och test scenarier. Medan skripten skiljer sig (PowerShell vs. bash), är begreppen samma mellan Linux och Windows.

## <a name="iot-edge-certificates"></a>IoT Edge-certifikat

Det finns två vanliga scenarier för att konfigurera certifikat på en IoT Edge enhet. Ibland kan slutanvändaren eller operatören av en enhet köpa en generisk enhet som tillverkas av en tillverkare och sedan hantera själva certifikaten. Vid andra tillfällen fungerar tillverkaren enligt avtal för att bygga en anpassad enhet för operatören och utför en del certifikat signering innan enheten överförs. IoT Edge-certifikatets design försöker göra båda scenarierna i kontot.

Följande bild illustrerar IoT Edge användningen av certifikat. Det kan finnas noll, ett eller flera mellanliggande signerings certifikat mellan rot certifikat utfärdarens certifikat och enhetens CA-certifikat, beroende på antalet enheter som berörs. Här visar vi ett ärende.

![Diagram över typiska certifikat relationer](./media/iot-edge-certs/edgeCerts-general.png)

<!--1.1-->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> För närvarande förhindrar en begränsning i libiothsm användningen av certifikat som upphör att gälla den 1 januari 2038. Den här begränsningen gäller för enhetens CA-certifikat, alla certifikat i det betrodda paketet och de enhets-ID-certifikat som används för etablerings metoder för X. 509.

:::moniker-end

### <a name="certificate-authority"></a>Certifikatutfärdare

Certifikat utfärdaren eller CA: n är en entitet som utfärdar digitala certifikat. En certifikat utfärdare fungerar som en betrodd tredje part mellan ägaren och mottagaren av certifikatet. Ett digitalt certifikat certifierar ägarskapet för en offentlig nyckel av mottagaren av certifikatet. Förtroendet för certifikat kedjan fungerar genom att utfärda ett rot certifikat som är grunden för förtroende i alla certifikat utfärdade av utfärdaren. Därefter kan ägaren använda rot certifikatet för att utfärda ytterligare mellanliggande certifikat (löv certifikat).

### <a name="root-ca-certificate"></a>Rot certifikat utfärdarens certifikat

Ett rot certifikat utfärdares certifikat är roten av förtroendet för hela processen. I produktions scenarier köps det här CA-certifikatet vanligt vis från en betrodd kommersiell certifikat utfärdare som Baltimore, VeriSign eller DigiCert. Om du har fullständig kontroll över de enheter som ansluter till dina IoT Edge enheter, är det möjligt att använda en certifikat utfärdare på företags nivå. I båda fall rullar hela certifikat kedjan från IoT Edge Hub upp till den, så att noderna på lövnivå måste ha förtroende för rot certifikatet. Du kan lagra rot certifikat utfärdarens certifikat antingen i arkivet för betrodda rot certifikat utfärdare eller ange certifikat information i program koden.

### <a name="intermediate-certificates"></a>Mellanliggande certifikat

I en typisk tillverknings process för att skapa säkra enheter används vanligt vis rotcertifikatutfärdarcertifikat i rot certifikat direkt, främst på grund av risken för läckage eller exponering. Rot-CA-certifikatet skapar och signerar ett eller flera mellanliggande CA-certifikat digitalt. Det kan bara finnas en, eller så kan det finnas en kedja av dessa mellanliggande certifikat. Scenarier som kräver en kedja av mellanliggande certifikat inkluderar:

* En hierarki med avdelningar inom en tillverkare.

* Flera företag involverar seriellt i produktionen av en enhet.

* En kund som köper en rot certifikat utfärdare och härleder ett signerings certifikat för tillverkaren för att kunna signera de enheter som de gör på kundens räkning.

I alla fall använder tillverkaren ett mellanliggande CA-certifikat i slutet av den här kedjan för att signera enhetens CA-certifikat som placerats på slut enheten. I allmänhet är dessa mellanliggande certifikat nära skyddade i tillverknings anläggningen. De genomgår strikta processer, både fysiska och elektroniska för deras användning.

### <a name="device-ca-certificate"></a>Enhetens CA-certifikat

Enhetens CA-certifikat genereras från och signeras av det slutliga mellanliggande CA-certifikatet i processen. Det här certifikatet installeras på den IoT Edge själva enheten, helst i säkert lagrings utrymme som en HSM-modul (Hardware Security Module). Dessutom identifieras en enhets CA-certifikat unikt för en IoT Edge enhet. Enhetens CA-certifikat kan signera andra certifikat.

### <a name="iot-edge-workload-ca"></a>IoT Edge arbets belastnings certifikat utfärdare

[IoT Edge Security Manager](iot-edge-security-manager.md) genererar arbets Belastningens CA-certifikat, det första på "operatörs sidan" i processen, när IoT Edge startas första gången. Certifikatet skapas från och signeras av enhetens CA-certifikat. Det här certifikatet, som bara är ett annat mellanliggande signerings certifikat, används för att generera och signera andra certifikat som används av IoT Edge Runtime. Idag är det i första hand det IoT Edge Hub-servercertifikat som diskuteras i följande avsnitt, men i framtiden kan det inkludera andra certifikat för autentisering av IoT Edge-komponenter.

### <a name="iot-edge-hub-server-certificate"></a>IoT Edge Hub-servercertifikat

IoT Edge Hub-servercertifikat är det faktiska certifikatet som presenteras för löv enheter och moduler för identitets verifiering vid upprättandet av den TLS-anslutning som krävs av IoT Edge. Det här certifikatet visar en fullständig kedja av signerings certifikat som används för att generera det till rot certifikat utfärdarens certifikat, som lövnivå för lövnivå måste ha förtroende för. När det genereras av IoT Edge anges det egna namnet (CN) för det här IoT Edge Hub-certifikatet till egenskapen hostname i konfigurations filen efter konvertering till gemener. Den här konfigurationen är en gemensam källa för förvirring med IoT Edge.

## <a name="production-implications"></a>Produktions konsekvenser

En rimlig fråga kan vara "Varför behöver IoT Edge ett extra certifikat för arbets belastnings CA? Det gick inte att använda enhetens CA-certifikat för att direkt generera IoT Edge Hubbs Server certifikatet? ". Tekniskt sett kan det. Syftet med mellanliggande certifikat "arbets belastning" är dock att skilja mellan enhetens tillverkare och enhets operatören. Tänk dig ett scenario där en IoT Edge enhet säljs eller överförs från en kund till en annan. Du vill förmodligen att enhetens CA-certifikat från tillverkaren ska vara oföränderliga. Arbets belastnings certifikaten som är speciella för enhetens funktion bör dock rensas och återskapas för den nya distributionen.

Eftersom tillverknings-och drift processerna är åtskilda bör du tänka på följande när du förbereder produktions enheter:

* Med en certifikatbaserad process bör rot certifikat utfärdaren och alla mellanliggande CA-certifikat skyddas och övervakas under hela processen med att distribuera en IoT Edge-enhet. Den IoT Edge enhets tillverkaren bör ha starka processer på plats för korrekt lagring och användning av deras mellanliggande certifikat. Dessutom bör enhetens CA-certifikat vara i så säkert lagrings utrymme som möjligt på själva enheten, helst en modul för maskin varu säkerhet.

* IoT Edge Hub-servercertifikatet presenteras av IoT Edge Hub till de anslutande klient enheterna och modulerna. Det egna namnet (CN) för enhetens CA-certifikat **får inte vara** detsamma som "hostname" som ska användas i konfigurations filen på den IoT Edge enheten. Namnet som används av klienter för att ansluta till IoT Edge (till exempel via GatewayHostName-parametern för anslutnings strängen eller kommandot CONNECT i MQTT) **kan inte vara** samma som det gemensamma namnet som används i ENHETens CA-certifikat. Den här begränsningen beror på att IoT Edge Hub visar hela certifikat kedjan för verifiering av klienter. Om IoT Edge Hub-servercertifikat och enhetens CA-certifikat båda har samma CN, får du i en verifierings slinga och certifikatet blir ogiltig.

* Eftersom enhetens CA-certifikat används av IoT Edge Security daemon för att generera de slutgiltiga IoT Edge certifikaten, måste det vara ett signerings certifikat, vilket innebär att det har funktioner för certifikat signering. När du använder "v3 Basic-begränsningar CA: true" i enhetens CA-certifikat konfigureras automatiskt de nödvändiga egenskaperna för nyckel användning.

>[!Tip]
> Om du redan har gått igenom installationen av IoT Edge som en transparent gateway i ett utvecklings-/testnings scenario med hjälp av våra "bekvämlighets skript" (se nästa avsnitt) och använder samma värdnamn när du skapar enhetens CA-certifikat som du gjorde för värd namnet i konfigurations filen, kanske du undrar varför det fungerade. I syfte att förenkla utvecklandet lägger du till en ". ca" i slutet av det namn som du skickar till skriptet. Om du till exempel använde "Gateway" för både enhetens namn i skripten och värd namnet i konfigurations filen, kommer den tidigare att aktive ras i mygateway.ca innan den används som CN för enhetens CA-certifikat.

## <a name="devtest-implications"></a>Effekter för utveckling och testning

För att under lätta utvecklings-och test scenarier tillhandahåller Microsoft en uppsättning [bekvämlighets skript](https://github.com/Azure/iotedge/tree/master/tools/CACertificates) för att skapa icke-produktions certifikat som är lämpliga för IoT Edge i scenariot med transparent Gateway. Exempel på hur skripten fungerar finns i [skapa demonstrations certifikat för att testa IoT Edge enhets funktioner](how-to-create-test-certificates.md).

>[!Tip]
> För att ansluta enheten IoT-enheter och program som använder vår IoT-enhets-SDK via IoT Edge, måste du lägga till valfri GatewayHostName-parameter i slutet av enhetens anslutnings sträng. När Server certifikatet för Edge Hub skapas, baseras det på en lägre bokstäver-version av värd namnet från konfigurations filen, så att namnen som matchar och verifieringen av TLS-certifikatet måste anges i gemener.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Exempel på IoT Edge-certifikathierarki

För att illustrera ett exempel på den här certifikat Sök vägen är följande skärm bild från en arbets IoT Edge enhet som har kon figurer ATS som en transparent Gateway. OpenSSL används för att ansluta till IoT Edge Hub, validera och dumpa certifikaten.

![Skärm bild av certifikathierarkin på varje nivå](./media/iot-edge-certs/iotedge-cert-chain.png)

Du kan se hierarkin för certifikat djupet som visas i skärm bilden:

| Rot certifikat utfärdarens certifikat         | Azure IoT Hub CA-cert endast test                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Mellanliggande CA-certifikat | Azure IoT Hub mellanliggande cert-test                                                                 |
| Enhetens CA-certifikat       | iotgateway.ca ("iotgateway" skickades som < Gateway-värdnamn > till de praktiska skripten)   |
| CA-certifikat för arbets belastning     | iotedge arbets belastnings certifikat utfärdare                                                                                       |
| IoT Edge Hub-servercertifikat | iotedgegw. local (matchar "hostname" från konfigurations filen)                                            |

## <a name="next-steps"></a>Nästa steg

[Förstå Azure IoT Edge-moduler](iot-edge-modules.md)

[Konfigurera en IoT Edge-enhet till att fungera som en transparent gateway](how-to-create-transparent-gateway.md)
