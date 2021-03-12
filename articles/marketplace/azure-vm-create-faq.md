---
title: Vanliga frågor om VM på Azure Marketplace
description: Vanliga frågor som påträffades när du skapade en virtuell dator på Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 09644747c36b5406960097e52bbeeef1fa157e89
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "102630001"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Vanliga frågor om VM på Azure Marketplace

Vanliga frågor och svar (FAQ) omfattar vanliga problem som kan uppstå när du skapar ett erbjudande för virtuell dator (VM) i Azure Marketplace.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Hur gör jag för att konfigurera ett virtuellt privat nätverk (VPN) för att fungera med mina virtuella datorer?

Om du använder Azure Resource Manager distributions modell har du tre alternativ:

- [Skapa en Route-baserad VPN-gateway med hjälp av Azure Portal](../vpn-gateway/tutorial-create-gateway-portal.md)
- [Skapa en Route-baserad VPN-gateway med hjälp av Azure PowerShell](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [Skapa en Route-baserad VPN-gateway med CLI](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Vad är Microsofts support principer för att köra Microsoft Server-program på Azure-baserade virtuella datorer?

Du hittar information på [Microsoft Server Software support för Microsoft Azure virtuella datorer](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Hur hanterar jag det anpassade skript tillägget i en virtuell dator i Start aktiviteten?

Mer information om hur du använder tillägget för anpassat skript med hjälp av Azure PowerShell modul, Azure Resource Manager mallar och fel söknings steg i Windows-system, finns i [anpassat skript tillägg för Windows](../virtual-machines/extensions/custom-script-windows.md).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Är 32-bitars program eller tjänster som stöds i Azure Marketplace?

Nej. De operativ system och standard tjänster som stöds för virtuella Azure-datorer är alla 64-bitars. Även om de flesta 64-bitars operativ system stöder 32-bitars versioner av program för bakåtkompatibilitet, stöds inte användning av 32-bitars program som en del av din VM-lösning och rekommenderas inte. Återskapa ditt program som ett 64-bitars projekt.

Mer information finns i de här artiklarna:

- [Köra 32-bitars program](/windows/desktop/WinProg64/running-32-bit-applications)
- [Stöd för 32-bitars operativsystem i Azure Virtual Machines](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsofts serverprogramsupport för Microsoft Azure Virtual Machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Fel: VHD har redan registrerats med avbildnings lagrings plats som resurs

Varje gång jag försöker skapa en avbildning från mina virtuella hård diskar visas felet "VHD har redan registrerats med avbildnings lagrings plats som resurs" i Azure PowerShell. Jag har inte skapat någon avbildning förut eller kunde inte hitta någon bild med det här namnet i Azure. Hur gör jag för att lösa det?

Det här problemet uppstår vanligt vis om du har skapat en virtuell dator från en virtuell hård disk som har låst den. Bekräfta att det inte finns någon virtuell dator som har allokerats från denna virtuella hård disk och försök sedan igen. Om problemet kvarstår öppnar du ett support ärende. Se [Support för partner Center](support.md).

## <a name="how-do-i-create-a-vm-from-a-generalized-vhd"></a>Hur gör jag för att skapa en virtuell dator från en generaliserad virtuell hård disk?

### <a name="prepare-an-azure-resource-manager-template"></a>Förbereda en Azure Resource Manager mall

I det här avsnittet beskrivs hur du skapar och distribuerar en användardefinierad avbildning av en virtuell dator (VM). Du kan göra detta genom att tillhandahålla VHD-avbildningar för operativ system och data diskar från en Azure-distribuerad virtuell hård disk. De här stegen distribuerar den virtuella datorn med generaliserad virtuell hård disk.

1. Logga in på Azure-portalen.
2. Överför dina generaliserade operativ system diskar och datadisk-VHD: ar till ditt Azure Storage-konto.
3. På sidan start väljer du skapa en resurs, söker efter "mall distribution" och väljer Skapa.
4. Välj Bygg en egen mall i redigeraren.

   :::image type="content" source="media/vm/template-deployment.png" alt-text="Visar valet av mall":::

5. Klistra in följande JSON-mall i redigeraren och välj Spara.
 ```json
  {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "userStorageAccountName": {
               "type": "String"
           },
           "userStorageContainerName": {
               "defaultValue": "vhds",
               "type": "String"
           },
           "dnsNameForPublicIP": {
               "type": "String"
           },
           "adminUserName": {
               "defaultValue": "isv",
               "type": "String"
           },
           "adminPassword": {
               "defaultValue": "",
               "type": "SecureString"
           },
           "osType": {
               "defaultValue": "windows",
               "allowedValues": [
                   "windows",
                   "linux"
               ],
               "type": "String"
           },
           "subscriptionId": {
               "type": "String"
           },
           "location": {
               "type": "String"
           },
           "vmSize": {
               "type": "String"
           },
           "publicIPAddressName": {
               "type": "String"
           },
           "vmName": {
               "type": "String"
           },
           "virtualNetworkName": {
               "type": "String"
           },
           "nicName": {
               "type": "String"
           },
           "vhdUrl": {
               "type": "String",
               "metadata": {
                   "description": "VHD Url..."
               }
           }
       },
       "variables": {
           "addressPrefix": "10.0.0.0/16",
           "subnet1Name": "Subnet-1",
           "subnet2Name": "Subnet-2",
           "subnet1Prefix": "10.0.0.0/24",
           "subnet2Prefix": "10.0.1.0/24",
           "publicIPAddressType": "Dynamic",
           "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
           "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
           "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
           "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
       },
       "resources": [
           {
               "type": "Microsoft.Network/publicIPAddresses",
               "apiVersion": "2015-06-15",
               "name": "[parameters('publicIPAddressName')]",
               "location": "[parameters('location')]",
               "properties": {
                   "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                   "dnsSettings": {
                       "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                   }
               }
           },
           {
               "type": "Microsoft.Network/virtualNetworks",
               "apiVersion": "2015-06-15",
               "name": "[parameters('virtualNetworkName')]",
               "location": "[parameters('location')]",
               "properties": {
                   "addressSpace": {
                       "addressPrefixes": [
                           "[variables('addressPrefix')]"
                       ]
                   },
                   "subnets": [
                       {
                           "name": "[variables('subnet1Name')]",
                           "properties": {
                               "addressPrefix": "[variables('subnet1Prefix')]"
                           }
                       },
                       {
                           "name": "[variables('subnet2Name')]",
                           "properties": {
                               "addressPrefix": "[variables('subnet2Prefix')]"
                           }
                       }
                   ]
               }
           },
           {
               "type": "Microsoft.Network/networkInterfaces",
               "apiVersion": "2015-06-15",
               "name": "[parameters('nicName')]",
               "location": "[parameters('location')]",
               "dependsOn": [
                   "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                   "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
               ],
               "properties": {
                   "ipConfigurations": [
                       {
                           "name": "ipconfig1",
                           "properties": {
                               "privateIPAllocationMethod": "Dynamic",
                               "publicIPAddress": {
                                   "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                               },
                               "subnet": {
                                   "id": "[variables('subnet1Ref')]"
                               }
                           }
                       }
                   ]
               }
           },
           {
               "type": "Microsoft.Compute/virtualMachines",
               "apiVersion": "2015-06-15",
               "name": "[parameters('vmName')]",
               "location": "[parameters('location')]",
               "dependsOn": [
                   "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
               ],
               "properties": {
                   "hardwareProfile": {
                       "vmSize": "[parameters('vmSize')]"
                   },
                   "osProfile": {
                       "computername": "[parameters('vmName')]",
                       "adminUsername": "[parameters('adminUsername')]",
                       "adminPassword": "[parameters('adminPassword')]"
                   },
                   "storageProfile": {
                       "osDisk": {
                           "name": "[concat(parameters('vmName'),'-osDisk')]",
                           "osType": "[parameters('osType')]",
                           "caching": "ReadWrite",
                           "image": {
                               "uri": "[parameters('vhdUrl')]"
                           },
                           "vhd": {
                               "uri": "[variables('osDiskVhdName')]"
                           },
                           "createOption": "FromImage"
                       }
                   },
                   "networkProfile": {
                       "networkInterfaces": [
                           {
                               "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                           }
                       ]
                   }
               }
           }
       ]
   }
   ```


6. Ange parameter värden för de visade egenskaps sidorna för anpassad distribution.

 **TABELL 1**

| **ResourceGroupName** | **Befintligt namn på Azure-resurs gruppen. Använd vanligt vis samma RG som nyckel valvet.** |
| --- | --- |
| TemplateFile | Fullständig sökväg till filen VHDtoImage.jspå. |
| userStorageAccountName | Namn på lagringskontot. |
| dnsNameForPublicIP | DNS-namn för den offentliga IP-adressen; måste vara gemener. |
| subscriptionId | Prenumerations-ID för Azure. |
| Location | Standard Azure-geografisk plats för resurs gruppen. |
| vmName | Namn på den virtuella datorn. |
| vhdUrl | Webb adressen för den virtuella hård disken. |
| vmSize | Storlek på den virtuella dator instansen. |
| publicIPAddressName | Namn på den offentliga IP-adressen. |
| virtualNetworkName | Namnet på det virtuella nätverket. |
| nicName | Nätverks gränssnitts kortets namn för det virtuella nätverket. |
| adminUserName | Användar namn för administratörs kontot. |
| adminPassword | Administratörs lösen ord. |
|

7. När du har angett dessa värden väljer du köp.

Azure kommer att börja distribuera. Den skapar en ny virtuell dator med den angivna ohanterade virtuella hård disken i den angivna sökvägen för lagrings kontot. Du kan följa förloppet i Azure Portal genom att välja Virtual Machines till vänster i portalen. När den virtuella datorn skapas kommer statusen att ändras från att börja köras.

Använd den här mallen för distribution av virtuella datorer i generation 2:
 ```json
 {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "userStorageAccountName": {
              "type": "String"
          },
          "userStorageContainerName": {
              "defaultValue": "vhds",
              "type": "String"
          },
          "dnsNameForPublicIP": {
              "type": "String"
          },
          "adminUserName": {
              "defaultValue": "isv",
              "type": "String"
          },
          "adminPassword": {
              "defaultValue": "",
              "type": "SecureString"
          },
          "osType": {
              "defaultValue": "windows",
              "allowedValues": [
                  "windows",
                  "linux"
              ],
              "type": "String"
          },
          "subscriptionId": {
              "type": "String"
          },
          "location": {
              "type": "String"
          },
          "vmSize": {
              "type": "String"
          },
          "publicIPAddressName": {
              "type": "String"
          },
          "vmName": {
              "type": "String"
          },
          "virtualNetworkName": {
              "type": "String"
          },
          "nicName": {
              "type": "String"
          },
          "vhdUrl": {
              "type": "String",
              "metadata": {
                  "description": "VHD Url..."
              }
          }
      },
      "variables": {
          "addressPrefix": "10.0.0.0/16",
          "subnet1Name": "Subnet-1",
          "subnet2Name": "Subnet-2",
          "subnet1Prefix": "10.0.0.0/24",
          "subnet2Prefix": "10.0.1.0/24",
          "publicIPAddressType": "Dynamic",
          "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
          "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
          "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
      },
      "resources": [
          {
              "type": "Microsoft.Network/publicIPAddresses",
              "apiVersion": "2015-06-15",
              "name": "[parameters('publicIPAddressName')]",
              "location": "[parameters('location')]",
              "properties": {
                  "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                  "dnsSettings": {
                      "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                  }
              }
          },
          {
              "type": "Microsoft.Network/virtualNetworks",
              "apiVersion": "2015-06-15",
              "name": "[parameters('virtualNetworkName')]",
              "location": "[parameters('location')]",
              "properties": {
                  "addressSpace": {
                      "addressPrefixes": [
                          "[variables('addressPrefix')]"
                      ]
                  },
                  "subnets": [
                      {
                          "name": "[variables('subnet1Name')]",
                          "properties": {
                              "addressPrefix": "[variables('subnet1Prefix')]"
                          }
                      },
                      {
                          "name": "[variables('subnet2Name')]",
                          "properties": {
                              "addressPrefix": "[variables('subnet2Prefix')]"
                          }
                      }
                  ]
              }
          },
          {
              "type": "Microsoft.Network/networkInterfaces",
              "apiVersion": "2015-06-15",
              "name": "[parameters('nicName')]",
              "location": "[parameters('location')]",
              "dependsOn": [
                  "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                  "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
              ],
              "properties": {
                  "ipConfigurations": [
                      {
                          "name": "ipconfig1",
                          "properties": {
                              "privateIPAllocationMethod": "Dynamic",
                              "publicIPAddress": {
                                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                              },
                              "subnet": {
                                  "id": "[variables('subnet1Ref')]"
                              }
                          }
                      }
                  ]
              }
          },
          {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2015-06-15",
              "name": "[parameters('vmName')]",
              "location": "[parameters('location')]",
              "dependsOn": [
                  "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
              ],
              "properties": {
                  "hardwareProfile": {
                      "vmSize": "[parameters('vmSize')]"
                  },
                  "osProfile": {
                      "computername": "[parameters('vmName')]",
                      "adminUsername": "[parameters('adminUsername')]",
                      "adminPassword": "[parameters('adminPassword')]"
                  },
                  "storageProfile": {
                      "osDisk": {
                          "name": "[concat(parameters('vmName'),'-osDisk')]",
                          "osType": "[parameters('osType')]",
                          "caching": "ReadWrite",
                          "image": {
                              "uri": "[parameters('vhdUrl')]"
                          },
                          "vhd": {
                              "uri": "[variables('osDiskVhdName')]"
                          },
                          "createOption": "FromImage"
                      }
                  },
                  "networkProfile": {
                      "networkInterfaces": [
                          {
                              "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                          }
                      ]
                  }
              }
          }
      ]
  }
  ```


### <a name="deploy-an-azure-vm-using-powershell"></a>Distribuera en virtuell Azure-dator med PowerShell

Kopiera och redigera följande skript för att ange värden för `$storageaccount` `$vhdUrl` variablerna och. Kör den för att skapa en Azure VM-resurs från din befintliga generaliserade virtuella hård disk.

```powershell
# storage account of existing generalized VHD
$storageaccount = "testwinrm11815"
# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName"
```


## <a name="next-steps"></a>Nästa steg

- [Felsökning av VM-certifiering](azure-vm-create-certification-faq.md)
