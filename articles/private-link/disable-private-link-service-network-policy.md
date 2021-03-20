---
title: 'Inaktivera nätverks principer för Azures tjänst källa IP-adress för privat länk '
description: Lär dig hur du inaktiverar nätverks principer för privat Azure-länk
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 7528d12dcbfba09b4dae4ac07a5c683a7c11bd88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99548810"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>Inaktivera nätverks principer för tjänst Källans IP-adress för privata länkar

För att kunna välja en käll-IP-adress för din privata länk tjänst krävs en explicit inaktivera inställning `privateLinkServiceNetworkPolicies` i under nätet. Den här inställningen gäller bara för den angivna privata IP-adressen som du valde som käll-IP för den privata länk tjänsten. För andra resurser i under nätet styrs åtkomsten baserat på säkerhets regel definitionen för nätverks säkerhets grupper (NSG). 
 
När du använder portalen för att skapa en privat länk-tjänst inaktive ras den här inställningen automatiskt som en del av processen för att skapa. Distributioner med valfri Azure-klient (PowerShell, CLI eller mallar) kräver ytterligare ett steg för att ändra den här egenskapen. Du kan inaktivera principen med hjälp av Cloud Shell från Azure Portal eller lokala installationer av Azure PowerShell, Azure CLI eller använda Azure Resource Manager mallar.  
 
Följ stegen nedan om du vill inaktivera nätverks principer för privata Länkar för ett virtuellt nätverk med namnet *myVirtualNetwork* med ett *standard* -undernät som finns i en resurs grupp med namnet *myResourceGroup*. 

## <a name="using-azure-powershell"></a>Använda Azure PowerShell
I det här avsnittet beskrivs hur du inaktiverar privata slut punkts principer för undernät med Azure PowerShell.
Ersätt "default" i koden med namnet på det virtuella under nätet.

```azurepowershell
$virtualSubnetName = "default"
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $virtualSubnetName} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Använda Azure CLI
I det här avsnittet beskrivs hur du inaktiverar privata slut punkts principer för undernät med Azure CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
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
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>Nästa steg
- Läs mer om [privat Azure-slutpunkt](private-endpoint-overview.md)
 
