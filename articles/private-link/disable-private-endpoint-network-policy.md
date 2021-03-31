---
title: Inaktivera nätverks principer för privata slut punkter i Azure
description: Lär dig hur du inaktiverar nätverks principer för privata slut punkter.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: a4c04033f3fb5ff523f0d80bb6c978955f4c9ec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84737520"
---
# <a name="disable-network-policies-for-private-endpoints"></a>Inaktivera nätverks principer för privata slut punkter

Nätverks principer som nätverks säkerhets grupper (NSG) stöds inte för privata slut punkter. För att kunna distribuera en privat slut punkt i ett angivet undernät krävs en explicit inaktivera inställning i det under nätet. Den här inställningen gäller bara för den privata slut punkten. För andra resurser i under nätet styrs åtkomsten baserat på säkerhets regel definitionen för nätverks säkerhets grupper (NSG). 
 
När du använder portalen för att skapa en privat slut punkt inaktive ras den här inställningen automatiskt som en del av processen för att skapa. Distribution som använder andra klienter kräver ytterligare ett steg för att ändra den här inställningen. Du kan inaktivera inställningen med Cloud Shell från Azure Portal eller lokala installationer av Azure PowerShell, Azure CLI eller använda Azure Resource Manager mallar.  
 
I följande exempel beskrivs hur du inaktiverar `PrivateEndpointNetworkPolicies` för ett virtuellt nätverk med namnet *myVirtualNetwork* med ett *standard* -undernät som finns i en resurs grupp med namnet *myResourceGroup*.

## <a name="using-azure-powershell"></a>Använda Azure PowerShell
I det här avsnittet beskrivs hur du inaktiverar privata slut punkts principer för undernät med Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Använda Azure CLI
I det här avsnittet beskrivs hur du inaktiverar privata slut punkts principer för undernät med Azure CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
```
## <a name="using-a-template"></a>Använda en mall
I det här avsnittet beskrivs hur du inaktiverar privata slut punkts principer för undernät med Azure Resource Manager-mall.
```json
{ 
          "name": "myVirtualNetwork", 
          "type": "Microsoft.Network/virtualNetworks", 
          "apiVersion": "2019-04-01", 
          "location": "WestUS", 
          "properties": { 
                "addressSpace": { 
                     "addressPrefixes": [ 
                          "10.0.0.0/16" 
                        ] 
                  }, 
                  "subnets": [ 
                         { 
                                "name": "default", 
                                "properties": { 
                                    "addressPrefix": "10.0.0.0/24", 
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>Nästa steg
- Läs mer om [privat Azure-slutpunkt](private-endpoint-overview.md)
 
