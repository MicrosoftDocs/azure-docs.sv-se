---
title: Kontrol lera användningen av Azure-resurser mot begränsningar | Microsoft Docs
description: Lär dig hur du kontrollerar din Azure-resursanvändning mot Azures prenumerations gränser.
services: networking
documentationcenter: na
author: KumudD
ms.author: kumud
tags: azure-resource-manager
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.openlocfilehash: 31eeb31fb78a4e9552e64121e0e85b5fd8d9b773
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210656"
---
# <a name="check-resource-usage-against-limits"></a>Kontrollera och jämföra resursanvändningen mot gränser

I den här artikeln får du lära dig hur du visar antalet nätverks resurs typer som du har distribuerat i din prenumeration och vilka [prenumerations begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) som finns. Möjligheten att Visa resursanvändningen mot gränser är användbart för att spåra aktuell användning och planera för framtida användning. Du kan använda [Azure Portal](#azure-portal), [POWERSHELL](#powershell)eller [Azure CLI](#azure-cli) för att spåra användningen.

## <a name="azure-portal"></a>Azure-portalen

1. Logga in på Azure- [portalen](https://portal.azure.com).
2. I det övre vänstra hörnet av Azure Portal väljer du **alla tjänster**.
3. Ange *prenumerationer* i **filter** rutan. När **Prenumerationer** visas bland sökresultaten väljer du posten.
4. Välj namnet på den prenumeration som du vill visa användnings information för.
5. Under **Inställningar** väljer du **användning + kvot**.
6. Följande alternativ finns:
   - **Resurs typer**: du kan välja alla resurs typer eller välja vilka typer av resurser som du vill visa.
   - **Providers**: du kan välja alla resurs leverantörer eller välja **Compute**, **nätverk** eller **lagring**.
   - **Platser**: du kan välja alla Azure-platser eller välja vissa platser.
   - Du kan välja att visa alla resurser eller bara de resurser där minst en har distribuerats.

     Exemplet på följande bild visar alla nätverks resurser med minst en resurs som distribuerats i USA, östra:

       ![Visa användnings data](./media/check-usage-against-limits/view-usage.png)

     Du kan sortera kolumnerna genom att välja kolumn rubriken. De gränser som visas är gränserna för din prenumeration. Om du behöver öka en standard gräns väljer du **begär ökning** och Slutför och skicka support förfrågan. Alla resurser har en övre gräns som anges i Azure- [gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits). Om din aktuella gräns redan har nått Max värdet går det inte att öka gränsen.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan köra kommandona som följer i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett kostnads fritt interaktivt gränssnitt. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn behöver du Azure PowerShell-modulen, version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` på datorn för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzAccount` för att logga in på Azure.

Visa din användning mot gränser med [Get-AzNetworkUsage](/powershell/module/az.network/get-aznetworkusage). I följande exempel hämtas användningen av resurser där minst en resurs distribueras på platsen USA, östra:

```azurepowershell-interactive
Get-AzNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

Du får utdata formaterade på samma sätt som i följande exempel på utdata:

```output
ResourceType            CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
```

## <a name="azure-cli"></a>Azure CLI

Om du använder kommando rads kommandon i Azure för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. Den här artikeln kräver Azure CLI version 2.0.32 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också köra `az login` för att logga in på Azure.

Visa din användning mot gränser med [AZ Network List-usages](/cli/azure/network#az-network-list-usages). I följande exempel hämtas användningen av resurser på platsen USA, östra:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

Du får utdata formaterade på samma sätt som i följande exempel på utdata:

```output
Name                    CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
Load Balancers                     0   100
Application Gateways               0    50
```