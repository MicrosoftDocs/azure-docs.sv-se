---
title: Geo-distribuerad skala
description: Lär dig hur du horisontellt skalar appar med hjälp av geo-distribution Traffic Manager och App Service miljöer.
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18, references_regions, devx-track-azurepowershell
ms.openlocfilehash: 215132888749a54996b3341e43ef8d91c101a460
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834308"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Geodistribuerad skalning med App Service Environment
## <a name="overview"></a>Översikt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Programscenarier som kräver mycket hög skala kan överskrida beräkningsresurskapaciteten som är tillgänglig för en enskild distribution av en app.  Röstningsprogram, sportevenemang och tv-sända underhållningsevenemang är exempel på scenarier som kräver mycket hög skala. Krav på hög skalning kan uppfyllas genom horisontell utskalning av appar. För att hantera extrema belastningskrav kan många appdistributioner göras inom en enda region och mellan regioner.

App Service miljöer är en perfekt plattform för horisontell utskalning. När utvecklare har valt en App Service-miljön-konfiguration som har stöd för en känd förfrågningsfrekvens kan de distribuera ytterligare App Service-miljöer i "cookie-läge" för att uppnå önskad kapacitet för hög belastning.

Anta till exempel att en app som körs på en App Service-miljön-konfiguration har testats för att hantera 20 000 begäranden per sekund (RPS).  Om den önskade högsta belastningskapaciteten är 100 000 RPS kan fem (5) App Service-miljöer skapas och konfigureras för att säkerställa att programmet kan hantera den maximala projicerade belastningen.

Eftersom kunder vanligtvis använder appar med hjälp av en anpassad (eller anpassad) domän behöver utvecklare ett sätt att distribuera appbegäranden över alla App Service-miljön instanser.  Ett bra sätt att uppnå det här målet är att lösa den anpassade domänen med en [Azure Traffic Manager profil][AzureTrafficManagerProfile].  Profilen Traffic Manager kan konfigureras så att den pekar på alla enskilda App Service miljöer.  Traffic Manager hanterar automatiskt distributionen av kunder i alla App Service-miljöer, baserat på inställningarna för belastningsutjämning i Traffic Manager profilen.  Den här metoden fungerar oavsett om alla App Service-miljöer distribueras i en enda Azure-region eller distribueras över hela världen över flera Azure-regioner.

Eftersom kunderna dessutom har åtkomst till appar via en enkel domän är kunderna inte medvetna om hur många App Service miljöer som kör en app.  Därför kan utvecklare snabbt och enkelt lägga till och ta bort App Service miljöer baserat på observerad trafikbelastning.

Diagrammet nedan visar en app som skalats ut vågrätt över tre App Service miljöer inom en enda region.

![Konceptuell arkitektur][ConceptualArchitecture] 

Resten av det här avsnittet går igenom stegen för att konfigurera en distribuerad topologi för exempelappen med flera App Service miljöer.

## <a name="planning-the-topology"></a>Planera topologin
Innan du skapar ett distribuerat appfotavtryck hjälper det att ha lite information i förväg.

