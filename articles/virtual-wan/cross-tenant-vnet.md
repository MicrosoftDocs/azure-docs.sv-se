---
title: 'Ansluta mellan klient virtuella nätverk till en hubb: PowerShell'
titleSuffix: Azure Virtual WAN
description: Den här artikeln hjälper dig att ansluta virtuella nätverk för flera klienter till en virtuell hubb med hjälp av PowerShell.
services: virtual-wan
author: wtnlee
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/28/2020
ms.author: wellee
ms.openlocfilehash: 82a5e90221a77b891df78984c7fddfd63b6532aa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585425"
---
# <a name="connect-cross-tenant-vnets-to-a-virtual-wan-hub"></a>Anslut virtuella nätverk för flera innehavare till en virtuell WAN-hubb

Den här artikeln hjälper dig att använda virtuella WAN-nätverk för att ansluta ett VNet till en virtuell hubb i en annan klient. Den här arkitekturen är användbar om du har klient arbets belastningar som måste vara anslutna till samma nätverk, men som finns på olika klienter. Som du ser i följande diagram kan du till exempel ansluta ett virtuellt nätverk (fjärran sluten) till ett contoso Virtual Hub (den överordnade klient organisationen).

:::image type="content" source="./media/cross-tenant-vnet/connectivity.png" alt-text="Konfigurera konfiguration av Routning" :::

I den här artikeln kan du se hur du:

* Lägg till en annan klient som deltagare i din Azure-prenumeration.
* Anslut ett virtuellt nätverk mellan innehavare till en virtuell hubb.

Stegen för den här konfigurationen utförs med en kombination av Azure Portal och PowerShell. Själva funktionen är dock bara tillgänglig i PowerShell och CLI.

## <a name="before-you-begin"></a>Innan du börjar

### <a name="prerequisites"></a>Förutsättningar

Om du vill använda stegen i den här artikeln måste du ha följande konfiguration som redan har kon figurer ATS i din miljö:

* En virtuell WAN-och virtuell hubb i din överordnade prenumeration.
* Ett virtuellt nätverk som kon figurer ATS i en prenumeration i en annan klient (Remote).
* Kontrol lera att VNet-adressutrymmet i fjärr klienten inte överlappar något annat adress utrymme i någon annan virtuella nätverk som redan är ansluten till den överordnade virtuella hubben.

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="assign-permissions"></a><a name="rights"></a>Tilldela behörigheter

För att den överordnade prenumerationen med den virtuella hubben ska kunna ändra och komma åt de virtuella nätverken i fjärr klienten, måste du tilldela **deltagar** behörighet till din överordnade prenumeration från fjärr anslutnings prenumerationen.

1. Lägg till roll tilldelningen **deltagare** i det överordnade kontot (det med den virtuella WAN-hubben). Du kan använda antingen PowerShell eller Azure Portal för att tilldela den här rollen. Se följande artiklar om **Lägg till eller ta bort roll tilldelningar** för steg:

   * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
   * [Portal](../role-based-access-control/role-assignments-portal.md)

1. Lägg sedan till fjärr klient prenumerationen och den överordnade klient prenumerationen i den aktuella sessionen av PowerShell. Kör följande kommando. Om du är inloggad på överordnad behöver du bara köra kommandot för fjärr klienten.

   ```azurepowershell-interactive
   Add-AzAccount -SubscriptionId "xxxxx-b34a-4df9-9451-4402dcaecc5b"
   ```

1. Verifiera att roll tilldelningen lyckas genom att logga in på Azure PowerShell att använda de överordnade autentiseringsuppgifterna och köra följande kommando:

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. Om behörigheterna har spridits till överordnad och har lagts till i sessionen, visas den prenumeration som ägs av fjärr klienten i kommandots utdata.

## <a name="connect-vnet-to-hub"></a><a name="connect"></a>Anslut VNet till hubb

I följande steg växlar du mellan kontexten för de två prenumerationerna när du länkar det virtuella nätverket till den virtuella hubben. Ersätt exempel värdena så att de återspeglar din egen miljö.

1. Kontrol lera att du är i kontexten för ditt fjärrkonto genom att köra följande kommando:

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[remote ID]"
   ```

1. Skapa en lokal variabel för att lagra metadata för det virtuella nätverk som du vill ansluta till hubben.

   ```azurepowershell-interactive
   $remote = Get-AzVirtualNetwork -Name "[vnet name]" -ResourceGroupName "[resource group name]"
   ```

1. Växla tillbaka till det överordnade kontot.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[parent ID]"
   ```

1. Anslut VNet till hubben.

   ```azurepowershell-interactive
   New-AzVirtualHubVnetConnection -ResourceGroupName "[parent resource group name]" -VirtualHubName "[virtual hub name]" -Name "[name of connection]" -RemoteVirtualNetwork $remote
   ```

1. Du kan visa den nya anslutningen antingen i PowerShell eller i Azure Portal.

   * **PowerShell:** Metadata från den nyligen utformade anslutningen visas i PowerShell-konsolen om anslutningen har skapats.
   * **Azure Portal:** Navigera till den virtuella hubben, **anslutnings > Virtual Network anslutningar**. Du kan visa pekaren för anslutningen. Om du vill se den faktiska resursen behöver du rätt behörighet.
   
## <a name="troubleshooting"></a><a name="troubleshoot"></a>Telefonbaserad

* Kontrol lera att metadata i $remote (från föregående [avsnitt](#connect)) matchar informationen från Azure Portal.
* Du kan kontrol lera behörigheter med hjälp av IAM-inställningarna i resurs gruppen för fjärran sluten klient eller använda Azure PowerShell kommandon (Get-AzSubscription).
* Se till att citat tecken ingår runt namnen på resurs grupper eller andra miljövariabler (t. ex. "VirtualHub1" eller "VirtualNetwork1").

## <a name="next-steps"></a>Nästa steg

Mer information om virtuellt WAN finns i:

* [Vanliga frågor och svar](virtual-wan-faq.md) om virtuella WAN
