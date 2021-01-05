---
title: Utforska dina Azure-resurser
description: Lär dig att använda det aktuella resurs diagrammets frågespråk för att utforska dina resurser och identifiera hur de är anslutna.
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 01f9c0c574d8a8d6d4b5f06fc1398313649cfb8d
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882916"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Utforska dina Azure-resurser med resursgrafer

Azure Resource Graph ger möjlighet att utforska och upptäcka dina Azure-resurser snabbt och i stor skala. Utformad för snabba svar är det ett bra sätt att lära dig om din miljö och även om de egenskaper som finns på dina Azure-resurser.

## <a name="explore-virtual-machines"></a>Utforska virtuella datorer

En gemensam resurs i Azure är en virtuell dator. Som resurs typ har virtuella datorer många egenskaper som kan frågas. Varje egenskap innehåller ett alternativ för att filtrera eller hitta exakt den resurs som du letar efter.

### <a name="virtual-machine-discovery"></a>Identifiering av virtuell dator

Vi börjar med en enkel fråga för att hämta en enskild virtuell dator från vår miljö och tittar på de egenskaper som returneras.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1" | ConvertTo-Json -Depth 100
```

> [!NOTE]
> Azure PowerShell `Search-AzGraph` cmdleten returnerar en **PSCustomObject** som standard. Om du vill att utdata ska se ut på samma sätt som det som returneras av Azure CLI, `ConvertTo-Json` används cmdleten. Standardvärdet för **djup** är _2_. Om du ställer in det på _100_ ska alla returnerade nivåer konverteras.

JSON-resultatet är strukturerat på samma sätt som i följande exempel:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1",
    "plan": {},
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_B2s"
      },
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "/subscriptions/<subscriptionId>/MyResourceGroup/providers/Microsoft.Network/networkInterfaces/contosovm1535",
            "resourceGroup": "MyResourceGroup"
          }
        ]
      },
      "osProfile": {
        "adminUsername": "localAdmin",
        "computerName": "ContosoVM1",
        "secrets": [],
        "windowsConfiguration": {
          "enableAutomaticUpdates": true,
          "provisionVMAgent": true
        }
      },
      "provisioningState": "Succeeded",
      "storageProfile": {
        "dataDisks": [],
        "imageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": 127,
          "managedDisk": {
            "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
            "resourceGroup": "MyResourceGroup",
            "storageAccountType": "Premium_LRS"
          },
          "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
          "osType": "Windows"
        }
      },
      "vmId": "bbb9b451-6dc7-4117-bec5-c971eb1118c6"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {},
    "subscriptionId": "<subscriptionId>",
    "tags": {},
    "type": "microsoft.compute/virtualmachines"
  }
]
```

Egenskaperna anger ytterligare information om den virtuella dator resursen. Dessa egenskaper är: operativ system, diskar, taggar och resurs gruppen och prenumerationen som den är medlem i.

### <a name="virtual-machines-by-location"></a>Virtuella datorer efter plats

Vi tar med det som vi lärt dig om den virtuella dator resursen. Använd egenskapen **location** för att räkna alla virtuella datorer efter plats. För att uppdatera frågan tar vi bort gränsen och sammanfattar antalet plats värden.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

JSON-resultatet är strukturerat på samma sätt som i följande exempel:

```json
[
  {
    "count_": 386,
    "location": "eastus"
  },
  {
    "count_": 215,
    "location": "southcentralus"
  },
  {
    "count_": 59,
    "location": "westus"
  }
]
```

Nu kan vi se hur många virtuella datorer som finns i varje Azure-region.

### <a name="virtual-machines-by-sku"></a>Virtuella datorer efter SKU

För att gå tillbaka till de ursprungliga egenskaperna för den virtuella datorn försöker vi hitta alla virtuella datorer som har en SKU-storlek på **Standard_B2s**. När du tittar på den JSON som returneras ser vi att den är lagrad i **egenskaperna. hardwareprofile. vmsize**. Vi uppdaterar frågan för att hitta alla virtuella datorer som matchar den här storleken och returnerar bara namnet på den virtuella datorn och regionen.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| project name, resourceGroup
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>Virtuella datorer som är anslutna till Premium-hanterade diskar

