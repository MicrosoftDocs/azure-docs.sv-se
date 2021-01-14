---
title: Kopiera en virtuell Linux-dator med Azure CLI
description: Lär dig hur du skapar en kopia av din virtuella Azure Linux-dator med hjälp av Azure CLI och Managed Disks.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 10/17/2018
ms.author: cynthn
ms.custom: legacy, devx-track-azurecli
ms.openlocfilehash: 7f9ac0ab9eacb90bde70c85ea06bc19a18aa0c05
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201152"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Skapa en kopia av en virtuell Linux-dator med hjälp av Azure CLI och Managed Disks

Den här artikeln visar hur du skapar en kopia av din virtuella Azure-dator (VM) som kör Linux med hjälp av Azure CLI. Om du vill kopiera, skapa, lagra och dela VM-avbildningar i skala, se [delade avbildnings gallerier](../shared-images-cli.md).

Du kan också [Ladda upp och skapa en virtuell dator från en virtuell hård disk](upload-vhd.md).

## <a name="prerequisites"></a>Förutsättningar

-   Installera [Azure CLI](/cli/azure/install-az-cli2).

-   Logga in på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index#az-login).

-   Använd en virtuell Azure-dator som källa för din kopia.

## <a name="stop-the-source-vm"></a>Stoppa den virtuella käll datorn

Frigör den virtuella käll datorn genom att använda [AZ VM deallokering](/cli/azure/vm#az-vm-deallocate).
I följande exempel avallokeras den virtuella datorn med namnet *myVM* i resurs gruppen *myResourceGroup*:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>Kopiera den virtuella käll datorn

Om du vill kopiera en virtuell dator skapar du en kopia av den underliggande virtuella hård disken. Den här processen skapar en specialiserad virtuell hård disk (VHD) som en hanterad disk som innehåller samma konfiguration och inställningar som den virtuella käll datorn.

Mer information om Azure Managed Disks finns i [Översikt över Azure Managed Disks](../managed-disks-overview.md). 

1.  Lista varje virtuell dator och namnet på sin OS-disk med [AZ VM List](/cli/azure/vm#az-vm-list). I följande exempel visas alla virtuella datorer i resurs gruppen med namnet *myResourceGroup*:
    
    ```azurecli
    az vm list -g myResourceGroup \
         --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' \
         --output table
    ```

    Utdata ser ut ungefär så här:

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  Kopiera disken genom att skapa en ny hanterad disk och genom att använda [AZ disk Create](/cli/azure/disk#az-disk-create). I följande exempel skapas en disk med namnet *myCopiedDisk* från den hanterade disken med namnet min *disk*:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Verifiera de hanterade diskarna nu i din resurs grupp med hjälp av [AZ disk List](/cli/azure/disk#az-disk-list). I följande exempel visas de hanterade diskarna i resurs gruppen med namnet *myResourceGroup*:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>Konfigurera ett virtuellt nätverk

Följande valfria steg skapar ett nytt virtuellt nätverk, undernät, offentlig IP-adress och ett virtuellt nätverks gränssnitts kort (NIC).

Om du kopierar en virtuell dator för fel söknings syfte eller ytterligare distributioner kanske du inte vill använda en virtuell dator i ett befintligt virtuellt nätverk.

Om du vill skapa en virtuell nätverks infrastruktur för dina kopierade virtuella datorer följer du de följande stegen. Om du inte vill skapa ett virtuellt nätverk kan du gå vidare till [skapa en virtuell dator](#create-a-vm).

1.  Skapa det virtuella nätverket med [AZ Network VNet Create](/cli/azure/network/vnet#az-network-vnet-create). I följande exempel skapas ett virtuellt nätverk med namnet *myVnet* och ett undernät med namnet *subsubnet*:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Skapa en offentlig IP-adress med [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create). I följande exempel skapas en offentlig IP-adress med namnet *myPublicIP* med DNS-namnet *mypublicdns*. (Eftersom DNS-namnet måste vara unikt anger du ett unikt namn.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Skapa NÄTVERKSKORTet med [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create).
    I följande exempel skapas ett nätverkskort med namnet *myNic* som är kopplat till under nätet för *under* nätet:

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>Skapa en virtuell dator

Skapa en virtuell dator genom att använda [AZ VM Create](/cli/azure/vm#az-vm-create).

Ange den kopierade hanterade disk som ska användas som OS-disk ( `--attach-os-disk` ) enligt följande:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder ett [delat avbildnings Galleri](../shared-images-cli.md) för att hantera VM-avbildningar.
