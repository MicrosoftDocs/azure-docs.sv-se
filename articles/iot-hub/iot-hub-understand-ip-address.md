---
title: Förstå IP-adressen för din IoT Hub | Microsoft Docs
description: Lär dig hur du frågar din IP-adress för IoT Hub och dess egenskaper. IP-adressen för din IoT Hub kan ändras under vissa scenarier som haveri beredskap eller regional redundans.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 1bfae8c7afbfdc6e73dd8bb17b94e6543361e9ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "83848236"
---
# <a name="iot-hub-ip-addresses"></a>IoT Hub IP-adresser

IP-adressprefix för IoT Hub offentliga slut punkter publiceras regelbundet under _AzureIoTHub_ [service tag](../virtual-network/service-tags-overview.md).

> [!NOTE]
> För enheter som distribueras i lokala nätverk stöder Azure IoT Hub VNET-anslutning med privata slut punkter. Mer information finns i [IoT Hub stöd för VNet](./virtual-network-support.md) .


Du kan använda dessa IP-adressprefix för att kontrol lera anslutningen mellan IoT Hub och dina enheter eller nätverks till gångar för att implementera en mängd olika mål för nätverks isolering:

| Mål | Tillämpliga scenarier | Metod |
|------|-----------|----------|
| Se till att enheter och tjänster endast kommunicerar med IoT Hub slut punkter | [Enhets-till-moln](./iot-hub-devguide-messaging.md)-meddelanden och meddelanden från [moln till enhet](./iot-hub-devguide-messages-c2d.md) , [direkta metoder](./iot-hub-devguide-direct-methods.md), [enhet och modul, dubbla](./iot-hub-devguide-device-twins.md) metoder och [enhets strömmar](./iot-hub-device-streams-overview.md) | Använd _AzureIoTHub_ och _EventHub_ service-taggar för att identifiera IoT Hub och prefix för IP-adressprefix i Event Hub och konfigurera Tillåt regler på inställningarna för enhetens och tjänsternas brand vägg för dessa IP-adressprefix enligt detta; släpp trafik till andra mål-IP-adresser som du inte vill att enheterna eller tjänsterna ska kommunicera med. |
| Se till att din IoT Hub enhets slut punkt tar emot anslutningar enbart från dina enheter och nätverks till gångar | [Enhets-till-moln](./iot-hub-devguide-messaging.md)-meddelanden och meddelanden från [moln till enhet](./iot-hub-devguide-messages-c2d.md) , [direkta metoder](./iot-hub-devguide-direct-methods.md), [enhet och modul, dubbla](./iot-hub-devguide-device-twins.md) metoder och [enhets strömmar](./iot-hub-device-streams-overview.md) | Använd IoT Hub [IP filter-funktion](iot-hub-ip-filtering.md) för att tillåta anslutningar från dina enheter och nätverks till gångens IP-adresser (se avsnittet [begränsningar](#limitations-and-workarounds) ). | 
| Se till att dina vägars anpassade slut punkts resurser (lagrings konton, Service Bus-och Event Hub) går att komma åt från dina nätverks till gångar | [Meddelanderoutning](./iot-hub-devguide-messages-d2c.md) | Följ resursens vägledning om hur du begränsar anslutningen (till exempel via [brand Väggs regler](../storage/common/storage-network-security.md), [privata länkar](../private-link/private-endpoint-overview.md)eller [tjänst slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md)). Använd _AzureIoTHub_ service-taggar för att identifiera IoT Hub IP-adressprefix och Lägg till Tillåt-regler för dessa IP-prefix i din resurs brand Väggs konfiguration (se avsnittet [begränsningar](#limitations-and-workarounds) ). |



## <a name="best-practices"></a>Bästa praxis

* När du lägger till Tillåt-regler i enhetens brand Väggs konfiguration, är det bäst att tillhandahålla vissa [portar som används av tillämpliga protokoll](./iot-hub-devguide-protocols.md#port-numbers).

* IP-adressprefix för IoT Hub kan komma att ändras. Dessa ändringar publiceras regelbundet via service tag innan de börjar att fungera. Det är därför viktigt att du utvecklar processer för att regelbundet hämta och använda de senaste service taggarna. Den här processen kan automatiseras via [API: et för identifiering av service märken](../virtual-network/service-tags-overview.md#service-tags-on-premises). Observera att API för identifiering av service märken fortfarande är i för hands version och i vissa fall får du inte skapa en fullständig lista över taggar och IP-adresser. Innan identifierings-API: et är allmänt tillgängligt, kan du överväga att använda [tjänst taggarna i nedladdnings Bart JSON](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) 

* Använd *AzureIoTHub. [ region namn]* -tagg för att identifiera IP-prefix som används av IoT Hub-slutpunkter i en speciell region. För att konto för haveri beredskap för data Center, eller [regional redundans](iot-hub-ha-dr.md) garanterar anslutning till IP-prefix för din IoT Hubs region för geo-par är också aktive rad.

* Genom att konfigurera brand Väggs regler i IoT Hub kan du blockera anslutningen som krävs för att köra Azure CLI-och PowerShell-kommandon mot din IoT Hub. För att undvika detta kan du lägga till regler för klienternas IP-adressprefix för att återaktivera CLI-eller PowerShell-klienter för att kommunicera med din IoT Hub.  


## <a name="limitations-and-workarounds"></a>Begränsningar och lösningar

* IoT Hub IP-filter-funktionen har en gräns på 10 regler. Den här gränsen och kan höjas via förfrågningar via Azure-kundsupport. 

* De konfigurerade [reglerna för IP-filtrering](iot-hub-ip-filtering.md) tillämpas bara på dina IoT Hub IP-slutpunkter och inte i IoT Hub: s inbyggda Event Hub-slutpunkt. Om du även behöver använda IP-filtrering på den Händelsehubben där dina meddelanden lagras, kan du göra det genom att ta med din egen Event Hub-resurs där du kan konfigurera önskade IP-filtrerings regler direkt. För att göra det måste du etablera en egen Event Hub-resurs och konfigurera [meddelanderoutning](./iot-hub-devguide-messages-d2c.md) för att skicka meddelanden till den resursen i stället för din IoT Hub inbyggda händelsehubben. Slutligen, som beskrivs i tabellen ovan, för att aktivera funktionen för meddelanderoutning måste du också tillåta anslutning från IoT Hub IP-adressprefix till din etablerade Event Hub-resurs.

* Vid routning till ett lagrings konto är det bara möjligt att tillåta trafik från IoT Hub IP-adressprefix när lagrings kontot finns i en annan region som IoT Hub.

## <a name="support-for-ipv6"></a>Stöd för IPv6 

IPv6 stöds för närvarande inte på IoT Hub.
