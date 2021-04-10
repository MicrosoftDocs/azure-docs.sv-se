---
title: Begränsa nätverks åtkomsten till PaaS-resurser – Azure CLI
description: I den här artikeln får du lära dig hur du begränsar och begränsar nätverks åtkomsten till Azure-resurser, till exempel Azure Storage och Azure SQL Database, med tjänst slut punkter för virtuella nätverk med hjälp av Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5c23259a9cb1f3c409ad437a1e91151b5f401129
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946291"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Begränsa nätverks åtkomsten till PaaS-resurser med tjänst slut punkter för virtuella nätverk med hjälp av Azure CLI

Med tjänstslutpunkter för virtuellt nätverk kan du begränsa nätverksåtkomsten till vissa Azure-tjänsters resurser till ett undernät för virtuella datorer. Du kan också ta bort resursernas internetåtkomst. Tjänstslutpunkterna möjliggör direktanslutning från ditt virtuella nätverk till Azure-tjänster som stöds, så att du kan använda det privata adressutrymmet i det virtuella nätverket för åtkomst till Azure-tjänsterna. Trafik till Azure-resurser genom tjänstslutpunkterna finns alltid kvar i Microsoft Azure-stamnätverket. I den här artikeln kan du se hur du:

* Skapa ett virtuellt nätverk med ett undernät
* Lägga till ett undernät och aktivera en tjänstslutpunkt
* Skapa en Azure-resurs och tillåt nätverksåtkomst till den från enbart ett undernät
* Distribuera en virtuell dator (VM) till varje undernät
* Bekräfta åtkomst till en resurs från ett undernät
* Bekräfta att åtkomst nekas för en resurs från ett undernät och internet

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.28 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Innan du skapar ett virtuellt nätverk måste du skapa en resurs grupp för det virtuella nätverket och alla andra resurser som skapas i den här artikeln. Skapa en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Skapa ett virtuellt nätverk med ett undernät med [AZ Network VNet Create](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Aktivera en tjänstslutpunkt 

Du kan bara aktivera tjänstens slut punkter för tjänster som stöder tjänst slut punkter. Visa tjänst slut punkt – aktiverade tjänster som är tillgängliga på en Azure-plats med [AZ Network VNet List-Endpoint-Services](/cli/azure/network/vnet). I följande exempel returneras en lista över tjänster-slutpunktbaserade tjänster som är tillgängliga i regionen *östra* . Listan över tjänster som returneras kommer att växa med tiden, eftersom fler Azure-tjänster blir tjänstens slut punkt aktive rad.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Skapa ytterligare ett undernät i det virtuella nätverket med [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet). I det här exemplet skapas en tjänst slut punkt för *Microsoft. Storage* för under nätet: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Begränsa nätverksåtkomst för ett undernät

Skapa en nätverks säkerhets grupp med [AZ Network NSG Create](/cli/azure/network/nsg). I följande exempel skapas en nätverks säkerhets grupp med namnet *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Koppla nätverks säkerhets gruppen till det *privata* under nätet med [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet). I följande exempel associeras nätverks säkerhets gruppen *myNsgPrivate* till det *privata* under nätet:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Skapa säkerhets regler med [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule). Regeln nedan tillåter utgående åtkomst till de offentliga IP-adresser som tilldelats den Azure Storage tjänsten: 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"
```

Varje nätverks säkerhets grupp innehåller flera [Standard säkerhets regler](./network-security-groups-overview.md#default-security-rules). Regeln nedan åsidosätter en standard säkerhets regel som tillåter utgående åtkomst till alla offentliga IP-adresser. `destination-address-prefix "Internet"`Alternativet nekar utgående åtkomst till alla offentliga IP-adresser. Den föregående regeln åsidosätter den här regeln, på grund av dess högre prioritet, vilket ger åtkomst till de offentliga IP-adresserna för Azure Storage.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"
```

Följande regel tillåter att SSH-trafik inkommande till under nätet var som helst. Regeln åsidosätter en standardsäkerhetsregel som nekar all inkommande trafik från internet. SSH tillåts till under nätet så att anslutningen kan testas i ett senare steg.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-a-resource"></a>Begränsa nätverksåtkomst till en resurs

De steg som behövs för att begränsa nätverksåtkomsten till resurser som har skapats via Azure-tjänster som är aktiverade för tjänstslutpunkter varierar från tjänst till tjänst. Läs dokumentationen för enskilda tjänster för specifika åtgärder för varje tjänst. Resten av den här artikeln innehåller steg för att begränsa nätverks åtkomsten för ett Azure Storage konto, som ett exempel.

### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Skapa ett Azure Storage-konto med [AZ lagrings konto skapa](/cli/azure/storage/account). Ersätt `<replace-with-your-unique-storage-account-name>` med ett namn som är unikt för alla Azure-platser, mellan 3-24 tecken långt, med enbart siffror och gemener.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

När lagrings kontot har skapats hämtar du anslutnings strängen för lagrings kontot till en variabel med [AZ lagrings konto show-Connection-String](/cli/azure/storage/account). Anslutnings strängen används för att skapa en fil resurs i ett senare steg.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Visa innehållet i variabeln och anteckna värdet för **AccountKey** som returneras i utdata, eftersom det används i ett senare steg.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Skapa en filresurs i lagringskontot

Skapa en fil resurs i lagrings kontot med [AZ Storage Share Create](/cli/azure/storage/share). I ett senare steg monteras den här fil resursen för att bekräfta nätverks åtkomsten till den.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Neka all nätverks åtkomst till ett lagrings konto

Som standard godkänner lagringskonton nätverksanslutningar från klienter i alla nätverk. Om du vill begränsa åtkomsten till valda nätverk ändrar du standard åtgärden till *neka* med [AZ Storage Account Update](/cli/azure/storage/account). När nätverksåtkomsten nekas är lagringskontot inte tillgängligt från något nätverk.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Aktivera nätverksåtkomst från ett undernät

Tillåt nätverks åtkomst till lagrings kontot från det *privata* under nätet med [AZ lagrings konto nätverks regel Lägg till](/cli/azure/storage/account/network-rule).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName \
  --vnet-name myVirtualNetwork \
  --subnet Private
```
## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Om du vill testa nätverksåtkomsten till ett lagringskonto distribuerar du en virtuell dator till varje undernät.

### <a name="create-the-first-virtual-machine"></a>Skapa din första virtuella dator

Skapa en virtuell dator i det *offentliga* under nätet med [AZ VM Create](/cli/azure/vm). Om det inte redan finns SSH-nycklar på en standardnyckelplats skapar kommandot dem. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. När den virtuella datorn har skapats visar Azure CLI information som liknar följande exempel: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPublic",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Anteckna **publicIpAddress** i de utdata som returneras. Den här adressen används för att komma åt den virtuella datorn från Internet i ett senare steg.

### <a name="create-the-second-virtual-machine"></a>Skapa den andra virtuella datorn

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. När du har skapat den noterar du **publicIpAddress** i resultatet som returneras. Den här adressen används för att komma åt den virtuella datorn från Internet i ett senare steg.

## <a name="confirm-access-to-storage-account"></a>Bekräfta åtkomst till lagringskontot

SSH till den virtuella *myVmPrivate* -datorn. Ersätt *\<publicIpAddress>* med den offentliga IP-adressen för din virtuella *myVmPrivate* -dator.

```bash 
ssh <publicIpAddress>
```

Skapa en mapp för en monterings punkt:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Montera Azure-filresursen i den katalog som du skapade. Innan du kör följande kommando ersätter `<storage-account-name>` du med konto namnet och `<storage-account-key>` den nyckel som du hämtade i [skapa ett lagrings konto](#create-a-storage-account).

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Du får meddelandet `user@myVmPrivate:~$` . Azure-filresursen har monterats på */mnt/MyAzureFileShare*.

Bekräfta att den virtuella datorn inte har någon utgående anslutning till någon annan offentlig IP-adress:

```bash
ping bing.com -c 4
```

Du får inga svar eftersom nätverkssäkerhetsgruppen som är kopplad till det *privata* undernätet inte tillåter utgående åtkomst till offentliga IP-adresser förutom de adresser som är kopplade till Azure Storage-tjänsten.

Avsluta SSH-sessionen till den virtuella *myVmPrivate* -datorn.

## <a name="confirm-access-is-denied-to-storage-account"></a>Bekräfta att åtkomst till lagringskontot nekas

Använd följande kommando för att skapa en SSH-session med den virtuella datorn *myVmPublic* . Ersätt `<publicIpAddress>` med den offentliga IP-adressen för din virtuella *myVmPublic* -dator: 

```bash 
ssh <publicIpAddress>
```

Skapa en katalog för en monterings punkt:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Försök att montera Azure-filresursen i den katalog som du skapade. Den här artikeln förutsätter att du har distribuerat den senaste versionen av Ubuntu. Om du använder tidigare versioner av Ubuntu kan du läsa mer instruktioner om hur du monterar fil resurser i avsnittet [montera på Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Innan du kör följande kommando ersätter `<storage-account-name>` du med konto namnet och `<storage-account-key>` den nyckel som du hämtade i [skapa ett lagrings konto](#create-a-storage-account):

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Åtkomst nekas och du får ett `mount error(13): Permission denied` fel meddelande eftersom den virtuella datorn *myVmPublic* distribueras i det *offentliga* under nätet. Det *offentliga* undernätet har ingen tjänstslutpunkt aktiverad för Azure Storage, och lagringskontot tillåter endast nätverksåtkomst från det *privata* undernätet, inte det *offentliga*.

Avsluta SSH-sessionen till den virtuella *myVmPublic* -datorn.

Från datorn försöker du Visa resurserna i ditt lagrings konto med [AZ Storage Share List](/cli/azure/storage/share). Ersätt `<account-name>` och `<account-key>` med lagrings kontots namn och nyckel från [skapa ett lagrings konto](#create-a-storage-account):

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

Åtkomst nekas och du får *den här begäran har inte behörighet att utföra den här åtgärden* eftersom datorn inte finns i det *privata* under nätet för det virtuella *MyVirtualNetwork* -nätverket.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs använder du [AZ Group Delete](/cli/azure) för att ta bort resurs gruppen och alla resurser den innehåller.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du aktiverat en tjänst slut punkt för ett virtuellt nätverks under nät. Du har lärt dig att tjänstslutpunkter kan aktiveras för resurser som distribueras med flera Azure-tjänster. Du har skapat ett Azure Storage-konto och begränsat nätverksåtkomst för lagringskontot till enbart resurser inom ett undernät för ett virtuellt nätverk. Om du vill veta mer om tjänstslutpunkter går du till [Översikt över tjänstslutpunkter](virtual-network-service-endpoints-overview.md) och [Hantera undernät](virtual-network-manage-subnet.md).

Om du har flera virtuella nätverk i ditt konto kanske du vill ansluta två virtuella nätverk så att resurserna i vart och ett kan kommunicera med varandra. Mer information finns i [ansluta virtuella nätverk](tutorial-connect-virtual-networks-cli.md).
