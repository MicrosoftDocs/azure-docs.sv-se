---
title: Använd en mall för att distribuera Azure Azure-Virtual Machines
description: Lär dig hur du använder en mall för att distribuera Azures Virtual Machines för att spara kostnader.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/25/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 04319066c59dda5d240f527d86894674a505eaed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669361"
---
# <a name="deploy-azure-spot-virtual-machines-using-a-resource-manager-template"></a>Distribuera Azure-Virtual Machines med en Resource Manager-mall

Med hjälp av [Azure-Virtual Machines](../spot-vms.md) kan du dra nytta av vår outnyttjade kapacitet till betydande kostnads besparingar. Vid alla tidpunkter då Azure behöver kapaciteten, tar Azure-infrastrukturen bort Azure-Virtual Machines. Därför är Azures Virtual Machines bra för arbets belastningar som kan hantera avbrott som bearbetnings jobb, utvecklings-/test miljöer, stora beräknings arbets belastningar med mera.

Priser för Azure-Virtual Machines är varierande, baserat på region och SKU. Mer information finns i prissättning för virtuella datorer för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

Du har möjlighet att ange ett högsta pris som du är villig att betala per timme för den virtuella datorn. Det maximala priset för en virtuell Azure-dator kan anges i kronor (USD) med upp till 5 decimaler. Värdet skulle till exempel `0.98765` vara ett max pris på $0,98765 USD per timme. Om du anger det högsta priset så `-1` kommer den virtuella datorn inte att avlägsnas baserat på priset. Priset för den virtuella datorn är det aktuella priset för Azure-Virtual Machines eller priset för en standard-VM, vilket någonsin är mindre, så länge det finns kapacitet och tillgänglig kvot. Mer information om hur du ställer in högsta pris finns i [Azure-Virtual Machines – prissättning](../spot-vms.md#pricing).


## <a name="use-a-template"></a>Använda en mall

För distributioner av mallar för virtuella Azure-datorer, Använd `"apiVersion": "2019-03-01"` eller senare. Lägg till `priority` - `evictionPolicy` och- `billingProfile` egenskaperna i mallen:

```json
"priority": "Spot",
"evictionPolicy": "Deallocate",
"billingProfile": {
    "maxPrice": -1
}
```

Här är en exempel-mall med de tillagda egenskaperna för en virtuell Azure-dator. Ersätt resurs namnen med ditt eget och `<password>` med ett lösen ord för det lokala administratörs kontot på den virtuella datorn.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2019-03-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "vnetId": "/subscriptions/ec9fcd04-e188-48b9-abfc-abcd515f1836/resourceGroups/spotVM/providers/Microsoft.Network/virtualNetworks/spotVM",
        "subnetName": "default",
        "networkInterfaceName": "spotVMNIC",
        "publicIpAddressName": "spotVM-ip",
        "publicIpAddressType": "Dynamic",
        "publicIpAddressSku": "Basic",
        "virtualMachineName": "spotVM",
        "osDiskType": "Premium_LRS",
        "virtualMachineSize": "Standard_D2s_v3",
        "adminUsername": "azureuser",
        "adminPassword": "<password>",
        "diagnosticsStorageAccountName": "diagstoragespot2019",
        "diagnosticsStorageAccountId": "Microsoft.Storage/storageAccounts/diagstoragespot2019",
        "diagnosticsStorageAccountType": "Standard_LRS",
        "diagnosticsStorageAccountKind": "Storage",
        "subnetRef": "[concat(variables('vnetId'), '/subnets/', variables('subnetName'))]"
    },
    "resources": [
        {
            "name": "spotVM",
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2019-03-01",
            "location": "eastus",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            },
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIpAddress": {
                                "id": "[resourceId(resourceGroup().name, 'Microsoft.Network/publicIpAddresses', variables('publicIpAddressName'))]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "name": "[variables('publicIpAddressName')]",
            "type": "Microsoft.Network/publicIpAddresses",
            "apiVersion": "2019-02-01",
            "location": "eastus",
            "properties": {
                "publicIpAllocationMethod": "[variables('publicIpAddressType')]"
            },
            "sku": {
                "name": "[variables('publicIpAddressSku')]"
            }
        },
        {
            "name": "[variables('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2019-03-01",
            "location": "eastus",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', variables('networkInterfaceName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[variables('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[variables('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "18.04-LTS",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[variables('virtualMachineName')]",
                    "adminUsername": "[variables('adminUsername')]",
                    "adminPassword": "[variables('adminPassword')]"
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('https://', variables('diagnosticsStorageAccountName'), '.blob.core.windows.net/')]"
                    }
                },
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            }
        },
        {
            "name": "[variables('diagnosticsStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "location": "eastus",
            "properties": {},
            "kind": "[variables('diagnosticsStorageAccountKind')]",
            "sku": {
                "name": "[variables('diagnosticsStorageAccountType')]"
            }
        }
    ],
    "outputs": {
        "adminUsername": {
            "type": "string",
            "value": "[variables('adminUsername')]"
        }
    }
}
```

## <a name="simulate-an-eviction"></a>Simulera en avtagning

Du kan [simulera en avlägsnande](/rest/api/compute/virtualmachines/simulateeviction) av en virtuell Azure-dator för att testa hur bra ditt program kommer att återdamma till en plötslig avlägsning. 

Ersätt följande med din information: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

## <a name="next-steps"></a>Nästa steg

Du kan också skapa en virtuell Azure-dator med [Azure PowerShell](../windows/spot-powershell.md) eller [Azure CLI](spot-cli.md).

Fråga aktuell pris information med hjälp av [Azures API för åter försäljning](/rest/api/cost-management/retail-prices/azure-retail-prices) för information om priser för Azure-priser för virtuella datorer. `meterName`Och `skuName` kommer båda att innehålla `Spot` .

Om du stöter på ett fel, se [felkoder](../error-codes-spot.md).