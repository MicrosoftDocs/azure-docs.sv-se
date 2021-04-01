---
title: Nätverks arkitektur v1
description: Arkitektur översikt över nätverk sto pol Ogin i App Service miljöer. Detta dokument tillhandahålls endast för kunder som använder den äldre v1-ASE.
author: stefsch
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: b1b866f3be789c59eea38c5c22b5557d557440be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84701821"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Översikt över nätverks arkitektur i App Service miljöer
App Service miljöer skapas alltid i ett undernät för ett [virtuellt nätverk][virtualnetwork] – appar som körs i en app service-miljön kan kommunicera med privata slut punkter i samma virtuella nätverk sto pol Ogin.  Eftersom kunder kan låsa delar av sin virtuella nätverks infrastruktur är det viktigt att förstå de typer av nätverks kommunikations flöden som sker med en App Service-miljön.

## <a name="general-network-flow"></a>Allmänt nätverks flöde
När en App Service-miljön (ASE) använder en offentlig virtuell IP-adress (VIP) för appar kommer all inkommande trafik att tas emot på den offentliga VIP.  Detta inkluderar HTTP-och HTTPS-trafik för appar, samt annan trafik för FTP, fjärrfelsökning och hantering av Azure-åtgärder.  En fullständig lista över de angivna portarna (både obligatoriska och valfria) som är tillgängliga i offentlig VIP finns i artikeln om att [kontrol lera inkommande trafik][controllinginboundtraffic] till en app service-miljön. 

App Service miljöer har även stöd för att köra appar som endast är kopplade till en intern adress för virtuella nätverk, även kallade en ILB (intern belastningsutjämnare).  På en ILB aktive rad ASE, HTTP-och HTTPS-trafik för appar samt fjärrfelsöknings anrop, kommer du till ILB-adressen.  För de flesta vanliga ILB-ASE-konfigurationer kommer FTP/FTPS-trafik också att komma till ILB-adressen.  Azure-hanterings åtgärder kommer dock fortfarande att flöda till portarna 454/455 på den offentliga VIP: en för en ILB-aktiverad ASE.

Diagrammet nedan visar en översikt över de olika inkommande och utgående nätverks flödena för en App Service-miljön där apparna är bundna till en offentlig virtuell IP-adress:

![Allmänna nätverks flöden][GeneralNetworkFlows]

En App Service-miljön kan kommunicera med en rad olika privata kund slut punkter.  Appar som körs i App Service-miljön kan till exempel ansluta till databas servrar som körs på virtuella IaaS-datorer i samma virtuella nätverk sto pol Ogin.

> [!IMPORTANT]
> När du tittar på nätverks diagrammet distribueras "andra beräknings resurser" i ett annat undernät än App Service-miljön. Om du distribuerar resurser i samma undernät med ASE blockeras anslutningen från ASE till dessa resurser (förutom för en bestämd ASE routning). Distribuera till ett annat undernät i stället (i samma VNET). App Service-miljön kommer sedan att kunna ansluta. Ingen ytterligare konfiguration krävs.
> 
> 

App Service miljöer kommunicerar också med SQL DB och Azure Storage resurser som krävs för att hantera och använda en App Service-miljön.  Några av de SQL-och lagrings resurser som en App Service-miljön kommunicerar med finns i samma region som App Service-miljön, medan andra finns i Azure-fjärrregionerna.  Det innebär att utgående anslutning till Internet alltid krävs för att en App Service-miljön ska fungera korrekt. 

Eftersom en App Service-miljön distribueras i ett undernät kan nätverks säkerhets grupper användas för att styra inkommande trafik till under nätet.  Mer information om hur du styr inkommande trafik till en App Service-miljön finns i följande [artikel][controllinginboundtraffic].

Mer information om hur du tillåter utgående Internet anslutning från en App Service-miljön finns i följande artikel om hur du arbetar med [Express Route][ExpressRoute].  Samma metod som beskrivs i artikeln gäller när du arbetar med plats-till-plats-anslutning och använder Tvingad tunnel trafik.

## <a name="outbound-network-addresses"></a>Utgående nätverks adresser
När en App Service-miljön gör utgående samtal är en IP-adress alltid kopplad till utgående samtal.  Vilken speciell IP-adress som används beror på om slut punkten som anropas finns i den virtuella nätverk sto pol Ogin eller utanför den virtuella nätverk sto pol Ogin.