* **Anpassad domän för appen:**  Vilket är det anpassade domännamn som kunder kommer att använda för att få åtkomst till appen?  För exempelappen är det anpassade domännamnet `www.scalableasedemo.com` .
* **Traffic Manager domän:** Välj ett domännamn när du skapar en [Azure Traffic Manager profil][AzureTrafficManagerProfile].  Det här namnet kombineras med suffixet *trafficmanager.net* för att registrera en domänpost som hanteras av Traffic Manager.  För exempelappen är det valda namnet *scalable-ase-demo*.  Det innebär att det fullständiga domännamnet som hanteras av Traffic Manager är *scalable-ase-demo.trafficmanager.net*.
* **Strategi för att skala appens fotavtryck:**  Kommer programmets fotavtryck att distribueras över App Service miljöer i en enda region?  Flera regioner?  En blandning av båda metoderna?  Basera beslutet på förväntningar på var kundtrafiken kommer ifrån och hur väl resten av en app stöder backend-infrastrukturen kan skalas.  Med ett tillståndslöst program på 100 % kan en app till exempel skalas massivt med en kombination av många App Service-miljöer i varje Azure-region multiplicerat med App Service-miljöer som distribuerats i många Azure-regioner.  Med över 15 globala Azure-regioner tillgängliga att välja mellan kan kunderna verkligen skapa ett globalt programfotavtryck i hyperskala.  För exempelappen som används för den här artikeln har tre App Service-miljöer skapats i en enda Azure-region (USA, södra centrala).
* **Namngivningskonvention för App Service miljöer:**  Varje App Service-miljön kräver ett unikt namn.  Utöver en eller två App Service miljöer är det bra att ha en namngivningskonvention som hjälper dig att identifiera varje App Service-miljön.  För exempelappen användes en enkel namngivningskonvention.  Namnen på de tre App Service miljöerna *är fe1ase*, *fe2ase* och *fe3ase*.
* **Namngivningskonvention för apparna:**  Eftersom flera instanser av appen distribueras behövs ett namn för varje instans av den distribuerade appen.  En lite känd men praktisk funktion i App Service miljöer är att samma appnamn kan användas i flera App Service miljöer.  Eftersom varje App Service-miljön har ett unikt domänsuffix kan utvecklare välja att återanvända exakt samma appnamn i varje miljö.  En utvecklare kan till exempel ha appar med följande namn:  *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net* osv.  För exempelappen har dock varje appinstans också ett unikt namn.  Appinstansnamnen som används *är webfrontend1,* *webfrontend2* och *webfrontend3.*

## <a name="setting-up-the-traffic-manager-profile"></a>Konfigurera Traffic Manager profilen
När flera instanser av en app har distribuerats i flera App Service-miljöer kan de enskilda appinstanserna registreras med Traffic Manager.  För exempelappen behövs Traffic Manager profil för *att scalable-ase-demo.trafficmanager.net* kan dirigera kunder till någon av följande distribuerade appinstanser:

* **webfrontend1.fe1ase.p.azurewebsites.net:**  En instans av exempelappen som distribueras på den första App Service-miljön.
* **webfrontend2.fe2ase.p.azurewebsites.net:**  En instans av exempelappen som distribuerats på den andra App Service-miljön.
* **webfrontend3.fe3ase.p.azurewebsites.net:**  En instans av exempelappen som distribuerats på den tredje App Service-miljön.

Det enklaste sättet att registrera Azure App Service slutpunkter,  som körs i samma Azure-region, är med [PowerShell-Azure Resource Manager Traffic Manager stöd för][ARMTrafficManager].  

Det första steget är att skapa en Azure Traffic Manager profil.  Koden nedan visar hur profilen skapades för exempelappen:

```azurepowershell-interactive
$profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

Observera hur *parametern RelativeDnsName* har angetts *till scalable-ase-demo*.  Den här parametern gör att *domännamnet scalable-ase-demo.trafficmanager.net* skapas och associeras med en Traffic Manager profil.

Parametern *TrafficRoutingMethod* definierar belastningsutjämningsprincipen som Traffic Manager för att fastställa hur kundernas belastning ska spridas över alla tillgängliga slutpunkter.  I det här exemplet *valdes metoden Viktad.*  På grund av det här valet sprids kundbegäranden över alla registrerade programslutpunkter baserat på de relativa vikter som är associerade med varje slutpunkt. 

När profilen har skapats läggs varje appinstans till i profilen som en intern Azure-slutpunkt.  Följande kod hämtar en referens till varje frontend-webbapp. Den lägger sedan till varje app som Traffic Manager slutpunkt via *parametern TargetResourceId.*

```azurepowershell-interactive
$webapp1 = Get-AzWebApp -Name webfrontend1
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

