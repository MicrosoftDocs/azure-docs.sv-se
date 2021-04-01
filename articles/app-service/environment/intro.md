---
title: Introduktion
description: Lär dig hur Azure App Service miljöer hjälper dig att skala, skydda och optimera dina appar i en helt isolerad och dedikerad miljö.
author: ccompy
ms.assetid: 3c7eaefa-1850-4643-8540-428e8982b7cb
ms.topic: overview
ms.date: 04/19/2018
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 6fff19498e9ca70991d3190165df70a48136f502
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92631146"
---
# <a name="introduction-to-the-app-service-environments"></a>Introduktion till Azure App Service-miljöer #
## <a name="overview"></a>Översikt ##

Azure App Service-miljön är en funktion i Azure App Service som ger en helt isolerad och dedikerad miljö där du kan köra App Service-appar säkert på hög nivå. Den här funktionen kan vara värd för:

* Windows-webbappar
* Linux-webbappar 
* Docker-containrar
* Mobilappar
* Functions

App Service-miljöer (ASE) är lämpliga för programarbetsbelastningar som kräver:

* Mycket hög skala.
* Isolering och säker nätverksåtkomst.
* Hög minnesanvändning.

Kunderna kan skapa flera ASE-miljöer inom en enda Azure-region eller över flera Azure-regioner. Den här flexibiliteten gör ASE perfekt för horisontell skalning av tillstånds lösa program nivåer i stöd för arbets belastningar med hög begär Anden per sekund (RPS).

ASE värd program från endast en kund och gör det i någon av deras virtuella nätverk. Kunderna har detaljerad kontroll över inkommande och utgående programnätverkstrafik. Programmen kan upprätta säkra anslutningar med hög hastighet över VPN till lokala företagsresurser.

