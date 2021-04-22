---
title: Förstå IP-adressen för din IoT Hub-| Microsoft Docs
description: Förstå hur du frågar din IP-adress för IoT Hub och dess egenskaper. IP-adressen för din IoT Hub kan ändras under vissa scenarier, till exempel haveriberedskap eller regional redundans.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: 7d807a15d358bd621baedbff253f0c731e43ed26
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874179"
---
# <a name="iot-hub-ip-addresses"></a>IoT Hub IP-adresser

IP-adressprefixen för IoT Hub offentliga slutpunkter publiceras regelbundet under _tjänsttaggen AzureIoTHub._ [](../virtual-network/service-tags-overview.md)

> [!NOTE]
> För enheter som distribueras i lokala nätverk stöder Azure IoT Hub VNET-anslutningsintegrering med privata slutpunkter. Mer [IoT Hub finns i IoT Hub för VNet.](./virtual-network-support.md)


Du kan använda dessa IP-adressprefix för att styra anslutningen mellan IoT Hub och dina enheter eller nätverkstillgångar för att implementera en mängd olika mål för nätverksisolering:

| Mål | Tillämpliga scenarier | Metod |
|------|-----------|----------|
| Se till att dina enheter och tjänster endast kommunicerar IoT Hub slutpunkter | [Enhet-till-moln,](./iot-hub-devguide-messaging.md)och meddelanden från moln till [enhet,](./iot-hub-devguide-messages-c2d.md) [direktmetoder,](./iot-hub-devguide-direct-methods.md) [enhets- och modultvilling och](./iot-hub-devguide-device-twins.md) [enhetsströmmar](./iot-hub-device-streams-overview.md) | Använd _AzureIoTHub-_ och _EventHub-tjänsttaggar_ för att identifiera ip-adressprefix för IoT Hub och Händelsehubb och konfigurera TILLÅT-regler för enheternas och tjänsternas brandväggsinställningar för dessa IP-adressprefix. släpp trafik till andra mål-IP-adresser som du inte vill att enheterna eller tjänsterna ska kommunicera med. |
| Se till IoT Hub enhetens slutpunkt endast tar emot anslutningar från dina enheter och nätverkstillgångar | [Enhet-till-moln,](./iot-hub-devguide-messaging.md)och meddelanden från moln till [enhet,](./iot-hub-devguide-messages-c2d.md) [direktmetoder,](./iot-hub-devguide-direct-methods.md) [enhets- och modultvilling och](./iot-hub-devguide-device-twins.md) [enhetsströmmar](./iot-hub-device-streams-overview.md) | Använd IoT Hub [IP-filterfunktion för](iot-hub-ip-filtering.md) att tillåta anslutningar från dina enheter och IP-adresser för nätverkstillgång (se [avsnittet om](#limitations-and-workarounds) begränsningar). | 
| Se till att vägarnas anpassade slutpunktsresurser (lagringskonton, Service Bus och händelsehubbar) endast kan nås från dina nätverkstillgångar | [Meddelanderoutning](./iot-hub-devguide-messages-d2c.md) | Följ resursens riktlinjer för att begränsa anslutningen (till exempel via [brandväggsregler,](../storage/common/storage-network-security.md) [privata länkar](../private-link/private-endpoint-overview.md)eller [tjänstslutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md)); använd _AzureIoTHub-tjänsttaggar_ för att identifiera IoT Hub IP-adressprefix och lägga till ALLOW-regler för dessa IP-prefix i resursens brandväggskonfiguration (se [avsnittet](#limitations-and-workarounds) om begränsningar). |



## <a name="best-practices"></a>Bästa praxis

* När du lägger till TILLÅT-regler i enheternas brandväggskonfiguration är det bäst att ange specifika [portar som används av tillämpliga protokoll.](./iot-hub-devguide-protocols.md#port-numbers)

* IP-adressprefixen för IoT Hub kan komma att ändras. Dessa ändringar publiceras regelbundet via tjänsttaggar innan de börjar gälla. Det är därför viktigt att du utvecklar processer för att regelbundet hämta och använda de senaste tjänsttaggarna. Den här processen kan automatiseras via [identifierings-API:et för tjänsttaggar.](../virtual-network/service-tags-overview.md#service-tags-on-premises) Observera att API:et för identifiering av tjänsttaggar fortfarande är i förhandsversion och i vissa fall kanske inte skapar en fullständig lista över taggar och IP-adresser. Tills identifierings-API:et är allmänt tillgängligt bör du överväga att använda [tjänsttaggarna i nedladdningsbart JSON-format.](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) 

* Använd *AzureIoTHub.[ regionnamn] tagg* för att identifiera IP-prefix som används av IoT Hub-slutpunkter i en viss region. För att ta hänsyn [](iot-hub-ha-dr.md) till haveriberedskap för datacenter, eller regional redundans, säkerställer du att anslutningen till IP-prefixen för din IoT Hub region för geo-par också är aktiverad.

* Att konfigurera brandväggsregler i IoT Hub kan blockera anslutningen som krävs för att köra Azure CLI och PowerShell-kommandon mot IoT Hub. För att undvika detta kan du lägga till ALLOW-regler för klienters IP-adressprefix för att återaktivera CLI eller PowerShell-klienter för att kommunicera med IoT Hub.  


## <a name="limitations-and-workarounds"></a>Begränsningar och lösningar

* IoT Hub IP-filterfunktionen har en gräns på 100 regler. Den här gränsen och kan höjas via begäranden via Azures kundsupport. 

* De konfigurerade IP-filtreringsreglerna tillämpas endast på dina IP IoT Hub slutpunkter och inte på din IoT-hubbs inbyggda Event Hub-slutpunkt. [](iot-hub-ip-filtering.md) Om du även kräver att IP-filtrering tillämpas på den händelsehubb där dina meddelanden lagras kan du göra det genom att ta med din egen Event Hub-resurs där du kan konfigurera dina önskade IP-filtreringsregler direkt. För att göra det måste du etablera en [](./iot-hub-devguide-messages-d2c.md) egen händelsehubbresurs och konfigurera meddelanderoutning för att skicka meddelanden till resursen i stället för din IoT Hub inbyggda Händelsehubb. Slutligen, som beskrivs i tabellen ovan, för att aktivera funktioner för meddelanderoutning måste du också tillåta anslutning från IoT Hub:s IP-adressprefix till din etablerade Event Hub-resurs.

* När du dirigerar till ett lagringskonto är det bara möjligt att tillåta trafik från IoT Hub:s IP-adressprefix när lagringskontot finns i en annan region än IoT Hub.

## <a name="support-for-ipv6"></a>Stöd för IPv6 

IPv6 stöds för närvarande inte på IoT Hub.
