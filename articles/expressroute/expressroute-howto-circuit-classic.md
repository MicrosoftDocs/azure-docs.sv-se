---
title: 'Azure-ExpressRoute: ändra en krets: PowerShell: klassisk'
description: Den här artikeln vägleder dig igenom stegen för att kontrol lera status, uppdatera eller ta bort och avetablera din ExpressRoute klassiska distributions modell krets.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/05/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: be45d49d3f445810c7ac6a38e3e12abe178a4bed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018196"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Ändra en ExpressRoute-krets med PowerShell (klassisk)

> [!div class="op_single_selector"]
> * [Azure-portalen](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-mall](expressroute-howto-circuit-resource-manager-template.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-circuit-classic.md)
>

Den här artikeln vägleder dig igenom stegen för att kontrol lera status, uppdatera eller ta bort och avetablera din ExpressRoute klassiska distributions modell krets. Den här artikeln gäller den klassiska distributionsmodellen.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Om Azures distributionsmodeller**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="get-the-status-of-a-circuit"></a>Hämta status för en krets

Du kan hämta den här informationen när som helst genom att använda `Get-AzureCircuit` cmdleten. Om du gör anropet utan några parametrar visas alla kretsar.

```powershell
Get-AzureDedicatedCircuit

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled

Bandwidth                        : 1000
CircuitName                      : MyAsiaCircuit
Location                         : Singapore
ServiceKey                       : #################################
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Du kan hämta information om en speciell ExpressRoute-krets genom att skicka tjänst nyckeln som en parameter till anropet.

```powershell
Get-AzureDedicatedCircuit -ServiceKey "*********************************"

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Du kan få detaljerade beskrivningar av alla parametrar genom att köra följande exempel:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Ändra en krets

Du kan ändra vissa egenskaper för en ExpressRoute-krets utan att påverka anslutningen.

Du kan utföra följande uppgifter utan avbrott:

* Aktivera eller inaktivera ett ExpressRoute Premium-tillägg för din ExpressRoute-krets.
* Öka bandbredden för din ExpressRoute-krets om det finns tillgänglig kapacitet på porten. Nedgradering av en kretss bandbredd stöds inte.
* Ändra Mät planen från datapriser till obegränsade data. Det finns inte stöd för att ändra Mät planen från obegränsade data till datapriser.
* Du kan aktivera och inaktivera *Tillåt klassiska åtgärder*.

Läs [vanliga frågor och svar om ExpressRoute](expressroute-faqs.md) för mer information om begränsningar och begränsningar.

### <a name="enable-the-expressroute-premium-add-on"></a>Aktivera ExpressRoute Premium-tillägget

Du kan aktivera ExpressRoute Premium-tillägget för din befintliga krets genom att använda följande PowerShell-cmdlet:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Premium
Status                           : Enabled
```

Din krets kommer nu att ha ExpressRoute Premium-funktionerna aktiverade. När kommandot har körts slutförs faktureringen för Premium-tilläggs funktionen.

### <a name="disable-the-expressroute-premium-add-on"></a>Inaktivera ExpressRoute Premium-tillägget

> [!IMPORTANT]
> Den här åtgärden kan inte utföras om du använder resurser som är större än vad som tillåts för standard kretsen.
>
>

#### <a name="considerations"></a>Överväganden

* Kontrol lera att antalet virtuella nätverk som är länkade till kretsen är mindre än 10 innan du degraderar från Premium till standard. Om du inte gör det Miss lyckas din uppdateringsbegäran och du debiteras premie priserna.
* Du måste ta bort länken mellan alla virtuella nätverk i andra politiska regioner. Om du inte gör det Miss lyckas uppdateringsbegäran och du debiteras premie priserna.
* Routningstabellen måste vara mindre än 4 000 vägar för privat peering. Om din väg tabell storlek är större än 4 000 vägar släpps BGP-sessionen och aktive ras inte igen förrän antalet annonserade prefix går under 4 000.

#### <a name="to-disable-the-premium-add-on"></a>Så här inaktiverar du Premium-tillägget

Du kan inaktivera ExpressRoute Premium-tillägget för din befintliga krets genom att använda följande PowerShell-cmdlet:

```powershell

Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

### <a name="update-the-expressroute-circuit-bandwidth"></a>Uppdatera ExpressRoute-kretsens bandbredd

Se [vanliga frågor och svar](expressroute-faqs.md) om vilka bandbredds alternativ som stöds för din Provider i ExpressRoute. Du kan välja valfri storlek som är större än den befintliga kretsens storlek så länge den fysiska porten (som din krets har skapats på) tillåter.

> [!IMPORTANT]
> Du kan behöva återskapa ExpressRoute-kretsen om det inte finns tillräckligt med kapacitet på den befintliga porten. Du kan inte uppgradera kretsen om det inte finns någon ytterligare kapacitet tillgänglig på den platsen.
>
> Du kan inte minska bandbredden för en ExpressRoute-krets utan avbrott. Med degradering av bandbredd måste du avetablera ExpressRoute-kretsen och sedan etablera om en ny ExpressRoute-krets.
>
>

#### <a name="resize-a-circuit"></a>Ändra storlek på en krets

När du har bestämt vilken storlek du behöver kan du använda följande kommando för att ändra storlek på kretsen:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

När din krets har ändrats på Microsoft-sidan måste du kontakta anslutnings leverantören för att uppdatera konfigurationerna på sidan för att matcha den här ändringen. Faktureringen börjar för det uppdaterade bandbredds alternativet från den här tidpunkten.

Om du ser följande fel när du ökar krets bandbredden innebär det att det inte finns tillräckligt med bandbredd kvar på den fysiska porten där din befintliga krets skapas. Du måste ta bort den här kretsen och skapa en ny krets av den storlek du behöver.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Avetablera och ta bort en krets

### <a name="considerations"></a>Överväganden

* Du måste ta bort länken mellan alla virtuella nätverk från ExpressRoute-kretsen för att åtgärden ska lyckas. Kontrol lera om det finns några virtuella nätverk som är länkade till kretsen om den här åtgärden Miss lyckas.
* Om etablerings statusen för ExpressRoute-kretsen **etableras** eller **etableras** måste du arbeta med tjänst leverantören för att avetablera kretsen på sin sida. Vi fortsätter att reservera resurser och fakturera dig tills tjänste leverantören har slutfört avetableringen av kretsen och meddelar oss oss.
* Om tjänst leverantören har avetablerat kretsen (etablerings statusen för tjänst leverantören är inställt på **inte etablerad**) kan du ta bort kretsen. Därmed avbryts faktureringen för kretsen.

#### <a name="delete-a-circuit"></a>Ta bort en krets

Du kan ta bort ExpressRoute-kretsen genom att köra följande kommando:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
