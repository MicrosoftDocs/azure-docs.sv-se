---
title: 'Azure-ExpressRoute: flytta klassiska kretsar till Resource Manager'
description: Den här sidan beskriver hur du flyttar en klassisk krets till distributions modellen för Resource Manager med hjälp av PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 460ea446fc6dfc43e81a1a57bbba032a61f3a72d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90532553"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>Flytta ExpressRoute-kretsar från klassisk till Resource Manager-distributions modell med PowerShell

Om du vill använda en ExpressRoute-krets för både den klassiska distributions modellen och Resource Manager-distributions modellerna måste du flytta kretsen till distributions modellen för Resource Manager. Följande avsnitt hjälper dig att flytta din krets med hjälp av PowerShell.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

* Kontrol lera att du har installerat både de klassiska och AZ-Azure PowerShell modulerna lokalt på datorn. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/).
* Kontrol lera att du har granskat [](expressroute-prerequisites.md) [kraven, routningsstatus](expressroute-routing.md)och [arbets flöden](expressroute-workflows.md) innan du påbörjar konfigurationen.
* Granska informationen som anges under [flytta en ExpressRoute-krets från klassisk till Resource Manager](expressroute-move.md). Se till att du fullständigt förstår begränsningarna och begränsningarna.
* Kontrol lera att kretsen är fullt fungerande i den klassiska distributions modellen.
* Se till att du har en resurs grupp som har skapats i distributions modellen för Resource Manager.

## <a name="move-an-expressroute-circuit"></a>Flytta en ExpressRoute-krets

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Steg 1: samla in krets information från den klassiska distributions modellen

Logga in på den klassiska Azure-miljön och samla in tjänst nyckeln.

1. Logga in på ditt Azure-konto.

   ```powershell
   Add-AzureAccount
   ```

2. Välj lämplig Azure-prenumeration.

   ```powershell
   Select-AzureSubscription "<Enter Subscription Name here>"
   ```

3. Importera PowerShell-modulerna för Azure och ExpressRoute.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
   ```

4. Använd cmdleten nedan för att hämta tjänst nycklarna för alla dina ExpressRoute-kretsar. När nycklarna har hämtats kopierar du **tjänst nyckeln** för den krets som du vill flytta till distributions modellen för Resource Manager.

   ```powershell
   Get-AzureDedicatedCircuit
   ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Steg 2: Logga in och skapa en resurs grupp

Logga in i Resource Manager-miljön och skapa en ny resurs grupp.

1. Logga in på din Azure Resource Managers miljö.

   ```powershell
   Connect-AzAccount
   ```

2. Välj lämplig Azure-prenumeration.

   ```powershell
   Get-AzSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzSubscription
   ```

3. Ändra kodfragmentet nedan om du vill skapa en ny resurs grupp om du inte redan har en resurs grupp.

   ```powershell
   New-AzResourceGroup -Name "DemoRG" -Location "West US"
   ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Steg 3: flytta ExpressRoute-kretsen till distributions modellen för Resource Manager

Du är nu redo att flytta din ExpressRoute-krets från den klassiska distributions modellen till distributions modellen för Resource Manager. Innan du fortsätter kan du läsa informationen som visas i [flytta en ExpressRoute-krets från den klassiska distributions modellen till Resource Manager](expressroute-move.md).

Ändra och kör följande kodfragment för att flytta din krets:

```powershell
Move-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

I klassiskt läge har en ExpressRoute-krets inte konceptet att vara knuten till en region. I Resource Manager måste du dock mappas varje resurs till en Azure-region. Den region som anges i Move-AzExpressRouteCircuit cmdleten kan tekniskt vara vilken region som helst. I organisatoriskt syfte kanske du vill välja en region som står nära din peering-plats.

> [!NOTE]
> * När du har flyttat din klassiska ExpressRoute-krets till distributions modellen Resource Manager, kommer den att ha åtkomst till både klassiska distributions modeller för och Resource Manager som standard.
> * Det nya namnet som anges i föregående cmdlet kommer att användas för att adressera resursen. Kretsen kommer i huvudsak att byta namn.

## <a name="modify-circuit-access"></a>Ändra krets åtkomst

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>Så här aktiverar du ExpressRoute krets åtkomst för båda distributions modellerna

Du kan aktivera åtkomst till den klassiska distributions modellen för ExpressRoute-kretsar som har skapats i distributions modellen för Resource Manager. Kör följande cmdlets för att ge åtkomst till båda distributions modellerna:

1. Hämta krets information.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Ange "Tillåt klassiska åtgärder" till TRUE.

   ```powershell
   $ckt.AllowClassicOperations = $true
   ```

3. Uppdatera kretsen. När åtgärden har slutförts kommer du att kunna visa kretsen i den klassiska distributions modellen.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

4. Kör följande cmdlet för att hämta information om ExpressRoute-kretsen. Du måste kunna se tjänst nyckeln som visas.

   ```powershell
   get-azurededicatedcircuit
   ```

5. Nu kan du hantera länkar till ExpressRoute-kretsen med hjälp av de klassiska distributions modell kommandona för klassiska virtuella nätverk och Resource Manager-kommandona för Resource Manager-virtuella nätverk. Följande artiklar hjälper dig att hantera länkar till ExpressRoute-kretsen:

    * [Länka ditt virtuella nätverk till din ExpressRoute-krets i distributions modellen för Resource Manager](expressroute-howto-linkvnet-arm.md)
    * [Länka ditt virtuella nätverk till din ExpressRoute-krets i den klassiska distributions modellen](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>Så här inaktiverar du ExpressRoute-krets åtkomst till den klassiska distributions modellen

Kör följande cmdlets för att inaktivera åtkomst till den klassiska distributions modellen.

1. Hämta information om ExpressRoute-kretsen.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Ange "Tillåt klassiska åtgärder" till FALSe.

   ```powershell
   $ckt.AllowClassicOperations = $false
   ```

3. Uppdatera kretsen. När åtgärden har slutförts går det inte att Visa kretsen i den klassiska distributions modellen.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

## <a name="next-steps"></a>Nästa steg

* [Skapa och ändra routning för din ExpressRoute-krets](expressroute-howto-routing-arm.md)
* [Länka ditt virtuella nätverk till din ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)
