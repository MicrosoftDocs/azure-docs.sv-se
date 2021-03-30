---
title: Konfigurera Azure ExpressRoute v1
description: Nätverks konfiguration för App Service-miljön för PowerApps med Azure ExpressRoute. Detta dokument tillhandahålls endast för kunder som använder den äldre v1-ASE.
author: stefsch
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 3fa2677f7c71b118ec3f7da717e29d5879ee982b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88961830"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Nätverks konfigurations information för App Service-miljön för PowerApps med Azure ExpressRoute

Kunder kan ansluta en [Azure ExpressRoute][ExpressRoute] -krets till sin virtuella nätverks infrastruktur för att utöka sitt lokala nätverk till Azure. App Service-miljön skapas i ett undernät av den [virtuella nätverks][virtualnetwork] infrastrukturen. Appar som körs på App Service-miljön upprätta säkra anslutningar till Server dels resurser som endast är tillgängliga via ExpressRoute-anslutningen.  

App Service-miljön kan skapas i följande scenarier:
- Azure Resource Manager virtuella nätverk.
- Klassiska virtuella nätverk för distributions modeller.
- Virtuella nätverk som använder offentliga adress intervall eller RFC1918 adress utrymmen (dvs. privata adresser). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Nödvändig nätverks anslutning

App Service-miljön har krav på nätverks anslutning som inlednings vis inte är uppfyllda i ett virtuellt nätverk som är anslutet till ExpressRoute.

App Service-miljön kräver följande inställningar för nätverks anslutning för att fungera korrekt:

* Utgående nätverks anslutning till Azure Storage slut punkter över hela världen både på port 80 och port 443. Dessa slut punkter finns i samma region som App Service-miljön och även andra Azure-regioner. Azure Storage slut punkter matchas enligt följande DNS-domäner: table.core.windows.net, blob.core.windows.net, queue.core.windows.net och file.core.windows.net.  

* Utgående nätverks anslutning till Azure Files tjänsten på port 445.

* Utgående nätverks anslutning till Azure SQL Database slut punkter som finns i samma region som App Service-miljön. SQL Database-slutpunkter matchas under database.windows.net-domänen, vilket kräver öppen åtkomst till portarna 1433, 11000-11999 och 14000-14999. Mer information om SQL Database V12 port Usage finns i [portar utöver 1433 för ADO.NET 4,5](../../azure-sql/database/adonet-v12-develop-direct-route-ports.md).

* Utgående nätverks anslutning till Azure Management – plan-slutpunkter (både den klassiska Azure-distributions modellen och Azure Resource Manager slut punkter). Anslutningarna till dessa slut punkter omfattar management.core.windows.net-och management.azure.com-domäner. 

* Utgående nätverks anslutning till ocsp.msocsp.com-, mscrl.microsoft.com-och crl.microsoft.com-domäner. Anslutning till de här domänerna krävs för att stödja TLS-funktioner.

* DNS-konfigurationen för det virtuella nätverket måste kunna matcha alla slut punkter och domäner som nämns i den här artikeln. Om slut punkterna inte kan matchas Miss lyckas App Service-miljön skapandet. Alla befintliga App Service-miljön har marker ATS som felaktiga.

* Utgående åtkomst på port 53 krävs för kommunikation med DNS-servrar.

* Om det finns en anpassad DNS-server i den andra änden av en VPN-gateway, måste DNS-servern kunna bli nåbar från det undernät som innehåller App Service-miljön. 

* Den utgående nätverks Sök vägen kan inte färdas via interna företags proxyservrar och kan inte tvingas tunnel trafik lokalt. De här åtgärderna ändrar den effektiva NAT-adressen för utgående nätverks trafik från App Service-miljön. Ändringar av NAT-adressen för App Service-miljön utgående nätverks trafik orsakar anslutnings fel till många av slut punkterna. Det gick inte att skapa App Service-miljön. Alla befintliga App Service-miljön har marker ATS som felaktiga.

