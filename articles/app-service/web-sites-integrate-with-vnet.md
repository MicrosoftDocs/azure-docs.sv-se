---
title: Integrera app med Azure Virtual Network
description: Integrera appen i Azure App Service med virtuella Azure-nätverk.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/05/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4666f03e529c568177ff56a2db84e226bc906132
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182918"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrera din app med ett virtuellt Azure-nätverk

Den här artikeln beskriver funktionen Azure App Service VNet-integrering och hur du konfigurerar den med appar i [Azure App Service](./overview.md). Med [Azure Virtual Network][VNETOverview] (virtuella nätverk) kan du placera många av dina Azure-resurser i ett nätverk som inte är Internet-dirigerbart. Funktionen för VNet-integrering gör att dina appar kan komma åt resurser i eller via ett VNet. VNet-integrering gör att dina appar inte kan nås privat.

Azure App Service har två varianter på funktionen för VNet-integrering:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Aktivera VNet-integrering

1. Gå till **nätverks** gränssnittet i App Service portalen. Under **VNet-integrering** väljer **du klicka här för att konfigurera**.

1. Välj **Lägg till VNet**.

   ![Välj VNet-integrering][1]

1. Den nedrullningsbara listan innehåller alla Azure Resource Manager virtuella nätverk i din prenumeration i samma region. Under det här är en lista över virtuella Resource Manager-nätverk i alla andra regioner. Välj det virtuella nätverk som du vill integrera med.

   ![Välj VNet][2]

   * Om VNet finns i samma region skapar du antingen ett nytt undernät eller väljer ett tomt befintligt undernät.
   * Om du vill välja ett VNet i en annan region måste du ha en VNet-Gateway etablerad med peka på plats aktive rad.
   * Om du vill integrera med ett klassiskt VNet i stället för att välja List rutan **Virtual Network** väljer du **Klicka här för att ansluta till ett klassiskt VNet**. Välj det klassiska virtuella nätverk som du vill använda. Mål-VNet måste redan ha en Virtual Network Gateway etablerad med punkt-till-plats aktive rad.

    ![Välj klassiskt VNet][3]

Under integrationen startas appen om. När integreringen är klar visas information om det virtuella nätverk som du är integrerad med.

## <a name="regional-vnet-integration"></a>Regional VNet-integrering

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Så här fungerar regional VNet-integrering

Appar i App Service finns i arbets roller. De grundläggande och högre pris planerna är dedikerade värd planer där det inte finns några andra kunders arbets belastningar som körs på samma arbetare. Regional VNet-integrering fungerar genom att montera virtuella gränssnitt med adresser i det delegerade under nätet. Eftersom från-adressen är i ditt VNet kan den komma åt de flesta saker i eller via ditt VNet som en virtuell dator i ditt VNet. Nätverks implementeringen skiljer sig från att köra en virtuell dator i ditt VNet. Det är därför som vissa nätverksfunktioner inte är tillgängliga än för den här funktionen.

![Så här fungerar regional VNet-integrering][5]

När regional VNet-integrering är aktive rad gör din app utgående samtal till Internet via samma kanaler som normalt. De utgående adresser som listas i app Properties-portalen är de adresser som fortfarande används av din app. Vilka ändringar som gjorts i din app är anropen till tjänstens slut punkts säkra tjänster, eller så kan RFC 1918-adresser ingå i ditt VNet. Om WEBSITE_VNET_ROUTE_ALL är inställt på 1, kan all utgående trafik skickas till ditt VNet.

> [!NOTE]
> `WEBSITE_VNET_ROUTE_ALL` stöds för närvarande inte i Windows-behållare.
> 

Funktionen stöder endast ett virtuellt gränssnitt per arbetare. Ett virtuellt gränssnitt per arbetare innebär en regional VNet-integration per App Service plan. Alla appar i samma App Service plan kan använda samma VNet-integrering. Om du behöver en app för att ansluta till ytterligare ett VNet måste du skapa en annan App Service plan. Det virtuella gränssnittet som används är inte en resurs som kunder har direkt åtkomst till.

