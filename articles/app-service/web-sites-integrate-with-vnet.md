---
title: Integrera app med Azure Virtual Network
description: Integrera app i Azure App Service med virtuella Azure-nätverk.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/05/2020
ms.author: ccompy
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 42391a073d7cb1d7e6850e298c2be32d550bb813
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832076"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrera din app med ett virtuellt Azure-nätverk

Den här artikeln beskriver Azure App Service VNet-integrering och hur du ställer in den med appar i [Azure App Service](./overview.md). Med [Azure Virtual Network][VNETOverview] (VNet) kan du placera många av dina Azure-resurser i ett icke-Internet-dirigerbart nätverk. Funktionen VNet-integrering gör att dina appar kan komma åt resurser i eller via ett VNet. VNet-integrering gör inte att dina appar kan nås privat.

Azure App Service har två varianter av VNet-integreringsfunktionen:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Aktivera VNet-integrering

1. Gå till **nätverksgränssnittet** i App Service portalen. Under **VNet-integrering** väljer du **Klicka här för att konfigurera**.

1. Välj **Lägg till VNet.**

   ![Välj VNet-integrering][1]

1. Listrutan innehåller alla virtuella Azure Resource Manager i din prenumeration i samma region. Under finns en lista över de virtuella Resource Manager nätverk i alla andra regioner. Välj det virtuella nätverk som du vill integrera med.

   ![Välj det virtuella nätverket][2]

   * Om det virtuella nätverket finns i samma region skapar du antingen ett nytt undernät eller väljer ett tomt undernät som redan finns.
   * Om du vill välja ett VNet i en annan region måste du ha en VNet-gateway som etablerats med punkt-till-plats aktiverad.
   * Om du vill integrera med ett klassiskt VNet väljer du Klicka här **för att ansluta till ett klassiskt VNet i stället** för att välja listrutan Virtual Network välj Välj här för att ansluta till ett **klassiskt VNet.** Välj det klassiska virtuella nätverk som du vill använda. Det virtuella målnätverket måste redan ha en Virtual Network-gateway som etablerats med punkt-till-plats aktiverat.

    ![Välj klassiskt VNet][3]

Under integreringen startas din app om. När integreringen är klar visas information om det virtuella nätverk som du är integrerad med.

## <a name="regional-vnet-integration"></a>Regional VNet-integrering

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Så här fungerar regional VNet-integrering

Appar i App Service finns på arbetsroller. Basic- och högre prisplaner är dedikerade värdplaner där det inte finns några andra kunders arbetsbelastningar som körs på samma arbetare. Regional VNet-integrering fungerar genom att montera virtuella gränssnitt med adresser i det delegerade undernätet. Eftersom from-adressen finns i ditt VNet kan den komma åt det mesta i eller via ditt VNet, som en virtuell dator i ditt VNet. Nätverksimplementering skiljer sig från att köra en virtuell dator i ditt virtuella nätverk. Därför är vissa nätverksfunktioner ännu inte tillgängliga för den här funktionen.

![Så här fungerar regional VNet-integrering][5]

När regional VNet-integrering har aktiverats gör din app utgående anrop till Internet via samma kanaler som vanligt. De utgående adresser som visas i appegenskaperna är de adresser som fortfarande används av din app. De ändringar som ändras för din app är anrop till tjänster som skyddas av tjänstslutpunkt eller RFC 1918-adresser till ditt virtuella nätverk. Om WEBSITE_VNET_ROUTE_ALL har angetts till 1 kan all utgående trafik skickas till ditt VNet.

> [!NOTE]
> `WEBSITE_VNET_ROUTE_ALL` stöds för närvarande inte i Windows-containrar.
> 

Funktionen stöder bara ett virtuellt gränssnitt per arbetsroll. Ett virtuellt gränssnitt per arbetare innebär en regional VNet-integrering per App Service plan. Alla appar i samma miljö App Service plan använda samma VNet-integrering. Om du behöver en app för att ansluta till ytterligare ett VNet måste du skapa en till App Service plan. Det virtuella gränssnittet som används är inte en resurs som kunderna har direkt åtkomst till.

På grund av den här teknikens natur visas inte den trafik som används med VNet-integrering i Azure Network Watcher- eller NSG-flödesloggar.

## <a name="gateway-required-vnet-integration"></a>Gateway-nödvändig VNet-integrering

Gateway-nödvändig VNet-integrering stöder anslutning till ett virtuellt nätverk i en annan region eller till ett klassiskt virtuellt nätverk. Gateway-nödvändig VNet-integrering:

