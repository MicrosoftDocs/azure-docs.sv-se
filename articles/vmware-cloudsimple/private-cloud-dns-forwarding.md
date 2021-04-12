---
title: Azure VMware-lösning – DNS-vidarebefordring från privat moln till lokalt
description: Beskriver hur du aktiverar DNS-servern för CloudSimple-privata moln för att vidarebefordra sökning av lokala resurser
author: Ajayan1008
ms.author: v-hborys
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3481e21a93fbf5f658c5ca00513ea179c8f95400
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899209"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>Aktivera CloudSimple privata moln DNS-servrar för att vidarebefordra DNS-sökning av lokala resurser till dina DNS-servrar

DNS-servrar för privata moln kan vidarebefordra DNS-sökning för alla lokala resurser till dina DNS-servrar.  Genom att aktivera sökningen kan du använda privata moln vSphere-komponenter för att leta upp alla tjänster som körs i din lokala miljö och kommunicera med dem med hjälp av fullständigt kvalificerade domän namn (FQDN).

## <a name="scenarios"></a>Scenarier 

Genom att vidarebefordra DNS-sökning för din lokala DNS-server kan du använda ditt privata moln i följande scenarier:

* Använd privat moln som en katastrof återställnings installation för din lokala VMware-lösning
* Använd lokala Active Directory som identitets källa för ditt privata moln vSphere
* Använda HCX för att migrera virtuella datorer från lokala platser till privata moln

## <a name="before-you-begin"></a>Innan du börjar

En nätverks anslutning måste vara tillgänglig från ditt privata moln nätverk till ditt lokala nätverk för att DNS-vidarebefordran ska fungera.  Du kan konfigurera nätverks anslutning med:

* [Ansluta från den lokala platsen till CloudSimple med ExpressRoute](on-premises-connection.md)
* [Konfigurera en plats-till-plats-VPN-gateway](./vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)

Brand Väggs portar måste öppnas på den här anslutningen för att DNS-vidarebefordran ska fungera.  Portarna som används är TCP-port 53 eller UDP-port 53.

> [!NOTE]
> Om du använder plats-till-plats-VPN måste ditt lokala DNS-Server-undernät läggas till som en del av lokala prefix.

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>Begär DNS-vidarebefordring från privat moln till lokalt

Om du vill aktivera DNS-vidarebefordran från det privata molnet till lokal, skickar du en [supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)och ger följande information.

* Typ av problem: **teknisk**
* Prenumeration: **prenumeration där CloudSimple-tjänsten har distribuerats**
* Tjänst: **VMware-lösning av CloudSimple**
* Problem typ: **rekommendation eller hur gör jag för att...**
* Problem under typ: **behöver hjälp med NW**
* Ange domän namnet för din lokala domän i informations fönstret.
* Ange en lista över dina lokala DNS-servrar som sökningen ska vidarebefordras från ditt privata moln i informations fönstret.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om konfiguration av lokala brand väggar](on-premises-firewall-configuration.md)
* [Lokal DNS-serverkonfiguration](on-premises-dns-setup.md)
