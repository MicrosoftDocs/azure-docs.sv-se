---
title: Öppna portar till en virtuell dator med Azure CLI
description: Lär dig hur du öppnar en port/skapar en slut punkt för din virtuella dator med hjälp av Azure CLI.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: a96c0f7c6fb767b96be273a615149143043e8bc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91975135"
---
# <a name="open-ports-and-endpoints-to-a-vm-with-the-azure-cli"></a>Öppna portar och slut punkter till en virtuell dator med Azure CLI

Du öppnar en port eller skapar en slut punkt för en virtuell dator (VM) i Azure genom att skapa ett nätverks filter i ett undernät eller ett nätverks gränssnitt för virtuella datorer. Du placerar dessa filter, som styr både inkommande och utgående trafik, i en nätverks säkerhets grupp som är kopplad till den resurs som tar emot trafiken. Vi använder ett vanligt exempel på webb trafik på port 80. Den här artikeln visar hur du öppnar en port till en virtuell dator med Azure CLI. 


Om du vill skapa en nätverks säkerhets grupp och regler måste du ha det senaste [Azure CLI](/cli/azure/install-az-cli2) installerat och inloggat på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index).

Ersätt exempel parameter namn med dina egna värden i följande exempel. Exempel på parameter namn är *myResourceGroup*, *myNetworkSecurityGroup* och *myVnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Öppna en port för en virtuell dator snabbt
Om du snabbt behöver öppna en port för en virtuell dator i ett utvecklings-/test scenario kan du använda kommandot [AZ VM Open-port](/cli/azure/vm) . Det här kommandot skapar en nätverks säkerhets grupp, lägger till en regel och tillämpar den på en virtuell dator eller undernät. I följande exempel öppnas port *80* på den virtuella datorn med namnet *myVM* i resurs gruppen med namnet *myResourceGroup*.

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

Om du vill ha mer kontroll över reglerna, till exempel definiera ett käll-IP-adressintervall, fortsätter du med de ytterligare stegen i den här artikeln.


## <a name="create-a-network-security-group-and-rules"></a>Skapa en nätverks säkerhets grupp och regler
Skapa nätverks säkerhets gruppen med [AZ Network NSG Create](/cli/azure/network/nsg). I följande exempel skapas en nätverks säkerhets grupp med namnet *myNetworkSecurityGroup* på platsen för *öster* :

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Lägg till en regel med [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule) för att tillåta http-trafik till din webserver (eller justera för ditt eget scenario, till exempel SSH-åtkomst eller databas anslutning). I följande exempel skapas en regel med namnet *myNetworkSecurityGroupRule* för att tillåta TCP-trafik på port 80:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>Tillämpa nätverks säkerhets gruppen på den virtuella datorn
Associera nätverks säkerhets gruppen med den virtuella datorns nätverks gränssnitt (NIC) med [AZ Network NIC Update](/cli/azure/network/nic). I följande exempel associeras ett befintligt nätverkskort med namnet *myNic* med nätverks säkerhets gruppen med namnet *myNetworkSecurityGroup*:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Du kan också associera din nätverks säkerhets grupp med ett virtuellt nätverks under nät med [AZ Network VNet-undernät](/cli/azure/network/vnet/subnet) i stället för bara nätverks gränssnittet på en enskild virtuell dator. I följande exempel associeras ett befintligt undernät med namnet mina *undernät* i det virtuella *myVnet* -nätverket med nätverks säkerhets gruppen med namnet *myNetworkSecurityGroup*:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Mer information om nätverks säkerhets grupper
Med hjälp av snabb kommandona kan du komma igång med trafik som flödar till den virtuella datorn. Nätverks säkerhets grupper ger många fantastiska funktioner och granularitet för att kontrol lera åtkomsten till dina resurser. Du kan läsa mer om att [skapa en nätverks säkerhets grupp och ACL-regler här](tutorial-virtual-network.md#secure-network-traffic).

För webb program med hög tillgänglighet bör du placera de virtuella datorerna bakom en Azure Load Balancer. Belastningsutjämnaren distribuerar trafik till virtuella datorer, med en nätverks säkerhets grupp som tillhandahåller trafik filtrering. Mer information finns i [så här kan du belastningsutjämna virtuella Linux-datorer i Azure för att skapa ett program med hög](tutorial-load-balancer.md)tillgänglighet.

## <a name="next-steps"></a>Nästa steg
I det här exemplet har du skapat en enkel regel för att tillåta HTTP-trafik. Du hittar information om att skapa mer detaljerade miljöer i följande artiklar:

* [Översikt över Azure Resource Manager](../../azure-resource-manager/management/overview.md)
* [Vad är en nätverkssäkerhetsgrupp (NSG)?](../../virtual-network/network-security-groups-overview.md)