---
title: Skapa och konfigurera en Azure DDoS Protection plan med Azure CLI
description: Lär dig hur du skapar en DDoS Protection plan med hjälp av Azure CLI
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 59c5ca9ce9e95319b36e002da0b5d1438ef3fdd1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102203784"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-cli"></a>Snabb start: skapa och konfigurera Azure DDoS Protection standard med hjälp av Azure CLI

Kom igång med Azure DDoS Protection standard med hjälp av Azure CLI. 

En DDoS skydds plan definierar en uppsättning virtuella nätverk som har DDoS Protection standard aktiverat, mellan prenumerationer. Du kan konfigurera en DDoS skydds plan för din organisation och länka virtuella nätverk från flera prenumerationer till samma plan. 

I den här snabb starten skapar du en DDoS skydds plan och länkar den till ett virtuellt nätverk. 

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure CLI installerat lokalt eller Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver den här snabb starten Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-ddos-protection-plan"></a>Skapa en DDoS Protection Plan

I Azure allokerar du relaterade resurser till en resursgrupp. Du kan antingen använda en befintlig resurs grupp eller skapa en ny.

Om du vill skapa en resurs grupp använder du [AZ Group Create](/cli/azure/group#az-group-create). I det här exemplet ska vi namnge vår resurs grupp _MyResourceGroup_ och använda platsen _USA, östra_ :

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus
```

Skapa nu en DDoS-skydds plan med namnet _MyDdosProtectionPlan_:

```azurecli-interactive
az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

## <a name="enable-ddos-protection-for-a-virtual-network"></a>Aktivera DDoS-skydd för ett virtuellt nätverk

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>Aktivera DDoS-skydd för ett nytt virtuellt nätverk

Du kan aktivera DDoS-skydd när du skapar ett virtuellt nätverk. I det här exemplet ska vi namnge vårt virtuella nätverk _MyVnet_: 

```azurecli-interactive
az network vnet create \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --location eastus \
    --ddos-protection true
    --ddos-protection-plan MyDdosProtectionPlan
```

Du kan inte flytta ett virtuellt nätverk till en annan resurs grupp eller prenumeration när DDoS standard är aktive rad för det virtuella nätverket. Om du behöver flytta ett virtuellt nätverk med DDoS standard aktiverat inaktiverar du DDoS standard först, flyttar det virtuella nätverket och aktiverar sedan DDoS standard. Efter flyttningen återställs automatiskt justerade princip tröskelvärden för alla skyddade offentliga IP-adresser i det virtuella nätverket.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>Aktivera DDoS-skydd för ett befintligt virtuellt nätverk

När du [skapar en skydds plan för DDoS](#create-a-ddos-protection-plan)kan du koppla ett eller flera virtuella nätverk till planen. Om du vill lägga till fler än ett virtuellt nätverk visar du bara namn eller ID: n, blankstegsavgränsad. I det här exemplet ska vi lägga till _MyVnet_:

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
    --vnets MyVnet
```

Du kan också aktivera DDoS-skydd för ett angivet virtuellt nätverk:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --ddos-protection true
    --ddos-protection-plan MyDdosProtectionPlan
```

## <a name="validate-and-test"></a>Validera och testa

Börja med att kontrol lera informationen om din DDoS-skydds plan:

```azurecli-interactive
az network ddos-protection show \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

Kontrol lera att kommandot returnerar rätt information om din DDoS-skydds plan.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan behålla dina resurser för nästa självstudie. Om du inte längre behöver tar du bort resurs gruppen _MyResourceGroup_ . När du tar bort resurs gruppen tar du även bort DDoS-skydds planen och alla relaterade resurser. 

Ta bort resurs gruppen med hjälp av [AZ Group Delete](/cli/azure/group#az_group_delete):

```azurecli-interactive
az group delete \
--name MyResourceGroup 
```

Uppdatera ett angivet virtuellt nätverk för att inaktivera DDoS-skydd:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --ddos-protection false
    --ddos-protection-plan ""
```

Om du vill ta bort en DDoS skydds plan måste du först ta bort alla virtuella nätverk från den. 

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig att visa och konfigurera telemetri för din DDoS-skydds plan fortsätter du till självstudierna.

> [!div class="nextstepaction"]
> [Visa och konfigurera telemetri för DDoS-skydd](telemetry.md)
