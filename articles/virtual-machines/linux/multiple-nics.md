---
title: Skapa en virtuell Linux-dator i Azure med flera nätverkskort
description: Lär dig hur du skapar en virtuell Linux-dator med flera anslutna nätverkskort med hjälp av Azure CLI eller Resource Manager mallar.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: cynthn
ms.openlocfilehash: b08e8ebbba3ba91c1c1aa0f135c4cba37ba038b1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769921"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Så här skapar du en virtuell Linux-dator i Azure med flera nätverkskort


Den här artikeln beskriver hur du skapar en virtuell dator med flera nätverkskort med Azure CLI.

## <a name="create-supporting-resources"></a>Skapa stödresurser
Installera senaste [Azure CLI och](/cli/azure/install-az-cli2) logga in på ett Azure-konto med az [login](/cli/azure/reference-index).

I följande exempel ersätter du exempelparameternamn med dina egna värden. Exempelparameternamnen *myResourceGroup,* *mystorageaccount* och *myVM*.

Skapa först en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med *namnet myResourceGroup* på *platsen eastus:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Skapa det virtuella nätverket med [az network vnet create](/cli/azure/network/vnet). I följande exempel skapas ett virtuellt nätverk med *namnet myVnet* och ett undernät med namnet *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Skapa ett undernät för backend-trafiken med [az network vnet subnet create](/cli/azure/network/vnet/subnet). I följande exempel skapas ett undernät med namnet *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Skapa en nätverkssäkerhetsgrupp med [az network nsg create](/cli/azure/network/nsg). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Skapa och konfigurera flera nätverkskort
Skapa två nätverkskort med [az network nic create](/cli/azure/network/nic). I följande exempel skapas två nätverkskort med namnet *myNic1* och *myNic2,* som är anslutna till nätverkssäkerhetsgruppen, med ett nätverkskort som ansluter till varje undernät:

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
När du skapar den virtuella datorn anger du de nätverkskort som du skapade med `--nics` . Du måste också vara försiktig när du väljer vm-storlek. Det finns gränser för det totala antalet nätverkskort som du kan lägga till i en virtuell dator. Läs mer om [storlekar på virtuella Linux-datorer.](../sizes.md)

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

