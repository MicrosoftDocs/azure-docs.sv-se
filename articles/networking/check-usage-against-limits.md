---
title: Kontrollera Azure-resursanvändningen mot begränsningar | Microsoft Docs
description: Lär dig hur du kontrollerar din Azure-resursanvändning mot azure-prenumerationsgränser.
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
ms.openlocfilehash: d629e65106145a4af364cd9dd489250c8910c25d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778579"
---
# <a name="check-resource-usage-against-limits"></a>Kontrollera och jämföra resursanvändningen mot gränser

I den här artikeln får du lära dig hur du ser antalet nätverksresurstyper som du har distribuerat i din prenumeration och vilka dina [prenumerationsgränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) är. Möjligheten att visa resursanvändning mot gränser är användbar för att spåra aktuell användning och planera för framtida användning. Du kan använda [Azure Portal,](#azure-portal) [PowerShell](#powershell)eller [Azure CLI för](#azure-cli) att spåra användningen.

## <a name="azure-portal"></a>Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Längst upp till vänster i Azure Portal väljer du **Alla tjänster.**
3. Ange *Prenumerationer* i **rutan** Filter. När **Prenumerationer** visas bland sökresultaten väljer du posten.
4. Välj namnet på den prenumeration som du vill visa användningsinformation för.
5. Under **INSTÄLLNINGAR** väljer du **Användning + kvot.**
6. Följande alternativ finns:
   - **Resurstyper:** Du kan välja alla resurstyper eller de specifika typer av resurser som du vill visa.
   - **Providers**: Du kan välja alla resursproviders eller **beräkning,** **nätverk** eller **lagring.**
   - **Platser:** Du kan välja alla Azure-platser eller specifika platser.
   - Du kan välja att visa alla resurser eller bara de resurser där minst en har distribuerats.

     Exemplet i följande bild visar alla nätverksresurser med minst en resurs som distribuerats i USA, östra:

       ![Visa användningsdata](./media/check-usage-against-limits/view-usage.png)

     Du kan sortera kolumnerna genom att välja kolumnrubriken. Gränserna som visas är gränserna för din prenumeration. Om du behöver öka en standardgräns väljer du **Begär ökning** och slutför sedan och skickar supportbegäran. Alla resurser har en maxgräns som anges i [Azure-gränser.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) Om din aktuella gräns redan har det högsta antalet kan du inte öka gränsen.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan köra kommandona som följer i [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Det Azure Cloud Shell är ett kostnadsfritt interaktivt gränssnitt. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn behöver du Azure PowerShell version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` på datorn för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra för att `Login-AzAccount` logga in på Azure.

Visa din användning mot gränser med [Get-AzNetworkUsage](/powershell/module/az.network/get-aznetworkusage). I följande exempel hämtar användningen för resurser där minst en resurs har distribuerats på platsen USA, östra:

```azurepowershell-interactive
Get-AzNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

Du får utdata formaterade på samma sätt som följande exempelutdata:

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

Om du använder Azure CLI-kommandon (Kommandoradsgränssnitt) för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. Den här artikeln kräver Azure CLI version 2.0.32 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också köra för att `az login` logga in på Azure.

Visa din användning mot gränser med [az network list-usages](/cli/azure/network#az_network_list_usages). I följande exempel hämtar användningen för resurser på platsen USA, östra:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

Du får utdata formaterade på samma sätt som följande exempelutdata:

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