På grund av hur den här tekniken fungerar visas inte trafiken som används med VNet-integrering i Azure Network Watcher-eller NSG Flow-loggar.

## <a name="gateway-required-vnet-integration"></a>Gateway – nödvändig VNet-integrering

Gateway-nödvändig VNet-integrering stöder anslutning till ett VNet i en annan region eller till ett klassiskt virtuellt nätverk. Gateway – nödvändig VNet-integrering:

* Gör det möjligt för en app att ansluta till endast ett VNet i taget.
* Gör det möjligt att integrera upp till fem virtuella nätverk i en App Service plan.
* Tillåter att samma VNet används av flera appar i en App Service plan utan att påverka det totala antalet som kan användas av en App Service plan. Om du har sex appar som använder samma VNet i samma App Service plan, räknas det som ett virtuellt nätverk som används.
* Har stöd för 99,9% SLA på grund av SLA på gatewayen.
* Gör att dina appar kan använda den DNS som VNet har kon figurer ATS med.
* Kräver en Virtual Network Route-baserad gateway som kon figurer ATS med en SSTP punkt-till-plats-VPN innan den kan anslutas till en app.

Du kan inte använda Gateway-nödvändig VNet-integrering:

* Med ett virtuellt nätverk som är anslutet till Azure ExpressRoute.
* Från en Linux-app.
* Från en [Windows-behållare](quickstart-custom-container.md).
* För att få åtkomst till tjänstens slut punkts säkra resurser.
* Med en gateway för samexistens som stöder både ExpressRoute-och punkt-till-plats-eller plats-till-plats-VPN.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Konfigurera en gateway i ditt virtuella Azure-nätverk ###

Så här skapar du en gateway:

1. [Skapa ett Gateway-undernät][creategatewaysubnet] i ditt VNet.

1. [Skapa VPN-gatewayen][creategateway]. Välj en Route-baserad VPN-typ.

1. [Ange punkt-till-plats-adresser][setp2saddresses]. Om gatewayen inte finns i Basic SKU måste IKEV2 vara inaktiverat i punkt-till-plats-konfigurationen och SSTP måste väljas. Punkt-till-plats-adress utrymmet måste finnas i RFC 1918-adress blocken 10.0.0.0/8, 172.16.0.0/12 och 192.168.0.0/16.

Om du skapar en gateway för användning med App Service VNet-integrering behöver du inte ladda upp ett certifikat. Det kan ta 30 minuter att skapa en gateway. Du kommer inte att kunna integrera din app med ditt VNet förrän gatewayen har tillhandahållits.

### <a name="how-gateway-required-vnet-integration-works"></a>Så här fungerar Gateway-nödvändig VNet-integrering

Gateway – nödvändig VNet-integrering skapas ovanpå punkt-till-plats-VPN-teknik. Punkt-till-plats-VPN begränsar nätverks åtkomsten till den virtuella datorn som är värd för appen. Appar är begränsade till att endast skicka trafik till Internet via Hybridanslutningar eller genom VNet-integrering. När din app har kon figurer ATS med portalen för att använda Gateway-nödvändig VNet-integrering, hanteras en komplex förhandling för din räkning för att skapa och tilldela certifikat på gatewayen och på program sidan. Resultatet är att de anställda som används som värd för dina appar kan ansluta direkt till den virtuella Nätverksgatewayen i det valda virtuella nätverket.

![Så här fungerar Gateway-nödvändig VNet-integrering][6]

### <a name="access-on-premises-resources"></a>Åtkomst till lokala resurser

Appar kan komma åt lokala resurser genom att integrera med virtuella nätverk som har plats-till-plats-anslutningar. Om du använder Gateway-nödvändig VNet-integrering kan du uppdatera din lokala VPN-gateway med dina punkt-till-plats-Adressblock. När plats-till-plats-VPN först konfigureras bör de skript som används för att konfigurera den Konfigurera vägar korrekt. Om du lägger till punkt-till-plats-adresser när du har skapat din plats-till-plats-VPN måste du uppdatera vägarna manuellt. Information om hur du gör detta varierar per gateway och inte beskrivs här. Du kan inte ha BGP konfigurerat med en plats-till-plats-VPN-anslutning.

