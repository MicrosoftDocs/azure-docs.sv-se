---
title: Förstå IP-adressen för din IoT Device Provisioning service-instans | Microsoft Docs
description: Lär dig hur du frågar din IoT-adress (IoT Device Provisioning service) och dess egenskaper. IP-adressen för din DPS-instans kan ändras under vissa scenarier, t. ex. haveri beredskap eller regional redundans.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "79284933"
---
# <a name="iot-hub-dps-ip-addresses"></a>IoT Hub DPS IP-adresser

IP-adressprefix för offentliga slut punkter för en IoT Hub Device Provisioning Service (DPS) publiceras regelbundet under _AzureIoTHub_ [service tag](../virtual-network/service-tags-overview.md). Du kan använda de här IP-adressprefix för att kontrol lera anslutningen mellan en IoT DPS-instans och enheter eller nätverks till gångar för att implementera en mängd olika mål för nätverks isolering:

| Mål | Metod |
|------|----------|
| Se till att enheter och tjänster endast kommunicerar med IoT Hub DPS-slutpunkter | Använd _AzureIoTHub_ -tjänst tag gen för att identifiera IoT Hub DPS-instanser. Konfigurera inställningarna för brand väggen för dina enheters och tjänsters brand vägg för dessa IP-adressprefix enligt detta. Konfigurera regler för att släppa trafik till andra mål-IP-adresser som du inte vill att enheter eller tjänster ska kommunicera med. |
| Se till att din IoT Hub DPS-slutpunkt tar emot anslutningar enbart från dina enheter och nätverks till gångar | Använd funktionen IoT DPS [IP filter](iot-dps-ip-filtering.md) för att skapa filter regler för API: er för enhet och DPS-tjänst. Dessa filter regler kan användas för att bara tillåta anslutningar från dina enheter och nätverks till gångens IP-adresser (se avsnittet [begränsningar](#limitations-and-workarounds) ). | 




## <a name="best-practices"></a>Bästa praxis

* När du lägger till Tillåt-regler i enhetens brand Väggs konfiguration, är det bäst att tillhandahålla vissa [portar som används av tillämpliga protokoll](../iot-hub/iot-hub-devguide-protocols.md#port-numbers).

* IP-adressprefix för IoT DPS-instanser kan komma att ändras. Dessa ändringar publiceras regelbundet via service tag innan de börjar att fungera. Det är därför viktigt att du utvecklar processer för att regelbundet hämta och använda de senaste service taggarna. Den här processen kan automatiseras via [API: et för identifiering av service märken](../virtual-network/service-tags-overview.md#service-tags-on-premises). API: et för identifiering av service märken är fortfarande i för hands version och i vissa fall får du inte skapa en fullständig lista över taggar och IP-adresser. Innan identifierings-API: et är allmänt tillgängligt, kan du överväga att använda [tjänst taggarna i nedladdnings Bart JSON](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) 

* Använd *AzureIoTHub. [ region namn]* -tagg för att identifiera IP-prefix som används av DPS-slutpunkter i en angiven region. Om du vill konto för haveri beredskap för data Center eller [regional redundans](../iot-hub/iot-hub-ha-dr.md)ser du till att anslutning till IP-prefix för din DPS-instanss region för geo-paret också är aktive rad.

* Genom att konfigurera brand Väggs regler för en DPS-instans kan du blockera anslutningen som krävs för att köra Azure CLI-och PowerShell-kommandon mot den. För att undvika dessa anslutnings problem kan du lägga till regler för klienternas IP-adressprefix för att återaktivera CLI-eller PowerShell-klienter för att kommunicera med din DPS-instans.  


## <a name="limitations-and-workarounds"></a>Begränsningar och lösningar

* Funktionen DPS IP-filter har en gräns på 100 regler. Den här gränsen och kan höjas via förfrågningar via Azure-kundsupport. 

* De konfigurerade [IP-filtrerings reglerna](iot-dps-ip-filtering.md) tillämpas bara på dina DPS-slutpunkter och inte på de länkade IoT Hub slut punkterna. IP-filtrering för länkade IoT-hubbar måste konfigureras separat. Mer information finns i [IoT Hub IP-regler för filtrering](../iot-hub/iot-hub-ip-filtering.md).

## <a name="support-for-ipv6"></a>Stöd för IPv6 

IPv6 stöds för närvarande inte i IoT Hub eller DPS.

## <a name="next-steps"></a>Nästa steg

Mer information om konfiguration av IP-adresser med DPS finns i:

* [Konfigurera IP-filtrering](iot-dps-ip-filtering.md)
