---
title: 'Registrera en peering service-anslutning – Azure PowerShell '
description: I den här självstudien får du lära dig hur du registrerar en peering service-anslutning med PowerShell.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: ed4f628f6c3b888a640d61048c39f945a81901d7
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060847"
---
# <a name="tutorial-register-a-peering-service-connection-using-azure-powershell"></a>Självstudie: registrera en peering service-anslutning med hjälp av Azure PowerShell

I den här självstudien får du lära dig hur du registrerar peering-tjänsten med hjälp av Azure PowerShell.

Azure peering-tjänsten är en nätverks tjänst som förbättrar kund anslutningen till Microsofts moln tjänster, till exempel Microsoft 365, Dynamics 365, SaaS-tjänster (Software as a Service), Azure eller Microsoft-tjänster som är tillgängliga via det offentliga Internet. I den här artikeln får du lära dig hur du registrerar en peering service-anslutning med hjälp av Azure PowerShell.

Om du inte har en Azure-prenumeration kan du skapa ett [konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du i stället väljer att installera och använda PowerShell lokalt kräver den här snabbstarten att du använder version 1.0.0 eller senare av Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Information om installation och uppgradering finns i [installera Azure PowerShell modul](/powershell/azure/install-az-ps).

Till sist, om du kör PowerShell lokalt måste du även köra `Connect-AzAccount`. Kommandot skapar en anslutning med Azure.

Använd Azure PowerShell-modulen för att registrera och hantera peering-tjänsten. Du kan registrera eller hantera peering-tjänsten från PowerShell-kommandoraden eller i skript.


## <a name="prerequisites"></a>Förutsättningar  
Du måste ha följande:

### <a name="azure-account"></a>Azure-konto

Du måste ha ett giltigt och aktivt Microsoft Azure-konto. Detta konto krävs för att konfigurera peering-tjänstens anslutning. Peering-tjänsten är en resurs i Azure-prenumerationer.

### <a name="connectivity-provider"></a>Anslutningsleverantör

Du kan arbeta med en Internet-leverantör eller en Internet Exchange-partner för att få peering-tjänsten att ansluta ditt nätverk till Microsoft-nätverket.

Se till att anslutnings leverantörerna är partner kopplade till Microsoft.

### <a name="register-a-subscription-with-the-resource-provider-and-feature-flag"></a>Registrera en prenumeration med resurs leverantören och funktions flaggan

Innan du fortsätter med stegen för att registrera peering-tjänsten registrerar du prenumerationen med resurs leverantören och funktions flaggan med hjälp av Azure PowerShell. Azure PowerShell-kommandon anges här:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowPeeringService ProviderNamespace Microsoft.Peering 

Register-AzResourceProvider -ProviderNamespace Microsoft.Peering 

```

### <a name="fetch-the-location-and-service-provider"></a>Hämta plats och tjänst leverantör 

Kör följande kommandon i Azure PowerShell för att hämta platsen och tjänst leverantören som peering-tjänsten ska vara aktive rad för. 

Hämta peering service-platser:

```azurepowershell-interactive
# Gets a list of available countries
Get-AzPeeringServiceCountry 
# Gets a list of metro locations serviced by country
Get-AzPeeringServiceLocation -Country "United States"
```

Hämta peering service-leverantörer:
              
```azurepowershell-interactive
Get-AzPeeringServiceProvider
```

### <a name="register-the-peering-service-connection"></a>Registrera peering-tjänstens anslutning

Registrera peering-tjänstens anslutning med hjälp av följande kommando uppsättningar via Azure PowerShell. I det här exemplet registreras peering-tjänsten med namnet myPeeringService.

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
```

### <a name="register-the-peering-service-prefix"></a>Registrera peering service-prefixet

Registrera prefixet som tillhandahålls av anslutnings leverantören genom att köra följande kommandon via Azure PowerShell. Det här exemplet registrerar prefixet prefixet med namnet prefix.

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
$prefixName = "myPrefix"
$prefix = “192.168.1.0/24”
$serviceKey = "6f48cdd6-2c2e-4722-af89-47e27b2513af"
$prefixService = $peeringService | New-AzPeeringServicePrefix -Name $prefixName -Prefix $prefix -ServiceKey $serviceKey
```

### <a name="list-all-the-peering-services-connections"></a>Visa en lista över alla anslutningar för peering Services

Kör följande kommando om du vill visa en lista över alla peering-tjänster:

```azurepowershell-interactive
$peeringService = Get-AzPeeringService
```

### <a name="list-all-the-peering-service-prefixes"></a>Visa en lista över alla peering service-prefix

Om du vill visa en lista över alla peering service-prefix kör du följande kommando:

```azurepowershell-interactive
 $prefixName = "myPrefix"
```

```azurepowershell-interactive
$prefix = Get-AzPeeringServicePrefix -PeeringServiceName "myPeeringService" -ResourceGroupName "MyResourceGroup" -Name "myPrefix"
```

### <a name="remove-the-peering-service-prefix"></a>Ta bort peering service-prefixet

Om du vill ta bort peering service-prefixet kör du följande kommando:

```azurepowershell-interactive
Remove-AzPeeringServicePrefix -ResourceGroupName  "MyResourceGroup" -Name "myPrefix" -PeeringServiceName "myPeeringService"
```

## <a name="next-steps"></a>Nästa steg

- Mer information om peering service-anslutning finns i [peering service-anslutning](connection.md).
- Om du vill veta mer om telemetri för peering service-anslutning läser du [telemetri för peering service-anslutning](connection-telemetry.md).
- Om du vill registrera en peering service-anslutning med hjälp av Azure Portal, se [Registrera en peering service-anslutning-Azure Portal](azure-portal.md).
- Information om hur du registrerar en peering service-anslutning med hjälp av Azure CLI finns i [Registrera en peering service-anslutning – Azure CLI](cli.md).
