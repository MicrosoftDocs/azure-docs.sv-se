---
title: Gatewayer för underordnade enheter – Azure IoT Edge | Microsoft Docs
description: Använd Azure IoT Edge för att skapa en transparent, ogenomskinlig eller proxy-gatewayenhet som skickar data från flera efterföljande enheter till molnet eller bearbetar dem lokalt.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ca8f02b375420590bcf1cc732c067a165e22b3fa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492718"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Så kan en IoT Edge-enhet användas som gateway

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge enheter kan användas som gatewayer, vilket ger en anslutning mellan andra enheter i nätverket och IoT Hub.

Modulen IoT Edge Hub fungerar som IoT Hub, så kan hantera anslutningar från alla enheter som har en identitet med IoT Hub, inklusive andra IoT Edge enheter. Den här typen av Gateway-mönster anropas *transparent* eftersom meddelanden kan skickas från efterföljande enheter till IoT Hub som om det inte fanns någon gateway mellan dem.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Från och med version 1,2 av IoT Edge kan transparenta gateways hantera underordnade anslutningar från andra IoT Edge enheter.
::: moniker-end

För enheter som inte eller inte kan ansluta till IoT Hub på egen hand kan IoT Edge gateways tillhandahålla den anslutningen. Den här typen av Gateway-mönster kallas för *översättning* eftersom den IoT Edge enheten måste utföra bearbetningen på inkommande, underordnade enhets meddelanden innan de kan vidarebefordras till IoT Hub. De här scenarierna kräver ytterligare moduler på IoT Edge Gateway för att hantera bearbetnings stegen.

Mönstren för transparent och översättning av gatewayen är inte ömsesidigt uteslutande. En enda IoT Edge enhet kan fungera både som en transparent gateway och en översättnings-Gateway.

Alla gateway-mönster ger följande fördelar:

* **Analys vid gränsen** – Använd AI-tjänster lokalt för att bearbeta data som kommer från underordnade enheter utan att skicka full Fidelity telemetri till molnet. Hitta och reagera på insikter lokalt och skicka endast en delmängd data till IoT Hub.
* **Isolering av underordnad enhet** – gateway-enheten kan förhindra att alla underordnade enheter exponeras för Internet. Den kan sitta mellan ett nätverk för drift teknik som inte har någon anslutning och ett IT-nätverk (IT-teknik) som ger åtkomst till webben. På samma sätt kan enheter som inte har möjlighet att ansluta till IoT Hub på egen hand ansluta till en gateway-enhet i stället.
* **Anslutnings-multiplexering** – alla enheter som ansluter till IoT Hub via en IoT Edge Gateway kan använda samma underliggande anslutning. Den här funktionen för multiplexering kräver att IoT Edge Gateway använder AMQP som dess överordnade protokoll.
* **Trafik utjämning** – IoT Edges enheten implementerar automatiskt exponentiella backoff om IoT Hub begränsar trafik, samtidigt som meddelandena sparas lokalt. Den här förmånen gör din lösning flexibel till toppar i trafik.
* **Offline-support** – gateway-enheten lagrar meddelanden och dubbla uppdateringar som inte kan levereras till IoT Hub.

## <a name="transparent-gateways"></a>Transparenta gatewayer

I det transparenta Gateway-mönstret kan enheter som teoretiskt kan ansluta till IoT Hub ansluta till en gateway-enhet i stället. De underordnade enheterna har sina egna IoT Hub identiteter och ansluter med antingen MQTT-eller AMQP-protokoll. Gatewayen skickar helt enkelt kommunikation mellan enheterna och IoT Hub. Både enheter och användare som interagerar med dem via IoT Hub är inte medvetna om att en gateway är underställd deras kommunikation. Detta brist på medvetenhet innebär att gatewayen betraktas som *transparent*.

Mer information om hur IoT Edge Hub hanterar kommunikation mellan underordnade enheter och molnet finns i [förstå Azure IoT Edge Runtime och dess arkitektur](iot-edge-runtime.md).

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge enheter får inte ligga bakom en IoT Edge Gateway.

![Diagram – transparent Gateway-mönster](./media/iot-edge-as-gateway/edge-as-gateway-transparent.png)

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

