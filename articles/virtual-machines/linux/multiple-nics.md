---
title: Skapa en virtuell Linux-dator i Azure med flera nätverkskort
description: Lär dig hur du skapar en virtuell Linux-dator med flera nätverkskort som är kopplade till den med hjälp av Azure CLI-eller Resource Manager-mallarna.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: cynthn
ms.openlocfilehash: c0eea74890665297a0d450c8afd0a5d60dd1ae00
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102551818"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Så här skapar du en virtuell Linux-dator i Azure med flera nätverks gränssnitts kort


Den här artikeln beskriver hur du skapar en virtuell dator med flera nätverkskort med Azure CLI.

## <a name="create-supporting-resources"></a>Skapa stöd resurser
Installera den senaste versionen av [Azure CLI](/cli/azure/install-az-cli2) och logga in på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index).

Ersätt exempel parameter namn med dina egna värden i följande exempel. Exempel på parameter namn som ingår *myResourceGroup*, *mystorageaccount* och *myVM*.

Skapa först en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli
az group create --name myResourceGroup --location eastus
```

Skapa det virtuella nätverket med [AZ Network VNet Create](/cli/azure/network/vnet). I följande exempel skapas ett virtuellt nätverk med namnet *myVnet* och under nätet med namnet *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Skapa ett undernät för backend-trafiken med [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet). I följande exempel skapas ett undernät med namnet *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Skapa en nätverks säkerhets grupp med [AZ Network NSG Create](/cli/azure/network/nsg). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Skapa och konfigurera flera nätverkskort
Skapa två nätverkskort med [AZ Network NIC Create](/cli/azure/network/nic). I följande exempel skapas två nätverkskort, med namnet *myNic1* och *myNic2*, som är anslutna till nätverks säkerhets gruppen med ett nätverkskort som ansluter till varje undernät:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Skapa en virtuell dator och koppla nätverkskorten
När du skapar den virtuella datorn anger du de nätverkskort som du skapade med `--nics` . Du måste också vara försiktig när du väljer storleken på den virtuella datorn. Det finns gränser för det totala antalet nätverkskort som du kan lägga till i en virtuell dator. Läs mer om [storlekar för virtuella Linux-datorer](../sizes.md).

Skapa en virtuell dator med [az vm create](/cli/azure/vm). I följande exempel skapas en virtuell dator med namnet *myVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

Lägg till routningstabeller i gäst operativ systemet genom att slutföra stegen i [Konfigurera gäst operativ systemet för flera nätverkskort](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-a-vm"></a>Lägga till ett nätverkskort till en virtuell dator
Föregående steg skapade en virtuell dator med flera nätverkskort. Du kan också lägga till nätverkskort till en befintlig virtuell dator med Azure CLI. Olika [VM-storlekar](../sizes.md) har stöd för olika antal nätverkskort, så storleken på den virtuella datorn. Om det behövs kan du [ändra storlek på en virtuell dator](change-vm-size.md).

Skapa ett annat nätverkskort med [AZ Network NIC Create](/cli/azure/network/nic). I följande exempel skapas ett nätverkskort med namnet *myNic3* som är anslutet till backend-undernätet och nätverks säkerhets gruppen som skapades i föregående steg:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Om du vill lägga till ett nätverkskort i en befintlig virtuell dator avallokerar du först den virtuella datorn med [AZ VM-frigören](/cli/azure/vm). I följande exempel avallokeras den virtuella datorn med namnet *myVM*:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Lägg till NÄTVERKSKORTet med [AZ VM NIC Add](/cli/azure/vm/nic). I följande exempel läggs *myNic3* till *myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Starta den virtuella datorn med [AZ VM start](/cli/azure/vm):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Lägg till routningstabeller i gäst operativ systemet genom att slutföra stegen i [Konfigurera gäst operativ systemet för flera nätverkskort](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-a-vm"></a>Ta bort ett nätverkskort från en virtuell dator
Om du vill ta bort ett nätverkskort från en befintlig virtuell dator avallokerar du först den virtuella datorn med [AZ VM-frigören](/cli/azure/vm). I följande exempel avallokeras den virtuella datorn med namnet *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Ta bort NÄTVERKSKORTet med [AZ VM NIC Remove](/cli/azure/vm/nic). I följande exempel tar bort *myNic3* från *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Starta den virtuella datorn med [AZ VM start](/cli/azure/vm):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Skapa flera nätverkskort med Resource Manager-mallar
Azure Resource Manager mallar använder deklarativ JSON-filer för att definiera din miljö. Du kan läsa en [Översikt över Azure Resource Manager](../../azure-resource-manager/management/overview.md). Resource Manager-mallar är ett sätt att skapa flera instanser av en resurs under distributionen, till exempel skapa flera nätverkskort. Du använder *copy* för att ange antalet instanser som ska skapas:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Läs mer om att [skapa flera instanser med hjälp av *Kopiera*](../../azure-resource-manager/templates/copy-resources.md). 

Du kan också använda en `copyIndex()` för att lägga till ett nummer till ett resurs namn, vilket gör att du kan skapa `myNic1` , `myNic2` osv. Nedan visas ett exempel på hur du lägger till indexvärdet:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Du kan läsa ett fullständigt exempel på [hur du skapar flera nätverkskort med Resource Manager-mallar](../../virtual-network/template-samples.md).

Lägg till routningstabeller i gäst operativ systemet genom att slutföra stegen i [Konfigurera gäst operativ systemet för flera nätverkskort](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurera gäst operativ system för flera nätverkskort

Föregående steg skapade ett virtuellt nätverk och undernät, anslutna nätverkskort och skapade sedan en virtuell dator. En offentlig IP-adress och regler för nätverks säkerhets grupper som tillåter SSH-trafik har inte skapats. Om du vill konfigurera gäst operativ systemet för flera nätverkskort måste du tillåta fjärr anslutningar och köra kommandon lokalt på den virtuella datorn.

Om du vill tillåta SSH-trafik skapar du en regel för nätverks säkerhets grupp med [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) enligt följande:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Skapa en offentlig IP-adress med [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) och tilldela den till det första nätverkskortet med [AZ Network NIC IP-config Update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update):

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

Om du vill visa den offentliga IP-adressen för den virtuella datorn använder du [AZ VM (se](/cli/azure/vm#az-vm-show) nedan):

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Nu kan du använda SSH till den offentliga IP-adressen för den virtuella datorn. Standard användar namnet som angavs i föregående steg var *azureuser*. Ange ditt eget användar namn och offentliga IP-adress:

```bash
ssh azureuser@137.117.58.232
```

Om du vill skicka till eller från ett sekundärt nätverks gränssnitt måste du manuellt lägga till beständiga vägar till operativ systemet för varje sekundärt nätverks gränssnitt. I den här artikeln är *eth1* det sekundära gränssnittet. Instruktioner för att lägga till beständiga vägar till operativ systemet varierar från distribution. Instruktioner finns i dokumentationen för din distribution.

När du lägger till vägen i operativ systemet är gateway-adressen 1 *för det undernät som nätverks* gränssnittet finns i. Om nätverks gränssnittet till exempel tilldelas adressen *10.0.2.4*, är den gateway som du anger för vägen *10.0.2.1*. Du kan definiera ett specifikt nätverk för vägens mål eller ange målet *0.0.0.0* om du vill att all trafik för gränssnittet ska gå via den angivna gatewayen. Gatewayen för varje undernät hanteras av det virtuella nätverket.

När du har lagt till vägen för ett sekundärt gränssnitt kontrollerar du att vägen finns i din routningstabell med `route -n` . Följande exempel på utdata är för routningstabellen som har de två nätverks gränssnitt som har lagts till i den virtuella datorn i den här artikeln:

```bash
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.1.1        0.0.0.0         UG    0      0        0 eth0
0.0.0.0         10.0.2.1        0.0.0.0         UG    0      0        0 eth1
10.0.1.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 eth1
168.63.129.16   10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
169.254.169.254 10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
```

Bekräfta att vägen som du har lagt till kvarstår i omstarter genom att kontrol lera routningstabellen igen efter en omstart. Om du vill testa anslutningen kan du ange följande kommando, till exempel där *eth1* är namnet på ett sekundärt nätverks gränssnitt:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Nästa steg
Granska [storleken på virtuella Linux-datorer](../sizes.md) när du försöker skapa en virtuell dator med flera nätverkskort. Observera högsta antalet nätverkskort som varje virtuell dator stöder.

Använd just-in-Time-åtkomst för virtuella datorer för att skydda dina virtuella datorer ytterligare. Den här funktionen öppnar regler för nätverks säkerhets grupper för SSH-trafik vid behov, och under en angiven tids period. Mer information finns i [Manage virtual machine access using just in time](../../security-center/security-center-just-in-time.md) (Hantera åtkomsten till virtuella datorer med Just-In-Time).