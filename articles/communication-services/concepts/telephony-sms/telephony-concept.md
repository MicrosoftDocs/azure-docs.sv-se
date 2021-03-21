---
title: Metoder för PSTN-integrering av telefoni för Azure Communication Services
description: Lär dig hur du integrerar PSTN-anrop i Azure Communication Services-programmet.
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: d5e4920dcc422e848266f35c8a59175b5149b924
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492752"
---
# <a name="telephony-concepts"></a>Telefoni koncept

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]
[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Azure Communication Services som anropar klient bibliotek kan användas för att lägga till telefoni och PSTN till dina program. På den här sidan sammanfattas viktiga begrepp och funktioner för telefoni. Se det [anropande biblioteket](../../quickstarts/voice-video-calling/calling-client-samples.md) om du vill veta mer om olika språk och funktioner för klient bibliotek.

## <a name="overview-of-telephony"></a>Översikt över telefoni
När dina användare interagerar med ett traditionellt telefonnummer, under lättas samtal av ett PSTN-röst samtal (Public Switched Phone Network). Om du vill göra och ta emot PSTN-samtal måste du lägga till telefoni funktioner i Azure Communication Services-resursen. I detta fall använder signalering och media en kombination av IP-baserade och PSTN-baserade tekniker för att ansluta dina användare. Kommunikations tjänsterna tillhandahåller två diskreta sätt att komma åt PSTN-nätverket: Azure Cloud Call och SIP-gränssnittet.

### <a name="azure-cloud-calling"></a>Azure-molnet ringer

Ett enkelt sätt att lägga till PSTN-anslutning till din app eller tjänst är i det här fallet Microsoft Telco-leverantören. Du kan köpa siffror direkt från Microsoft. Azure Cloud Call är en allt-i-moln-telefoni lösning för kommunikations tjänster. Detta är det enklaste alternativet som ansluter ACS till det offentliga switchade telefonnätet (PSTN) för att möjliggöra samtal till landlines och mobil telefoner över hela världen. Med det här alternativet fungerar Microsoft som PSTN-bärvåg, som du ser i följande diagram:

![Azure Cloud-samtals diagram.](../media/telephony-concept/azure-calling-diagram.png)

Om du svarar ja på följande är Azure Cloud Call rätt lösning för dig:
- Azures moln anrop är tillgängligt i din region.
- Du behöver inte behålla den aktuella PSTN-bärvågen.
- Du vill använda Microsoft-hanterad åtkomst till PSTN.

Med det här alternativet:
- Du får siffror direkt från Microsoft och kan anropa telefoner runtom i världen.
- Du behöver inte distribuera eller underhålla en lokal distribution – eftersom Azure Cloud-anropet fungerar utanför Azure Communication Services.
- OBS! om det behövs kan du välja att ansluta en session Border Controller (SBC) som stöds via SIP-gränssnittet för interoperabilitet med PBXs från tredje part, analoga enheter och annan extern telefoni utrustning som stöds av SBC.

Det här alternativet kräver en oavbruten anslutning till Azure Communication Services.

### <a name="sip-interface"></a>SIP-gränssnitt

Med det här alternativet kan du ansluta äldre lokala telefoni tjänster och operatören som du väljer för Azure Communication Services. Den tillhandahåller PSTN-anrop till ACS-programmen även om Azures moln anrop inte är tillgängligt i ditt land/din region. 

![Diagram över SIP-gränssnitt.](../media/telephony-concept/sip-interface-diagram.png)

Om du svarar ja på någon av följande frågor, är SIP-gränssnittet rätt lösning för dig:

- Du vill använda ACS med funktioner för PSTN-anrop.
- Du måste behålla den aktuella PSTN-bärvågen.
- Du vill blanda routningen, med vissa anrop via Azure-molnet, vissa via din operatör.
- Du måste samar beta med PBXs och/eller utrustning från tredje part, t. ex. överliggande pagers, analoga enheter och så vidare.

Med det här alternativet:

- Du ansluter dina egna SBC till Azure Communication Services utan att behöva ytterligare lokal program vara.
- Du kan använda bokstavligen alla telefoni bär Vågs operatörer med ACS.
- Du kan välja att konfigurera och hantera det här alternativet, eller så kan det konfigureras och hanteras av operatören eller partnern (fråga om operatören eller partnern tillhandahåller det här alternativet).
- Du kan konfigurera samverkan mellan din telefoni utrustning, till exempel en PBX-och analog-enhet från tredje part, och ACS.

Det här alternativet kräver följande:

- Oavbruten anslutning till Azure.
- Distribuera och underhålla en SBC som stöds.
- Ett avtal med en tredje parts operatör. (Om du inte har distribuerat som ett alternativ för att tillhandahålla en anslutning till en tredjeparts PBX, analoga enheter eller annan telefoni utrustning för användare som använder kommunikations tjänster.)

## <a name="next-steps"></a>Nästa steg

### <a name="conceptual-documentation"></a>Konceptuell dokumentation

- [Telefonnummer typer i Azure Communication Services](./plan-solution.md)
- [Planera för SIP-gränssnitt](./sip-interface-infrastructure.md)
- [Prissättning](../pricing.md)

### <a name="quickstarts"></a>Snabbstarter

- [Hämta ett telefonnummer](../../quickstarts/telephony-sms/get-phone-number.md)
- [Ring till telefon](../../quickstarts/voice-video-calling/pstn-call.md)
