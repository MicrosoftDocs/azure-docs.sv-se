---
title: Skapa och konfigurera en Azure DDoS Protection plan med Azure CLI
description: Lär dig hur du skapar en DDoS Protection plan med Azure CLI
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 8a8da50dc703d59dc16b5cb6253d39aeb33fd76d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777661"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-cli"></a>Snabbstart: Skapa och konfigurera Azure DDoS Protection Standard med Azure CLI

Kom igång med Azure DDoS Protection Standard med hjälp av Azure CLI. 

En DDoS-skyddsplan definierar en uppsättning virtuella nätverk som har DDoS-skyddsstandard aktiverad för flera prenumerationer. Du kan konfigurera en DDoS-skyddsplan för din organisation och länka virtuella nätverk från flera prenumerationer till samma plan. 

I den här snabbstarten skapar du en DDoS-skyddsplan och länkar den till ett virtuellt nätverk. 

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure CLI installerat lokalt eller Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver den här snabbstarten Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-ddos-protection-plan"></a>Skapa en DDoS Protection plan

I Azure allokerar du relaterade resurser till en resursgrupp. Du kan antingen använda en befintlig resursgrupp eller skapa en ny.

Använd az group create för att skapa [en resursgrupp.](/cli/azure/group#az_group_create) I det här exemplet ger vi resursgruppen namnet _MyResourceGroup och_ använder platsen _USA, östra:_

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus
```

Skapa nu en DDoS-skyddsplan med _namnet MyDdosProtectionPlan:_

```azurecli-interactive
az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

## <a name="enable-ddos-protection-for-a-virtual-network"></a>Aktivera DDoS-skydd för ett virtuellt nätverk

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>Aktivera DDoS-skydd för ett nytt virtuellt nätverk

Du kan aktivera DDoS-skydd när du skapar ett virtuellt nätverk. I det här exemplet ger vi det virtuella nätverket namnet _MyVnet_: 

```azurecli-interactive
az network vnet create \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --location eastus \
    --ddos-protection true
    --ddos-protection-plan MyDdosProtectionPlan
```

Du kan inte flytta ett virtuellt nätverk till en annan resursgrupp eller prenumeration när DDoS Standard är aktiverat för det virtuella nätverket. Om du behöver flytta ett virtuellt nätverk med DDoS Standard aktiverat inaktiverar du DDoS Standard först, flyttar det virtuella nätverket och aktiverar sedan DDoS-standarden. Efter flytten återställs de automatiskt finjusterade principtrösklarna för alla skyddade offentliga IP-adresser i det virtuella nätverket.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>Aktivera DDoS-skydd för ett befintligt virtuellt nätverk

När [du skapar en DDoS-skyddsplan](#create-a-ddos-protection-plan)kan du associera ett eller flera virtuella nätverk med planen. Om du vill lägga till fler än ett virtuellt nätverk listar du bara namnen eller DED:erna, avgränsade med blanksteg. I det här exemplet lägger vi till _MyVnet_:

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
    --vnets MyVnet
```

Du kan också aktivera DDoS-skydd för ett visst virtuellt nätverk:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --ddos-protection true
    --ddos-protection-plan MyDdosProtectionPlan
```

## <a name="validate-and-test"></a>Validera och testa

Kontrollera först informationen om din DDoS-skyddsplan:

```azurecli-interactive
az network ddos-protection show \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

Kontrollera att kommandot returnerar rätt information om din DDoS-skyddsplan.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan behålla dina resurser för nästa självstudie. Ta bort resursgruppen _MyResourceGroup om den inte längre_ behövs. När du tar bort resursgruppen tar du även bort DDoS-skyddsplanen och alla dess relaterade resurser. 

Om du vill ta bort resursgruppen använder [du az group delete](/cli/azure/group#az_group_delete):

```azurecli-interactive
az group delete \
--name MyResourceGroup 
```

Uppdatera ett visst virtuellt nätverk för att inaktivera DDoS-skydd:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --ddos-protection false
    --ddos-protection-plan ""
```

Om du vill ta bort en DDoS-skyddsplan måste du först ta bort alla virtuella nätverk från den. 

## <a name="next-steps"></a>Nästa steg

Fortsätt till självstudierna om du vill lära dig hur du visar och konfigurerar telemetri för din DDoS-skyddsplan.

> [!div class="nextstepaction"]
> [Visa och konfigurera telemetri för DDoS-skydd](telemetry.md)
