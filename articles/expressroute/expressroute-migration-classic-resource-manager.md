---
title: 'Azure-ExpressRoute: Migrera klassiska virtuella nätverk till Resource Manager'
description: Den här sidan beskriver hur du migrerar ExpressRoute virtuella nätverk till Resource Manager när du har flyttat din krets.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: how-to
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 10a2da903ecd59abb7930f9f20c5c905748891cb
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509900"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Migrera ExpressRoute-associerade virtuella nätverk från klassiskt läge till Resource Manager

Den här artikeln förklarar hur du migrerar ExpressRoute virtuella nätverk från den klassiska distributions modellen till Azure Resource Manager distributions modell när du har flyttat din ExpressRoute-krets. 

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Kontrol lera att du har de senaste versionerna av Azure PowerShell-modulerna. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/). Information om hur du installerar PowerShell-modulen för service hantering (som behövs för den klassiska distributions modellen) finns i [installera modulen för Azure PowerShell Service Management](/powershell/azure/servicemanagement/install-azure-ps).
* Kontrol lera att du har granskat [prerequisites](expressroute-prerequisites.md) [kraven, routningsstatus](expressroute-routing.md)och [arbets flöden](expressroute-workflows.md) innan du påbörjar konfigurationen.
* Granska informationen som anges under [flytta en ExpressRoute-krets från klassisk till Resource Manager](expressroute-move.md). Se till att du fullständigt förstår begränsningarna och begränsningarna.
* Kontrol lera att kretsen är fullt fungerande i den klassiska distributions modellen.
* Se till att du har en resurs grupp som har skapats i distributions modellen för Resource Manager.
* Läs följande dokumentation om migrering av resurser:

    * [Plattformsstödd migrering av IaaS-resurser från klassisk till Azure Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-overview.md)
    * [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-deep-dive.md)
    * [Vanliga frågor och svar: migrering av IaaS-resurser som stöds från klassisk till Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-faq.md)
    * [Granska de vanligaste migrerings felen och begränsningarna](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Scenarier som stöds och som inte stöds

* En ExpressRoute-krets kan flyttas från den klassiska till Resource Manager-miljön utan drift avbrott. Du kan flytta en ExpressRoute-krets från den klassiska resursen till Resource Manager-miljön utan drift avbrott. Följ anvisningarna i [Flytta ExpressRoute-kretsar från den klassiska distributions modellen till Resource Manager med hjälp av PowerShell](expressroute-howto-move-arm.md). Detta är ett krav för att flytta resurser som är anslutna till det virtuella nätverket.
* Virtuella nätverk, gatewayer och kopplade distributioner inom det virtuella nätverket som är kopplade till en ExpressRoute-krets i samma prenumeration kan migreras till Resource Manager-miljön utan avbrott. Du kan följa stegen som beskrivs senare för att migrera resurser som virtuella nätverk, gatewayer och virtuella datorer som distribueras i det virtuella nätverket. Du måste se till att de virtuella nätverken är korrekt konfigurerade innan de migreras. 
* Virtuella nätverk, gatewayer och kopplade distributioner inom det virtuella nätverket som inte ingår i samma prenumeration som ExpressRoute-kretsen kräver viss stillestånds tid för att slutföra migreringen. Det sista avsnittet av dokumentet beskriver de steg som ska följas för migrering av resurser.
* Det går inte att migrera ett virtuellt nätverk med både ExpressRoute-gateway och VPN Gateway.
* Migrering av ExpressRoute-krets mellan prenumerationer stöds inte. Mer information finns i [stöd för Microsoft. nätverks flytt](../azure-resource-manager/management/move-support-resources.md#microsoftnetwork).

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Flytta en ExpressRoute-krets från klassisk till Resource Manager
Du måste flytta en ExpressRoute-krets från den klassiska resursen till Resource Manager-miljön innan du försöker migrera resurser som är kopplade till ExpressRoute-kretsen. Information om hur du utför den här uppgiften finns i följande artiklar:

* Granska informationen som anges under [flytta en ExpressRoute-krets från klassisk till Resource Manager](expressroute-move.md).
* [Flytta en krets från klassisk till Resource Manager med hjälp av Azure PowerShell](expressroute-howto-move-arm.md).
* Använd Azure Service Management-portalen. Du kan följa arbets flödet för att [skapa en ny ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) och välja alternativet importera. 

Den här åtgärden omfattar inte stillestånds tid. Du kan fortsätta att överföra data mellan dina lokaler och Microsoft medan migreringen pågår.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Migrera virtuella nätverk, gatewayer och associerade distributioner

De steg som du följer när du migrerar beror på om dina resurser finns i samma prenumeration, olika prenumerationer eller både och.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migrera virtuella nätverk, gatewayer och kopplade distributioner i samma prenumeration som ExpressRoute-kretsen
I det här avsnittet beskrivs de steg som ska följas för att migrera ett virtuellt nätverk, en gateway och associerade distributioner i samma prenumeration som ExpressRoute-kretsen. Ingen nedtid har associerats med den här migreringen. Du kan fortsätta att använda alla resurser genom migreringsprocessen. Hanterings planet är låst medan migreringen pågår. 

1. Se till att ExpressRoute-kretsen har flyttats från den klassiska resursen till Resource Manager-miljön.
2. Se till att det virtuella nätverket har förberetts på lämpligt sätt för migreringen.
3. Registrera prenumerationen för resurs migrering. Om du vill registrera din prenumeration för resursallokering använder du följande PowerShell-kodfragment:

   ```powershell 
   Select-AzSubscription -SubscriptionName <Your Subscription Name>
   Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   ```
4. Verifiera, Förbered och migrera. Använd följande PowerShell-kodfragment för att flytta det virtuella nätverket:

   ```powershell
   Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
   ```

   Du kan också avbryta migreringen genom att köra följande PowerShell-cmdlet:

   ```powershell
   Move-AzureVirtualNetwork -Abort $vnetName
   ```

## <a name="next-steps"></a>Nästa steg
* [Plattformsstödd migrering av IaaS-resurser från klassisk till Azure Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-overview.md)
* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-deep-dive.md)
* [Vanliga frågor och svar: migrering av IaaS-resurser som stöds från klassisk till Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-faq.md)
* [Granska de vanligaste migrerings felen och begränsningarna](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
