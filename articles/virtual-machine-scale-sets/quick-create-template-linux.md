---
title: Snabb start – skapa en skalnings uppsättning för virtuella Linux-datorer med en Azure Resource Manager-mall
description: Lär dig hur du snabbt skapar en skalningsuppsättning för virtuella Linux-datorer med en Azure Resource Manager-mall som distribuerar en exempelapp och konfigurerar regler för automatisk skalning
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.subservice: linux
ms.date: 03/27/2020
ms.reviewer: mimckitt
ms.custom: mimckitt, subject-armqs, devx-track-azurecli
ms.openlocfilehash: 52e0e50d3c0c68b57181645c3eb695308fdac65a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97703831"
---
# <a name="quickstart-create-a-linux-virtual-machine-scale-set-with-an-arm-template"></a>Snabb start: skapa en skalnings uppsättning för virtuella Linux-datorer med en ARM-mall

Med en skalnings uppsättning för virtuella datorer kan du distribuera och hantera en uppsättning virtuella datorer som skalas automatiskt. Du kan skala antalet virtuella datorer i skalningsuppsättningen manuellt eller definiera regler för automatisk skalning baserat på resursanvändning, till exempel CPU, minneskrav eller nätverkstrafik. En Azure-lastbalanserare distribuerar sedan trafiken till de virtuella datorinstanserna i skalningsuppsättningen. I den här snabb starten skapar du en skalnings uppsättning för virtuella datorer och distribuerar ett exempel program med en Azure Resource Manager-mall (ARM-mall).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Med ARM-mallar kan du distribuera grupper av relaterade resurser. Med en enda mall kan du skapa VM-skalningsuppsättningen, installera program och ange regler för automatisk skalning. Du kan återanvända mallen och använda variabler och parametrar till att uppdatera befintliga, eller skapa ytterligare, skalningsuppsättningar. Du kan distribuera mallar via Azure-portalen, Azure CLI eller Azure PowerShell eller från pipelines för kontinuerlig integrering/kontinuerlig leverans (CI/CD).

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-bottle-autoscale%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/201-vmss-bottle-autoscale/).

:::code language="json" source="~/quickstart-templates/201-vmss-bottle-autoscale/azuredeploy.json":::

Dessa resurser definieras i mallen:

- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/belastningsutjämnare**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Compute/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets)
- [**Microsoft. Insights/autoscaleSettings**](/azure/templates/microsoft.insights/autoscalesettings)

### <a name="define-a-scale-set"></a>Definiera en skalningsuppsättning

När du skapar en skalningsuppsättning med en mall definierar du lämpliga resurser. Huvuddelarna i resurstypen för VM-skalningsuppsättning:

| Egenskap                     | Egenskapsbeskrivning                                  | Exempelmallvärde                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| typ                         | Azure-resurstypen som ska skapas                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | Namnet på skalningsuppsättningen                                       | myScaleSet                                |
| location                     | Platsen där skalningsuppsättningen skapas                     | East US                                   |
| sku.name                     | VM-storleken för varje skalningsuppsättningsinstans                  | Standard_A1                               |
| sku.capacity                 | Antal VM-instanser som skapas inledningsvis           | 2                                         |
| upgradePolicy.mode           | Uppgraderingsläge för VM-instanser när ändringar sker              | Automatiskt                                 |
| imageReference               | Plattform eller anpassad avbildning som ska användas för VM-instanserna | Canonical Ubuntu Server 16.04-LTS         |
| osProfile.computerNamePrefix | Namnprefix för varje VM-instans                     | myvmss                                    |
| osProfile.adminUsername      | Användarnamn för varje VM-instans                        | azureuser                                 |
| osProfile.adminPassword      | Lösenord för varje VM-instans                        | P@ssw0rd!                                 |

Om du vill anpassa en mall för skalnings uppsättningar kan du ändra storleken på den virtuella datorn eller den ursprungliga kapaciteten. Ett annat alternativ är att använda en annan plattform eller en anpassad avbildning.

### <a name="add-a-sample-application"></a>Lägg till ett exempelprogram

Testa din skalningsuppsättning genom att installera ett grundläggande webbprogram. När du distribuerar en skalningsuppsättning kan du använda VM-tillägg för att utföra konfigurations- och automatiseringsåtgärder efter distributionen, till exempel installera en app. Skript kan laddas ned från Azure Storage eller GitHub, eller tillhandahållas via Azure Portal vid tilläggskörning. Om du vill använda ett tillägg för skalningsuppsättningen lägger du till avsnittet *extensionProfile* i föregående resursexempel. Tilläggsprofilen definierar vanligtvis följande egenskaper:

- Tilläggstyp
- Tilläggsutgivare
- Tilläggsversion
- Plats för konfiguration eller installation av skript
- Kommandon som körs för VM-instanser

Mallen använder tillägget för anpassat skript för att installera [Flask](https://bottlepy.org/docs/dev/), ett python-webbramverk och en enkel http-server.

Två skript definieras i **fileUris**  -  -*installserver.sh* och *workserver.py*. Filerna laddas ned från GitHub och sedan  körs commandToExecute `bash installserver.sh` för att installera och konfigurera appen.

## <a name="deploy-the-template"></a>Distribuera mallen

Du kan distribuera mallen genom att välja följande **distribuera till Azure** -knapp. Knappen öppnar Azure Portal, läser in hela mallen och frågar efter ett par parametrar, till exempel namn på skalningsuppsättningen, antal instanser och administratörsautentiseringsuppgifter.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-bottle-autoscale%2Fazuredeploy.json)

Du kan också distribuera en Resource Manager-mall med hjälp av Azure CLI:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az deployment group create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

Svara på frågorna och ange namn på skalningsuppsättningen, antal instanser och administratörsautentiseringsuppgifter för VM-instanserna. Det tar några minuter att skapa skalningsuppsättningen och de resurser som behövs.

## <a name="validate-the-deployment"></a>Verifiera distributionen

Om du vill se din skalningsuppsättning i praktiken så gå till exempelwebbprogrammet i en webbläsare. Hämta den offentliga IP-adressen för lastbalanseraren med [az network public-ip list](/cli/azure/network/public-ip) på följande sätt:

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

Ange den offentliga IP-adressen för belastningsutjämnaren i en webbläsare i formatet *http: \/ /publicIpAddress: 9000/do_work*. Lastbalanseraren distribuerar trafik till en av dina VM-instanser enligt följande exempel:

![Standardwebbsida i NGINX](media/virtual-machine-scale-sets-create-template/running-python-app.png)

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda [AZ Group Delete](/cli/azure/group) för att ta bort resurs gruppen, skalnings uppsättningen och alla relaterade resurser på följande sätt. Parametern `--no-wait` återför kontrollen till kommandotolken utan att vänta på att uppgiften slutförs. Parametern `--yes` bekräftar att du vill ta bort resurserna utan att tillfrågas ytterligare en gång.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Linux-skalnings uppsättning med en ARM-mall och använde tillägget för anpassat skript för att installera en grundläggande python-webbserver på VM-instanserna. Om du vill veta mer kan du gå till självstudiekursen om hur man skapar och hanterar VM-skalningsuppsättningar för Azure.

> [!div class="nextstepaction"]
> [Skapa och hantera VM-skalningsuppsättningar för Azure](tutorial-create-and-manage-cli.md)