$webapp2 = Get-AzWebApp -Name webfrontend2
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

$webapp3 = Get-AzWebApp -Name webfrontend3
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile
```

Observera att det finns ett anrop *till Add-AzureTrafficManagerEndpointConfig* för varje enskild appinstans.  Parametern *TargetResourceId* i varje PowerShell-kommando refererar till en av de tre distribuerade appinstanserna.  Den Traffic Manager profilen sprider belastningen över alla tre slutpunkter som registrerats i profilen.

Alla tre slutpunkterna använder samma värde (10) för *parametern Vikt.*  Den här situationen leder Traffic Manager att kundförfrågningar sprids över alla tre appinstanserna relativt jämnt. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Peka appens Custom Domain på Traffic Manager domänen
Det sista steget är att peka appens anpassade domän mot den Traffic Manager domänen.  För exempelappen pekar du `www.scalableasedemo.com` på `scalable-ase-demo.trafficmanager.net` .  Slutför det här steget med domänregistratorn som hanterar den anpassade domänen.  

Med hjälp av registratorns domänhanteringsverktyg måste en CNAME-post skapas som pekar den anpassade domänen Traffic Manager domän.  Bilden nedan visar ett exempel på hur den här CNAME-konfigurationen ser ut:

![CNAME för Custom Domain][CNAMEforCustomDomain] 

Även om det inte tas upp i det här avsnittet bör du komma ihåg att varje enskild appinstans måste ha den anpassade domänen registrerad med den också.  Annars misslyckas begäran om en begäran kommer till en appinstans och programmet inte har registrerat den anpassade domänen med appen.

I det här exemplet är den anpassade domänen `www.scalableasedemo.com` , och varje programinstans har den anpassade domän som är associerad med den.

![Anpassad domän][CustomDomain] 

En sammanfattning av hur du registrerar en anpassad domän med Azure App Service-appar finns i [Registrera anpassade domäner][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Testa den distribuerade topologin
Slutresultatet av konfigurationen Traffic Manager DNS är att begäranden för `www.scalableasedemo.com` flödar genom följande sekvens:

1. En webbläsare eller enhet gör en DNS-sökning för `www.scalableasedemo.com`
2. CNAME-posten hos domänregistratorn gör att DNS-sökning omdirigeras till Azure Traffic Manager.
3. En DNS-sökning görs för *att scalable-ase-demo.trafficmanager.net* mot en av de Azure Traffic Manager DNS-servrarna.
4. Baserat på den belastningsutjämningsprincip som angavs tidigare i parametern *TrafficRoutingMethod* väljer Traffic Manager en av de konfigurerade slutpunkterna. Den returnerar sedan FQDN för slutpunkten till webbläsaren eller enheten.
5. Eftersom FQDN för slutpunkten är webbadressen till en appinstans som körs på en App Service-miljön, kommer webbläsaren eller enheten att be en Microsoft Azure DNS-server att matcha FQDN till en IP-adress. 
6. Webbläsaren eller enheten skickar HTTP/S-begäran till IP-adressen.  
7. Begäran tas emot vid en av de appinstanser som körs på en av App Service miljöerna.

Konsolbilden nedan visar en DNS-sökning för exempelappens anpassade domän. Den matchar en appinstans som körs på någon av de tre App Service-miljöerna (i det här fallet den andra av de tre App Service miljöerna):

![DNS-sökning][DNSLookup] 

## <a name="additional-links-and-information"></a>Ytterligare länkar och information
Dokumentation om PowerShell-Azure Resource Manager Traffic Manager [stöd][ARMTrafficManager]för .  

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: ../../traffic-manager/traffic-manager-manage-profiles.md
[ARMTrafficManager]: ../../traffic-manager/traffic-manager-powershell-arm.md
[RegisterCustomDomain]: ../app-service-web-tutorial-custom-domain.md


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