Från och med version 1.2.0 kan IoT Edge enheter ansluta via transparenta gatewayer.

<!-- TODO add a downstream IoT Edge device to graphic -->

::: moniker-end

### <a name="parent-and-child-relationships"></a>Överordnade och underordnade relationer

Du deklarerar transparenta Gateway-relationer i IoT Hub genom att ange IoT Edge gateway som *överordnad* till en *underordnad underordnad* enhet som ansluter till den.

Överordnad/underordnad relation upprättas vid tre platser i Gateway-konfigurationen:

#### <a name="cloud-identities"></a>Moln identiteter

Alla enheter i ett scenario med transparent Gateway behöver moln identiteter så att de kan autentisera till IoT Hub. När du skapar eller uppdaterar en enhets identitet kan du ange enhetens överordnade eller underordnade enheter. Den här konfigurationen tillåter att den överordnade gatewayenheten hanterar autentisering för sina underordnade enheter.

>[!NOTE]
>Att ställa in den överordnade enheten i IoT Hub används som ett valfritt steg för underordnade enheter som använder symmetrisk nyckel autentisering. Men från och med version 1.1.0 måste varje underordnad enhet tilldelas till en överordnad enhet.
>
>Du kan konfigurera IoT Edge Hub så att den går tillbaka till föregående beteende genom att ställa in miljövariabeln **AuthenticationMode** på värdet **CloudAndScope**.

Underordnade enheter kan bara ha en överordnad. Varje överordnad kan ha upp till 100 underordnade.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
IoT Edge-enheter kan vara både överordnade och underordnade i transparent Gateway-relationer. En hierarki med flera IoT Edge enheter som rapporterar till varandra kan skapas. Den översta noden i en gateway-hierarki kan ha upp till fem generationer av underordnade. En IoT Edge enhet kan till exempel ha fem lager IoT Edge enheter som är länkade som underordnade under den. Men IoT Edge-enheten i den femte generationen kan inte ha underordnade IoT Edge eller på annat sätt.
::: moniker-end

#### <a name="gateway-discovery"></a>Gateway-identifiering

En underordnad enhet måste kunna hitta sin överordnade enhet i det lokala nätverket. Konfigurera Gateway-enheter med ett **värdnamn**, antingen ett fullständigt kvalificerat domän namn (FQDN) eller en IP-adress som de underordnade enheterna ska använda för att hitta den.

På underordnade IoT-enheter använder du parametern **gatewayHostname** i anslutnings strängen för att peka på den överordnade enheten.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
På underordnade IoT Edge enheter använder du parametern **parent_hostname** i konfigurations filen för att peka på den överordnade enheten.
::: moniker-end

#### <a name="secure-connection"></a>Säker anslutning

Överordnade och underordnade enheter måste också autentisera sina anslutningar till varandra. Varje enhet behöver en kopia av ett delat rot certifikat för certifikat utfärdare som de underordnade enheterna använder för att kontrol lera att de ansluter till rätt Gateway.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
När flera IoT Edge gatewayer ansluter till varandra i en gateway-hierarki, bör alla enheter i hierarkin använda en enda certifikat kedja.
::: moniker-end

### <a name="device-capabilities-behind-transparent-gateways"></a>Enhets kapacitet bakom transparenta gateways

Alla IoT Hub primitiver som fungerar med IoT Edges pipeline-pipeline stöder även transparenta Gateway-scenarier. Varje IoT Edge Gateway har lagrings-och vidarebefordrings funktioner för meddelanden som kommer genom den.

Använd följande tabell för att se hur olika IoT Hub funktioner stöds för enheter som jämförs med enheter bakom gatewayer.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