* Gör att en app endast kan ansluta till ett VNet i taget.
* Gör att upp till fem virtuella nätverk kan integreras i en App Service plan.
* Tillåter att samma virtuella nätverk används av flera appar i en App Service plan utan att det totala antalet som kan användas av en App Service plan. Om du har sex appar som använder samma VNet i samma App Service plan räknas det som ett VNet som används.
* Stöder ett serviceavtal på 99,9 % på grund av serviceavtalet på gatewayen.
* Gör att dina appar kan använda den DNS som det virtuella nätverket är konfigurerat med.
* Kräver en Virtual Network-baserad gateway som konfigurerats med en punkt-till-plats-VPN för SSTP innan den kan anslutas till en app.

Du kan inte använda gateway-nödvändig VNet-integrering:

* Med ett VNet anslutet med Azure ExpressRoute.
* Från en Linux-app.
* Från en [Windows-container](quickstart-custom-container.md).
* För att få åtkomst till tjänstslutpunktens skyddade resurser.
* Med en gateway för samexistens som stöder både ExpressRoute och punkt-till-plats- eller plats-till-plats-VPN: er.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Konfigurera en gateway i ditt virtuella Azure-nätverk ###

Så här skapar du en gateway:

1. [Skapa ett gateway-undernät][creategatewaysubnet] i ditt VNet.

1. [Skapa VPN-gatewayen][creategateway]. Välj en ruttbaserad VPN-typ.

1. [Ange punkt-till-plats-adresserna][setp2saddresses]. Om gatewayen inte finns i den grundläggande SKU:n måste IKEV2 inaktiveras i punkt-till-plats-konfigurationen och SSTP måste väljas. Punkt-till-plats-adressutrymmet måste finnas i RFC 1918-adressblocken 10.0.0.0/8, 172.16.0.0/12 och 192.168.0.0/16.

Om du skapar gatewayen för användning App Service VNet-integrering behöver du inte ladda upp ett certifikat. Det kan ta 30 minuter att skapa gatewayen. Du kommer inte att kunna integrera din app med ditt VNet förrän gatewayen har etablerats.

### <a name="how-gateway-required-vnet-integration-works"></a>Så här fungerar gateway-nödvändig VNet-integrering

Gateway-nödvändig VNet-integrering bygger på punkt-till-plats-VPN-teknik. Punkt-till-plats-VPN:er begränsar nätverksåtkomsten till den virtuella dator som är värd för appen. Appar är begränsade till att skicka trafik till Internet endast via hybridanslutningar eller via VNet-integrering. När din app har konfigurerats med portalen för att använda gateway-nödvändig VNet-integrering hanteras en komplex förhandling för din räkning för att skapa och tilldela certifikat på gatewayen och programsidan. Resultatet är att de arbetare som används som värd för dina appar kan ansluta direkt till den virtuella nätverksgatewayen i det valda virtuella nätverket.

![Så här fungerar gateway-nödvändig VNet-integrering][6]

### <a name="access-on-premises-resources"></a>Få åtkomst till lokala resurser

Appar kan komma åt lokala resurser genom att integrera med virtuella nätverk som har plats-till-plats-anslutningar. Om du använder gateway-nödvändig VNet-integrering uppdaterar du dina lokala VPN-gatewayvägar med dina punkt-till-plats-adressblock. När plats-till-plats-VPN först konfigureras bör skripten som används för att konfigurera det konfigurera vägarna korrekt. Om du lägger till punkt-till-plats-adresserna när du har skapat ditt plats-till-plats-VPN måste du uppdatera vägarna manuellt. Information om hur du gör detta varierar beroende på gateway och beskrivs inte här. Du kan inte konfigurera BGP med en PLATS-till-plats-VPN-anslutning.

Ingen ytterligare konfiguration krävs för att den regionala VNet-integreringsfunktionen ska kunna nå via ditt VNet till lokala resurser. Du behöver bara ansluta ditt VNet till lokala resurser med hjälp av ExpressRoute eller en plats-till-plats-VPN.

> [!NOTE]
> Funktionen för gateway-obligatorisk VNet-integrering integrerar inte en app med ett VNet som har en ExpressRoute-gateway. Även om ExpressRoute-gatewayen är konfigurerad [i samexistensläge][VPNERCoex]fungerar inte VNet-integreringen. Om du behöver komma åt resurser via en ExpressRoute-anslutning använder du funktionen för regional VNet-integrering [eller en App Service-miljön][ASE], som körs i ditt virtuella nätverk.
>
>