* Inkommande nätverks åtkomst till nödvändiga portar för App Service-miljön måste tillåtas. Mer information finns i [så här styr du inkommande trafik till App Service-miljön][requiredports].

För att uppfylla DNS-kraven kontrollerar du att en giltig DNS-infrastruktur har kon figurer ATS och underhålls för det virtuella nätverket. Om DNS-konfigurationen ändras efter att App Service-miljön har skapats kan utvecklare tvinga App Service-miljön att hämta den nya DNS-konfigurationen. Du kan utlösa en rullande miljö omstart genom att använda ikonen **starta om** under App Service-miljön hantering i [Azure Portal][NewPortal]. Omstarten gör att miljön hämtar den nya DNS-konfigurationen.

Konfigurera en [nätverks säkerhets grupp (NSG)][NetworkSecurityGroups] i App Service-miljön under nätet för att uppfylla kraven för inkommande nätverks åtkomst. NSG ger den åtkomst som krävs [för att kontrol lera inkommande trafik till App Service-miljön][requiredports].

## <a name="outbound-network-connectivity"></a>Utgående nätverks anslutning

En nyligen skapad ExpressRoute-krets annonserar som standard en standard väg som tillåter utgående Internet anslutning. App Service-miljön kan använda den här konfigurationen för att ansluta till andra Azure-slutpunkter.

En vanlig kund konfiguration är att definiera sin egen standard väg (0.0.0.0/0), vilket tvingar utgående Internet trafik att flöda lokalt. Det här trafikflödet bryts App Service-miljön. Utgående trafik blockeras antingen lokalt eller NAT till en okänd uppsättning adresser som inte längre fungerar med olika Azure-slutpunkter.

Lösningen är att definiera en (eller flera) användardefinierade vägar (UDR) på det undernät som innehåller App Service-miljön. En UDR definierar de undernät-/regionsspecifika vägar som följer i stället för standard vägen.

Använd om möjligt följande konfiguration:

* ExpressRoute-konfigurationen annonserar 0.0.0.0/0. Som standard tunnlar konfigurations tvång all utgående trafik lokalt.
* UDR som tillämpas på det undernät som innehåller App Service-miljön definierar 0.0.0.0/0 med en nästa hopp typ av Internet. Ett exempel på den här konfigurationen beskrivs längre fram i den här artikeln.

Den kombinerade påverkan av den här konfigurationen är att UDR på under näts nivån prioriteras över ExpressRoute-tunneln. Utgående Internet åtkomst från App Service-miljön garanteras.

> [!IMPORTANT]
> Vägarna som definieras i en UDR måste vara tillräckligt bestämda för att prioriteras för alla vägar som annonseras av ExpressRoute-konfigurationen. I exemplet som beskrivs i nästa avsnitt används det breda adress intervallet 0.0.0.0/0. Det här intervallet kan oavsiktligt åsidosättas av väg annonseringar som använder mer exakta adress intervall.
> 
> App Service-miljön stöds inte med ExpressRoute-konfigurationer som passerar vägar från den offentliga peering-vägen till den privata peering-sökvägen. ExpressRoute konfigurationer med offentlig peering konfigurerad ta emot väg annonseringar från Microsoft för en stor uppsättning Microsoft Azure IP-adressintervall. Om de här adress intervallen är över-annonserade på den privata peering-sökvägen tvingas alla utgående nätverks paket från App Service-miljön under nätet att tunnlas till kundens lokala nätverks infrastruktur. Det här nätverks flödet stöds inte för närvarande med App Service-miljön. En lösning är att stoppa kors annonserings vägar från den offentliga peering-vägen till den privata peering-sökvägen.
> 
> 

Bakgrunds information om användardefinierade vägar finns i [trafik dirigering i virtuella nätverk][UDROverview].  

Information om hur du skapar och konfigurerar användardefinierade vägar finns i [dirigera nätverks trafik med en routningstabell med hjälp av PowerShell][UDRHowTo].

## <a name="udr-configuration"></a>UDR-konfiguration

