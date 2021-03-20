---
title: Skapa och hantera Azure ExpressRoute offentlig peering
description: Lär dig mer om och hantera Azures offentliga peering
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: duau
ms.openlocfilehash: 477145619e1b4d8b41c422389b57a46615597478
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92202556"
---
# <a name="create-and-manage-expressroute-public-peering"></a>Skapa och hantera ExpressRoute offentlig peering

> [!div class="op_single_selector"]
> * [Artikel – offentlig peering](about-public-peering.md)
> * [Video – offentlig peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Artikel – Microsoft-peering](expressroute-circuit-peerings.md#microsoftpeering)
>

Den här artikeln hjälper dig att skapa och hantera konfiguration av offentlig peering-routning för en ExpressRoute-krets. Du kan också kontrol lera status, uppdatera eller ta bort och avetablera peer-kopplingar. Den här artikeln gäller Resource Manager-kretsar som skapades innan offentlig peering var inaktuell. Om du har en tidigare befintlig krets (som skapats innan offentlig peering är inaktuell), kan du hantera/konfigurera offentlig peering med hjälp av [Azure PowerShell](#powershell), [Azure CLI](#cli)och [Azure Portal](#portal).

>[!NOTE]
>Offentlig peering är föråldrad. Du kan inte skapa offentlig peering på nya ExpressRoute-kretsar. Om du har en ny ExpressRoute-krets använder du i stället [Microsoft-peering](expressroute-circuit-peerings.md#microsoftpeering) för dina Azure-tjänster.
>

## <a name="connectivity"></a>Anslutning

Anslutningen initieras alltid från WAN till Microsoft Azure-tjänster. Microsoft Azure Services kommer inte att kunna initiera anslutningar till nätverket via den här routningsdomänen. Om din ExpressRoute-krets är aktive rad för offentlig Azure-peering, kan du komma åt de [offentliga IP-adressintervall som används i Azure](../virtual-network/public-ip-addresses.md#public-ip-addresses) över kretsen.

När offentlig peering har Aktiver ATS kan du ansluta till de flesta Azure-tjänster. Vi tillåter inte att du selektivt väljer tjänster som vi annonserar vägar till.

* Tjänster som Azure Storage, SQL-databaser och webbplatser erbjuds på offentliga IP-adresser.
* Via den offentliga peering-routningsdomänen kan du privat ansluta till tjänster som finns på offentliga IP-adresser, inklusive VIP: er för dina moln tjänster.
* Du kan ansluta den offentliga peering-domänen till din DMZ och ansluta till alla Azure-tjänster på sina offentliga IP-adresser från ditt WAN utan att behöva ansluta via Internet.

## <a name="services"></a><a name="services"></a>Tjänster

I det här avsnittet visas de tjänster som är tillgängliga via offentlig peering. Eftersom offentlig peering är inaktuell finns det ingen plan för att lägga till nya eller ytterligare tjänster till offentlig peering. Om du använder offentlig peering och tjänsten som du vill använda bara stöds via Microsoft-peering, måste du växla till Microsoft-peering. Se [Microsoft-peering](expressroute-faqs.md#microsoft-peering) för en lista över tjänster som stöds.

**Tillåtna**

* Power BI
* De flesta Azure-tjänster stöds. Markera direkt med den tjänst som du vill använda för att verifiera stödet.

**Stöds inte:**
  * CDN
  * Azure Front Door
  * Multi-Factor Authentication-Server (bakåtkompatibelt)
  * Traffic Manager

Om du vill verifiera tillgänglighet för en speciell tjänst kan du kontrol lera dokumentationen för tjänsten för att se om det finns ett reserverat intervall publicerat för tjänsten. Sedan kan du leta upp IP-intervallen för mål tjänsten och jämföra med de intervall som anges i [Azure IP-intervall och service Tags – XML-fil för offentliga moln](https://www.microsoft.com/download/details.aspx?id=56519). Du kan också öppna ett support ärende för tjänsten i fråga för att klargöra.

## <a name="peering-comparison"></a><a name="compare"></a>Jämförelse av peering

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

> [!NOTE]
> Offentlig Azure-peering har en NAT-IP-adress kopplad till varje BGP-session. För fler än 2 NAT IP-adresser flyttar du till Microsoft-peering. Med Microsoft-peering kan du konfigurera dina egna NAT-allokeringar, samt använda väg filter för annonser av selektivt prefix. Mer information finns i [Flytta till Microsoft-peering](./how-to-move-peering.md).
>

## <a name="custom-route-filters"></a>Anpassade väg filter

Du kan definiera anpassade väg filter i nätverket så att de bara använder de vägar du behöver. På sidan [routning](expressroute-routing.md) hittar du detaljerad information om konfiguration av routning.

## <a name="azure-powershell-steps"></a><a name="powershell"></a>Azure PowerShell steg


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

Eftersom offentlig peering är inaktuell kan du inte konfigurera offentlig peering på en ny ExpressRoute-krets.

1. Kontrol lera att du har en ExpressRoute-krets som är etablerad och också aktive rad. Använd följande exempel:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   Svaret liknar följande exempel:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                      "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
2. Konfigurera Azures offentliga peering för kretsen. Se till att du har följande information innan du fortsätter.

   * Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
   * Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
   * Valfritt:
   * En MD5-hash om du väljer att använda en.

   Kör följande exempel för att konfigurera Azures offentliga peering för din krets

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Om du väljer att använda en MD5-hash använder du följande exempel:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > Kontrollera att du anger AS-talet som peering-ASN, inte kund-ASN.
   > 
   >

### <a name="to-get-azure-public-peering-details"></a><a name="getpublic"></a>Så här hämtar du information om offentliga Azure-peering

Du kan hämta konfigurations information med följande cmdlet:

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Så här uppdaterar du konfigurationen av Azures offentliga peering

Du kan uppdatera valfri del av konfigurationen med hjälp av följande exempel. I det här exemplet uppdateras VLAN-ID: t för kretsen från 200 till 600.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Så här tar du bort Azures offentliga peering

Du kan ta bort peering-konfigurationen genom att köra följande exempel:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-cli-steps"></a><a name="cli"></a>Azure CLI-steg


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

1. Kontrol lera ExpressRoute-kretsen för att säkerställa att den är etablerad och också aktive rad. Använd följande exempel:

   ```azurecli-interactive
   az network express-route list
   ```

   Svaret liknar följande exempel:

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

2. Konfigurera Azures offentliga peering för kretsen. Se till att du har följande information innan du fortsätter.

   * Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
   * Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
   * **Valfritt-** En MD5-hash om du väljer att använda en.

   Kör följande exempel för att konfigurera Azures offentliga peering för din krets:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   Om du väljer att använda en MD5-hash använder du följande exempel:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Kontrollera att du anger AS-talet som peering-ASN, inte kund-ASN.

### <a name="to-view-azure-public-peering-details"></a><a name="getpublic"></a>Så här visar du Azures offentliga peering-information

Du kan hämta konfigurations information med hjälp av följande exempel:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

Utdata ser ut ungefär så här:

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Så här uppdaterar du konfigurationen av Azures offentliga peering

Du kan uppdatera valfri del av konfigurationen med hjälp av följande exempel. I det här exemplet uppdateras VLAN-ID: t för kretsen från 200 till 600.

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Så här tar du bort Azures offentliga peering

Du kan ta bort peering-konfigurationen genom att köra följande exempel:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="azure-portal-steps"></a><a name="portal"></a>Azure Portal steg

Om du vill konfigurera peering använder du de PowerShell-eller CLI-steg som finns i den här artikeln. Om du vill hantera en peering kan du använda avsnitten nedan. De här stegen ser ut ungefär så här när du hanterar en [Microsoft-peering i portalen](expressroute-howto-routing-portal-resource-manager.md#msft).

### <a name="to-view-azure-public-peering-details"></a><a name="get"></a>Så här visar du Azures offentliga peering-information

Visa egenskaperna för Azures offentliga peering genom att välja peering i portalen.

### <a name="to-update-azure-public-peering-configuration"></a><a name="update"></a>Så här uppdaterar du konfigurationen av Azures offentliga peering

Välj raden för peering och ändra sedan peering-egenskaperna.

### <a name="to-delete-azure-public-peering"></a><a name="delete"></a>Så här tar du bort Azures offentliga peering

Ta bort peering-konfigurationen genom att välja ikonen Ta bort.

## <a name="next-steps"></a>Nästa steg

Nästa steg är [att länka ett virtuellt nätverk till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md).

* Mer information om ExpressRoute-arbetsflöden finns i [ExpressRoute-arbetsflöden](expressroute-workflows.md).
* Mer information om krets-peering finns i [ExpressRoute-kretsar och routningsdomäner](expressroute-circuit-peerings.md).
* Mer information om hur du arbetar med virtuella nätverk finns i [Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md).