Lägg till routningstabeller i gästoperativsystemet genom att slutföra stegen [i Konfigurera gästoperativsystemet för flera nätverkskort.](#configure-guest-os-for-multiple-nics)

## <a name="add-a-nic-to-a-vm"></a>Lägga till ett nätverkskort till en virtuell dator
I föregående steg skapades en virtuell dator med flera nätverkskort. Du kan också lägga till nätverkskort till en befintlig virtuell dator med Azure CLI. Olika [VM-storlekar](../sizes.md) stöder ett varierande antal nätverkskort, så ändra storlek på den virtuella datorn därefter. Om det behövs kan du ändra storlek [på en virtuell dator.](change-vm-size.md)

Skapa ett annat nätverkskort med [az network nic create](/cli/azure/network/nic). I följande exempel skapas ett nätverkskort med namnet *myNic3* som är anslutet till det backend-undernät och nätverkssäkerhetsgrupp som skapades i föregående steg:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Om du vill lägga till ett nätverkskort till en befintlig virtuell dator måste du först frisallokera den virtuella datorn [med az vm deallocate](/cli/azure/vm). I följande exempel frislöses den virtuella datorn med *namnet myVM*:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Lägg till nätverkskortet med [az vm nic add](/cli/azure/vm/nic). I följande exempel läggs *myNic3 till* *i myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Starta den virtuella datorn med [az vm start:](/cli/azure/vm)

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Lägg till routningstabeller i gästoperativsystemet genom att slutföra stegen [i Konfigurera gästoperativsystemet för flera nätverkskort.](#configure-guest-os-for-multiple-nics)

## <a name="remove-a-nic-from-a-vm"></a>Ta bort ett nätverkskort från en virtuell dator
Om du vill ta bort ett nätverkskort från en befintlig virtuell dator måste du först frisöka den virtuella datorn [med az vm deallocate](/cli/azure/vm). I följande exempel frisallokerar den virtuella datorn *med namnet myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Ta bort nätverkskortet med [az vm nic remove](/cli/azure/vm/nic). I följande exempel tas *myNic3 bort* från *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Starta den virtuella datorn med [az vm start](/cli/azure/vm):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Skapa flera nätverkskort med hjälp Resource Manager mallar
Azure Resource Manager använder deklarativa JSON-filer för att definiera din miljö. Du kan läsa en [översikt över Azure Resource Manager](../../azure-resource-manager/management/overview.md). Resource Manager är ett sätt att skapa flera instanser av en resurs under distributionen, till exempel att skapa flera nätverkskort. Du använder *kopiera* för att ange antalet instanser som ska skapas:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Läs mer om [att skapa flera instanser med hjälp av *kopiera*](../../azure-resource-manager/templates/copy-resources.md). 

Du kan också använda en för att sedan lägga till ett tal i ett `copyIndex()` resursnamn, vilket gör att du kan `myNic1` skapa `myNic2` , osv. Nedan visas ett exempel på hur du kan göra om indexvärdet:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Du kan läsa ett komplett exempel på hur [du skapar flera nätverkskort med hjälp Resource Manager mallar](../../virtual-network/template-samples.md).

Lägg till routningstabeller i gästoperativsystemet genom att slutföra stegen [i Konfigurera gästoperativsystemet för flera nätverkskort.](#configure-guest-os-for-multiple-nics)

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurera gästoperativsystem för flera nätverkskort

Föregående steg skapade ett virtuellt nätverk och undernät, anslutna nätverkskort och skapade sedan en virtuell dator. En offentlig IP-adress och regler för nätverkssäkerhetsgrupp som tillåter SSH-trafik skapades inte. Om du vill konfigurera gästoperativsystemet för flera nätverkskort måste du tillåta fjärranslutningar och köra kommandon lokalt på den virtuella datorn.

Om du vill tillåta SSH-trafik skapar du en regel för nätverkssäkerhetsgruppen [med az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) enligt följande:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Skapa en offentlig IP-adress [med az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) och tilldela den till det första nätverkskortet med az network [nic ip-config update](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update):

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

Om du vill visa den virtuella datorns offentliga IP-adress använder [du az vm show](/cli/azure/vm#az_vm_show) på följande sätt::

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

SSH till den virtuella datorns offentliga IP-adress. Standardanvändarnamnet som angavs i föregående steg *var azureuser*. Ange ditt eget användarnamn och din offentliga IP-adress:

```bash
ssh azureuser@137.117.58.232
```

Om du vill skicka till eller från ett sekundärt nätverksgränssnitt måste du manuellt lägga till beständiga vägar i operativsystemet för varje sekundärt nätverksgränssnitt. I den här artikeln *är eth1* det sekundära gränssnittet. Anvisningar för att lägga till beständiga vägar till operativsystemet varierar beroende på distribution. Instruktioner finns i dokumentationen för distributionen.

När du lägger till vägen till operativsystemet är gateway-adressen *.1* för det undernät som nätverksgränssnittet finns i. Om nätverksgränssnittet till exempel tilldelas adressen *10.0.2.4* är den gateway som du anger för vägen *10.0.2.1*. Du kan definiera ett specifikt nätverk för vägens mål eller ange ett mål på *0.0.0.0* om du vill att all trafik för gränssnittet ska gå via den angivna gatewayen. Gatewayen för varje undernät hanteras av det virtuella nätverket.

När du har lagt till vägen för ett sekundärt gränssnitt kontrollerar du att vägen finns i din vägtabell med `route -n` . Följande exempelutdata är för den vägtabell där de två nätverksgränssnitten har lagts till för den virtuella datorn i den här artikeln:

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

Bekräfta att vägen som du har lagt till finns kvar vid omstarter genom att kontrollera din vägtabell igen efter en omstart. Om du vill testa anslutningen kan du ange följande kommando, till exempel där *eth1* är namnet på ett sekundärt nätverksgränssnitt:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Nästa steg
Granska [linux VM-storlekar](../sizes.md) när du försöker skapa en virtuell dator med flera nätverkskort. Var uppmärksam på det maximala antalet nätverkskort som varje VM-storlek stöder.

För att ytterligare skydda dina virtuella datorer använder du just-in-time-åtkomst till virtuella datorer. Den här funktionen öppnar regler för nätverkssäkerhetsgrupp för SSH-trafik vid behov och under en definierad tidsperiod. Mer information finns i [Manage virtual machine access using just in time](../../security-center/security-center-just-in-time.md) (Hantera åtkomsten till virtuella datorer med Just-In-Time).