---
title: Skapa en DNS-zon och -post för ett domännamn – Azure CLI – Azure DNS
description: I det här Azure CLI-skriptexemplet visas hur du skapar en DNS-zon och -post för ett domännamn
services: dns
author: rohinkoul
ms.service: dns
ms.topic: sample
ms.date: 09/20/2019
ms.author: rohink
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5692c7a81d34ec9005c1c4675c71d63e697c5f47
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783709"
---
# <a name="azure-cli-script-example-create-a-dns-zone-and-record"></a>Azure CLI-skriptexempel: Skapa en DNS-zon och -post

I det här Azure CLI-skriptexemplet skapas en DNS-zon och -post för ett domännamn. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/dns/create-dns-zone-and-record/create-dns-zone-and-record.sh "Create DNS zone and record")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, DNS-zonen och alla relaterade resurser.

```azurecli
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, virtuell dator, tillgänglighetsuppsättning, lastbalanserare och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az network dns zone create](/cli/azure/network/dns/zone#az_network_dns_zone_create) | Skapar en Azure DNS-zon. |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set) | Lägger till en *A*-post för en DNS-zon. |
| [az network dns record-set list](/cli/azure/network/dns/record-set) | Visar alla *A*-postuppsättningar i en DNS-zon. |
| [az group delete](/cli/azure/vm/extension#az_vm_extension_set) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).