Ingen ytterligare konfiguration krävs för den regionala VNet-integrerings funktionen för att gå via ditt VNet till lokala resurser. Du behöver bara ansluta ditt VNet till lokala resurser med hjälp av ExpressRoute eller VPN för plats till plats.

> [!NOTE]
> Den gateway-obligatoriska VNet-integrerings funktionen integrerar inte en app med ett VNet som har en ExpressRoute-Gateway. Även om ExpressRoute-gatewayen har kon figurer ATS i [läget för samexistens][VPNERCoex]fungerar inte VNet-integreringen. Om du behöver åtkomst till resurser via en ExpressRoute-anslutning använder du funktionen regional VNet-integrering eller en [App Service-miljön][ASE]som körs i ditt VNet.
>
>

### <a name="peering"></a>Peering

Om du använder peering med den regionala VNet-integrationen behöver du inte göra någon ytterligare konfiguration.

Om du använder Gateway-nödvändig VNet-integrering med peering måste du konfigurera ytterligare några objekt. Konfigurera peering så att den fungerar med din app:

1. Lägg till en peering-anslutning på det virtuella nätverk som din App ansluter till. När du lägger till peering-anslutningen aktiverar du **Tillåt åtkomst till virtuellt nätverk** och väljer **Tillåt vidarebefordrad trafik** och **Tillåt Gateway-överföring**.
1. Lägg till en peering-anslutning på det virtuella nätverket som peer-kopplas till det virtuella nätverk som du är ansluten till. När du lägger till peering-anslutningen på målets VNet aktiverar du **Tillåt åtkomst till virtuellt nätverk** och väljer **Tillåt vidarebefordrad trafik** och **Tillåt fjärrgatewayer**.
1. Gå till **App Service plan**  >  **Network**  >  **VNet integration** UI i portalen. Välj det VNet som appen ansluter till. Under avsnittet routning lägger du till adress intervallet för det VNet som är peer-kopplat med det virtuella nätverk som appen är ansluten till.

## <a name="manage-vnet-integration"></a>Hantera VNet-integrering

Anslutning och från koppling med ett VNet finns på en app-nivå. Åtgärder som kan påverka VNet-integrering över flera appar finns på App Service plan nivå. Du kan få information om ditt VNet från appen > **nätverkets**  >  **VNet-integrerings** Portal. Du kan se liknande information på App Service plan nivå i **App Service plan**  >  **nätverkets**  >  **VNet integrations** Portal.

Den enda åtgärd som du kan vidta i vyn app för din VNet-integrering är att koppla från appen från det virtuella nätverk som den är ansluten till. Om du vill koppla från din app från ett VNet väljer du **Koppla från**. Din app startas om när du kopplar från ett virtuellt nätverk. Om du kopplar från, ändras inte ditt VNet. Under nätet eller gatewayen tas inte bort. Om du sedan vill ta bort ditt VNet tar du först bort din app från VNet och tar bort resurserna i den, till exempel gatewayer.

GRÄNSSNITTET App Service plan VNet-integration visar alla VNet-integreringar som används av apparna i App Service plan. Om du vill se information om varje VNet väljer du det VNet som du är intresse rad av. Det finns två åtgärder som du kan utföra här för gateway-nödvändig VNet-integrering:

* **Sync-nätverk**: åtgärden synkronisera nätverk används endast för funktionen Gateway-beroende VNet-integrering. Att utföra en synkroniserad nätverks åtgärd garanterar att dina certifikat och nätverksinformation är synkroniserade. Om du lägger till eller ändrar DNS för ditt VNet, utför du en synkronisera nätverks åtgärd. Den här åtgärden startar om alla appar som använder det här virtuella nätverket. Den här åtgärden fungerar inte om du använder en app och ett virtuellt nätverk som tillhör olika prenumerationer.
* **Lägg till vägar: Lägg** till vägar enheter utgående trafik till ditt VNet.

