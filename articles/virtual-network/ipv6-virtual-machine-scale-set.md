---
title: Distribuera skalnings uppsättningar för virtuella datorer med IPv6 i Azure
titlesuffix: Azure Virtual Network
description: Den här artikeln visar hur du distribuerar skalnings uppsättningar för virtuella datorer med IPv6 i ett virtuellt Azure-nätverk.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: f969d7edc22c9c36481ca42449193af5f8c7b0d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84710005"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure"></a>Distribuera skalnings uppsättningar för virtuella datorer med IPv6 i Azure

Den här artikeln visar hur du distribuerar en skalnings uppsättning för virtuella datorer med dubbla stackar (IPv4 + IPv6) med en extern stack-extern belastningsutjämnare i ett virtuellt Azure-nätverk. Processen för att skapa en IPv6-kompatibel skalnings uppsättning för virtuella datorer är nästan identisk med processen för att skapa enskilda virtuella datorer som beskrivs [här](ipv6-configure-standard-load-balancer-template-json.md). Du börjar med stegen som liknar de som beskrivs för enskilda virtuella datorer:
1.    Skapa IPv4-och IPv6-offentliga IP-adresser.
2.    Skapa en belastningsutjämnare med dubbla stackar.  
3.    Skapa regler för nätverks säkerhets grupper (NSG).  

Det enda steg som skiljer sig från enskilda virtuella datorer är att skapa konfigurationen för nätverks gränssnitt (NIC) som använder resursen för skalnings uppsättning för virtuella datorer: networkProfile/networkInterfaceConfigurations. JSON-strukturen liknar den för Microsoft. Network/networkInterfaces-objektet som används för enskilda virtuella datorer med att lägga till NÄTVERKSKORTet och IPv4 IpConfiguration som det primära gränssnittet med hjälp av attributet **"Primary": true**  som det visas i följande exempel:

```json
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[variables('nicName')]",
                "properties": {
                  "primary": true,
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups','VmssNsg')]"
          },                  
                  "ipConfigurations": [
                    {
                      "name": "[variables('ipConfigName')]",
                      "properties": {
                        "primary": true,
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv4",                       
                        "publicipaddressconfiguration": {
                          "name": "pub1",
                          "properties": {
                            "idleTimeoutInMinutes": 15
                          }
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer', 'bePool'))]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatPools', 'loadBalancer', 'natPool')]"
                          }
                        ]
                      }
                    },
                    {
                      "name": "[variables('ipConfigNameV6')]",
                      "properties": {
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets','MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv6",
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer','bePoolv6')]"
                          }
                        ],                        
                      }
                    }
                  ]
                }
              }
            ]
          }

```


## <a name="sample-virtual-machine-scale-set-template-json"></a>Exempel på mall för skalnings uppsättning för virtuell dator

Så här distribuerar du en skalnings uppsättning för virtuella datorer med dubbla stackar (IPv4 + IPv6) med externa stack-Load Balancer och exempel mal len för virtuella nätverk [här](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/).
## <a name="next-steps"></a>Nästa steg

Mer information om IPv6-stöd i virtuella Azure-nätverk finns i [Vad är IPv6 för Azure Virtual Network?](ipv6-overview.md).