* ASE levereras med sin egen prisnivå. Läs mer om hur [isolerade erbjudanden](https://channel9.msdn.com/Shows/Azure-Friday/Security-and-Horsepower-with-App-Service-The-New-Isolated-Offering?term=app%20service%20environment) främjar storskalighet och säkerhet.
* [App Service Environment v2](https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud?term=app%20service%20environment) ger en omgivning för att skydda dina program i ett undernät i nätverket och ger en privat distribution av Azure App Service.
* Flera ASE-miljöer kan användas för att skala vågrätt. Om du vill ha mer information kan du läsa om [hur man konfigurerar ett geodistribuerat appfotavtryck](app-service-app-service-environment-geo-distributed-scale.md).
* ASE-miljöer kan användas för att konfigurera en säkerhetsarkitektur, vilket visas i AzureCon Deep Dive (Djupgående om AzureCon). Om du vill se hur säkerhetsarkitekturen som visades i AzureCon Deep Dive (Djupgående om AzureCon) har konfigurerats kan du läsa [artikeln om att implementera en arkitektur med flernivåsäkerhet](app-service-app-service-environment-layered-security.md) med App Service-miljöer.
* Appar som körs i ASE kan ha sin egen åtkomst begränsad av överordnade enheter, t.ex. brandväggar för webbaserade program (WAF). Mer information finns i [Brandvägg för webbaserade program (WAF)][AppGW].
* App Service miljöer kan distribueras till Tillgänglighetszoner (AZ) med zon fäst.  Mer information finns i [App Service-miljön support för Tillgänglighetszoner][ASEAZ] .

## <a name="dedicated-environment"></a>Dedikerad miljö ##

En ASE är strikt reserverad för en prenumeration och kan innehålla 100 App Service-planer med varsin instans. Omfånget kan sträcka sig över 100 instanser i en enda App Service-plan till 100 App Service-planer med varsin instans, och allt däremellan.

En ASE-miljö består av klientdelar och arbetare. Klientdelarna ansvarar för HTTP/HTTPS-avslutning och automatisk belastningsutjämning av appförfrågningar i en ASE-miljö. Klientdelarna läggs till automatiskt när App Service-planerna i ASE skalas ut.

Arbetare är roller som är värdar för kundappar. Arbetare finns i tre fasta storlekar:

* En vCPU/3,5 GB RAM
* Två vCPU/7 GB RAM
* Fyra vCPU/14 GB RAM

Kunderna behöver inte hantera klientdelar och arbetare. All infrastruktur läggs till automatiskt när kunderna skalar ut sina App Service-planer. Vartefter App Service-planer skapas eller skalas i en ASE-miljö kommer den infrastruktur som krävs att läggas till eller tas bort efter behov.

Det finns en fast månadskostnad för en ASE-miljö som betalar för infrastrukturen och som inte ändras med storleken på ASE-miljön. Dessutom finns en kostnad per vCPU för App Service-planen. Alla appar som har en ASE som värd finns i SKU med isolerad prissättning. Mer information om priser för en ASE-miljö finns på sidan [Prissättning för App Service][Pricing], där du även kan se de tillgängliga alternativen för ASE-miljöer.

## <a name="virtual-network-support"></a>Stöd för virtuellt nätverk ##

ASE-funktionen är en distribution av Azure App Service direkt till kundens Azure Resource Manager virtuella nätverk. Mer information om virtuella Azure-nätverk finns i [Azure virtual networks FAQ](../../virtual-network/virtual-networks-faq.md) (Vanliga frågor och svar om virtuella Azure-nätverk). En ASE-miljö finns alltid i ett virtuellt nätverk och, mer exakt, i ett undernät till ett virtuellt nätverk. Du kan använda säkerhetsfunktionerna för virtuella nätverk för att styra inkommande och utgående nätverkskommunikation för apparna.

En ASE-miljö kan vara antingen Internetuppkopplad med en offentlig IP-adress eller intern med bara en adress för en intern lastbalanserare (ILB) i Azure.

[Nätverkssäkerhetsgrupper][NSGs] begränsar inkommande kommunikation till undernätet där en ASE-miljö finns. Du kan använda nätverkssäkerhetsgrupper för att köra appar bakom överordnade enheter och tjänster, t.ex. WAF och SaaS-nätverksleverantörer.

Apparna måste ofta även komma åt företagsresurser, t.ex. interna databaser och webbtjänster. Om du distribuerar ASE-miljön i ett virtuellt nätverk som har en VPN-anslutning till det lokala nätverket kan apparna i ASE-miljön komma åt de lokala resurserna. Den här funktionen gäller oavsett om VPN är en VPN för [plats-till-plats](../../vpn-gateway/vpn-gateway-multi-site.md) eller [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Mer information om hur ASE-miljöer fungerar med virtuella nätverk och lokala nätverk finns i [App Service Environment network considerations][ASENetwork] (Nätverksöverväganden för App Service Environment).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud/player]

## <a name="app-service-environment-v1"></a>App Service Environment v1 ##

App Service Environment finns i två versioner: ASEv1 och ASEv2. Informationen ovan baserades på ASEv2. Det här avsnittet visar skillnaderna mellan ASEv1 och ASEv2. 

I ASEv1 måste du hantera alla resurser manuellt. Det omfattar klientdelar, arbetare och IP-adresser som används för IP-baserad SSL. Innan du kan skala ut App Service-planen måste du först skala ut arbetarpoolen som du vill använda som värd.

ASEv1 använder en annan prissättningsmodell än ASEv2. I ASEv1 betalar du för varje allokerad vCPU. Det omfattar vCPU-enheter som används för klientdelar eller arbetare som inte är värdar för någon arbetsbelastning. I ASEv1 är den högsta skalstorleken för en ASE-miljö totalt 55 värdar. Det omfattar arbetare och klientdelar. En fördel med ASEv1 är att det går att distribuera i ett klassiskt virtuellt nätverk och ett virtuellt Resource Manager-nätverk. Mer information om ASEv1 finns i [App Service Environment v1 introduction][ASEv1Intro] (Introduktion till App Service Environment v1).

<!--Links-->
[App Service Environments v2]: https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud?term=app%20service%20environment
[Isolated offering]: https://channel9.msdn.com/Shows/Azure-Friday/Security-and-Horsepower-with-App-Service-The-New-Isolated-Offering?term=app%20service%20environment
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ASEAZ]: https://azure.github.io/AppService/2019/12/12/App-Service-Environment-Support-for-Availability-Zones.html