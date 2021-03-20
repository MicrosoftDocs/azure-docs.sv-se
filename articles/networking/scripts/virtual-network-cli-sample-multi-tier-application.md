---
title: Skript exempel för Azure CLI – skapa ett nätverk för program med flera nivåer
description: Azure CLI-skriptexempel – Skapa ett virtuellt nätverk för flernivåprogram.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: c0964eb5e44a0e1a2329ec6acef91d70cbd5c32f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87503879"
---
# <a name="use-an-azure-cli-script-sample-to-create-a-network-for-multi-tier-applications"></a>Använd ett skript exempel för Azure CLI för att skapa ett nätverk för program på flera nivåer

Det här skriptexemplet skapar ett virtuellt nätverk med klient- och serverdelsundernät. Trafik till klientdelsundernätet är begränsad till HTTP och SSH, medan trafik till serverdelsundernätet är begränsad till MySQL, port 3306. När du kört skriptet har du två virtuella datorer, en i varje undernät, som du kan distribuera webbservern och MySQL-programvaran till.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Exempelskript


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.sh  "Virtual network for multi-tier application")]

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
| [az network public-ip create](/cli/azure/network/public-ip) | Skapar en offentlig IP-adress för att få åtkomst till den virtuella datorn från Internet. |
| [az network nic create](/cli/azure/network/nic) | Skapar virtuella nätverksgränssnitt och kopplar dem till det virtuella nätverkets klient- och serverdelsundernät. |
| [az network nsg create](/cli/azure/network/nsg) | Skapar nätverkssäkerhetsgrupper (NSG) som är kopplade till klient- och serverdelsundernäten. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) |Skapar NSG-regler som tillåter eller blockerar specifika portar till specifika undernät. |
| [az vm create](/cli/azure/vm) | Skapar virtuella datorer och kopplar ett nätverkskort till varje virtuell dator. Det här kommandot anger även den virtuella datoravbildning som ska användas samt administrativa autentiseringsuppgifter. |
| [az group delete](/cli/azure/group) | Tar bort en resursgrupp och alla resurser den innehåller. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare Network CLI-skript exempel finns i [översikts dokumentationen för Azure-nätverk](../cli-samples.md)
