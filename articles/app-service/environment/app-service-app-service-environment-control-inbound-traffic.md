---
title: Kontrol lera inkommande trafik v1
description: Lär dig hur du kontrollerar inkommande trafik till en App Service-miljön. Detta dokument tillhandahålls endast för kunder som använder den äldre v1-ASE.
author: ccompy
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: fe9326ea9ebd5afe981b7ba6c34b1a5d51e084b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88962068"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Så här styr du inkommande trafik till en App Service-miljön
## <a name="overview"></a>Översikt
En App Service-miljön kan skapas **antingen** i ett Azure Resource Manager virtuellt nätverk **eller** i ett klassiskt [virtuellt nätverk][virtualnetwork]för distributions modellen.  Ett nytt virtuellt nätverk och nytt undernät kan definieras när en App Service-miljön skapas. I stället kan en App Service-miljön skapas i ett befintligt virtuellt nätverk och ett redan befintligt undernät.  Från och med juni 2016 kan ASE också distribueras till virtuella nätverk som använder antingen offentliga adress intervall eller RFC1918 adress utrymmen (privata adresser).  Mer information finns i [så här skapar du en app service-miljön][HowToCreateAnAppServiceEnvironment].

Skapa alltid en App Service-miljön inom ett undernät. Ett undernät ger en nätverks gränser som kan användas för att låsa inkommande trafik bakom överordnade enheter och tjänster. Den här installationen tillåter endast vissa överordnade IP-adresser för att godkänna HTTP-och HTTPS-trafik.

Inkommande och utgående nätverks trafik i ett undernät styrs med en [nätverks säkerhets grupp][NetworkSecurityGroups]. Om du vill kontrol lera inkommande trafik skapar du nätverks säkerhets regler i en nätverks säkerhets grupp. Tilldela sedan nätverks säkerhets gruppen det undernät som innehåller App Service-miljön.

När du tilldelar en nätverks säkerhets grupp till ett undernät tillåts eller blockeras inkommande trafik till appar i App Service-miljön baserat på reglerna för att tillåta och neka som definierats i nätverks säkerhets gruppen.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Inkommande nätverks portar som används i en App Service-miljön
Innan du låser inkommande nätverks trafik med en nätverks säkerhets grupp, känner du till de obligatoriska och valfria nätverks portarna som används av en App Service-miljön.  Oavsiktlig stängning av trafik till vissa portar kan leda till förlust av funktioner i en App Service-miljön.

Följande lista innehåller de portar som används av en App Service-miljön. Alla portar är **TCP**, om inget annat anges:

