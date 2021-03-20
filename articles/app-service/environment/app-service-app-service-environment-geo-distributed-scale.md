---
title: Geo-distribuerad skala
description: Lär dig att horisontellt Skala appar med hjälp av Geo-distribution med Traffic Manager-och App Service miljöer.
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18, references_regions
ms.openlocfilehash: 004b32118521f72c5b59ad7bab2d4e41244b85c4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85833612"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Geodistribuerad skalning med App Service Environment
## <a name="overview"></a>Översikt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Program scenarier som kräver mycket hög skalning kan överstiga beräknings resurs kapaciteten som är tillgänglig för en enskild distribution av en app.  Röstnings program, idrotts evenemang och televised underhållnings händelser är exempel på scenarier som kräver mycket hög skalning. Hög skalnings kraven kan uppfyllas genom att skala ut appar horisontellt. För att hantera extrema belastnings krav kan många distributioner av appar göras inom en och samma region och i flera regioner.

App Service miljöer är en perfekt plattform för horisontell skalbarhet. När du har valt en App Service-miljön-konfiguration som har stöd för en känd begär ande frekvens kan utvecklare distribuera ytterligare App Service miljöer i "cookie-kniv" för att uppnå önskad högsta belastnings kapacitet.

Anta till exempel att en app som körs på en App Service-miljön-konfiguration har testats för att hantera 20 000-begäranden per sekund (RPS).  Om den önskade belastnings belastningen är 100 000 RPS kan fem (5) App Service miljöer skapas och konfigureras för att säkerställa att programmet kan hantera den maximala beräknade belastningen.

Eftersom kunderna vanligt vis använder appar med en anpassad (eller anpassad) domän, behöver utvecklare ett sätt att distribuera app-begäranden över alla App Service-miljön instanser.  Ett bra sätt att uppnå det här målet är att lösa den anpassade domänen med hjälp av en [Azure Traffic Manager-profil][AzureTrafficManagerProfile].  Traffic Manager profilen kan konfigureras så att den pekar på alla enskilda App Service miljöer.  Traffic Manager hanterar automatiskt distribution av kunder i alla App Service miljöer, baserat på inställningarna för belastnings utjämning i Traffic Manager profilen.  Den här metoden fungerar oavsett om alla App Services miljöer distribueras i en enda Azure-region eller distribueras över hela världen över flera Azure-regioner.

Dessutom, eftersom kunder kommer åt appar via anpassad-domänen, är kunderna inte medvetna om antalet App Service miljöer som kör en app.  Det innebär att utvecklare snabbt och enkelt kan lägga till och ta bort App Service miljöer baserat på observerad trafik belastning.

Det konceptuella diagrammet nedan visar en app som är vågrätt skalad över tre App Service miljöer inom en enda region.

![Konceptuell arkitektur][ConceptualArchitecture] 

Resten av det här avsnittet vägleder dig genom stegen i att konfigurera en distribuerad topologi för exempel appen med hjälp av flera App Service miljöer.

## <a name="planning-the-topology"></a>Planera topologin
Innan du skapar en distribuerad app kan det vara bra att ha några delar av informationen i förväg.