Om slut punkten som anropas ligger **utanför** den virtuella nätverk sto pol Ogin är den utgående adressen (aka utgående NAT-adress) som används den offentliga VIP för App Service-miljön.  Adressen finns i användar gränssnittet för portalen för App Service-miljön i egenskaper-bladet.

![Utgående IP-adress][OutboundIPAddress]

Den här adressen kan också fastställas för ASE som bara har en offentlig VIP genom att skapa en app i App Service-miljön och sedan utföra ett *nslookup* på appens adress. Den resulterande IP-adressen är både den offentliga VIP-adressen och App Service-miljöns utgående NAT-adress.

Om den slut punkt som anropas finns **inuti** den virtuella nätverk sto pol Ogin blir den utgående adressen för den anropande appen den interna IP-adressen för den enskilda beräknings resursen som kör appen.  Det finns dock ingen permanent mappning av virtuella nätverk interna IP-adresser till appar.  Appar kan förflytta sig över olika beräknings resurser och poolen med tillgängliga beräknings resurser i en App Service-miljön kan ändras på grund av skalnings åtgärder.

Men eftersom en App Service-miljön alltid finns i ett undernät garanterar du att den interna IP-adressen för en beräknings resurs som kör en app alltid ligger inom under nätets CIDR-intervall.  Det innebär att när detaljerade ACL: er eller nätverks säkerhets grupper används för att skydda åtkomsten till andra slut punkter inom det virtuella nätverket, måste under näts intervallet som innehåller App Service-miljön beviljas åtkomst.

Följande diagram visar dessa begrepp i mer detalj:

![Utgående nätverks adresser][OutboundNetworkAddresses]

I diagrammet ovan:

* Eftersom den offentliga VIP: en för App Service-miljön är 192.23.1.2, det vill säga den utgående IP-adressen som används vid anrop till "Internet"-slut punkter.
* CIDR-intervallet för det innehåll ande under nätet för App Service-miljön är 10.0.1.0/26.  Andra slut punkter inom samma virtuella nätverks infrastruktur kommer att se anrop från appar som härstammar någonstans i det här adress intervallet.

## <a name="calls-between-app-service-environments"></a>Anrop mellan App Service miljöer
Ett mer komplext scenario kan uppstå om du distribuerar flera App Service miljöer i samma virtuella nätverk och gör utgående anrop från ett App Service-miljön till en annan App Service-miljön.  Dessa typer av kors App Service-miljöns anrop behandlas också som "Internet-anrop".

I följande diagram visas ett exempel på en lager arkitektur med appar på en App Service-miljön (t. ex. "Front dörr"-webbappar) som anropar appar på en andra App Service-miljön (t. ex. interna Server-API-appar som inte är avsedda att vara tillgängliga från Internet). 

![Anrop mellan App Service miljöer][CallsBetweenAppServiceEnvironments] 

I exemplet ovan har App Service-miljön "ASE One" en utgående IP-adress till 192.23.1.2.  Om en app som körs på den här App Service-miljön gör ett utgående anrop till en app som körs på en andra App Service-miljön ("ASE två") som finns i samma virtuella nätverk, behandlas det utgående anropet som ett "Internet-anrop".  Det innebär att nätverks trafiken som kommer till den andra App Service-miljön visas som från 192.23.1.2 (dvs. inte under nätets adress intervall för det första App Service-miljön).

Även om anrop mellan olika App Service miljöer behandlas som "Internet"-anrop, när båda App Service miljöer finns i samma Azure-region, kommer nätverks trafiken att finnas kvar i det regionala Azure-nätverket och kommer inte att flöda över det offentliga Internet.  Det innebär att du kan använda en nätverks säkerhets grupp i under nätet för den andra App Service-miljön för att bara tillåta inkommande anrop från den första App Service-miljön (vars utgående IP-adress är 192.23.1.2) och därigenom säkerställa säker kommunikation mellan App Service miljöer.

## <a name="additional-links-and-information"></a>Ytterligare länkar och information
Information om inkommande portar som används av App Service miljöer och att använda nätverks säkerhets grupper för att kontrol lera inkommande trafik finns [här][controllinginboundtraffic].

Information om hur du använder användardefinierade vägar för att bevilja utgående Internet åtkomst till App Service miljöer finns i den här [artikeln][ExpressRoute]. 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

