---
title: SKU:n är inte tillgänglig
description: Beskriver hur du felsöker felet SKU:n är inte tillgänglig när du distribuerar resurser med Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 04/14/2021
ms.openlocfilehash: 3baedf6a5c9f2dbfd3ddf666b458fac649fce2ac
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503906"
---
# <a name="resolve-errors-for-sku-not-available"></a>Åtgärda fel med otillgänglig SKU

I den här artikeln beskrivs hur du löser **felet SkuNotAvailable.** Om du inte kan hitta en lämplig SKU i den regionen/zonen eller en alternativ region/zon som uppfyller dina affärsbehov skickar du en [SKU-begäran](/troubleshoot/azure/general/region-access-request-process) till Azure Support.

## <a name="symptom"></a>Symptom

När du distribuerar en resurs (vanligtvis en virtuell dator) får du följande felkod och felmeddelande:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Orsak

Du får det här felet när resurs-SKU:n som du har valt (till exempel VM-storlek) inte är tillgänglig för den plats som du har valt.

Om du distribuerar en virtuell Azure Spot-dator eller skalningsuppsättningsinstans för oskadlig plats finns det ingen kapacitet för Azure Spot på den här platsen. Mer information finns i [Felmeddelanden för spot.](../../virtual-machines/error-codes-spot.md)

## <a name="solution-1---powershell"></a>Lösning 1 – PowerShell

Om du vill ta reda på vilka SKU:er som är tillgängliga i en region/zon använder du kommandot [Get-AzComputeResourceSku.](/powershell/module/az.compute/get-azcomputeresourcesku) Filtrera resultaten efter plats. Du måste ha den senaste versionen av PowerShell för det här kommandot.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Resultatet innehåller en lista över SKU:er för platsen och eventuella begränsningar för denna SKU. Observera att en SKU kan visas som `NotAvailableForSubscription` .

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

Om du vill filtrera efter plats och SKU använder du:

```azurepowershell-interactive
$SubId = (Get-AzContext).Subscription.Id

$Region = "centralus" # change region here
$VMSku = "Standard_M" # change VM SKU here

$VMSKUs = Get-AzComputeResourceSku | where {$_.Locations.Contains($Region) -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains($VMSku)}

$OutTable = @()

foreach ($SkuName in $VMSKUs.Name)
        {
            $LocRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Location")){"NotAvavalableInRegion"}else{"Available - No region restrictions applied" }
            $ZoneRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Zone")){"NotAvavalableInZone: "+(((($VMSKUs | where Name -EQ $SkuName).Restrictions.RestrictionInfo.Zones)| Where-Object {$_}) -join ",")}else{"Available - No zone restrictions applied"}
            
            
            $OutTable += New-Object PSObject -Property @{
                                                         "Name" = $SkuName
                                                         "Location" = $Region
                                                         "Applies to SubscriptionID" = $SubId
                                                         "Subscription Restriction" = $LocRestriction
                                                         "Zone Restriction" = $ZoneRestriction
                                                         }
         }

$OutTable | select Name, Location, "Applies to SubscriptionID", "Region Restriction", "Zone Restriction" | Sort-Object -Property Name | FT
```

Kommandot returnerar resultat som:

```output
Name                   Location  Applies to SubscriptionID            Region Restriction                         Zone Restriction                        
----                   --------  -------------------------            ------------------------                   ----------------     
Standard_M128          centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-32ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-64ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128dms_v2    centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx NotAvavalableInRegion                      NotAvavalableInZone: 1,2,3
```

## <a name="solution-2---azure-cli"></a>Lösning 2 – Azure CLI

Du kan ta reda på vilka SKU:er som är tillgängliga i en region med kommandot [az vm list-skus.](/cli/azure/vm#az_vm_list_skus) Använd `--location` parametern för att filtrera utdata efter plats. Använd `--size` parametern för att söka efter ett namn med partiell storlek. Använd `--all` parametern för att visa all information, inklusive storlekar som inte är tillgängliga för den aktuella prenumerationen.

Du måste ha Azure CLI version 2.15.0 eller senare. Använd för att kontrollera din `az --version` version. Uppdatera installationen [om det behövs.](/cli/azure/update-azure-cli)

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --all --output table
```

Kommandot returnerar resultat som:

```output
ResourceType     Locations       Name              Zones    Restrictions
---------------  --------------  ----------------  -------  --------------
virtualMachines  southcentralus  Standard_F1       1,2,3    None
virtualMachines  southcentralus  Standard_F2       1,2,3    None
virtualMachines  southcentralus  Standard_F4       1,2,3    None
...
virtualMachines  southcentralus  Standard_F72s_v2  1,2,3    NotAvailableForSubscription, type: Zone, locations: southcentralus, zones: 1,2,3
...
```

## <a name="solution-3---azure-portal"></a>Lösning 3 – Azure Portal

Om du vill ta reda på vilka SKU:er som är tillgängliga i en region använder du [portalen](https://portal.azure.com). Logga in på portalen och lägg till en resurs via gränssnittet. När du anger värdena visas tillgängliga SKU:er för den resursen. Du behöver inte slutföra distributionen.

Starta till exempel processen med att skapa en virtuell dator. Om du vill se en annan tillgänglig storlek väljer **du Ändra storlek.**

![Skapa en virtuell dator](./media/error-sku-not-available/create-vm.png)

Du kan filtrera och bläddra igenom de tillgängliga storlekarna.

![Tillgängliga SKU:er](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>Lösning 4 – REST

Om du vill ta reda på vilka SKU:er som är tillgängliga i en region använder du åtgärden [Resurs-SKU:er –](/rest/api/compute/resourceskus/list) lista.

Den returnerar tillgängliga SKU:er och regioner i följande format:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```