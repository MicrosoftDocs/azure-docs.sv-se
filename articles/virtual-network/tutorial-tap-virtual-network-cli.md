---
title: Skapa, ändra eller ta bort ett VNet-tryck – Azure CLI
description: Lär dig hur du skapar, ändrar eller tar bort ett virtuellt nätverk genom att trycka på Azure CLI.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/18/2018
ms.author: kaanan
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8e3a56e4a6eb1fb6eb633021178ef78f8ac7287d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96014796"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>Arbeta med ett virtuellt nätverk genom att trycka på Azure CLI

Med hjälp av Azure Virtual Network-TRYCKNINGen (Terminal Access Point) kan du kontinuerligt strömma din virtuella dators nätverks trafik till en insamlare eller ett analys verktyg för nätverks paket. Insamlings-eller analys verktyget tillhandahålls av en [virtuell nätverks utrustnings](https://azure.microsoft.com/solutions/network-appliances/) partner. För en lista över partner lösningar som har verifierats för att fungera med Virtual Network-tryckning, se [partner lösningar](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions). 

## <a name="create-a-virtual-network-tap-resource"></a>Skapa ett virtuellt nätverk tryck på resurs

Läs [nödvändiga komponenter](virtual-network-tap-overview.md#prerequisites) innan du skapar ett virtuellt nätverk tryck på resurs. Du kan köra kommandona som följer i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra kommando rads gränssnittet för Azure (CLI) från datorn. Azure Cloud Shell är ett kostnads fritt interaktivt gränssnitt, som inte kräver att Azure CLI installeras på datorn. Du måste logga in på Azure med ett konto som har rätt [behörigheter](virtual-network-tap-overview.md#permissions). Den här artikeln kräver Azure CLI version 2.0.46 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Det virtuella nätverkets tryckning är för närvarande tillgängligt som ett tillägg. För att installera tillägget måste du köra `az extension add -n virtual-network-tap` . Om du kör Azure CLI lokalt måste du också köra `az login` för att skapa en anslutning till Azure.

1. Hämta ID för din prenumeration i en variabel som används i ett senare steg:

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. Ange det prenumerations-ID som du ska använda för att skapa ett virtuellt nätverk tryck på resurs.

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. Registrera det prenumerations-ID som du ska använda för att skapa ett virtuellt nätverk tryck på resurs. Om du får ett registrerings fel när du skapar en tryck resurs kör du följande kommando:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. Om målet för det virtuella nätverket trycker är nätverks gränssnittet på den virtuella nätverks enheten för insamlare eller analys verktyg-

   - Hämta IP-konfigurationen för nätverks gränssnittet för den virtuella nätverks enheten i en variabel som används i ett senare steg. ID är den slut punkt som används för att aggregera kran trafiken. I följande exempel hämtas ID: t för *ipconfig1* IP-konfiguration för ett nätverks gränssnitt med namnet *myNetworkInterface* i en resurs grupp med namnet *myResourceGroup*:

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - Skapa det virtuella nätverket tryck på westcentralus Azure-region med ID: t för IP-konfigurationen som mål och en valfri port egenskap. Porten anger mål porten för nätverks gränssnittets IP-konfiguration där trafiken tas emot:  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. Om målet för det virtuella nätverket trycker på är en intern Azure Load Balancer:
  
   - Hämta klient delens IP-konfiguration för den interna Azure-belastningsutjämnaren i en variabel som används i ett senare steg. ID är den slut punkt som används för att aggregera kran trafiken. I följande exempel hämtas ID: t för *frontendipconfig1* klient DELENS IP-konfiguration för en belastningsutjämnare med namnet *myInternalLoadBalancer* i en resurs grupp med namnet *myResourceGroup*:

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```

   - Skapa det virtuella nätverket genom att trycka på med ID: t för klient delens IP-konfiguration som mål och en valfri port egenskap. Porten anger mål porten på klient delens IP-konfiguration där kran trafiken tas emot:  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. Bekräfta skapandet av det virtuella nätverket tryck på:

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>Lägg till en tryck konfiguration i ett nätverks gränssnitt

1. Hämta ID för ett befintligt virtuellt nätverk tryck på resurs. I följande exempel hämtas ett virtuellt nätverk med namnet *myTap* i en resurs grupp med namnet *myResourceGroup*:

   ```azurecli-interactive
   tapId=$(az network vnet tap show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. Skapa en tryckning på konfigurationen av den övervakade virtuella datorns nätverks gränssnitt. I följande exempel skapas en tryck konfiguration för ett nätverks gränssnitt med namnet *myNetworkInterface*:

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. Bekräfta skapandet av kran-konfigurationen:

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>Ta bort TRYCKNINGs konfigurationen i ett nätverks gränssnitt

   ```azurecli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>Visa en lista över virtuella nätverks kranar i en prenumeration

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>Ta bort ett virtuellt nätverk tryck i en resurs grupp

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