* **Anpassad domän för appen:**  Vad är det anpassade domän namnet som kunder kommer att använda för att få åtkomst till appen?  För exempel appen är det anpassade domän namnet `www.scalableasedemo.com` .
* **Traffic Manager domän:** Välj ett domän namn när du skapar en [Azure Traffic Manager-profil][AzureTrafficManagerProfile].  Det här namnet kombineras med *trafficmanager.net* -suffixet för att registrera en domän post som hanteras av Traffic Manager.  För exempel appen är det valda namnet *skalbart-ASE-demo*.  Därför är det fullständiga domän namnet som hanteras av Traffic Manager *Scalable-ASE-demo.trafficmanager.net*.
* **Strategi för skalning av appens avtryck:**  Kommer programmet att placeras i flera App Service miljöer i samma region?  Flera regioner?  Vill du blanda och matcha båda metoderna?  Basera beslutet på förväntningarna där kund trafiken kommer fram och hur väl resten av en Apps stödjande backend-infrastruktur kan skalas.  Till exempel, med ett tillstånds löst program på 100%, kan en app skalas enorma med en kombination av många App Service miljöer i varje Azure-region, multiplicerat med App Service miljöer som distribuerats över flera Azure-regioner.  Med 15 globala Azure-regioner som är tillgängliga för att välja bland kan kunder verkligen bygga en världs omfattande storskalig program miljö.  För den exempel app som används för den här artikeln skapades tre App Service miljöer i en enda Azure-region (södra centrala USA).
* **Namngivnings konvention för App Service miljöer:**  Varje App Service-miljön kräver ett unikt namn.  Utöver en eller två App Service miljöer är det bra att ha en namngivnings konvention som hjälper dig att identifiera varje App Service-miljön.  För exempel appen användes en enkel namngivnings konvention.  Namnen på de tre App Services miljöerna är *fe1ase*, *fe2ase* och *fe3ase*.
* **Namngivnings konvention för apparna:**  Eftersom flera instanser av appen kommer att distribueras krävs ett namn för varje instans av den distribuerade appen.  En lite välkänd men bekväm funktion i App Service miljöer är att samma app-namn kan användas i flera App Service miljöer.  Eftersom varje App Service-miljön har ett unikt domänsuffix, kan utvecklare välja att återanvända exakt samma app-namn i varje miljö.  En utvecklare kan till exempel ha appar som heter enligt följande:  *MyApp.foo1.p.azurewebsites.net*, *MyApp.foo2.p.azurewebsites.net*, *MyApp.foo3.p.azurewebsites.net* osv.  För exempel appen har dock varje App-instans också ett unikt namn.  De instans namn som används för appar är *webfrontend1*, *webfrontend2* och *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Konfigurera Traffic Manager profilen
När flera instanser av en app har distribuerats i flera App Service-miljöer kan de enskilda app-instanserna registreras med Traffic Manager.  För exempel appen behövs en Traffic Manager profil för *Scalable-ASE-demo.trafficmanager.net* som kan dirigera kunder till någon av följande distribuerade App-instanser:

* **webfrontend1.fe1ase.p.azurewebsites.net:**  En instans av exempel programmet som distribuerats på den första App Service-miljön.
* **webfrontend2.fe2ase.p.azurewebsites.net:**  En instans av exempel programmet som distribuerats på den andra App Service-miljön.
* **webfrontend3.fe3ase.p.azurewebsites.net:**  En instans av exempel programmet som distribuerats på den tredje App Service-miljön.

Det enklaste sättet att registrera flera Azure App Service-slutpunkter, som körs i **samma** Azure-region, är med PowerShell- [Azure Resource Manager Traffic Manager Support][ARMTrafficManager].  

Det första steget är att skapa en Azure Traffic Manager-profil.  Koden nedan visar hur profilen skapades för exempel appen:

```azurepowershell-interactive
$profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

Observera att *RelativeDnsName* -parametern har angetts till *Scalable-ASE-demo*.  Den här parametern gör att domän namnet *Scalable-ASE-demo.trafficmanager.net* skapas och associeras med en Traffic Manager-profil.

Parametern *TrafficRoutingMethod* definierar den princip för belastnings utjämning som Traffic Manager använder för att avgöra hur kund belastningen ska spridas över alla tillgängliga slut punkter.  I det här exemplet valdes den *viktade* metoden.  På grund av detta val kommer kund förfrågningar att spridas över alla registrerade program slut punkter baserat på de relativa vikter som är associerade med varje slut punkt. 

När profilen har skapats läggs varje App-instans till i profilen som en intern Azure-slutpunkt.  Följande kod hämtar en referens till varje frontend-webbapp. Den lägger sedan till varje app som en Traffic Manager slut punkt via *TargetResourceId* -parametern.

```azurepowershell-interactive
$webapp1 = Get-AzWebApp -Name webfrontend1
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