### <a name="peering"></a>Peering

Om du använder peering med regional VNet-integrering behöver du inte göra någon ytterligare konfiguration.

Om du använder gateway-nödvändig VNet-integrering med peering måste du konfigurera några ytterligare objekt. Så här konfigurerar du peering så att det fungerar med din app:

1. Lägg till en peeringanslutning på det virtuella nätverk som din app ansluter till. När du lägger till peering-anslutningen aktiverar **du Tillåt åtkomst till** virtuellt nätverk och väljer Tillåt **vidarebefordrad trafik** och Tillåt **gateway-överföring.**
1. Lägg till en peeringanslutning på det virtuella nätverk som peerkopplas till det virtuella nätverk som du är ansluten till. När du lägger till peering-anslutningen på  det virtuella målnätverket aktiverar du Tillåt åtkomst till virtuellt nätverk och väljer Tillåt **vidarebefordrad trafik** **och Tillåt fjärrgatewayer.**
1. Gå till App Service plan  >  **användargränssnittet**  >  **för VNet-integrering** i portalen. Välj det virtuella nätverk som din app ansluter till. Under avsnittet routning lägger du till adressintervallet för det virtuella nätverk som är peerkopplat med det virtuella nätverk som appen är ansluten till.

## <a name="manage-vnet-integration"></a>Hantera VNet-integrering

Det finns en appnivå för att ansluta och koppla från med ett VNet. Åtgärder som kan påverka VNet-integreringen mellan flera appar är på App Service plan nivå. Från appen >   >  **VNet-integreringsportalen** för nätverk kan du få information om ditt VNet. Du kan se liknande information på App Service plan i App Service plan  >    >  **VNet-integreringsportalen** för nätverk.

Den enda åtgärden du kan vidta i appvyn för din VNet-integreringsinstans är att koppla bort appen från det virtuella nätverk som den för närvarande är ansluten till. Om du vill koppla bort appen från ett VNet väljer du **Koppla från**. Appen startas om när du kopplar från ett VNet. Om du kopplar från ändras inte ditt VNet. Undernätet eller gatewayen tas inte bort. Om du sedan vill ta bort ditt VNet kopplar du först bort appen från det virtuella nätverket och tar bort resurserna i det, till exempel gatewayer.

Användargränssnittet App Service plan VNet-integrering visar alla VNet-integreringar som används av apparna i din App Service plan. Om du vill se information om varje VNet väljer du det virtuella nätverk som du är intresserad av. Det finns två åtgärder som du kan utföra här för gateway-nödvändig VNet-integrering:

* **Synkroniseringsnätverk:** Synkroniseringsnätverksåtgärden används endast för den gatewayberoende VNet-integreringsfunktionen. När du utför en nätverkssynkroniseringsåtgärd säkerställer du att dina certifikat och nätverksinformationen är synkroniserade. Om du lägger till eller ändrar DNS för ditt VNet utför du en nätverkssynkroniseringsåtgärd. Den här åtgärden startar om alla appar som använder det här virtuella nätverket. Den här åtgärden fungerar inte om du använder en app och ett vnet som tillhör olika prenumerationer.
* **Lägg till vägar:** Genom att lägga till vägar får du utgående trafik till ditt virtuella nätverk.

Den privata IP-adressen som tilldelats instansen exponeras via **miljövariabeln, WEBSITE_PRIVATE_IP**. Kudu-konsolens användargränssnitt visar också listan över miljövariabler som är tillgängliga för webbappen. Den här IP-adressen tilldelas från adressintervallet för det integrerade undernätet. För regional VNet-integrering är värdet för WEBSITE_PRIVATE_IP en IP-adress från adressintervallet för det delegerade undernätet, och för Gateway-obligatorisk VNet-integrering är värdet en IP-adress från adressintervallet för den punkt-till-plats-adresspool som konfigurerats på Virtual Network Gateway. Det här är den IP-adress som används av webbappen för att ansluta till resurserna via Virtual Network. 

> [!NOTE]
> Värdet för WEBSITE_PRIVATE_IP måste ändras. Det är dock en IP-adress inom adressintervallet för integreringsundernätet eller punkt-till-plats-adressintervallet, så du måste tillåta åtkomst från hela adressintervallet.
>