I det här avsnittet visas ett exempel på en UDR-konfiguration för App Service-miljön.

### <a name="prerequisites"></a>Förutsättningar

* Installera Azure PowerShell från [sidan för Azure-nedladdningar][AzureDownloads]. Välj en nedladdning med datumet juni 2015 eller senare. Under **kommando rads verktyg**  >  **Windows PowerShell** väljer du **Installera** för att installera de senaste PowerShell-cmdletarna.

* Skapa ett unikt undernät för exklusiv användning av App Service-miljön. Det unika under nätet säkerställer att UDR som tillämpas på under nätet bara öppnar utgående trafik för App Service-miljön.

> [!IMPORTANT]
> Distribuera bara App Service-miljön när du har slutfört konfigurations stegen. Stegen ser till att utgående nätverks anslutning är tillgänglig innan du försöker distribuera App Service-miljön.

### <a name="step-1-create-a-route-table"></a>Steg 1: skapa en routningstabell

Skapa en routningstabell med namnet **DirectInternetRouteTable** i Azure-regionen USA, västra, som du ser i det här kodfragmentet:

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>Steg 2: skapa vägar i tabellen

Lägg till vägar i routningstabellen för att aktivera utgående Internet åtkomst.  

Konfigurera utgående åtkomst till Internet. Definiera en väg för 0.0.0.0/0 som visas i det här kodfragmentet:

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

0.0.0.0/0 är ett brett adress intervall. Intervallet åsidosätts av adress intervall som annonseras av ExpressRoute som är mer exakta. En UDR med router 0.0.0.0/0 bör användas tillsammans med en ExpressRoute-konfiguration som endast annonserar 0.0.0.0/0. 

Alternativt kan du hämta en aktuell, fullständig lista över CIDR-intervall som används av Azure. XML-filen för alla Azure IP-adressintervall är tillgänglig från [Microsoft Download Center][DownloadCenterAddressRanges].  

> [!NOTE]
>
> Azure IP-adressintervall ändras med tiden. Användardefinierade vägar kräver periodiska manuella uppdateringar som ska synkroniseras.
>
> En enda UDR har en övre standard gräns på 100 vägar. Du måste sammanfatta Azure IP-adressintervall så att de passar inom 100-flödes gränsen. UDR vägar måste vara mer exakta än vägar som annonseras av din ExpressRoute-anslutning.
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>Steg 3: koppla tabellen till under nätet

Koppla routningstabellen till det undernät där du tänker distribuera App Service-miljön. Det här kommandot kopplar **DirectInternetRouteTable** -tabellen till **ASESubnet** -undernätet som innehåller App Service-miljön.

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>Steg 4: testa och bekräfta vägen

När routningstabellen är kopplad till under nätet testar du och bekräftar vägen.

Distribuera en virtuell dator till under nätet och bekräfta följande villkor:

* Utgående trafik till de Azure-och icke-Azure-slutpunkter som beskrivs i den här artikeln flödar **inte** ned ExpressRoute-kretsen. Om utgående trafik från under nätet har Tvingad tunnel trafik lokalt, så Miss lyckas App Service-miljön skapandet.
* DNS-sökningar för de slut punkter som beskrivs i den här artikeln är korrekt lösta. 

När du har slutfört konfigurations stegen och bekräftat vägen, tar du bort den virtuella datorn. Under nätet måste vara tomt när App Service-miljön skapas.

Nu är du redo att distribuera App Service-miljön!

## <a name="next-steps"></a>Nästa steg

För att komma igång med App Service-miljön för PowerApps, se [Introduktion till App Service-miljön][IntroToAppServiceEnvironment].

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/ 
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/ 
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md 
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[UDROverview]: ../../virtual-network/virtual-networks-udr-overview.md
<!-- Old link -- [UDRHowTo]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/ -->

[UDRHowTo]: ../../virtual-network/tutorial-create-route-table-powershell.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md 
[AzureDownloads]: https://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653 
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md 
[NewPortal]:  https://portal.azure.com 


<!-- IMAGES -->