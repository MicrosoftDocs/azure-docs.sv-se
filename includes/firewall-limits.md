---
title: ta med fil
description: ta med fil
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 04/07/2021
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: c4c36c0e099ed7474a5d27f6edcbd4b3ac435f4f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030839"
---
| Resurs | Gräns |
| --- | --- |
| Dataflöde |30 Gbit/s<sup>1</sup> |
|Regel gränser|10 000 unika käll/destinationer i nätverks regler|
|Högsta antal DNAT-regler|298 för en enskild offentlig IP-adress.<br>Eventuella ytterligare offentliga IP-adresser bidrar till de tillgängliga SNAT-portarna, men minskar antalet tillgängliga DNAT-regler. Två offentliga IP-adresser tillåter till exempel 297 DNAT-regler. Om en regels protokoll har kon figurer ATS för både TCP och UDP räknas det som två regler.|
|Minsta AzureFirewallSubnet-storlek |/26|
|Portintervall i nätverk och regler|1 - 65535|
|Offentliga IP-adresser|250 max. Alla offentliga IP-adresser kan användas i DNAT-regler och alla bidrar till tillgängliga SNAT-portar.|
|IP-adresser i IP-grupper|Högst 100 IP-grupper per brand vägg.<br>Högst 5000 enskilda IP-adresser eller IP-prefix per IP-grupp.
|Routningstabell|Som standard har AzureFirewallSubnet en router på 0.0.0.0/0 med NextHopType-värdet inställt på **Internet**.<br><br>Azure Firewall måste ha direktanslutning till internet. Om din AzureFirewallSubnet lär sig en standard väg till ditt lokala nätverk via BGP, måste du åsidosätta att med värdet 0.0.0.0/0 UDR med **NextHopType** -värdet inställt på **Internet** för att upprätthålla direkt Internet anslutning. Som standard stöder inte Azure-brandväggen Tvingad tunnel trafik till ett lokalt nätverk.<br><br>Men om konfigurationen kräver Tvingad tunnel trafik till ett lokalt nätverk, kommer Microsoft att stödja den på ett fall med hjälp av fall. Kontakta supporten så att vi kan granska ditt ärende. Om det godkänns kommer vi att tillåta din prenumeration och se till att den nödvändiga brand Väggs anslutningen för Internet underhålls.|
|FQDN i nätverks regler|För bästa prestanda får du inte överstiga 1000 FQDN över alla nätverks regler per brand vägg.|

<sup>1</sup> Kontakta Azure-supporten om du behöver öka dessa gränser.
