---
title: 'Snabbstart: Skapa och ändra en krets med ExpressRoute – Azure PowerShell'
description: Den här snabbstarten visar hur du skapar, etablerar, verifierar, uppdaterar, tar bort och avetabler en ExpressRoute-krets.
services: expressroute
author: duongau
ms.author: duau
ms.date: 10/12/2020
ms.topic: quickstart
ms.service: expressroute
ms.custom:
- mode-api
ms.openlocfilehash: 50307e40526f5cce4d1349180b29fac00fb70943
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534954"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit-using-azure-powershell"></a>Snabbstart: Skapa och ändra en ExpressRoute-krets med Azure PowerShell

Den här snabbstarten visar hur du skapar en ExpressRoute-krets med PowerShell-cmdlets och Azure Resource Manager distributionsmodellen. Du kan också kontrollera status, uppdatera, ta bort eller avetablera en krets.

## <a name="prerequisites"></a>Förutsättningar

* Granska kraven [och arbetsflödena](expressroute-prerequisites.md) [innan](expressroute-workflows.md) du påbörjar konfigurationen.
* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure PowerShell lokalt eller Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Skapa och etablera en ExpressRoute-krets
### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Logga in på ditt Azure-konto och välj din prenumeration

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="get-the-list-of-supported-providers-locations-and-bandwidths"></a>Hämta listan över leverantörer, platser och bandbredder som stöds
Innan du skapar en ExpressRoute-krets behöver du en lista över anslutningsleverantörer som stöds, platser och bandbreddsalternativ.

PowerShell-cmdleten **Get-AzExpressRouteServiceProvider** returnerar den här informationen, som du kommer att använda i senare steg:

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

Kontrollera om anslutningsleverantören visas där. Anteckna följande information som du behöver senare när du skapar en krets:

* Name
* PeeringLocations
* BandwidthsOffered

Nu är du redo att skapa en ExpressRoute-krets.

### <a name="create-an-expressroute-circuit"></a>Skapa en ExpressRoute-krets
Om du inte redan har en resursgrupp måste du skapa en innan du skapar expressroute-kretsen. Du kan göra det genom att köra följande kommando:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

I följande exempel visas hur du skapar en ExpressRoute-krets på 200 Mbit/s via Equinix i Silicon Valley. Om du använder en annan provider och olika inställningar ersätter du informationen när du gör din begäran. Använd följande exempel för att begära en ny tjänstnyckel:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Kontrollera att du anger rätt SKU-nivå och SKU-familj:

* SKU-nivån avgör om en ExpressRoute-krets [är Lokal,](expressroute-faqs.md#expressroute-local)Standard eller [Premium.](expressroute-faqs.md#expressroute-premium) Du kan ange *Lokal*, *Standard eller *Premium.* Du kan inte ändra SKU:n från *Standard/Premium* till *Lokal*.
* SKU-familjen avgör faktureringstypen. Du kan ange *MeteredData för* en dataförbrukningsplan och *UnlimitedData för* en obegränsad dataplan. Du kan ändra faktureringstyp från *MeteredData* till *UnlimitedData,* men du kan inte ändra typen från *UnlimitedData* till *MeteredData*. En *lokal* krets är alltid *UnlimitedData*.

> [!IMPORTANT]
> ExpressRoute-kretsen debiteras från den tidpunkt då en tjänstnyckel utfärdas. Se till att du utför den här åtgärden när anslutningsleverantören är redo att etablera kretsen.
>

Svaret innehåller tjänstnyckeln. Du kan få detaljerade beskrivningar av alla parametrar genom att köra följande kommando:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="list-all-expressroute-circuits"></a>Lista alla ExpressRoute-kretsar
Om du vill hämta en lista över alla ExpressRoute-kretsar som du har skapat kör du kommandot **Get-AzExpressRouteCircuit:**

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Svaret ser ut ungefär som i följande exempel:

```azurepowershell
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
CircuitProvisioningState          : Enabled
ServiceProviderProvisioningState  : NotProvisioned
ServiceProviderNotes              :
ServiceProviderProperties         : {
                                    "ServiceProviderName": "Equinix",
                                    "PeeringLocation": "Silicon Valley",
                                    "BandwidthInMbps": 200
                                    }
ServiceKey                        : **************************************
Peerings                          : []
```

Du kan hämta den här informationen när som helst med hjälp av `Get-AzExpressRouteCircuit` cmdleten . Om du gör anropet utan parametrar visas alla kretsar. Din tjänstnyckel visas i fältet *ServiceKey:*

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```

Svaret ser ut ungefär som i följande exempel:

```azurepowershell
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
ServiceProviderProvisioningState : NotProvisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                    "ServiceProviderName": "Equinix",
                                    "PeeringLocation": "Silicon Valley",
                                    "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Skicka tjänstnyckeln till anslutningsleverantören för etablering
*ServiceProviderProvisioningState* innehåller information om det aktuella tillståndet för etablering på tjänstleverantörssidan. Status ger dig tillståndet på Microsoft-sidan. Mer information om kretsetablerings tillstånd finns i [Arbetsflöden.](expressroute-workflows.md#expressroute-circuit-provisioning-states)

När du skapar en ny ExpressRoute-krets har kretsen följande tillstånd:

```azurepowershell
ServiceProviderProvisioningState : NotProvisioned
CircuitProvisioningState         : Enabled
```

Kretsen ändras till följande tillstånd när anslutningsleverantören för närvarande aktiverar den åt dig:

```azurepowershell
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

Om du vill använda ExpressRoute-kretsen måste den ha följande tillstånd:

```azurepowershell
ServiceProviderProvisioningState : Provisioned
CircuitProvisioningState         : Enabled
```

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Kontrollera regelbundet kretsnyckelns status och tillstånd
Om du kontrollerar status och status för tjänstnyckeln får du veta när din leverantör har etablerat kretsen. När kretsen har konfigurerats visas *ServiceProviderProvisioningState* som *Etablerat*, som du ser i följande exempel:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Svaret ser ut ungefär som i följande exempel:

```azurepowershell
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

### <a name="create-your-routing-configuration"></a>Skapa din routningskonfiguration
Stegvisa instruktioner finns i artikeln Konfiguration av [ExpressRoute-kretsroutning](expressroute-howto-routing-arm.md) för att skapa och ändra krets-peerings.

> [!IMPORTANT]
> Dessa instruktioner gäller endast för kretsar som skapas med tjänstleverantörer som erbjuder Layer 2-anslutningstjänster. Om du använder en tjänstleverantör som erbjuder hanterade Layer 3-tjänster (vanligtvis ett IP VPN, t.ex. MPLS), konfigurerar och hanterar anslutningsleverantören routning åt dig.
>

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Länka ett virtuellt nätverk till en ExpressRoute-krets
Länka sedan ett virtuellt nätverk till din ExpressRoute-krets. Använd artikeln [Länka virtuella nätverk till ExpressRoute-kretsar](expressroute-howto-linkvnet-arm.md) när du arbetar med Resource Manager distributionsmodellen.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Hämta status för en ExpressRoute-krets
Du kan hämta den här informationen när som helst med hjälp av cmdleten **Get-AzExpressRouteCircuit.** Om du gör anropet utan parametrar visas alla kretsar.

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```

Svaret liknar följande exempel:

```azurepowershell
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

Du kan få information om en specifik ExpressRoute-krets genom att skicka resursgruppens namn och kretsnamn som en parameter till anropet:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Svaret ser ut ungefär som i följande exempel:

```azurepowershell
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

Du kan få detaljerade beskrivningar av alla parametrar genom att köra följande kommando:

```azurepowershell-interactive
get-help Get-AzExpressRouteCircuit -detailed
```

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Ändra en ExpressRoute-krets
Du kan ändra vissa egenskaper för en ExpressRoute-krets utan att påverka anslutningen.

Du kan utföra följande uppgifter utan avbrottstid:

* Aktivera eller inaktivera ett ExpressRoute Premium-tillägg för din ExpressRoute-krets. Det går inte att ändra  *SKU:n från Standard/Premium* till Lokal.
* Öka bandbredden för din ExpressRoute-krets förutsatt att det finns tillgänglig kapacitet på porten. Nedgradering av bandbredden för en krets stöds inte.
* Ändra avläsningsplanen från Data med dataförbrukning till Obegränsade data. Det går inte att ändra avläsningsplanen från Obegränsade data till Data som mäts.
* Du kan aktivera och inaktivera *Tillåt klassiska åtgärder.*

Mer information om gränser och begränsningar finns i Vanliga frågor [och svar om ExpressRoute.](expressroute-faqs.md)

### <a name="to-enable-the-expressroute-premium-add-on"></a>Så här aktiverar du Premium-tillägget för ExpressRoute
Du kan aktivera ExpressRoute Premium-tillägget för din befintliga krets med hjälp av följande PowerShell-kodavsnitt:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Kretsen har nu ExpressRoute Premium-tilläggsfunktioner aktiverade. Vi börjar fakturera dig för premium-tillägget så snart kommandot har körts.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Så här inaktiverar du Premium-tillägget för ExpressRoute
> [!IMPORTANT]
> Om du använder resurser som är större än vad som tillåts för standardkretsen kan den här åtgärden misslyckas.
>

Notera följande information:

* Innan du nedgraderar från premium till standard måste du se till att antalet virtuella nätverk som är länkade till kretsen är mindre än 10. Om du inte gör det misslyckas din uppdateringsbegäran och vi fakturerar dig till Premium-priser.
* Alla virtuella nätverk i andra geopolitiska regioner måste först avlänkas. Om du inte tar bort länken kommer din uppdateringsbegäran att misslyckas och vi fortsätter att debitera dig enligt premiumpriser.
* Din vägtabell måste vara mindre än 4 000 vägar för privat peering. Om din vägtabellstorlek är större än 4 000 vägar, kommer BGP-sessionen att släppas. BGP-sessionen aktiveras inte igen förrän antalet annonserade prefix är under 4 000.

Du kan inaktivera ExpressRoute Premium-tillägget för den befintliga kretsen med hjälp av följande PowerShell-cmdlet:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Så här uppdaterar du ExpressRoute-kretsens bandbredd
Bandbreddsalternativ som stöds för din leverantör finns i Vanliga frågor [och svar om ExpressRoute.](expressroute-faqs.md) Du kan välja vilken storlek som helst som är större än storleken på din befintliga krets.

> [!IMPORTANT]
> Du kan behöva återskapa ExpressRoute-kretsen om det inte finns tillräckligt med kapacitet på den befintliga porten. Du kan inte uppgradera kretsen om det inte finns någon ytterligare kapacitet tillgänglig på den platsen.
>
> Du kan inte minska bandbredden för en ExpressRoute-krets utan avbrott. Om du vill nedgradera bandbredden måste du avetablera ExpressRoute-kretsen och sedan ometablera en ny ExpressRoute-krets.
>

När du har bestämt vilken storlek du behöver kan du använda följande kommando för att ändra storlek på kretsen:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Kretsen uppgraderas på Microsoft-sidan. Sedan måste du kontakta anslutningsleverantören för att uppdatera konfigurationerna på deras sida för att matcha den här ändringen. När du har aviseringen börjar vi fakturera dig för det uppdaterade bandbreddsalternativet.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Flytta SKU:n från uppmätt till obegränsad
Du kan ändra SKU:n för en ExpressRoute-krets med hjälp av följande PowerShell-kodavsnitt:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Kontrollera åtkomsten till klassiska och Resource Manager miljöer
Läs anvisningarna i [Flytta ExpressRoute-kretsar från den klassiska till den Resource Manager distributionsmodellen](expressroute-howto-move-arm.md).

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>Avetablera en ExpressRoute-krets
Notera följande information:

* Alla virtuella nätverk måste avlänkas från ExpressRoute-kretsen. Om den här åtgärden misslyckas kontrollerar du om några virtuella nätverk är länkade till kretsen.
* Om etableringstillståndet för ExpressRoute-kretsens tjänstleverantör är Etablering eller Etablerat måste du samarbeta med tjänstleverantören för att avetablera kretsen på deras sida.   Vi fortsätter att reservera resurser och fakturera dig tills tjänstleverantören har slutfört avetablering av kretsen och meddelar oss.
* Om tjänstleverantören har avetablera kretsen, vilket innebär att etableringstillståndet för tjänstleverantören anges till **Inte etablerat,** kan du ta bort kretsen. Faktureringen för kretsen stoppas sedan.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort ExpressRoute-kretsen genom att köra följande kommando:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Nästa steg

När du har skapat kretsen och etablerat den med din leverantör fortsätter du till nästa steg för att konfigurera peering:

> [!div class="nextstepaction"]
> [Skapa och ändra routning för din ExpressRoute-krets](expressroute-howto-routing-arm.md)
