---
title: Scenarier för privata zoner – Azure DNS
description: I den här artikeln lär du dig om vanliga scenarier för användning av Azure DNS Private Zones.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: ab850adb2e9a25778d5f44ba711eb0762fe562c8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "76939341"
---
# <a name="azure-dns-private-zones-scenarios"></a>Scenarier för Azure DNS privata zoner

Azure DNS Private Zones tillhandahålla namn matchning i ett virtuellt nätverk samt mellan virtuella nätverk. I den här artikeln tittar vi på några vanliga scenarier som kan realiseras med hjälp av den här funktionen.

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Scenario: namn matchningen är begränsad till ett enda virtuellt nätverk
I det här scenariot har du ett virtuellt nätverk i Azure som har ett antal Azure-resurser, inklusive Virtual Machines (VM). Du vill lösa resurserna inifrån det virtuella nätverket via ett särskilt domän namn (DNS-zon) och du behöver namn matchningen som privat och inte tillgänglig från Internet. För de virtuella datorerna i det virtuella nätverket måste Azure dessutom registrera dem automatiskt i DNS-zonen. 

Det här scenariot beskrivs nedan. Virtual Network med namnet "A" innehåller två virtuella datorer (VNETA-VM1 och VNETA-VM2). Vart och ett av dessa har privata IP-adresser associerade. När du skapar en privat zon med namnet contoso.com och länkat det här virtuella nätverket som ett virtuellt registrerings nätverk skapar Azure DNS automatiskt två poster i zonen som de visas. DNS-frågor från VNETA-VM1 för att lösa VNETA-VM2.contoso.com kommer nu att få ett DNS-svar som innehåller den privata IP-adressen för VNETA-VM2. Dessutom kommer en omvänd DNS-fråga (PTR) för den privata IP-adressen för VNETA-VM1 (10.0.0.1) som utfärdats från VNETA-VM2 att få ett DNS-svar som innehåller namnet på VNETA-VM1, som förväntat. 

![Enskild virtuell nätverks lösning](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Scenario: namn matchning över virtuella nätverk

Det här scenariot är det vanligaste fallet där du behöver associera en privat zon med flera virtuella nätverk. Det här scenariot kan passa arkitekturer som Hub-och-eker-modellen där det finns ett virtuellt nätverk i Central hubb som flera andra eker-virtuella nätverk är anslutna till. Det virtuella nätverket i den centrala hubben kan länkas till en privat zon för det virtuella nätverket, och de virtuella ekrar som kan länkas till virtuella nätverk. 

Följande diagram visar en enkel version av det här scenariot där det bara finns två virtuella nätverk – A och B. A anges som ett virtuellt registrerings nätverk och B anges som ett virtuellt lösnings nätverk. Avsikten är att båda virtuella nätverken ska dela en gemensam zon contoso.com. När zonen har skapats och de virtuella datorerna för matchning och registrering är länkade till zonen registrerar Azure automatiskt DNS-poster för de virtuella datorerna (VNETA-VM1 och VNETA-VM2) från det virtuella nätverket A. Du kan också manuellt lägga till DNS-poster i zonen för virtuella datorer i det virtuella matchnings nätverket B. Med den här installationen ser du följande saker för att vidarebefordra och omvända DNS-frågor:
* En DNS-fråga från VNETB-VM1 i lösningens virtuella nätverk B, för VNETA-VM1.contoso.com, kommer att få ett DNS-svar som innehåller den privata IP-adressen för VNETA-VM1.
* En omvänd DNS-fråga (PTR) från VNETB-VM2 i den virtuella matchnings nätverket B, för 10.1.0.1, kommer att få ett DNS-svar som innehåller FQDN-VNETB-VM1.contoso.com.  
* En omvänd DNS-fråga (PTR) från VNETB-VM3 i den virtuella matchnings nätverket B, för 10.0.0.1, kommer att få NXDOMAIN. Orsaken är att omvända DNS-frågor bara är begränsade till samma virtuella nätverk. 


![Flera lösningar för virtuella nätverk](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Scenario: Split-Horizon funktioner

I det här scenariot har du ett användnings fall där du vill realisera olika DNS-matchnings beteende beroende på var klienten finns (i Azure eller på Internet), för samma DNS-zon. Du kan till exempel ha en privat och offentlig version av programmet som har olika funktioner eller beteende, men du vill använda samma domän namn för båda versionerna. Det här scenariot kan realiseras med Azure DNS genom att skapa en offentlig DNS-zon och en privat zon med samma namn.

Följande diagram illustrerar det här scenariot. Du har ett virtuellt nätverk som har två virtuella datorer (VNETA-VM1 och VNETA-VM2) som har både privata IP-adresser och offentliga IP-allokerade. Du skapar en offentlig DNS-zon med namnet contoso.com och registrerar de offentliga IP-adresserna för de virtuella datorerna som DNS-poster i zonen. Du skapar också en Privat DNS zon som också kallas contoso.com som anger ett som registrerings virtuella nätverk. Azure registrerar automatiskt de virtuella datorerna som en post i den privata zonen, vilket leder till sina privata IP-adresser.

Nu när en Internet-klient utfärdar en DNS-fråga för att söka efter VNETA-VM1.contoso.com, returnerar Azure den offentliga IP-posten från den offentliga zonen. Om samma DNS-fråga utfärdas från en annan virtuell dator (till exempel: VNETA-VM2) i samma virtuella nätverk A, returnerar Azure den privata IP-posten från den privata zonen. 

![Dela Brian-lösning](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Nästa steg
Mer information om privata DNS-zoner finns i [Using Azure DNS for private domains](private-dns-overview.md) (Använda Azure DNS för privata domäner).

Lär dig hur du [skapar en privat DNS-zon](./private-dns-getstarted-powershell.md) i Azure DNS.

Lär dig mer om DNS-zoner och poster genom att besöka: [Översikt över DNS-zoner och-poster](dns-zones-records.md).

Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.