| Funktion | IoT-enhet | IoT bakom en gateway |
| ---------- | ---------- | -------------------- |
| [D2C-meddelanden (enhet till moln)](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![Ja – IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – underordnad IoT-D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [Meddelanden från moln till enhet (C2D)](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![Ja – IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – underordnad IoT-C2D](./media/iot-edge-as-gateway/check-yes.png) |
| [Direkta metoder](../iot-hub/iot-hub-devguide-direct-methods.md) | ![Ja – IoT Direct-metod](./media/iot-edge-as-gateway/check-yes.png) | ![Ja-underordnad IoT Direct-metod](./media/iot-edge-as-gateway/check-yes.png) |
| [Enhetens dubblare](../iot-hub/iot-hub-devguide-device-twins.md) och [modul är dubbla](../iot-hub/iot-hub-devguide-module-twins.md) | ![Ja – IoT-dubbla](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – underordnad IoT är dubbla](./media/iot-edge-as-gateway/check-yes.png) |
| [Fil uppladdning](../iot-hub/iot-hub-devguide-file-upload.md) | ![Ja – IoT File Upload](./media/iot-edge-as-gateway/check-yes.png) | ![Ingen-IoT, underordnad fil uppladdning](./media/iot-edge-as-gateway/crossout-no.png) |

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

| Funktion | IoT-enhet | IoT bakom en gateway | IoT Edge-enhet | IoT Edge bakom en gateway |
| ---------- | ---------- | --------------------------- | --------------- | -------------------------------- |
| [D2C-meddelanden (enhet till moln)](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![Ja – IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – underordnad IoT-D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Ja-IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Ja-underordnad IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [Meddelanden från moln till enhet (C2D)](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![Ja – IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – underordnad IoT-C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Ingen-IoT Edge C2D](./media/iot-edge-as-gateway/crossout-no.png) | ![Ingen-IoT Edge underordnade C2D](./media/iot-edge-as-gateway/crossout-no.png) |
| [Direkta metoder](../iot-hub/iot-hub-devguide-direct-methods.md) | ![Ja – IoT Direct-metod](./media/iot-edge-as-gateway/check-yes.png) | ![Ja-underordnad IoT Direct-metod](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – IoT Edge Direct-metod](./media/iot-edge-as-gateway/check-yes.png) | ![Ja-underordnad IoT Edge Direct-metod](./media/iot-edge-as-gateway/check-yes.png) |
| [Enhetens dubblare](../iot-hub/iot-hub-devguide-device-twins.md) och [modul är dubbla](../iot-hub/iot-hub-devguide-module-twins.md) | ![Ja – IoT-dubbla](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – underordnad IoT är dubbla](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – IoT Edge dubbla](./media/iot-edge-as-gateway/check-yes.png) | ![Ja-underordnade IoT Edge dubbla](./media/iot-edge-as-gateway/check-yes.png) |
| [Fil uppladdning](../iot-hub/iot-hub-devguide-file-upload.md) | ![Ja – IoT File Upload](./media/iot-edge-as-gateway/check-yes.png) | ![Ingen-IoT, underordnad fil uppladdning](./media/iot-edge-as-gateway/crossout-no.png) | ![Ingen-IoT Edge fil uppladdning](./media/iot-edge-as-gateway/crossout-no.png) | ![Ingen-IoT Edge underordnade fil uppladdning](./media/iot-edge-as-gateway/crossout-no.png) |
| Container image-hämtningar |   |   | ![Ja – IoT Edge container pull](./media/iot-edge-as-gateway/check-yes.png) | ![Ja-underordnad IoT Edge container pull](./media/iot-edge-as-gateway/check-yes.png) |
| BLOB-uppladdning |   |   | ![Ja – IoT Edge BLOB-uppladdning](./media/iot-edge-as-gateway/check-yes.png) | ![Ja-underordnad IoT Edge BLOB-uppladdning](./media/iot-edge-as-gateway/check-yes.png) |

**Behållar avbildningar** kan laddas ned, lagras och levereras från överordnade enheter till underordnade enheter.

**Blobbar**, inklusive stöd paket och loggar, kan överföras från underordnade enheter till överordnade enheter.

::: moniker-end

## <a name="translation-gateways"></a>Översättnings-gatewayer

Om underordnade enheter inte kan ansluta till IoT Hub måste IoT Edge Gateway fungera som en översättare. Detta mönster krävs ofta för enheter som inte stöder MQTT, AMQP eller HTTP. Eftersom dessa enheter inte kan ansluta till IoT Hub, kan de inte heller ansluta till IoT Edge Hub-modulen utan någon för bearbetning.

Anpassade eller tredje parts moduler som ofta är speciella för den underordnade enhetens maskin vara eller protokoll måste distribueras till IoT Edge Gateway. Dessa översättnings moduler tar inkommande meddelanden och omvandlar dem till ett format som kan förstås av IoT Hub.

Det finns två mönster för översättnings-gatewayer: *protokoll översättning* och *identitets översättning*.

![Diagram – översättning Gateway-mönster](./media/iot-edge-as-gateway/edge-as-gateway-translation.png)

### <a name="protocol-translation"></a>Protokoll Översättning

I protokoll översättningets Gateway-mönster har endast IoT Edge Gateway en identitet med IoT Hub. Modulen för översättning tar emot meddelanden från underordnade enheter, översätter dem till ett protokoll som stöds, och sedan skickar IoT Edge-enheten meddelandena för de underordnade enheternas räkning. All information ser ut som om den kommer från en enhet, gatewayen. Nedströmsenheter måste bädda in ytterligare identifierande information i sina meddelanden om molnprogram vill analysera data för varje enhet. Dessutom stöds inte IoT Hub primitiver som dubbla och direkta metoder för gateway-enheten, inte underordnade enheter. Gatewayer i det här mönstret betraktas som *täckande* i motsats till transparenta gatewayer, eftersom de skymmer de underordnade enheternas identiteter.

Protokoll översättning stöder enheter som är begränsade till resurserna. Många befintliga enheter producerar data som kan leda till affärs insikter. de har dock inte utformats med moln anslutning i åtanke. Täckande gateways gör att dessa data kan låsas upp och användas i en IoT-lösning.

### <a name="identity-translation"></a>Identitets Översättning

Gateway-mönstret för identitets översättning bygger på protokoll översättning, men IoT Edge Gateway tillhandahåller också en IoT Hub enhets identitet för de underordnade enheternas räkning. Översättnings modulen ansvarar för att förstå det protokoll som används av de underordnade enheterna, ge dem identitet och översätta sina meddelanden till IoT Hub primitiver. Nedströmsenheter visas i IoT Hub som enheter av första klass med tvillingar och metoder. Användare kan interagera med enheterna i IoT Hub och känner inte till den mellanliggande gatewayenheten.

Identitets översättning ger fördelarna med protokoll översättning och ger också fullständig hanterbarhet av underordnade enheter från molnet. Alla enheter i din IoT-lösning visas i IoT Hub oavsett vilket protokoll de använder.

### <a name="device-capabilities-behind-translation-gateways"></a>Enhets kapacitet bakom översättnings-gatewayer

I följande tabell förklaras hur IoT Hub funktioner utökas till underordnade enheter i båda mönstren för översättnings Gateway.

| Funktion | Protokoll Översättning | Identitets Översättning |
| ---------- | -------------------- | -------------------- |
| Identiteter lagrade i IoT Hub identitets registret | Endast gateway-enhetens identitet | Identiteter för alla anslutna enheter |
| Enhetstvilling | Endast gatewayen har en enhet och modul är dubbla | Varje ansluten enhet har sin egen enhet |
| Direkta metoder och meddelanden från moln till enhet | Molnet kan bara adressera gateway-enheten | Molnet kan adressera varje ansluten enhet individuellt |
| [IoT Hub begränsningar och kvoter](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Tillämpa på gateway-enheten | Använd för varje enhet |

När du använder protokoll översättnings mönstret delar alla enheter som ansluter via den gatewayen samma moln-till-enhet-kö, som kan innehålla högst 50 meddelanden. Använd bara det här mönstret när få enheter ansluter via varje fält-gateway och deras trafik från moln till enhet är låg.

Den IoT Edge körningen omfattar inte funktioner för protokoll eller identitets översättning. Dessa mönster kräver anpassade moduler eller tredjepartsprogram som ofta är speciella för den maskin vara och det protokoll som används. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) innehåller flera moduler för protokoll översättning att välja mellan. Ett exempel som använder identitets översättnings mönstret finns i [Azure IoT Edge LoRaWAN Starter Kit](https://github.com/Azure/iotedge-lorawan-starterkit).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar en transparent gateway på tre sätt:

* [Konfigurera en IoT Edge-enhet till att fungera som en transparent gateway](how-to-create-transparent-gateway.md)
* [Autentisera en underordnad enhet på Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Ansluta en underordnad enhet till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md)