* 454:  **obligatorisk port** som används av Azure-infrastrukturen för att hantera och underhålla App Service miljöer via TLS.  Blockera inte trafik till den här porten.  Den här porten är alltid kopplad till den offentliga VIP: en för en ASE.
* 455:  **obligatorisk port** som används av Azure-infrastrukturen för att hantera och underhålla App Service miljöer via TLS.  Blockera inte trafik till den här porten.  Den här porten är alltid kopplad till den offentliga VIP: en för en ASE.
* 80: standard porten för inkommande HTTP-trafik till appar som körs i App Service planer i en App Service-miljön.  På en ILB-aktiverad ASE är den här porten kopplad till ILB-adressen för ASE.
* 443: standard porten för inkommande TLS-trafik till appar som körs i App Service planer i en App Service-miljön.  På en ILB-aktiverad ASE är den här porten kopplad till ILB-adressen för ASE.
* 21: kontroll kanal för FTP.  Den här porten kan blockeras på ett säkert sätt om FTP inte används.  På en ILB-aktiverad ASE kan den här porten bindas till ILB-adressen för en ASE.
* 990: kontroll kanal för FTPS.  Den här porten kan blockeras på ett säkert sätt om FTPS inte används.  På en ILB-aktiverad ASE kan den här porten bindas till ILB-adressen för en ASE.
* 10001-10020: data kanaler för FTP.  Precis som med kontroll kanalen kan dessa portar blockeras på ett säkert sätt om FTP inte används.  På en ILB-aktiverad ASE kan den här porten bindas till ASE ILB-adress.
* 4016: används för fjärrfelsökning med Visual Studio 2012.  Den här porten kan blockeras på ett säkert sätt om funktionen inte används.  På en ILB-aktiverad ASE är den här porten kopplad till ILB-adressen för ASE.
* 4018: används för fjärrfelsökning med Visual Studio 2013.  Den här porten kan blockeras på ett säkert sätt om funktionen inte används.  På en ILB-aktiverad ASE är den här porten kopplad till ILB-adressen för ASE.
* 4020: används för fjärrfelsökning med Visual Studio 2015.  Den här porten kan blockeras på ett säkert sätt om funktionen inte används.  På en ILB-aktiverad ASE är den här porten kopplad till ILB-adressen för ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Krav för utgående anslutning och DNS
För att en App Service-miljön ska fungera korrekt måste även utgående åtkomst till olika slut punkter. En fullständig lista över de externa slut punkter som används av en ASE finns i avsnittet "nödvändig nätverks anslutning" i artikeln [nätverks konfiguration för ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

App Service miljöer kräver en giltig DNS-infrastruktur som kon figurer ATS för det virtuella nätverket.  Om DNS-konfigurationen ändras efter att en App Service-miljön har skapats kan utvecklare tvinga en App Service-miljön att hämta den nya DNS-konfigurationen.  Om du utlöser en rullande miljö omstart med hjälp av ikonen **starta om** , hämtar miljön den nya DNS-konfigurationen. (Ikonen **starta om** finns överst på bladet App Service-miljön hantering i [Azure Portal][NewPortal].)

Vi rekommenderar också att alla anpassade DNS-servrar i VNet ställs in före tiden innan du skapar en App Service-miljön.  Om ett virtuellt nätverks DNS-konfiguration ändras när ett App Service-miljön skapas, kommer processen för att skapa App Service-miljön att Miss förvänta.  Om det finns en anpassad DNS-server som inte kan nås eller som inte är tillgänglig i den andra änden av en VPN-gateway, kommer App Service-miljön skapande processen också att Miss klaras.

## <a name="creating-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp
Fullständig information om hur nätverks säkerhets grupper fungerar finns i följande [information][NetworkSecurityGroups].  Exemplet på Azure Service Management nedan rör sig av högdagrar om nätverks säkerhets grupper. Exemplet konfigurerar och tillämpar en nätverks säkerhets grupp på ett undernät som innehåller en App Service-miljön.

**Obs:** Nätverks säkerhets grupper kan konfigureras grafiskt med hjälp av [Azure Portal](https://portal.azure.com) eller via Azure PowerShell.

Nätverks säkerhets grupper skapas först som en fristående enhet som är associerad med en prenumeration. Eftersom nätverks säkerhets grupper skapas i en Azure-region skapar du nätverks säkerhets gruppen i samma region som App Service-miljön.

Följande kommando visar hur du skapar en nätverks säkerhets grupp:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

När en nätverks säkerhets grupp har skapats läggs en eller flera nätverks säkerhets regler till i den.  Eftersom uppsättningen regler kan ändras över tid, bör du använda det numreringsschema som används för regel prioriteringar. Den här metoden gör det enkelt att infoga ytterligare regler över tid.

I exemplet nedan ger en regel uttryckligen åtkomst till de hanterings portar som krävs av Azure-infrastrukturen för att hantera och underhålla en App Service-miljön.  All hanterings trafik flödar över TLS och skyddas av klient certifikat. Även om portarna öppnas är de inte tillgängliga för andra entiteter än Azures hanterings infrastruktur.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
```

När du låser åtkomsten till port 80 och 443 för att "dölja" en App Service-miljön bakom överordnade enheter eller tjänster ska du komma ihåg den överordnade IP-adressen.  Om du till exempel använder en brand vägg för webbaserade program (WAF), kommer WAF att ha sina egna IP-adresser eller adresser. WAF använder dem vid proxy-trafik till ett underordnat App Service-miljön.  Du måste använda den här IP-adressen i *SourceAddressPrefix* -parametern för en nätverks säkerhets regel.

I exemplet nedan tillåts inkommande trafik från en specifik överordnad IP-adress uttryckligen.  Adressen *1.2.3.4* används som plats hållare för IP-adressen för en överordnad WAF.  Ändra värdet så att det matchar den adress som används av den överordnade enheten eller tjänsten.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Om FTP-stöd önskas använder du följande regler som mall för att bevilja åtkomst till FTP-kontrollens port och data kanals portar.  Eftersom FTP är ett tillstånds känsligt protokoll kanske du inte kan dirigera FTP-trafik via en traditionell HTTP/HTTPS-brandvägg eller proxyserver.  I så fall måste du ange *SourceAddressPrefix* till ett annat värde, till exempel IP-adressintervallet för de utvecklare eller distributions datorer där FTP-klienter körs. 

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP
```

(**Obs:**  data kanalens port intervall kan ändras under för hands perioden.)

Om fjärrfelsökning med Visual Studio används visar följande regler hur du beviljar åtkomst.  Det finns en separat regel för varje version av Visual Studio som stöds eftersom varje version använder en annan port för fjärrfelsökning.  Precis som med FTP-åtkomst kanske fjärrfelsöknings trafik inte flödar korrekt via en traditionell WAF eller proxyserver.  *SourceAddressPrefix* kan i stället anges till IP-adressintervallet för de utvecklare som kör Visual Studio.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP
```

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Tilldela en nätverks säkerhets grupp till ett undernät
En nätverks säkerhets grupp har en standard säkerhets regel som nekar åtkomst till all extern trafik. När du kombinerar den här regeln med nätverks säkerhets reglerna ovan kommer endast trafik från käll adress intervall som är associerade med en *Tillåt* -åtgärd att kunna skicka trafik till appar som körs i en app service-miljön.

När en nätverks säkerhets grupp har fyllts med säkerhets regler tilldelar du den till det undernät som innehåller App Service-miljön.  Tilldelnings kommandot refererar till två namn: namnet på det virtuella nätverk där App Service-miljön är och namnet på under nätet där App Service-miljön skapades.  

I exemplet nedan visas en nätverks säkerhets grupp som tilldelas till ett undernät och ett virtuellt nätverk:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

Tilldelningen är en tids krävande åtgärd och det kan ta några minuter att slutföra åtgärden. När nätverks säkerhets grupp tilldelningen lyckas, kommer bara inkommande trafik som matchar *tillåtna* regler att uppnå appar i App Service-miljön.

För att vara klar visas följande exempel hur du tar bort och avassocierar nätverks säkerhets gruppen från under nätet:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

## <a name="special-considerations-for-explicit-ip-ssl"></a>Särskilda överväganden för explicit IP-SSL
Om en app har kon figurer ATS med en explicit IP-SSL-adress (gäller *endast* ASE som har en offentlig VIP), i stället för att använda standard-IP-adressen för den app service-miljön, flödar både http-och https-trafiken till under nätet över andra portar än portarna 80 och 443.

För att hitta det enskilda port paret som används av varje IP-SSL-adress går du till portalen och visar App Service-miljönens informations blads blad.  Välj **alla inställningar**  >  **IP-adresser**.  Bladet **IP-adresser** visar en tabell med alla explicit konfigurerade IP-SSL-adresser för App Service-miljön. Bladet visar också det särskilda port paret som används för att dirigera HTTP-och HTTPS-trafik som är associerad med varje IP-SSL-adress.  Använd det här port paret för DestinationPortRange-parametrarna när du konfigurerar regler i en nätverks säkerhets grupp.

När en app på en ASE har kon figurer ATS för att använda IP-SSL, kommer externa kunder inte att se eller behöva bekymra sig över den särskilda mappningen av port par.  Trafik till apparna kommer normalt att flöda till den konfigurerade IP-SSL-adressen.  Översättningen till det särskilda port paret sker automatiskt internt, under Dirigerings trafikens sista steg till det undernät som innehåller ASE. 

## <a name="getting-started"></a>Komma igång
Information om hur du kommer igång med App Service miljöer finns i [Introduktion till App Service-miljön][IntroToAppServiceEnvironment].

Mer information finns i [säker anslutning till Server dels resurser från en app service-miljön][SecurelyConnecttoBackend].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->