---
title: Översikt över Azure IoT Hub X. 509 CA-säkerhet | Microsoft Docs
description: Översikt – hur du autentiserar enheter för att IoT Hub att använda X. 509 certifikat utfärdare.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: System Architecture'
ms.openlocfilehash: 455f1f3e1c8181646fbe165d0f1aea6a2fb2fb13
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92150697"
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Enhetsautentisering med X.509-certifikatutfärdarcertifikat

I den här artikeln beskrivs hur du använder certifikat från X. 509 certifikat utfärdare för att autentisera enheter som ansluter IoT Hub.  I den här artikeln får du lära dig:

* Så här hämtar du ett X. 509 CA-certifikat
* Så här registrerar du X. 509 CA-certifikatet för IoT Hub
* Så här signerar du enheter med X. 509 CA-certifikat
* Hur enheter som är signerade med X. 509 CA: er autentiseras

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="overview"></a>Översikt

Funktionen X. 509-CA: er aktiverar enhetsautentisering för att IoT Hub med hjälp av en certifikat utfärdare (CA). Det fören klar den första processen för registrering av enheter och levererar logistik för att tillhandahålla kedja vid enhets tillverkning. [Läs mer i den här scenario artikeln om värdet för att använda X. 509 CA-certifikat](iot-hub-x509ca-concept.md) för enhetsautentisering.  Vi rekommenderar att du läser den här scenario artikeln innan du fortsätter, vilket förklarar varför stegen som följer.

## <a name="prerequisite"></a>Förutsättning

Om du använder funktionen X. 509-CA måste du ha ett IoT Hub-konto.  [Lär dig hur du skapar en IoT Hub instans](quickstart-send-telemetry-dotnet.md) om du inte redan har en.

## <a name="how-to-get-an-x509-ca-certificate"></a>Så här hämtar du ett X. 509 CA-certifikat

CA-certifikatet för X. 509 är högst upp i certifikat kedjan för var och en av dina enheter.  Du kan köpa eller skapa ett beroende på hur du tänker använda det.

För produktions miljön rekommenderar vi att du köper ett X. 509 CA-certifikat från en offentlig rot certifikat utfärdare. Att köpa ett CA-certifikat har fördelen att rot certifikat utfärdaren fungerar som en betrodd tredje part för att få en giltighet av dina enheter. Överväg det här alternativet om du vill att enheterna ska ingå i ett öppet IoT-nätverk där de förväntas samverka med produkter eller tjänster från tredje part.

Du kan också skapa en självsignerad X. 509-certifikatutfärdare för experimentering eller för användning i stängda IoT-nätverk.

Oavsett hur du skaffar ditt X. 509-CA-certifikat, se till att behålla dess motsvarande privata nyckel hemlighet och skyddade hela tiden.  Detta är nödvändigt för förtroende för skapande i X. 509 CA-autentisering.

Lär dig hur du [skapar ett självsignerat CA-certifikat](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md), som du kan använda för att experimentera i den här funktions beskrivningen.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Signera enheter i certifikat kedjan

Ägaren av ett X. 509 CA-certifikat kan signera en mellanliggande certifikat utfärdare som i sin tur kan signera en annan mellanliggande certifikat utfärdare och så vidare tills den senaste mellanliggande certifikat utfärdaren avslutar den här processen genom att signera en enhet. Resultatet är en överlappande kedja av certifikat som kallas förtroende för certifikat kedjan. I real tid spelar detta upp ut som delegering av förtroende för signerings enheter. Den här delegeringen är viktig eftersom den upprättar en kryptografiskt varierande kedja av vårdnad och förhindrar delning av signerings nycklar.

![IMG-Generic-cert-kedja-of-Trust](./media/generic-cert-chain-of-trust.png)

Enhets certifikatet (även kallat ett löv certifikat) måste ha *ämnes namnet* inställt på **enhets-ID: t** ( `CN=deviceId` ) som användes när IoT-enheten registrerades i Azure-IoT Hub. Den här inställningen krävs för autentisering.

Lär dig hur du [skapar en certifikat kedja](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) som när du signerar enheter.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Så här registrerar du X. 509 CA-certifikatet för IoT Hub

Registrera ditt X. 509-CA-certifikat för att IoT Hub där det ska användas för att autentisera dina enheter under registrering och anslutning.  Att registrera X. 509 CA-certifikatet är en två stegs process som omfattar fil överföring av certifikat och bevis på innehav.

Uppladdnings processen laddar upp en fil som innehåller ditt certifikat.  Den här filen bör aldrig innehålla några privata nycklar.

Syftet med det här steget är en kryptografisk utmaning och svars process mellan dig och IoT Hub.  Med tanke på att digitalt certifikat innehåll är offentligt och därför är sårbart för avlyssning, vill IoT Hub vill fastställa att du verkligen äger CA-certifikatet.  Det gör du genom att generera en slumpmässig utmaning som du måste signera med CA-certifikatets motsvarande privata nyckel.  Om du har kvar hemligheten för privata nycklar och skyddat det tidigare, kommer du bara att ha kunskapen för att slutföra det här steget. Sekretess för privata nycklar är källan till förtroende i den här metoden.  När du har signerat utmaningen slutför du det här steget genom att ladda upp en fil som innehåller resultatet.

Lär dig hur du [registrerar CA-certifikat](iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)

## <a name="how-to-create-a-device-on-iot-hub"></a>Så här skapar du en enhet på IoT Hub

För att förhindra att enhets personifiering används måste du i IoT Hub ta reda på vilka enheter som ska förväntas.  Du gör detta genom att skapa en enhets post i IoT Hub enhets registret.  Den här processen automatiseras när du använder IoT Hub [enhets etablerings tjänst](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/). 

Lär dig hur du [manuellt skapar en enhet i IoT Hub](iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub).

Skapa en X. 509-enhet för din IoT Hub

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>Autentisera enheter som är signerade med X. 509 CA-certifikat

Med X. 509 CA-certifikat registrerat och enheter som är inloggade i en certifikat kedja, vad som är kvar är enhets autentiseringen när enheten ansluter, även för första gången.  När en X. 509 CA-signerad enhet ansluter, laddar den upp certifikat kedjan för verifiering. Kedjan innehåller alla mellanliggande certifikat utfärdare och enhets certifikat.  Med den här informationen autentiserar IoT Hub enheten i en två stegs process.  IoT Hub kryptografiskt verifierar certifikat kedjan för intern konsekvens och utfärdar sedan en utmanings utmaning för enheten.  IoT Hub deklarerar enheten autentiskt på ett framgångs rik svar från enheten.  Den här deklarationen förutsätter att enhetens privata nyckel är skyddad och att endast enheten kan svara på den här utmaningen.  Vi rekommenderar att du använder säker chips som t. ex. maskin säkra moduler (HSM) i enheter för att skydda privata nycklar.

En lyckad enhets anslutning till IoT Hub är klar med autentiseringsprocessen och indikerar också en korrekt installation.

Lär dig här steg för att [slutföra den här enhets anslutningen](iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [värdet X. 509 ca-autentisering](iot-hub-x509ca-concept.md) i IoT.

Kom igång med IoT Hub [Device Provisioning-tjänsten](../iot-dps/index.yml).