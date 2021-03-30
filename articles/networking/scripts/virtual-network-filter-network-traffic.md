---
title: Azure CLI-skriptexempel – Filtrera VM-nätverkstrafik | Microsoft Docs
description: Använd ett Azure CLI-skript för att filtrera inkommande och utgående virtuell dators nätverks trafik med klient dels-och backend-undernät.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: 61f2441d68954a167b9887a4dfd4b99a53c14166
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88037226"
---
# <a name="use-an-azure-cli-script-to-filter-inbound-and-outbound-vm-network-traffic"></a>Använd ett Azure CLI-skript för att filtrera inkommande och utgående VM-nätverkstrafik

Det här skriptexemplet skapar ett virtuellt nätverk med klient- och serverdelsundernät. Inkommande nätverks trafik till klient dels under nätet är begränsad till HTTP, HTTPS och SSH, medan utgående trafik till Internet från Server dels under nätet inte är tillåten. När du kört skriptet har du en virtuell dator med två nätverkskort. Nätverkskorten är anslutna till olika undernät.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/filter-network-traffic/filter-network-traffic.sh  "Filter VM network traffic")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, ett virtuellt nätverk och nätverkssäkerhetsgrupper. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az network vnet create](/cli/azure/network/vnet) | Skapar ett virtuellt Azure-nätverk och klientdelsundernät. |
| [az network subnet create](/cli/azure/network/vnet/subnet) | Skapar ett serverdelsundernät. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet) | Associerar NSG:er med undernät. |
| [az network public-ip create](/cli/azure/network/public-ip) | Skapar en offentlig IP-adress för att få åtkomst till den virtuella datorn från Internet. |
| [az network nic create](/cli/azure/network/nic) | Skapar virtuella nätverksgränssnitt och kopplar dem till det virtuella nätverkets klient- och serverdelsundernät. |
| [az network nsg create](/cli/azure/network/nsg) | Skapar nätverkssäkerhetsgrupper (NSG) som är kopplade till klient- och serverdelsundernäten. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) |Skapar NSG-regler som tillåter eller blockerar specifika portar till specifika undernät. |
| [az vm create](/cli/azure/vm) | Skapar virtuella datorer och kopplar ett nätverkskort till varje virtuell dator. Det här kommandot anger även den virtuella datoravbildning som ska användas samt administrativa autentiseringsuppgifter. |
| [az group delete](/cli/azure/group) | Tar bort en resursgrupp och alla resurser den innehåller. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare Network CLI-skript exempel finns i [översikts dokumentationen för Azure-nätverk](../cli-samples.md)
