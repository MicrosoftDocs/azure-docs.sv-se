---
title: Skapa transparent gateway-enhet – Azure IoT Edge | Microsoft Docs
description: Använd en Azure IoT Edge enhet som en transparent gateway som kan bearbeta information från underordnade enheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f7f05fb84ff6cbe320e8f479912bdcdefdc41021
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201643"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Konfigurera en IoT Edge-enhet till att fungera som en transparent gateway

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Den här artikeln innehåller detaljerade anvisningar för hur du konfigurerar en IoT Edge-enhet så att den fungerar som en transparent Gateway för andra enheter att kommunicera med IoT Hub. I den här artikeln används termen *IoT Edge Gateway* för att referera till en IoT Edge enhet som kon figurer ATS som en transparent Gateway. Mer information finns i [så här kan en IoT Edge enhet användas som en gateway](./iot-edge-as-gateway.md).

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

>[!NOTE]
>I IoT Edge versioner 1,1 och äldre kan en IoT Edge enhet inte visas bakom en IoT Edge Gateway.
>
>Underordnade enheter kan inte använda fil uppladdning.

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>Underordnade enheter kan inte använda fil uppladdning.

::: moniker-end

Det finns tre allmänna steg för att konfigurera en lyckad transparent Gateway-anslutning. Den här artikeln beskriver det första steget:

1. **Konfigurera gateway-enheten som en server så att underordnade enheter kan ansluta till den på ett säkert sätt. Konfigurera gatewayen för att ta emot meddelanden från underordnade enheter och dirigera dem till rätt plats.**
2. Skapa en enhets identitet för den underordnade enheten så att den kan autentiseras med IoT Hub. Konfigurera den underordnade enheten för att skicka meddelanden via gateway-enheten. De här stegen finns i [autentisera en underordnad enhet till Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. Anslut den underordnade enheten till gateway-enheten och börja skicka meddelanden. De här stegen finns i [ansluta en underordnad enhet till en Azure IoT Edge Gateway](how-to-connect-downstream-device.md).

För att en enhet ska fungera som en gateway måste den ansluta till dess underordnade enheter på ett säkert sätt. Med Azure IoT Edge kan du använda en PKI (Public Key Infrastructure) för att konfigurera säkra anslutningar mellan enheter. I det här fallet låter vi en underordnad enhet ansluta till en IoT Edge-enhet som fungerar som en transparent Gateway. För att upprätthålla rimlig säkerhet bör den underordnade enheten bekräfta gateway-enhetens identitet. Den här identitets kontrollen förhindrar att enheterna ansluter till potentiellt skadliga gatewayer.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
En underordnad enhet kan vara ett program eller en plattform som har en identitet som skapats med [Azure IoT Hub](../iot-hub/index.yml) Cloud service. De här programmen använder ofta [Azure IoT-enhetens SDK](../iot-hub/iot-hub-devguide-sdks.md). En underordnad enhet kan även vara ett program som körs på själva IoT Edge gateway-enheten. En IoT Edge enhet kan dock inte underordnas IoT Edge Gateway.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
En underordnad enhet kan vara ett program eller en plattform som har en identitet som skapats med [Azure IoT Hub](../iot-hub/index.yml) Cloud service. De här programmen använder ofta [Azure IoT-enhetens SDK](../iot-hub/iot-hub-devguide-sdks.md). En underordnad enhet kan även vara ett program som körs på själva IoT Edge gateway-enheten.
:::moniker-end
<!-- end 1.2 -->

Du kan skapa en certifikat infrastruktur som aktiverar det förtroende som krävs för din enhets-Gateway-topologi. I den här artikeln förutsätter vi samma certifikat inställningar som du använde för att aktivera [x. 509 ca-säkerhet](../iot-hub/iot-hub-x509ca-overview.md) i IoT Hub, vilket inbegriper ett X. 509 CA-certifikat som är kopplat till en speciell IoT-hubb (IoT Hub rot certifikat utfärdare), en serie med certifikat som har signerats med denna certifikat utfärdare och en certifikat utfärdare för IoT Edges enheten.

>[!NOTE]
>Termen *rot certifikat utfärdarens certifikat* som används i de här artiklarna avser det offentliga auktoritets certifikatet för PKI-certifikat kedjan och inte nödvändigt vis certifikat roten för en syndikerad certifikat utfärdare. I många fall är det faktiskt ett offentligt certifikat för certifikat utfärdare.

Följande steg vägleder dig genom processen att skapa certifikaten och installera dem på rätt plats på gatewayen. Du kan använda vilken dator som helst för att generera certifikaten och sedan kopiera dem till din IoT Edge-enhet.

## <a name="prerequisites"></a>Förutsättningar

En Linux-eller Windows-enhet med IoT Edge installerat.

Om du inte har en enhet redo kan du skapa en på en virtuell Azure-dator. Följ stegen i [distribuera din första IoT Edge-modul till en virtuell Linux-enhet](quickstart-linux.md) för att skapa en IoT Hub, skapa en virtuell dator och konfigurera IoT Edge Runtime.

## <a name="set-up-the-device-ca-certificate"></a>Konfigurera enhetens CA-certifikat

Alla IoT Edge gateways behöver ett certifikat för enhets certifikat utfärdare installerat på dem. IoT Edge Security daemon använder IoT Edge enhetens CA-certifikat för att signera ett CA-certifikat för arbets belastning, vilket i sin tur signerar ett Server certifikat för IoT Edge Hub. Gatewayen visar sitt Server certifikat för den underordnade enheten under initieringen av anslutningen. Den underordnade enheten kontrollerar att Server certifikatet är en del av en certifikat kedja som slås samman till rot certifikat utfärdarens certifikat. Den här processen gör att den underordnade enheten kan bekräfta att gatewayen kommer från en betrodd källa. Mer information finns i [förstå hur Azure IoT Edge använder certifikat](iot-edge-certs.md).

![Konfiguration av Gateway-certifikat](./media/how-to-create-transparent-gateway/gateway-setup.png)

Rot-CA-certifikatet och enhetens CA-certifikat (med dess privata nyckel) måste finnas på IoT Edge gateway-enheten och konfigureras i IoT Edge konfigurations filen. Kom ihåg att i det här fallet är *rot certifikat* utfärdaren den översta certifikat utfärdaren för den här IoT Edge scenariot. Gateway-enhetens CA-certifikat och certifikat för efterföljande enheter måste sammanställas till samma rot certifikat för certifikat utfärdare.

>[!TIP]
>Processen för att installera rot certifikat utfärdarens certifikat och enhetens CA-certifikat på en IoT Edge enhet förklaras också i detalj i [Hantera certifikat på en IoT Edge enhet](how-to-manage-device-certificates.md).

Ha följande filer klara:

* Rot certifikat utfärdarens certifikat
* Enhetens CA-certifikat
* Privat nyckel för enhets certifikat utfärdare

För produktions scenarier bör du generera dessa filer med din egen certifikat utfärdare. Du kan använda demo certifikat för utvecklings-och test scenarier.

Om du inte har en egen certifikat utfärdare och vill använda demo certifikat, följer du anvisningarna i [skapa demonstrations certifikat för att testa IoT Edge enhets funktioner](how-to-create-test-certificates.md) för att skapa dina filer. På den sidan måste du utföra följande steg:

   1. Starta genom att ställa in skripten för att skapa certifikat på enheten.
   2. Skapa ett rot certifikat för certifikat utfärdare. I slutet av dessa instruktioner har du en certifikat fil för rot certifikat utfärdaren:
      * `<path>/certs/azure-iot-test-only.root.ca.cert.pem`.
   3. Skapa IoT Edge enhetens CA-certifikat. I slutet av dessa anvisningar har du ett certifikat för enhets certifikat och dess privata nyckel:
      * `<path>/certs/iot-edge-device-<cert name>-full-chain.cert.pem` särskilt
      * `<path>/private/iot-edge-device-<cert name>.key.pem`

Om du har skapat certifikaten på en annan dator kopierar du dem till din IoT Edge-enhet och fortsätter sedan med nästa steg.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Öppna konfigurations filen för daemon på din IoT Edge-enhet.

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

1. Hitta avsnittet **certifikat inställningar** i filen. Ta bort kommentarer till de fyra raderna som börjar med **certifikat:** och ange fil-URI: erna till dina tre filer som värden för följande egenskaper:
   * **device_ca_cert**: ENHETens CA-certifikat
   * **device_ca_pk**: ENHETens ca-privata nyckel
   * **trusted_ca_certs**: rot certifikat utfärdarens certifikat

   Se till att det inte finns något föregående blank steg på raden **certifikat:** och att de andra raderna har indrag ATS av två blank steg.

1. Spara och stäng filen.

1. Starta om IoT Edge.
   * Windows: `Restart-Service iotedge`
   * Linux: `sudo systemctl restart iotedge`
:::moniker-end
<!-- end 1.1 -->

<!--1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Öppna konfigurations filen på din IoT Edge-enhet: `/etc/aziot/config.toml`

   >[!TIP]
   >Om konfigurations filen inte finns på enheten ännu använder du `/etc/aziot/config.toml.edge.template` som mall för att skapa en.

1. Hitta `trust_bundle_cert` parametern. Ta bort kommentaren till den här raden och ange fil-URI: n för rot certifikat utfärdarens certifikat fil på enheten.

1. Hitta `[edge_ca]` avsnittet i filen. Ta bort kommentarer till de tre raderna i det här avsnittet och ange fil-URI: er för ditt certifikat och viktiga filer som värden för följande egenskaper:
   * **certifikat**: ENHETens CA-certifikat
   * **PK**: ENHETens ca-privata nyckel

1. Spara och stäng filen.

1. Spara ändringarna.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="deploy-edgehub-and-route-messages"></a>Distribuera edgeHub och dirigera meddelanden

Underordnade enheter skickar telemetri och meddelanden till gateway-enheten, där modulen IoT Edge Hub ansvarar för att vidarebefordra informationen till andra moduler eller till IoT Hub. För att förbereda din gateway-enhet för den här funktionen, se till att:

* Modulen IoT Edge Hub har distribuerats till enheten.

  Första gången du installerar IoT Edge på en enhet startar bara en systemmodul automatiskt: IoT Edge agenten. När du har skapat den första distributionen för en enhet startar den andra systemmodulen, IoT Edge hubben, också. Om modulen **edgeHub** inte körs på enheten skapar du en distribution för enheten.

* Modulen IoT Edge Hub har vägar konfigurerade för hantering av inkommande meddelanden från underordnade enheter.

  Gateway-enheten måste ha en väg för att hantera meddelanden från underordnade enheter, annars kommer de inte att bearbetas. Du kan skicka meddelanden till moduler på gateway-enheten eller direkt till IoT Hub.

Följ dessa steg om du vill distribuera modulen IoT Edge Hub och konfigurera den med vägar för att hantera inkommande meddelanden från underordnade enheter:

1. Gå till din IoT-hubb på Azure Portal.

2. Gå till **IoT Edge** och välj IoT Edge enheten som du vill använda som en gateway.

3. Välj **Ange moduler**.

4. På sidan **moduler** kan du lägga till alla moduler som du vill distribuera till gateway-enheten. I den här artikeln fokuserar vi på att konfigurera och distribuera edgeHub-modulen, som inte behöver anges explicit på den här sidan.

5. Välj **Nästa: vägar**.

6. På sidan **vägar** ser du till att det finns en väg för att hantera meddelanden från underordnade enheter. Exempel:

   * En väg som skickar alla meddelanden, antingen från en modul eller från en underordnad enhet, till IoT Hub:
       * **Namn**: `allMessagesToHub`
       * **Värde**: `FROM /messages/* INTO $upstream`

   * En väg som skickar alla meddelanden från alla underordnade enheter till IoT Hub:
      * **Namn**: `allDownstreamToHub`
      * **Värde**: `FROM /messages/* WHERE NOT IS_DEFINED ($connectionModuleId) INTO $upstream`

      Den här vägen fungerar på grund av IoT Edge att meddelanden från underordnade enheter inte har något associerat ID för meddelanden från efterföljande enheter. Med hjälp av vägens **WHERE** -sats kan vi filtrera bort alla meddelanden med den system egenskapen.

      Mer information om meddelanderoutning finns i [distribuera moduler och upprätta vägar](./module-composition.md#declare-routes).

7. När du har skapat din väg eller dina vägar väljer du **Granska + skapa**.

8. På sidan **Granska och skapa** väljer du **skapa**.

## <a name="open-ports-on-gateway-device"></a>Öppna portar på gateway-enhet

Standard IoT Edge enheter behöver ingen inkommande anslutning för att fungera eftersom all kommunikation med IoT Hub görs via utgående anslutningar. Gateway-enheter skiljer sig åt eftersom de måste ta emot meddelanden från deras underordnade enheter. Om en brand vägg är mellan de efterföljande enheterna och gateway-enheten, behöver kommunikationen också vara möjlig genom brand väggen.

För att ett Gateway-scenario ska fungera måste minst ett av de protokoll som stöds av IoT Edge Hub vara öppet för inkommande trafik från efterföljande enheter. De protokoll som stöds är MQTT, AMQP, HTTPS, MQTT över WebSockets och AMQP för WebSockets.

| Port | Protokoll |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS |

## <a name="next-steps"></a>Nästa steg

Nu när du har en IoT Edge enhet som har kon figurer ATS som en transparent Gateway måste du konfigurera de underordnade enheterna till att lita på gatewayen och skicka meddelanden till den. Fortsätt med att [autentisera en underordnad enhet till Azure IoT Hub](how-to-authenticate-downstream-device.md) för nästa steg i konfigurationen av scenariot för transparent Gateway.