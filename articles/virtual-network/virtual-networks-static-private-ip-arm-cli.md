---
title: Konfigurera privata IP-adresser för virtuella datorer – Azure CLI
description: Lär dig hur du konfigurerar privata IP-adresser för virtuella datorer med hjälp av Azures kommando rads gränssnitt (CLI).
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: allensu
ms.openlocfilehash: c34ab73422d8dd41feb9da542ed63fdba060fe3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84708169"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Konfigurera privata IP-adresser för en virtuell dator med hjälp av Azure CLI


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> Följande exempel på Azure CLI-kommandon förväntar sig en befintlig enkel miljö. Om du vill köra kommandona som de visas i det här dokumentet skapar du först test miljön som beskrivs i [skapa ett VNet](quick-create-cli.md).

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Ange en statisk privat IP-adress när du skapar en virtuell dator

Utför följande steg för att skapa en virtuell dator med namnet *DNS01* i *klient delens* undernät med namnet *TestVNet* med en statisk privat IP-adress för *192.168.1.101*:

1. Om du inte redan har gjort det kan du installera och konfigurera den senaste [Azure CLI](/cli/azure/install-azure-cli) och logga in på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index).

2. Kör kommandot [AZ Network NIC Create](/cli/azure/network/nic) för att skapa ett nätverkskort med en statisk privat IP-adress. Listan som visas efter utdatan beskriver de parametrar som används. 
   
    ```azurecli
    az network nic create \
    --resource-group TestRG \
    --name TestNIC \
    --location centralus \
    --subnet FrontEnd \
    --private-ip-address 192.168.1.101 \
    --vnet-name TestVNet
    ```

    Förväntad utdata:
   
    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.101",
                "privateIPAllocationMethod": "Static",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>"
        }
    }
    ```
    
    Parametrar:

    * `--private-ip-address`: NÄTVERKSKORTets statiska privata IP-adress.
    * `--vnet-name`: Namnet på det VNet som NÄTVERKSKORTet ska skapas i.
    * `--subnet`: Namnet på det undernät som NÄTVERKSKORTet ska skapas i.

3. Kör kommandot [Azure VM Create](/cli/azure/vm/nic) för att skapa den virtuella datorn med hjälp av den offentliga IP-adressen och NIC som skapades tidigare. Listan som visas efter utdatan beskriver de parametrar som används.
   
    ```azurecli
    az vm create \
    --resource-group TestRG \
    --name DNS01 \
    --location centralus \
    --image Debian \
    --admin-username adminuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics TestNIC
    ```

    Förväntad utdata:
   
    ```json
    {
        "fqdns": "",
        "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01",
        "location": "centralus",
        "macAddress": "00-0D-3A-92-C1-66",
        "powerState": "VM running",
        "privateIpAddress": "192.168.1.101",
        "publicIpAddress": "",
        "resourceGroup": "TestRG"
    }
    ```
   
   Andra parametrar än de grundläggande [AZ](/cli/azure/vm) -parametrarna för VM-skapande.

   * `--nics`: Namnet på det nätverkskort som den virtuella datorn är kopplad till.
   
Vi rekommenderar att du inte statiskt tilldelar den privata IP-adress som tilldelats den virtuella Azure-datorn i operativ systemet på en virtuell dator, om det inte behövs, t. ex. När [du tilldelar flera IP-adresser till en virtuell Windows-dator](virtual-network-multiple-ip-addresses-cli.md). Om du manuellt ställer in den privata IP-adressen i operativ systemet måste du se till att den är samma adress som den privata IP-adress som tilldelats Azure- [nätverks gränssnittet](virtual-network-network-interface-addresses.md#change-ip-address-settings), eller så kan du förlora anslutningen till den virtuella datorn. Läs mer om inställningar för [privata IP-adresser](virtual-network-network-interface-addresses.md#private) .

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Hämta information om statisk privat IP-adress för en virtuell dator

Kör följande Azure CLI-kommando för att kontrol lera värdena för *privat IP-allokering-metod* och *privat IP-adress*:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Förväntad utdata:

```json
"192.168.1.101"
```

Om du vill visa den specifika IP-informationen för NÄTVERKSKORTet för den virtuella datorn frågar du NÄTVERKSKORTet specifikt:

```azurecli
az network nic show \
-g testrg \
-n testnic \
--query 'ipConfigurations[0].{PrivateAddress:privateIpAddress,IPVer:privateIpAddressVersion,IpAllocMethod:p
rivateIpAllocationMethod,PublicAddress:publicIpAddress}'
```

Resultatet ser ut ungefär så här:

```json
{
    "IPVer": "IPv4",
    "IpAllocMethod": "Static",
    "PrivateAddress": "192.168.1.101",
    "PublicAddress": null
}
```

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Ta bort en statisk privat IP-adress från en virtuell dator

Du kan inte ta bort en statisk privat IP-adress från ett nätverkskort i Azure CLI för Azure Resource Manager distributioner. Du måste:
- Skapa ett nytt nätverkskort som använder en dynamisk IP-adress
- Ange NÄTVERKSKORTet på den virtuella datorn med det nyligen skapade NÄTVERKSKORTet. 

Utför följande steg för att ändra NÄTVERKSKORTet för den virtuella datorn som används i föregående kommandon:

1. Kör kommandot **Azure Network NIC Create** för att skapa ett nytt nätverkskort med dynamisk IP-allokering med en ny IP-adress. Eftersom ingen IP-adress har angetts är tilldelnings metoden **dynamisk**.

    ```azurecli
    az network nic create     \
    --resource-group TestRG     \
    --name TestNIC2     \
    --location centralus     \
    --subnet FrontEnd    \
    --vnet-name TestVNet
    ```        
   
    Förväntad utdata:

    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.4",
                "privateIPAllocationMethod": "Dynamic",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "0808a61c-476f-4d08-98ee-0fa83671b010"
        }
    }
    ```

2. Kör kommandot **Azure VM set** för att ändra nätverkskortet som används av den virtuella datorn.
   
    ```azurecli
   az vm nic set --resource-group TestRG --vm-name DNS01 --nics TestNIC2
    ```

    Förväntad utdata:
   
    ```json
    [
        {
            "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC3",
            "primary": true,
            "resourceGroup": "TestRG"
        }
    ]
    ```

    > [!NOTE]
    > Om den virtuella datorn är tillräckligt stor för att ha mer än ett nätverkskort kör du kommandot **Azure Network NIC Delete** för att ta bort det gamla nätverkskortet.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att hantera [Inställningar för IP-adresser](virtual-network-network-interface-addresses.md).