### <a name="gateway-required-vnet-integration-routing"></a>Routning för gateway-nödvändig VNet-integrering
Vägarna som definieras i ditt VNet används för att dirigera trafik till ditt VNet från din app. Om du vill skicka ytterligare utgående trafik till det virtuella nätverket lägger du till dessa adressblock här. Den här funktionen fungerar bara med gateway-nödvändig VNet-integrering. Vägtabeller påverkar inte din apptrafik när du använder gateway-nödvändig VNet-integrering på det sätt som de gör med regional VNet-integrering.

### <a name="gateway-required-vnet-integration-certificates"></a>Gateway-nödvändiga VNet-integrationscertifikat
När gateway-nödvändig VNet-integrering är aktiverad, finns det ett nödvändigt utbyte av certifikat för att säkerställa anslutningens säkerhet. Tillsammans med certifikaten finns DNS-konfiguration, vägar och andra liknande saker som beskriver nätverket.

Om certifikat eller nätverksinformation ändras väljer du **Synkronisera nätverk.** När du väljer **Synkronisera** nätverk orsakar du ett kort avbrott i anslutningen mellan din app och ditt VNet. När appen inte startas om kan förlust av anslutning göra att webbplatsen inte fungerar korrekt.

## <a name="pricing-details"></a>Prisinformation
Funktionen för regional VNet-integrering har inga extra avgifter för användning utöver App Service plan på prisnivån.

Tre avgifter är relaterade till användningen av funktionen för gateway-nödvändig VNet-integrering:

* **App Service plan prisnivåavgifter:** Dina appar måste finnas i en Standard-, Premium-, PremiumV2- eller PremiumV3-App Service plan. Mer information om dessa kostnader finns i [App Service priser.][ASPricing]
* **Kostnader för dataöverföring:** Det finns en avgift för utgående data, även om det virtuella nätverket finns i samma datacenter. Dessa avgifter beskrivs i [prisinformationen för dataöverföring.][DataPricing]
* **Kostnader för** VPN-gateway: Det finns en kostnad för den virtuella nätverksgateway som krävs för punkt-till-plats-VPN. Mer information finns i Prissättning [för VPN-gateway.][VNETPricing]

## <a name="troubleshooting"></a>Felsökning

> [!NOTE]
> VNET-integrering stöds inte för Docker Compose-scenarier i App Service.
> Azure Functions åtkomstbegränsningar ignoreras om det finns en privat slutpunkt.
>

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

CLI-stöd är tillgängligt för regional VNet-integrering. Om du vill komma åt följande kommandon [installerar du Azure CLI][installCLI].

```azurecli
az webapp vnet-integration --help

Group
    az webapp vnet-integration : Methods that list, add, and remove virtual network
    integrations from a webapp.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a regional virtual network integration to a webapp.
    list   : List the virtual network integrations on a webapp.
    remove : Remove a regional virtual network integration from webapp.

az appservice vnet-integration --help

Group
    az appservice vnet-integration : A method that lists the virtual network
    integrations used in an appservice plan.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    list : List the virtual network integrations used in an appservice plan.
```

PowerShell-stöd för regional VNet-integrering är också tillgängligt, men du måste skapa en allmän resurs med en egenskapsmatris för undernätets resourceID

```azurepowershell
# Parameters
$sitename = 'myWebApp'
$resourcegroupname = 'myRG'
$VNetname = 'myVNet'
$location = 'myRegion'
$integrationsubnetname = 'myIntegrationSubnet'
$subscriptionID = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee'

#Property array with the SubnetID
$properties = @{
  subnetResourceId = "/subscriptions/$subscriptionID/resourceGroups/$resourcegroupname/providers/Microsoft.Network/virtualNetworks/$VNetname/subnets/$integrationsubnetname"
}

#Creation of the VNet integration
$vNetParams = @{
  ResourceName = "$sitename/VirtualNetwork"
  Location = $location
  ResourceGroupName = $resourcegroupname
  ResourceType = 'Microsoft.Web/sites/networkConfig'
  PropertyObject = $properties
}
New-AzResource @vNetParams
```


För gateway-nödvändig VNet-integrering kan du integrera App Service ett virtuellt Azure-nätverk med hjälp av PowerShell. Ett skript som är klart att köra finns i [Ansluta en app i Azure App Service till ett virtuellt Azure-nätverk](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: ../virtual-network/virtual-networks-overview.md
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: ../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[setp2saddresses]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#addresspool
[VNETRouteTables]: ../virtual-network/manage-route-table.md
[installCLI]: /cli/azure/install-azure-cli
[privateendpoints]: networking/private-endpoint.md