$webapp2 = Get-AzWebApp -Name webfrontend2
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

$webapp3 = Get-AzWebApp -Name webfrontend3
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile
```

Observera hur det finns ett anrop till *Add-AzureTrafficManagerEndpointConfig* för varje enskild app-instans.  Parametern *TargetResourceId* i varje PowerShell-kommando refererar till en av de tre distribuerade app-instanserna.  Den Traffic Manager profilen kommer att sprida belastningen över alla tre slut punkter som registrerats i profilen.

Alla tre slut punkterna använder samma värde (10) för *viktnings* parametern.  I den här situationen uppstår Traffic Manager att sprida kund förfrågningar över alla tre App-instanser relativt jämnt. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Att peka appens anpassade domän på Traffic Manager domän
Det sista steget som krävs är att peka den anpassade domänen för appen på Traffic Manager domän.  För exempel appen, peka `www.scalableasedemo.com` på `scalable-ase-demo.trafficmanager.net` .  Slutför det här steget med den domän registrator som hanterar den anpassade domänen.  

Med hjälp av din registrators domän hanterings verktyg måste du skapa CNAME-poster som pekar på den anpassade domänen på den Traffic Manager domänen.  I bilden nedan visas ett exempel på hur denna CNAME-konfiguration ser ut så här:

![CNAME för anpassad domän][CNAMEforCustomDomain] 

Även om det inte beskrivs i det här avsnittet måste du komma ihåg att varje enskild app-instans måste ha den anpassade domänen registrerad.  Om en begäran till exempel gör den till en app-instans och programmet inte har registrerat den anpassade domänen med appen, kommer begäran att Miss klar.

I det här exemplet är den anpassade domänen `www.scalableasedemo.com` och varje program instans har den anpassade domän som är kopplad till den.

![Anpassad domän][CustomDomain] 

En sammanfattning för att registrera en anpassad domän med Azure App Service appar finns i [Registrera anpassade domäner][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Testar distribuerad topologi
Slut resultatet av Traffic Manager och DNS-konfigurationen är att begär Anden för `www.scalableasedemo.com` kommer att flöda genom följande sekvens:

1. En webbläsare eller enhet gör en DNS-sökning för `www.scalableasedemo.com`
2. CNAME-posten på domän registratorn gör att DNS-sökningen omdirigeras till Azure Traffic Manager.
3. En DNS-sökning görs för *Scalable-ASE-demo.trafficmanager.net* gentemot en av Azure Traffic Manager DNS-servrarna.
4. Baserat på principen för belastnings utjämning som angavs tidigare i *TrafficRoutingMethod* -parametern, väljer Traffic Manager en av de konfigurerade slut punkterna. Den returnerar sedan det fullständiga domän namnet för den slut punkten till webbläsaren eller enheten.
5. Eftersom FQDN för slut punkten är URL: en för en app-instans som körs på en App Service-miljön ber webbläsaren eller enheten en Microsoft Azure DNS-server för att matcha FQDN-namnet till en IP-adress. 
6. Webbläsaren eller enheten skickar HTTP/S-begäran till IP-adressen.  
7. Begäran kommer till en av de App-instanser som körs på någon av App Services miljöerna.

I konsol bilden nedan visas en DNS-sökning för exempel appens anpassade domän. Den har lösts till en app-instans som körs på ett av de tre exempel App Service miljöer (i det här fallet den andra av de tre App Service-miljöerna):

![DNS-sökning][DNSLookup] 

## <a name="additional-links-and-information"></a>Ytterligare länkar och information
Dokumentation om PowerShell- [Azure Resource Manager Traffic Manager Support][ARMTrafficManager].  

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
