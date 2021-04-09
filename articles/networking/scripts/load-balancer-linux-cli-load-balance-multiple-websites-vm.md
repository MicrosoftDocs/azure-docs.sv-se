---
title: Skript exempel för Azure CLI – belastningsutjämna flera webbplatser med Azure CLI | Microsoft Docs
description: Skript exempel för Azure CLI – belastningsutjämna flera webbplatser till samma virtuella dator
services: load-balancer
documentationcenter: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: allensu
ms.openlocfilehash: 7a3bd4cab8123814fe360fe4ab724650c785e9f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98234124"
---
# <a name="load-balance-multiple-websites"></a>Belastningsutjämning för flera webbplatser

Det här skript exemplet skapar ett virtuellt nätverk med två virtuella datorer (VM) som är medlemmar i en tillgänglighets uppsättning. En lastbalanserare dirigerar trafik för två olika IP-adresser till de två virtuella datorerna. När du har kört skriptet kan du distribuera webbserverprogramvara för virtuella datorer och vara värd för flera webbplatser, var och en med egen IP-adress.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript


[!code-azurecli-interactive[main](../../../cli_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.sh  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, virtuellt nätverk, lastbalanserare och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az network vnet create](/cli/azure/network/vnet) | Skapar ett virtuellt Azure-nätverk och undernät. |
| [az network public-ip create](/cli/azure/network/public-ip) | Skapar en offentlig IP-adress med en statisk IP-adress och ett tillhörande DNS-namn. |
| [az network lb create](/cli/azure/network/lb) | Skapar en Azure-lastbalanserare. |
| [az network lb probe create](/cli/azure/network/lb/probe) | Skapar en lastbalanseringsavsökning. En lastbalanseringsavsökning används för att övervaka varje virtuell dator i lastbalanseringsuppsättningen. Om en virtuell dator blir otillgänglig dirigeras trafiken förbi den. |
| [az network lb rule create](/cli/azure/network/lb/rule) | Skapar en lastbalanseringsregel. I det här exemplet skapas en regel för port 80. När HTTP-trafik anländer vid lastbalanseraren dirigeras den till port 80, en av de virtuella datorerna i lastbalanseringsuppsättningen. |
| [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip) | Skapa en klientdels-IP-adress för lastbalanseraren. |
| [az network lb address-pool create](/cli/azure/network/lb/address-pool) | Skapar en serverdelsadresspool. |
| [az network nic create](/cli/azure/network/nic) | Skapar ett virtuellt nätverkskort och ansluter det till det virtuella nätverket och undernätet. |
| [az vm availability-set create](/cli/azure/network/lb/rule) | Skapar en tillgänglighetsuppsättning. Tillgänglighetsuppsättningar garanterar programmets drifttid genom att fördela virtuella datorer mellan fysiska resurser så att hela uppsättningen inte berörs om något fel inträffar. |
| [az network nic ip-config create](/cli/azure/network/nic/ip-config) | Skapar en IP-konfiguration. Du måste ha funktionen Microsoft.Network/AllowMultipleIpConfigurationsPerNic aktiverad för din prenumeration. Bara en konfiguration kan utses som den primära IP-konfigurationen per NIC, med hjälp av flaggan --make-primary. |
| [az vm create](/cli/azure/vm/availability-set) | Skapar den virtuella datorn och ansluter den till nätverkskortet, ett virtuellt nätverk, ett undernät och en NSG. Kommandot specificerar även avbildningen av den virtuella dator som ska användas samt administrativa autentiseringsuppgifter.  |
| [az group delete](/cli/azure/vm/extension) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare exempel på CLI-nätverksskript finns i [dokumentation för översikt över Azure-nätverk](../cli-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).