Den privata IP-adress som tilldelas till instansen exponeras via miljövariabeln **WEBSITE_PRIVATE_IP**. Kudu-konsolens gränssnitt visar också en lista över de miljövariabler som är tillgängliga för webbappen. Den här IP-adressen tilldelas från adress intervallet för det integrerade under nätet. För regional VNet-integrering är värdet för WEBSITE_PRIVATE_IP en IP-adress från adress intervallet för det delegerade under nätet och för gateway-nödvändig VNet-integrering är värdet en IP från adress intervallet för den punkt-till-plats-adresspool som kon figurer ATS på Virtual Network Gateway. Detta är den IP-adress som ska användas av webbappen för att ansluta till resurserna via Virtual Network. 

> [!NOTE]
> Värdet för WEBSITE_PRIVATE_IP har bundits för att ändras. Det kommer dock att vara en IP-adress inom adress intervallet för integrations under nätet eller adress intervallet för punkt-till-plats, så du måste tillåta åtkomst från hela adress intervallet.
>

### <a name="gateway-required-vnet-integration-routing"></a>Gateway-nödvändig VNet-integrering routning
Vägarna som definieras i ditt VNet används för att dirigera trafik till ditt VNet från din app. Om du vill skicka ytterligare utgående trafik till VNet lägger du till dessa adress block här. Den här funktionen fungerar bara med Gateway-nödvändig VNet-integrering. Routningstabeller påverkar inte din app-trafik när du använder Gateway-nödvändig VNet-integrering på det sätt som de gör med regional VNet-integration.

### <a name="gateway-required-vnet-integration-certificates"></a>Gateway – nödvändiga VNet-integrerings certifikat
När Gateway-nödvändig VNet-integrering är aktive rad finns det ett nödvändigt utbyte av certifikat för att säkerställa anslutningens säkerhet. Tillsammans med certifikaten är DNS-konfigurationen, vägarna och andra liknande saker som beskriver nätverket.

Om certifikat eller nätverksinformation har ändrats väljer du **Synkronisera nätverk**. När du väljer **Synkronisera nätverk** orsakar du ett kort avbrott i anslutningen mellan appen och ditt VNet. När din app inte startas om kan det leda till att din webbplats inte fungerar korrekt.

## <a name="pricing-details"></a>Prisinformation
Funktionen för regional VNet-integrering har ingen extra avgift för användning utöver App Service plan pris nivå kostnader.

Tre kostnader är relaterade till användningen av den gateway-nödvändiga funktionen för VNet-integrering:

* **App Service plan pris nivå avgifter**: dina appar måste finnas i en standard-, Premium-, PremiumV2-eller PremiumV3-App Service plan. Mer information om dessa kostnader finns [App Service prissättning][ASPricing].
* **Kostnader för data överföring**: det finns en avgift för utgående data, även om VNet finns i samma data Center. Dessa avgifter beskrivs i [dataöverföring pris information][DataPricing].
* **Kostnader för VPN gateway**: det finns en kostnad för den virtuella nätverksgateway som krävs för punkt-till-plats-VPN. Mer information finns i [priser för VPN gateway][VNETPricing].

## <a name="troubleshooting"></a>Felsökning

> [!NOTE]
> VNET-integrering stöds inte för Docker-handskrivna scenarier i App Service.
> Azure Functions åtkomst begränsningar ignoreras om det finns en privat slut punkt.
>

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

CLI-stöd är tillgängligt för regional VNet-integrering. [Installera Azure CLI][installCLI]för att få åtkomst till följande kommandon.

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

PowerShell-stöd för regional VNet-integrering är också tillgängligt, men du måste skapa en allmän resurs med en egenskaps mat ris för under nätets resourceID

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


För gateway-nödvändig VNet-integrering kan du integrera App Service med ett virtuellt Azure-nätverk med hjälp av PowerShell. Ett skript som är klart att köra finns i [ansluta en app i Azure App Service till ett virtuellt Azure-nätverk](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


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
