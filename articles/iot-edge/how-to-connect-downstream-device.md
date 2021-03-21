---
title: Anslut underordnade enheter – Azure IoT Edge | Microsoft Docs
description: Konfigurera underordnade eller löv enheter för att ansluta till Azure IoT Edge gateway-enheter.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: dc2d2d3e92435c7a028b43a095f456c2c383ecb4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199623"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Ansluta en underordnad enhet till en Azure IoT Edge-gateway

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Den här artikeln innehåller instruktioner för att upprätta en betrodd anslutning mellan underordnade enheter och IoT Edge transparenta gatewayer. I ett scenario med transparent Gateway kan en eller flera enheter skicka sina meddelanden via en enda gateway-enhet som upprätthåller anslutningen till IoT Hub.

Det finns tre allmänna steg för att konfigurera en lyckad transparent Gateway-anslutning. Den här artikeln beskriver det tredje steget:

1. Konfigurera gateway-enheten som en server så att underordnade enheter kan ansluta till den på ett säkert sätt. Konfigurera gatewayen för att ta emot meddelanden från underordnade enheter och dirigera dem till rätt plats. De här stegen finns i [Konfigurera en IoT Edge enhet att fungera som en transparent Gateway](how-to-create-transparent-gateway.md).
2. Skapa en enhets identitet för den underordnade enheten så att den kan autentiseras med IoT Hub. Konfigurera den underordnade enheten för att skicka meddelanden via gateway-enheten. De här stegen finns i [autentisera en underordnad enhet till Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. **Anslut den underordnade enheten till gateway-enheten och börja skicka meddelanden.**

Den här artikeln beskriver grundläggande begrepp för underordnade enhets anslutningar och vägleder dig när du konfigurerar dina efterföljande enheter genom att:

* Förklarar TLS (Transport Layer Security) och grundläggande certifikat.
* Förklarar hur TLS-bibliotek fungerar i olika operativ system och hur varje operativ system hanterar certifikat.
* Kom igång med Azure IoT-exempel på flera språk för att hjälpa dig att komma igång.

I den här artikeln hänvisar termerna *Gateway* och *IoT Edge gateway* till en IoT Edge enhet som kon figurer ATS som en transparent Gateway.

## <a name="prerequisites"></a>Förutsättningar

* Ha rot certifikat utfärdarens certifikat fil som användes för att generera enhetens CA-certifikat i [Konfigurera en IoT Edge-enhet så att den fungerar som en transparent gateway som](how-to-create-transparent-gateway.md) är tillgänglig på din underordnade enhet. Den underordnade enheten använder det här certifikatet för att verifiera identiteten för gateway-enheten. Om du använde demonstrations certifikaten kallas rot certifikat UTFÄRDARens certifikat **Azure-IoT-test-Only. root. ca. cert. pem**.
* Ha den ändrade anslutnings strängen som pekar på gateway-enheten, enligt beskrivningen i [autentisera en underordnad enhet till Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="prepare-a-downstream-device"></a>Förbered en underordnad enhet

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
En underordnad enhet kan vara ett program eller en plattform som har en identitet som skapats med Azure IoT Hub Cloud service. I många fall använder dessa program [Azure IoT-enhetens SDK](../iot-hub/iot-hub-devguide-sdks.md). En underordnad enhet kan även vara ett program som körs på själva IoT Edge gateway-enheten. En annan IoT Edge enhet kan dock inte underordnas IoT Edge Gateway.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
En underordnad enhet kan vara ett program eller en plattform som har en identitet som skapats med Azure IoT Hub Cloud service. I många fall använder dessa program [Azure IoT-enhetens SDK](../iot-hub/iot-hub-devguide-sdks.md). En underordnad enhet kan även vara ett program som körs på själva IoT Edge gateway-enheten.

Den här artikeln innehåller anvisningar för hur du ansluter en IoT-enhet som en underordnad enhet. Om du har en IoT Edge enhet som en underordnad enhet kan du läsa [Anslut en underordnad IoT Edge enhet till en Azure IoT Edge Gateway](how-to-connect-downstream-iot-edge-device.md).
:::moniker-end
<!-- end 1.2 -->

>[!NOTE]
>IoT-enheter som är registrerade med IoT Hub kan använda [modulerna](../iot-hub/iot-hub-devguide-module-twins.md) för att isolera olika processer, maskin vara eller funktioner på en enskild enhet. IoT Edge gateways stöder underordnad modul anslutningar med hjälp av symmetrisk nyckel autentisering men inte X. 509-certifikatautentisering.

Om du vill ansluta en underordnad enhet till en IoT Edge Gateway behöver du två saker:

* En enhet eller ett program som har kon figurer ATS med en IoT Hub enhets anslutnings sträng bifogad med information för att ansluta den till gatewayen.

    Det här steget slutfördes i föregående artikel, [autentisera en underordnad enhet till Azure IoT Hub](how-to-authenticate-downstream-device.md#retrieve-and-modify-connection-string).

* Enheten eller programmet måste lita på gatewayens **rotcertifikatutfärdarcertifikat** för att verifiera TLS-anslutningar (Transport Layer Security) till gateway-enheten.

    Det här steget beskrivs i detalj i resten av den här artikeln. Det här steget kan utföras på ett av två sätt: genom att installera CA-certifikatet i operativ systemets certifikat arkiv eller (för vissa språk) genom att referera till certifikatet i program med hjälp av Azure IoT SDK: er.

## <a name="tls-and-certificate-fundamentals"></a>TLS och certifikat grunderna

Utmaningen med att ansluta underordnade enheter på ett säkert sätt till IoT Edge är precis som vilken annan säker klient/server-kommunikation som sker via Internet. En klient och en Server kommunicerar säkert via Internet med [TLS (Transport Layer Security)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS har skapats med hjälp av standardiserad [PKI-konstruktion (Public Key Infrastructure)](https://en.wikipedia.org/wiki/Public_key_infrastructure) som kallas certifikat. TLS är en ganska engagerad specifikation och åtgärdar en mängd olika ämnen relaterade till att skydda två slut punkter. I det här avsnittet sammanfattas de begrepp som är relevanta för att ansluta enheter till en IoT Edge-gateway på ett säkert sätt.

När en klient ansluter till en server presenterar servern en kedja av certifikat, som kallas *Server certifikat kedjan*. En certifikat kedja består vanligt vis av ett certifikat från en rot certifikat utfärdare, ett eller flera mellanliggande CA-certifikat och slutligen själva serverns certifikat. En klient upprättar förtroende med en server genom att kryptografiskt verifiera hela Server certifikat kedjan. Den här klient verifieringen av Server certifikat kedjan kallas för *Server kedje verifiering*. Klienten utmanar servern för att bevisa att den privata nyckeln är kopplad till Server certifikatet i en process som kallas *bevis på innehavet*. Kombinationen av Server kedje validering och bevis på innehav kallas *serverautentisering*. För att verifiera en server certifikat kedja måste en-klient ha en kopia av rot certifikat utfärdarens certifikat som användes för att skapa (eller utfärda) Server certifikatet. Normalt när du ansluter till webbplatser, är en webbläsare förkonfigurerad med certifikat som ofta används, så att klienten har en sömlös process.

När en enhet ansluter till Azure IoT Hub är enheten klienten och moln tjänsten IoT Hub är servern. IoT Hub moln tjänsten backas upp av ett rot certifikat för certifikat utfärdare som kallas **Baltimore CyberTrust-rot**, som är offentligt tillgänglig och används ofta. Eftersom det IoT Hub CA-certifikatet redan är installerat på de flesta enheter, använder många TLS-implementeringar (OpenSSL, Schannel, LibreSSL) automatiskt det vid verifiering av Server certifikat. En enhet som ansluter till IoT Hub kan dock ha problem som försöker ansluta till en IoT Edge Gateway.

När en enhet ansluter till en IoT Edge Gateway, är den underordnade enheten klienten och gateway-enheten är servern. Azure IoT Edge låter dig bygga Gateway-certifikat kedjor som de ser bäst. Du kan välja att använda ett offentligt CA-certifikat, t. ex. Baltimore, eller använda ett självsignerat certifikat för rot certifikat utfärdare (eller internt). Offentliga CA-certifikat har ofta en kostnad som är kopplad till dem, så de används vanligt vis i produktions scenarier. Självsignerade CA-certifikat rekommenderas för utveckling och testning. Om du använder demonstrations certifikaten är det självsignerade certifikat från rot certifikat utfärdaren.

När du använder ett självsignerat certifikat för rot certifikat utfärdare för en IoT Edge Gateway måste det installeras på eller anges till alla underordnade enheter som försöker ansluta till gatewayen.

![Konfiguration av Gateway-certifikat](./media/how-to-create-transparent-gateway/gateway-setup.png)

Mer information om IoT Edge certifikat och vissa konsekvenser för produktion finns i [informationen om IoT Edge certifikat användning](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Ange rot certifikat utfärdarens certifikat

För att verifiera gateway-enhetens certifikat behöver den underordnade enheten en egen kopia av rot certifikat utfärdarens certifikat. Om du använde skripten som tillhandahölls i IoT Edge git-lagringsplatsen för att skapa test certifikat, kallas rot certifikat utfärdarens certifikat **Azure-IoT-test-Only. root. ca. cert. pem**. Om du inte redan har en del av de andra stegen för att förbereda enheten i den andra enheten flyttar du den här certifikat filen till valfri katalog på den underordnade enheten. Du kan använda en tjänst som [Azure Key Vault](../key-vault/index.yml) eller en funktion som [säker kopierings protokoll](https://www.ssh.com/ssh/scp/) för att flytta certifikat filen.

## <a name="install-certificates-in-the-os"></a>Installera certifikat i operativ systemet

När rot certifikat utfärdarens certifikat finns på den underordnade enheten måste du kontrol lera att de program som ansluter till gatewayen har åtkomst till certifikatet.

Om du installerar rot certifikat utfärdarens certifikat i operativ systemets certifikat Arkiv, kan de flesta program använda rot certifikat utfärdarens certifikat. Det finns vissa undantag, t. ex. NodeJS-program som inte använder sig av operativ systemets certifikat Arkiv, men som i stället använder Node Runtime: s interna certifikat arkiv. Om du inte kan installera certifikatet på operativ Systems nivå kan du gå vidare till [använda certifikat med Azure IoT SDK](#use-certificates-with-azure-iot-sdks): er.

### <a name="ubuntu"></a>Ubuntu

Följande kommandon är ett exempel på hur du installerar ett CA-certifikat på en Ubuntu-värd. I det här exemplet förutsätter vi att du använder certifikatet **Azure-IoT-test-Only. root. ca. cert. pem** från de nödvändiga artiklarna och att du har kopierat certifikatet till en plats på den underordnade enheten.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Du bör se ett meddelande som säger "uppdaterar certifikat i/etc/ssl/certs... 1 tillagt, 0 borttagen; färdig. "

### <a name="windows"></a>Windows

Följande steg är ett exempel på hur du installerar ett CA-certifikat på en Windows-värd. I det här exemplet förutsätter vi att du använder certifikatet **Azure-IoT-test-Only. root. ca. cert. pem** från de nödvändiga artiklarna och att du har kopierat certifikatet till en plats på den underordnade enheten.

Du kan installera certifikat med hjälp av PowerShell: s [import-Certificate](/powershell/module/pkiclient/import-certificate) som administratör:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

Du kan också installera certifikat med **certlm** -verktyget:

1. I Start-menyn söker du efter och väljer **Hantera dator certifikat**. Ett verktyg som kallas **certlm** öppnas.
2. Navigera till **certifikat – lokal dator**  >  **betrodda rot certifikat utfärdare**.
3. Högerklicka på **certifikat** och välj **alla aktiviteter**  >  **Importera**. Guiden Importera certifikat ska startas.
4. Följ stegen som dirigerad och importera certifikat filen `<path>/azure-iot-test-only.root.ca.cert.pem` . När du är klar bör du se ett meddelande som har importer ATS.

Du kan också installera certifikat program mässigt med .NET-API: er, som du ser i .NET-exemplet senare i den här artikeln.

Normalt använder programmen Windows-TLS-stacken som kallas [Schannel](/windows/desktop/com/schannel) för att ansluta säkert via TLS. Schannel *kräver* att alla certifikat installeras i certifikat arkivet i Windows innan du försöker upprätta en TLS-anslutning.

## <a name="use-certificates-with-azure-iot-sdks"></a>Använda certifikat med Azure IoT SDK: er

I det här avsnittet beskrivs hur Azure IoT SDK: er ansluter till en IoT Edge enhet med hjälp av enkla exempel program. Målet för alla exempel är att ansluta enhets klienten och skicka telemetri-meddelanden till gatewayen, sedan stänga anslutningen och avsluta.

Ha två saker klara innan du använder program nivå exempel:

* Den underordnade enhetens IoT Hub anslutnings sträng har ändrats för att peka på gateway-enheten och eventuella certifikat som krävs för att autentisera din underordnade enhet till IoT Hub. Mer information finns i [autentisera en underordnad enhet till Azure IoT Hub](how-to-authenticate-downstream-device.md).

* Den fullständiga sökvägen till rot certifikat utfärdarens certifikat som du kopierade och sparade någonstans på din underordnade enhet.

    Till exempel `<path>/azure-iot-test-only.root.ca.cert.pem`.

### <a name="nodejs"></a>NodeJS

Det här avsnittet innehåller ett exempel program för att ansluta en Azure IoT NodeJS-enhets klient till en IoT Edge Gateway. För NodeJS-program måste du installera rot certifikat utfärdarens certifikat på program nivå enligt vad som visas här. NodeJS-program använder inte systemets certifikat arkiv.

1. Hämta exemplet för **edge_downstream_device.js** från [Azure IoT-enhetens SDK för Node.js exempel lagrings platsen](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples).
2. Kontrol lera att du har alla krav för att köra exemplet genom att granska **Readme.MD** -filen.
3. Uppdatera **ConnectionString** -och **edge_ca_cert_path** -variablerna i edge_downstream_device.js-filen.
4. I SDK-dokumentationen hittar du instruktioner för hur du kör exemplet på enheten.

För att förstå exemplet som du kör, är följande kodfragment hur klient-SDK läser certifikat filen och använder den för att upprätta en säker TLS-anslutning:

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

Det här avsnittet introducerar ett exempel program för att ansluta en Azure IoT .NET-enhets klient till en IoT Edge Gateway. .NET-program kan dock automatiskt använda alla installerade certifikat i systemets certifikat Arkiv på både Linux-och Windows-värdar.

1. Hämta exemplet för **EdgeDownstreamDevice** från [mappen IoT Edge .net-exempel](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice).
2. Kontrol lera att du har alla krav för att köra exemplet genom att granska **Readme.MD** -filen.
3. I **egenskaperna/launchSettings.jspå** filen uppdaterar du **DEVICE_CONNECTION_STRING** och **CA_CERTIFICATE_PATH** variabler. Lämna den här variabeln tom om du vill använda certifikatet som är installerat i det betrodda certifikat arkivet på värd systemet.
4. I SDK-dokumentationen hittar du instruktioner för hur du kör exemplet på enheten.

Program mässigt installera ett betrott certifikat i certifikat arkivet via ett .NET-program finns i **InstallCACert ()** -funktionen i filen **EdgeDownstreamDevice/program. cs** . Den här åtgärden är idempotenta, så kan köras flera gånger med samma värden utan ytterligare påverkan.

### <a name="c"></a>C

Det här avsnittet introducerar ett exempel program för att ansluta en Azure IoT C-klient till en IoT Edge Gateway. C SDK kan användas med många TLS-bibliotek, inklusive OpenSSL, WolfSSL och Schannel. Mer information finns i [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

1. Hämta **iotedge_downstream_device_sample** -programmet från [Azure IoT Device SDK för C-exempel](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples).
2. Kontrol lera att du har alla krav för att köra exemplet genom att granska **Readme.MD** -filen.
3. Uppdatera **ConnectionString** -och **edge_ca_cert_path** -variablerna i filen iotedge_downstream_device_sample. c.
4. I SDK-dokumentationen hittar du instruktioner för hur du kör exemplet på enheten.


Azure IoT-enhetens SDK för C innehåller ett alternativ för att registrera ett CA-certifikat när klienten konfigureras. Den här åtgärden installerar inte certifikatet var som helst, utan använder i stället ett sträng format för certifikatet i minnet. Det sparade certifikatet tillhandahålls till den underliggande TLS-stacken när en anslutning upprättas.

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

>[!NOTE]
> Metoden för att registrera ett CA-certifikat när du konfigurerar klienten kan ändras om du använder ett [hanterat](https://github.com/Azure/azure-iot-sdk-c#packages-and-libraries) paket eller bibliotek. Till exempel måste [ARDUINO IDE-baserade bibliotek](https://github.com/azure/azure-iot-arduino) lägga till ca-certifikatet till en certifikat mat ris som definierats i en global [certifikats. c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) -fil i stället för att använda `IoTHubDeviceClient_LL_SetOption` åtgärden.  

Om du inte använder OpenSSL eller något annat TLS-bibliotek på Windows-värdar, använder SDK: n som standard för att använda Schannel. För att Schannel ska fungera bör IoT Edge rot certifikat utfärdare installeras i Windows certifikat Arkiv och inte anges med `IoTHubDeviceClient_SetOption` åtgärden.

### <a name="java"></a>Java

Det här avsnittet introducerar ett exempel program för att ansluta en Azure IoT Java-enhet till en IoT Edge Gateway.

1. Hämta exemplet för **skicka-händelse** från [Azure IoT-enhetens SDK för Java-exempel](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples).
2. Kontrol lera att du har alla krav för att köra exemplet genom att granska **Readme.MD** -filen.
3. I SDK-dokumentationen hittar du instruktioner för hur du kör exemplet på enheten.

### <a name="python"></a>Python

Det här avsnittet introducerar ett exempel program för att ansluta en Azure IoT python-enhets klient till en IoT Edge Gateway.

1. Hämta exemplet för **send_message_downstream** från [Azure IoT-enhetens SDK för python-exempel](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios).
2. Ställ in `IOTHUB_DEVICE_CONNECTION_STRING` `IOTEDGE_ROOT_CA_CERT_PATH` miljövariablerna och enligt vad som anges i kommentarerna för python-skriptet.
3. Mer information om hur du kör exemplet på enheten finns i SDK-dokumentationen.

## <a name="test-the-gateway-connection"></a>Testa Gateway-anslutningen

Använd det här exempel kommandot på den underordnade enheten för att testa att den kan ansluta till gateway-enheten:

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

Det här kommandot testar anslutningar över MQTTS (port 8883). Om du använder ett annat protokoll, justera kommandot efter behov för AMQPS (5671) eller HTTPS (433)

Utdata från det här kommandot kan vara långa, inklusive information om alla certifikat i kedjan. Om anslutningen lyckas visas en rad som `Verification: OK` eller `Verify return code: 0 (ok)` .

![Verifiera Gateway-anslutning](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Felsöka Gateway-anslutningen

Om din löv enhet har en tillfällig anslutning till sin gateway-enhet kan du prova följande steg för att lösa problemet.

1. Är Gateway-värdnamnet i anslutnings strängen detsamma som hostname-värdet i IoT Edge konfigurations filen på gatewayenheten?
2. Går det att matcha Gateway-värdnamnet till en IP-adress? Du kan lösa tillfälliga anslutningar antingen genom att använda DNS eller genom att lägga till en värd fil post på löv enheten.
3. Är kommunikations portar öppna i brand väggen? Kommunikation baserat på det protokoll som används (MQTTS: 8883/AMQPS: 5671/HTTPS: 433) måste vara möjlig mellan underordnad enhet och transparent IoT Edge.

## <a name="next-steps"></a>Nästa steg

Lär dig hur IoT Edge kan utöka [offline-funktioner](offline-capabilities.md) till underordnade enheter.