För att få information om Premium-hanterade diskar som är anslutna till dessa **Standard_B2s** virtuella datorer expanderar vi frågan för att returnera resurs-ID för de hanterade diskarna.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

Resultatet är en lista över disk-ID: n.

### <a name="managed-disk-discovery"></a>Identifiering av hanterad disk

Med den första posten från föregående fråga kommer vi att utforska de egenskaper som finns på den hanterade disk som var kopplad till den första virtuella datorn. Den uppdaterade frågan använder disk-ID och ändrar typen.

Exempel på utdata från föregående fråga till exempel:

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
```

```kusto
Resources
| where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

Innan du kör frågan vet vi att **typen** borde nu vara **Microsoft. Compute/disks**? Om du tittar på det fullständiga ID: t visas **/providers/Microsoft.Compute/disks/** som en del av strängen.
Detta sträng fragment ger dig en ledtråd för vilken typ av sökning du vill söka efter. En alternativ metod är att ta bort gränsen efter typ och i stället bara söka efter fältet ID. Eftersom ID: t är unikt returneras bara en post och **typ** egenskapen i den innehåller information.

> [!NOTE]
> För att det här exemplet ska fungera måste du ersätta ID-fältet med ett resultat från din egen miljö.

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

JSON-resultatet är strukturerat på samma sätt som i följande exempel:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "plan": {},
    "properties": {
      "creationData": {
        "createOption": "Empty"
      },
      "diskSizeGB": 127,
      "diskState": "ActiveSAS",
      "provisioningState": "Succeeded",
      "timeCreated": "2018-09-14T12:17:32.2570000Z"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {
      "name": "Premium_LRS",
      "tier": "Premium"
    },
    "subscriptionId": "<subscriptionId>",
    "tags": {
      "environment": "prod"
    },
    "type": "microsoft.compute/disks"
  }
]
```

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>Utforska virtuella datorer för att hitta offentliga IP-adresser

Den här uppsättningen med frågor söker först efter och lagrar alla nätverks gränssnitts resurser (NIC) som är anslutna till virtuella datorer. Sedan använder frågorna listan med nätverkskort för att hitta varje IP-adressresurs som är en offentlig IP-adress och lagra dessa värden. Slutligen tillhandahåller frågorna en lista över offentliga IP-adresser.

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nics.txt' file
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

```azurepowershell-interactive
# Use Resource Graph to get all NICs and store in the $nics variable
$nics = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20"

# Review the output of the query stored in the variable
$nics.nic
```

Använd filen (Azure CLI) eller variabeln (Azure PowerShell) i nästa fråga för att hämta information om relaterade nätverks gränssnitts resurser där det finns en offentlig IP-adress som är ansluten till NÄTVERKSKORTet.

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

```azurepowershell-interactive
# Use Resource Graph  with the $nics variable to get all related public IP addresses and store in $ips variable
$ips = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$($nics.nic -join "','")') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp"

# Review the output of the query stored in the variable
$ips.publicIp
```

Senast använder du listan över offentliga IP-adressresurser som lagras i filen (Azure CLI) eller variabeln (Azure PowerShell) för att hämta den faktiska offentliga IP-adressen från det relaterade objektet och Visa.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

```azurepowershell-interactive
# Use Resource Graph with the $ips variable to get the IP address of the public IP address resources
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$($ips.publicIp -join "','")') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip"
```

Information om hur du utför de här stegen i en enskild fråga med `join` -operatorn finns i [listan över virtuella datorer med deras nätverks gränssnitt och offentliga IP-](../samples/advanced.md#join-vmpip) exempel.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [frågespråket](query-language.md).
- Se språket som används i [Start frågor](../samples/starter.md).
- Se avancerade användnings områden i [avancerade frågor](../samples/advanced.md).
