---
title: 'Azure-ExpressRoute: länka ett VNet till en krets: klassisk'
description: Det här dokumentet ger en översikt över hur du länkar virtuella nätverk (virtuella nätverk) till ExpressRoute-kretsar med hjälp av den klassiska distributions modellen och PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0e8a1d48934d73f1035c6f2c4c76e56d9a0e4052
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92206978"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Ansluta ett virtuellt nätverk till en ExpressRoute-krets med PowerShell (klassisk)
> [!div class="op_single_selector"]
> * [Azure-portalen](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-linkvnet-classic.md)
>

I den här artikeln får du hjälp med att länka virtuella nätverk (virtuella nätverk) till Azure ExpressRoute-kretsar med PowerShell. Ett enda VNet kan länkas till upp till fyra ExpressRoute-kretsar. Följ stegen i den här artikeln för att skapa en ny länk till varje ExpressRoute-krets som du ansluter till. ExpressRoute-kretsarna kan vara i samma prenumeration, olika prenumerationer eller en blandning av båda. Den här artikeln gäller virtuella nätverk som skapats med den klassiska distributions modellen.

Du kan länka upp till 10 virtuella nätverk till en ExpressRoute-krets. Alla virtuella nätverk måste finnas i samma naturpolitisk region. Du kan länka ett större antal virtuella nätverk till din ExpressRoute-krets eller länka virtuella nätverk som finns i andra politiska regioner om du aktiverar ExpressRoute Premium-tillägget. Läs [vanliga frågor och svar](expressroute-faqs.md) om du vill ha mer information om Premium-tillägget.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Om Azures distributionsmodeller**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Förutsättningar för konfiguration

* Granska kraven [](expressroute-prerequisites.md), kraven för [routning](expressroute-routing.md)och [arbets flöden](expressroute-workflows.md) innan du påbörjar konfigurationen.
* Du måste ha en aktiv ExpressRoute-krets.
   * Följ anvisningarna för att [skapa en ExpressRoute-krets](expressroute-howto-circuit-classic.md) och be anslutnings leverantören aktivera kretsen.
   * Se till att du har konfigurerat Azures privata peering för din krets. Mer information finns i artikeln [om konfigurering av routning](expressroute-howto-routing-classic.md) .
   * Se till att Azures privata peering har kon figurer ATS och BGP-peering mellan ditt nätverk och Microsoft är igång så att du kan aktivera slut punkt till slut punkt.
   * Du måste ha ett virtuellt nätverk och en virtuell nätverksgateway skapad och helt etablerad. Följ anvisningarna för att [Konfigurera ett virtuellt nätverk för ExpressRoute](./expressroute-howto-add-gateway-portal-resource-manager.md).

### <a name="download-the-latest-powershell-cmdlets"></a>Hämta de senaste PowerShell-cmdletarna

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Anslut ett virtuellt nätverk i samma prenumeration till en krets
Du kan länka ett virtuellt nätverk till en ExpressRoute-krets med hjälp av följande cmdlet. Kontrol lera att den virtuella Nätverksgatewayen har skapats och är redo för länkning innan du kör cmdleten.

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>Ta bort ett virtuellt nätverks länk till en krets
Du kan ta bort en virtuell nätverks länk till en ExpressRoute-krets med hjälp av följande cmdlet. Kontrol lera att den aktuella prenumerationen har valts för det angivna virtuella nätverket. 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Anslut ett virtuellt nätverk i en annan prenumeration till en krets
Du kan dela en ExpressRoute-krets över flera prenumerationer. Följande bild visar en enkel Schematisk över hur delning fungerar för ExpressRoute-kretsar över flera prenumerationer.

Vart och ett av de mindre molnen i det stora molnet används för att representera prenumerationer som tillhör olika avdelningar i en organisation. Var och en av avdelningarna i organisationen kan använda sin egen prenumeration för att distribuera tjänsterna – men avdelningarna kan dela en enda ExpressRoute-krets för att ansluta till ditt lokala nätverk. En enda avdelning (i det här exemplet: IT) kan äga ExpressRoute-kretsen. Andra prenumerationer inom organisationen kan använda ExpressRoute-kretsen.

> [!NOTE]
> Anslutnings-och bandbredds avgifter för den dedikerade kretsen kommer att användas för ExpressRoute-kretsens ägare. Alla virtuella nätverk delar samma bandbredd.
> 
> 

![Anslutning mellan prenumerationer](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administration
*Krets ägaren* är administratör/medadministratör för den prenumeration som ExpressRoute-kretsen skapas i. Krets ägaren kan auktorisera administratörer/medadministratörer för andra prenumerationer, vilket kallas *krets användare*, för att använda den dedikerade kretsen som de äger. Krets användare som har behörighet att använda organisationens ExpressRoute-krets kan länka det virtuella nätverket i sin prenumeration till ExpressRoute-kretsen när de har behörighet.

Krets ägaren har möjlighet att ändra och återkalla auktorisering när som helst. Om du återkallar en auktorisering tas alla länkar bort från prenumerationen vars åtkomst har återkallats.

### <a name="circuit-owner-operations"></a>Krets ägare åtgärder

**Skapa en auktorisering**

Krets ägaren godkänner administratörer för andra prenumerationer att använda den angivna kretsen. I följande exempel gör kretsen administratör (contoso IT) administratören för en annan prenumeration (dev-test) för att länka upp till två virtuella nätverk till kretsen. Contoso IT-administratören möjliggör detta genom att ange Dev-Test Microsoft ID. Cmdleten skickar inte e-post till det angivna Microsoft-ID: t. Krets ägaren måste uttryckligen meddela den andra prenumerations ägaren att auktoriseringen är slutförd.

```powershell
New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
```

  Returnera:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : **********************************
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Granska auktoriseringar**

Krets ägaren kan granska alla auktoriseringar som utfärdats på en viss krets genom att köra följande cmdlet:

```powershell
Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
```
  Returnera:

  ```powershell
  Description         : EngineeringTeam
  Limit               : 3
  LinkAuthorizationId : ####################################
  MicrosoftIds        : engadmin@contoso.com
  Used                : 1

  Description         : MarketingTeam
  Limit               : 1
  LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
  MicrosoftIds        : marketingadmin@contoso.com
  Used                : 0

  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : salesadmin@contoso.com
  Used                : 2
  ```

**Uppdaterar auktoriseringar**

Krets ägaren kan ändra auktoriseringar med hjälp av följande cmdlet:

```powershell
Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
```

  Returnera:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 5
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Ta bort auktoriseringar**

Krets ägaren kan återkalla/ta bort auktoriseringarna till användaren genom att köra följande cmdlet:

```powershell
Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"
```

### <a name="circuit-user-operations"></a>Krets användar åtgärder

**Granska auktoriseringar**

Krets användaren kan granska auktoriseringar med hjälp av följande cmdlet:

```powershell
Get-AzureAuthorizedDedicatedCircuit
```

  Returnera:

  ```powershell
  Bandwidth                        : 200
  CircuitName                      : ContosoIT
  Location                         : Washington DC
  MaximumAllowedLinks              : 2
  ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  ServiceProviderName              : equinix
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  UsedLinks                        : 0
  ```

**Lösa in länk auktoriseringar**

Krets användaren kan köra följande cmdlet för att lösa in en länk auktorisering:

```powershell
New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'
```

  Returnera:

  ```powershell
  State VnetName
  ----- --------
  Provisioned SalesVNET1
  ```

Kör det här kommandot i den nyligen länkade prenumerationen för det virtuella nätverket:

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```

## <a name="next-steps"></a>Nästa steg

Mer information om ExpressRoute finns i [vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).