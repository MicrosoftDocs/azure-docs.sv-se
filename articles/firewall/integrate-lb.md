---
title: Integrera Azure Firewall med Azure Standard Load Balancer
description: Du kan integrera en Azure Firewall i ett virtuellt nätverk med en Azure Standard Load Balancer (antingen offentlig eller intern).
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 04/14/2021
ms.author: victorh
ms.openlocfilehash: e14a8afe27fc9dd9ca40730dd7e681c3093e0b50
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505912"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Integrera Azure Firewall med Azure Standard Load Balancer

Du kan integrera en Azure Firewall i ett virtuellt nätverk med en Azure Standard Load Balancer (antingen offentlig eller intern). 

Den rekommenderade designen är att integrera en intern lastbalanserare med Azure Firewall eftersom det är en mycket enklare design. Du kan använda en offentlig lastbalanserare om du redan har en sådan distribuerad och vill behålla den. Du måste dock vara medveten om ett problem med asymmetrisk routning som kan få scenariot med den offentliga lastbalanseraren att sluta fungera.

Mer information om Azure Load Balancer finns i [Vad är Azure Load Balancer?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Offentlig lastbalanserare

Med en offentlig lastbalanserare distribueras lastbalanseraren med en offentlig IP-adress för frontend.

### <a name="asymmetric-routing"></a>Asymmetrisk routning

Asymmetrisk routning är när ett paket tar en väg till målet och tar en annan väg när det återgår till källan. Det här problemet uppstår när ett undernät har en standardväg som går till brandväggens privata IP-adress och du använder en offentlig lastbalanserare. I det här fallet tas den inkommande lastbalanseringstrafiken emot via dess offentliga IP-adress, men returvägen går genom brandväggens privata IP-adress. Eftersom brandväggen är tillståndsfull ignorerar den det returnerade paketet eftersom brandväggen inte känner till en sådan upprättad session.

### <a name="fix-the-routing-issue"></a>Åtgärda routningsproblemet

När du distribuerar en Azure Firewall till ett undernät är ett steg att skapa en standardväg för undernätet som dirigerar paket genom brandväggens privata IP-adress som finns i AzureFirewallSubnet. Mer information finns i [Självstudie: Distribuera och konfigurera Azure Firewall med hjälp av Azure Portal](tutorial-firewall-deploy-portal.md#create-a-default-route).

När du introducerar brandväggen i ditt lastbalanseringsscenario vill du att internettrafiken ska komma in genom brandväggens offentliga IP-adress. Därifrån tillämpar brandväggen sina brandväggsregler och NAT-paketen till lastbalanserings offentliga IP-adress. Det är här som problemet uppstår. Paket tas emot på brandväggens offentliga IP-adress, men återgår till brandväggen via den privata IP-adressen (med standardvägen).
Undvik det här problemet genom att skapa ytterligare en värdväg för brandväggens offentliga IP-adress. Paket som går till brandväggens offentliga IP-adress dirigeras via Internet. På så sätt undviker du att ta standardvägen till brandväggens privata IP-adress.

![Asymmetrisk routning](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>Exempel på vägtabell

Följande vägar är till exempel för en brandvägg på den offentliga IP-adressen 20.185.97.136 och den privata IP-adressen 10.0.1.4.

> [!div class="mx-imgBorder"]
> ![Routningstabell](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>EXEMPEL på NAT-regel

I följande exempel översätter en NAT-regel RDP-trafik till brandväggen på 20.185.97.136 till lastbalanseraren på 20.42.98.220:

> [!div class="mx-imgBorder"]
> ![NAT-regel](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>Hälsotillståndsavsökningar

Kom ihåg att du måste ha en webbtjänst som körs på värdarna i lastbalanseringspoolen om du använder TCP-hälsoavsökningar till port 80 eller HTTP/HTTPS-avsökningar.

## <a name="internal-load-balancer"></a>Intern lastbalanserare

Med en intern lastbalanserare distribueras lastbalanseraren med en privat IP-adress för frontend.

Det finns inget problem med asymmetrisk routning i det här scenariot. Inkommande paket kommer till brandväggens offentliga IP-adress, översätts till lastbalanseringsens privata IP-adress och återgår sedan till brandväggens privata IP-adress med samma retursökväg.

Du kan därför distribuera det här scenariot på ett liknande sätt som i scenariot med en offentlig lastbalanserare, men utan att brandväggens värdväg för offentliga IP-adresser behövs.

De virtuella datorerna i serverpoolen kan ha utgående Internetanslutning via Azure Firewall. Konfigurera en användardefinierad väg i den virtuella datorns undernät med brandväggen som nästa hopp.


## <a name="additional-security"></a>Ytterligare säkerhet

Om du vill förbättra säkerheten ytterligare i ditt belastningsutjämnade scenario kan du använda nätverkssäkerhetsgrupper (NSG:er).

Du kan till exempel skapa en NSG i serverundernätet där de belastningsutjämnade virtuella datorerna finns. Tillåt inkommande trafik från brandväggens IP-adress/port.

![Nätverkssäkerhetsgrupp](media/integrate-lb/nsg-01.png)

Mer information om NSG:er finns i [Säkerhetsgrupper.](../virtual-network/network-security-groups-overview.md)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur [du distribuerar och konfigurerar en Azure Firewall](tutorial-firewall-deploy-portal.md).