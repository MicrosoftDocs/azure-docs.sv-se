---
title: Anslut till Server delen v1
description: Lär dig mer om hur du ansluter säkert till Server dels resurser från en App Service-miljön. Detta dokument tillhandahålls endast för kunder som använder den äldre v1-ASE.
author: stefsch
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 9f8e288f771b9d584a0fd3430115f5fa60f68e47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88961813"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>Anslut säkert till Server dels resurser från en App Service-miljö
Eftersom en App Service-miljön alltid skapas **antingen** i ett Azure Resource Manager virtuellt nätverk **eller** i ett klassiskt [virtuellt nätverk][virtualnetwork]för distributions modellen, kan utgående anslutningar från en app service-miljön till andra server dels resurser flöda exklusivt över det virtuella nätverket. Från och med juni 2016 kan ASE också distribueras till virtuella nätverk som använder antingen offentliga adress intervall eller RFC1918 adress utrymmen (privata adresser).  

Det kan till exempel finnas en SQL Server som körs på ett kluster med virtuella datorer med port 1433 låst.  Slut punkten kan vara ACLd att endast tillåta åtkomst från andra resurser i samma virtuella nätverk.  

Som ett annat exempel kan känsliga slut punkter köras lokalt och vara anslutna till Azure via antingen [plats-till-plats-][SiteToSite] eller [Azure ExpressRoute][ExpressRoute] -anslutningar.  Därför kan endast resurser i virtuella nätverk som är anslutna till plats-till-plats-eller ExpressRoute-tunnlar komma åt lokala slut punkter.

För alla dessa scenarier kan appar som körs på en App Service-miljön säkert ansluta till de olika servrarna och resurserna. Om utgående trafik från appar körs i en App Service-miljön till privata slut punkter i samma virtuella nätverk eller anslutna till samma virtuella nätverk, flödar det bara över det virtuella nätverket.  Utgående trafik till privata slut punkter flödar inte över det offentliga Internet.

Ett problem gäller utgående trafik från en App Service-miljön till slut punkter inom ett virtuellt nätverk. App Service miljöer kan inte komma åt slut punkter för virtuella datorer som finns i **samma** undernät som App Service-miljön. Den här begränsningen bör normalt inte vara ett problem, om App Service miljöer distribueras till ett undernät som är reserverat för användning exklusivt av App Service-miljön.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Krav för utgående anslutning och DNS
För att en App Service-miljön ska fungera korrekt krävs utgående åtkomst till olika slut punkter. En fullständig lista över de externa slut punkter som används av en ASE finns i avsnittet "nödvändig nätverks anslutning" i artikeln [nätverks konfiguration för ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

App Service miljöer kräver en giltig DNS-infrastruktur som kon figurer ATS för det virtuella nätverket.  Om DNS-konfigurationen ändras efter att en App Service-miljön har skapats kan utvecklare tvinga en App Service-miljön att hämta den nya DNS-konfigurationen. Överst på bladet App Service-miljön hantering i portalen väljer du ikonen **starta om** för att utlösa en rullande miljö omstart, vilket gör att miljön hämtar den nya DNS-konfigurationen.

Vi rekommenderar också att alla anpassade DNS-servrar i VNet ställs in före tiden innan du skapar en App Service-miljön.  Om ett virtuellt nätverks DNS-konfiguration ändras när ett App Service-miljön skapas, vilket leder till att processen för att skapa App Service-miljön Miss lyckas. I det andra slutet av en VPN-gateway, om det finns en anpassad DNS-server som inte kan nås eller inte är tillgänglig, kommer App Service-miljön processen att skapas.

## <a name="connecting-to-a-sql-server"></a>Ansluta till en SQL Server
En vanlig SQL Server konfiguration har en slut punkt som lyssnar på port 1433:

![SQL Server slut punkt][SqlServerEndpoint]

Det finns två metoder för att begränsa trafik till den här slut punkten:

* [Nätverks Access Control listor][NetworkAccessControlLists] (nätverks-ACL: er)
* [Nätverkssäkerhetsgrupper][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Begränsa åtkomst med en nätverks-ACL
Port 1433 kan skyddas med hjälp av en lista över nätverks åtkomst kontroll.  Exemplet nedan lägger till tilldelnings behörighet för klient adresserna från i ett virtuellt nätverk och blockerar åtkomsten till alla andra klienter.

![Exempel på nätverks Access Control listan][NetworkAccessControlListExample]

Alla program som körs i App Service-miljön, i samma virtuella nätverk som SQL Server, kan ansluta till SQL Server-instansen. Använd den **interna VNet** -IP-adressen för den SQL Server virtuella datorn.  

Anslutnings strängen nedan refererar till SQL Server med hjälp av dess privata IP-adress.

`Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient`

Även om den virtuella datorn har en offentlig slut punkt, kommer anslutnings försök att använda den offentliga IP-adressen att avvisas på grund av nätverks åtkomst kontrol listan. 

## <a name="restricting-access-with-a-network-security-group"></a>Begränsa åtkomsten med en nätverks säkerhets grupp
En annan metod för att skydda åtkomsten är med en nätverks säkerhets grupp.  Nätverks säkerhets grupper kan tillämpas på enskilda virtuella datorer eller till ett undernät som innehåller virtuella datorer.

Först måste du skapa en nätverks säkerhets grupp:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

Det är enkelt att begränsa åtkomsten till intern VNet-trafik med en nätverks säkerhets grupp.  Standard reglerna i en nätverks säkerhets grupp tillåter endast åtkomst från andra nätverks klienter i samma virtuella nätverk.

Därför är det enkelt att låsa åtkomsten till SQL Server. Använd bara en nätverks säkerhets grupp med standard reglerna för antingen de virtuella datorerna som kör SQL Server eller det undernät som innehåller de virtuella datorerna.

Exemplet nedan tillämpar en nätverks säkerhets grupp för det innehåll ande under nätet:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
```

Det slutliga resultatet är en uppsättning säkerhets regler som blockerar extern åtkomst och möjliggör intern VNet-åtkomst:

![Standard regler för nätverks säkerhet][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Komma igång
Information om hur du kommer igång med App Service miljöer finns i [Introduktion till App Service-miljön][IntroToAppServiceEnvironment]

Mer information om hur du kontrollerar inkommande trafik till App Service-miljön finns i [styra inkommande trafik till en app service-miljön][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-multi-site.md
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: /previous-versions/azure/virtual-network/virtual-networks-acl
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png