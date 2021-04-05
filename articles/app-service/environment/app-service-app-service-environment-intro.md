---
title: Introduktion till ASE v1
description: Läs mer om de App Service-miljön v1-funktionerna. Detta dokument tillhandahålls endast för kunder som använder den äldre v1-ASE.
author: stefsch
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 878cef39ade0487c03dba83c9e3631fe3c72088f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008640"
---
# <a name="introduction-to-app-service-environment-v1"></a>Introduktion till App Service-miljön v1

> [!NOTE]
> Den här artikeln gäller App Service-miljön v1.  Det finns en nyare version av App Service-miljön som är enklare att använda och som körs på en kraftfullare infrastruktur. Om du vill veta mer om den nya versionen börjar [du med introduktionen till App Service-miljön](intro.md).

## <a name="overview"></a>Översikt

Ett App Service-miljön är ett alternativ för en [Premium][PremiumTier] service-tjänst för [Azure App Service](../overview.md) som ger en fullständigt isolerad och dedikerad miljö för säker körning av Azure App Service appar i hög skala, inklusive Web Apps, Mobile Apps och API Apps.  

App Service miljöer är idealiska för program arbets belastningar som kräver:

* Mycket hög skala
* Isolering och säker nätverks åtkomst

Kunder kan skapa flera App Service miljöer i en enda Azure-region, samt i flera Azure-regioner.  Detta gör App Service miljöer som är idealiska för horisontell skalning av tillstånds lägre program nivåer i stöd för höga RPS-arbetsbelastningar.

App Service miljöer är isolerade för att bara köra en enskild kunds program och distribueras alltid till ett virtuellt nätverk.  Kunderna har detaljerad kontroll över både inkommande och utgående program nätverks trafik, och program kan upprätta snabba säkra anslutningar över virtuella nätverk till lokala företags resurser.

En översikt över hur App Service miljöer möjliggör hög skalbarhet och säker nätverks åtkomst finns i [AzureCon djupgående][AzureConDeepDive] i App Service miljöer!

En djup ökning på horisontell skalning med flera App Service miljöer finns i artikeln om hur du konfigurerar en [geo-distribuerad app][GeodistributedAppFootprint].

Om du vill se hur den säkerhets arkitektur som visas i AzureCon djupet har kon figurer ATS kan du läsa artikeln om att implementera en [lager säkerhets arkitektur](app-service-app-service-environment-layered-security.md) med App Service miljöer.

Appar som körs på App Service miljöer kan ha sin åtkomst-gated genom att överordnade enheter, till exempel brand väggar för webbaserade program (WAF).  I artikeln om hur du [konfigurerar en WAF för App Service miljöer](app-service-app-service-environment-web-application-firewall.md) täcker det här scenariot.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Dedikerade beräknings resurser

Alla beräknings resurser i en App Service-miljön är enbart dedikerade till en enda prenumeration och en App Service-miljön kan konfigureras med upp till 50 (50) beräknings resurser för exklusiv användning av ett enda program.

En App Service-miljön består av en beräknings resurs för en frontend-slutpunkt, samt en till tre resurspooler för Working-resurspool.

Frontend-poolen innehåller beräknings resurser som ansvarar för TLS-terminering och automatisk belastnings utjämning för program begär Anden inom en App Service-miljön.

Varje arbets grupp innehåller beräknings resurser som allokerats till [App Service-planer][AppServicePlan], vilket i sin tur innehåller en eller flera Azure App Service appar.  Eftersom det kan finnas upp till tre olika Worker-pooler i en App Service-miljön har du möjlighet att välja olika beräknings resurser för varje arbets grupp.  

Detta innebär till exempel att du kan skapa en arbets grupp med mindre kraftfulla beräknings resurser för App Service planer som är avsedda för utveckling eller testning av appar.  En andra (eller även tredje) arbets grupp kan använda mer kraftfulla beräknings resurser som är avsedda för App Service planer som kör produktions program.

Mer information om mängden av beräknings resurser som är tillgängliga för klient delen och arbets grupper finns i [så här konfigurerar du en app service-miljön][HowToConfigureanAppServiceEnvironment].  

Mer information om de tillgängliga beräknings resurs storlekarna som stöds i en App Service-miljön finns på sidan för [App Service prissättning][AppServicePricing] och granska de tillgängliga alternativen för App Service-miljöer på Premium pris nivån.

## <a name="virtual-network-support"></a>Virtual Network support

En App Service-miljön kan skapas **antingen** i ett Azure Resource Manager virtuellt nätverk **eller** i ett klassiskt virtuellt nätverk för distributions modellen ([Mer information om virtuella nätverk][MoreInfoOnVirtualNetworks]).  Eftersom en App Service-miljön alltid finns i ett virtuellt nätverk och mer exakt inom ett undernät i ett virtuellt nätverk, kan du utnyttja säkerhetsfunktionerna i virtuella nätverk för att kontrol lera både inkommande och utgående nätverkskommunikation.  

En App Service-miljön kan antingen vara Internet riktad mot en offentlig IP-adress, eller internt som är riktad mot en Azure Internal Load Balancer-adress (ILB).

Du kan använda [nätverks säkerhets grupper][NetworkSecurityGroups] för att begränsa inkommande nätverkskommunikation till under nätet där App Service-miljön finns.  På så sätt kan du köra appar bakom överordnade enheter och tjänster, till exempel brand väggar för webb program och SaaS-Providers.

Apparna måste ofta även komma åt företagsresurser, t.ex. interna databaser och webbtjänster.  En vanlig metod är att göra dessa slut punkter tillgängliga enbart för intern nätverks trafik som flödar i ett virtuellt Azure-nätverk.  När en App Service-miljön är ansluten till samma virtuella nätverk som de interna tjänsterna kan appar som körs i miljön komma åt dem, inklusive slut punkter som kan nås via [plats-till-plats][SiteToSite] -och [Azure ExpressRoute][ExpressRoute] -anslutningar.

Mer information om hur App Service-miljöer fungerar med virtuella nätverk och lokala nätverk finns i följande artiklar om [nätverks arkitektur][NetworkArchitectureOverview], kontroll av [inkommande trafik][ControllingInboundTraffic]och [säker anslutning till][SecurelyConnectingToBackends]Server delar. 

## <a name="getting-started"></a>Komma igång

Information om hur du kommer igång med App Service miljöer finns i [så här skapar du en app service-miljön][HowToCreateAnAppServiceEnvironment]

En översikt över App Service-miljön nätverks arkitekturen finns i artikeln [Översikt över nätverks arkitektur][NetworkArchitectureOverview] .

Mer information om hur du använder en App Service-miljön med ExpressRoute finns i följande artikel om [Express Route och App Service miljöer][NetworkConfigDetailsForExpressRoute].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: https://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: ../../virtual-network/virtual-networks-faq.md
[AppServicePlan]: ../overview-hosting-plans.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[LogicApps]: ../../logic-apps/logic-apps-overview.md
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  app-service-app-service-environment-geo-distributed-scale.md
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-multi-site.md
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  app-service-web-configure-an-app-service-environment.md
[ControllingInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SecurelyConnectingToBackends]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NetworkArchitectureOverview]:  app-service-app-service-environment-network-architecture-overview.md
[NetworkConfigDetailsForExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->