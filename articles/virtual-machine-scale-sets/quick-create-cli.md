---
title: Snabb start – skapa en skalnings uppsättning för virtuella datorer med Azure CLI
description: Kom igång med dina distributioner genom att lära dig hur du snabbt skapar en skalnings uppsättning för virtuella datorer med Azure CLI.
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: d65df03bc23704fb4d4c037655cc4b84ccc4e576
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934656"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-with-the-azure-cli"></a>Snabbstart: Skapa en VM-skalningsuppsättning med Azure CLI
Med en skalnings uppsättning för virtuella datorer kan du distribuera och hantera en uppsättning virtuella datorer som skalas automatiskt. Du kan skala antalet virtuella datorer i skalningsuppsättningen manuellt eller definiera regler för automatisk skalning baserat på resursanvändning, till exempel CPU, minneskrav eller nätverkstrafik. En Azure-lastbalanserare distribuerar sedan trafiken till de virtuella datorinstanserna i skalningsuppsättningen. I den här snabbstarten skapar du en VM-skalningsuppsättning och distribuerar ett exempelprogram med Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.29 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad. 


## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning
Innan du kan skapa en skalningsuppsättning skapar du en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Skapa nu en skalningsuppsättning för en virtuell dator med [az vmss create](/cli/azure/vmss). I följande exempel skapas en skalningsuppsättning med namnet *myScaleSet* som är inställd på att uppdateras automatiskt när ändringar tillämpas och genererar SSH-nycklar om de inte finns i *~/.ssh/id_rsa*. Dessa SSH-nycklar används om du behöver logga in på VM-instanser. Om du vill använda en befintlig uppsättning SSH-nycklar, använd istället `--ssh-key-value`-parametern och ange platsen för dina nycklar.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorer.


## <a name="deploy-sample-application"></a>Distribuera exempelprogram
Testa din skalningsuppsättning genom att installera ett grundläggande webbprogram. Tillägg för anpassat skript i Azure används för att hämta och köra ett skript som installerar ett program på VM-instanser. Det här tillägget är användbart för konfiguration efter distribution, programvaruinstallation eller andra konfigurerings-/hanteringsuppgifter. Mer information finns i [översikten över tillägget för anpassat skript](../virtual-machines/extensions/custom-script-linux.md).

Använd tillägget för anpassat skript för att installera en grundläggande NGINX-webbserver. Använd tillägget för anpassat skript som installerar NGINX med [az vmss-tilläggsuppsättningen](/cli/azure/vmss/extension) på följande sätt:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'
```


## <a name="allow-traffic-to-application"></a>Tillåta trafik till program
En Azure-lastbalanserare distribuerades automatiskt när skalningsuppsättning skapades. Lastbalanserare distribuerar trafiken till VM-instanserna i skalningsuppsättningen. Använd [az network lb rule create](/cli/azure/network/lb/rule) för att skapa en regel för lastbalanseraren för att tillåta att trafik når exempelwebbprogrammet. I följande exempel skapas en regel med namnet *myLoadBalancerRuleWeb*:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```


## <a name="test-your-scale-set"></a>Testa din skalningsuppsättning
Om du vill se din skalningsuppsättning i praktiken så gå till exempelwebbprogrammet i en webbläsare. Hämta den offentliga IP-adressen för lastbalanseraren med [az network public-ip show](/cli/azure/network/public-ip). I följande exempel hämtas IP-adressen för *myScaleSetLBPublicIP* som skapas som en del av skalningsuppsättningen:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query '[ipAddress]' \
  --output tsv
```

Skriv den offentliga IP-adressen för lastbalanseraren i en webbläsare. Lastbalanseraren distribuerar trafik till en av dina VM-instanser enligt följande exempel:

![Standardwebbsida i NGINX](media/virtual-machine-scale-sets-create-cli/running-nginx-site.png)


## <a name="clean-up-resources"></a>Rensa resurser
När de inte längre behövs kan du använda [AZ Group Delete](/cli/azure/group) för att ta bort resurs gruppen, skalnings uppsättningen och alla relaterade resurser på följande sätt. Parametern `--no-wait` återför kontrollen till kommandotolken utan att vänta på att uppgiften slutförs. Parametern `--yes` bekräftar att du vill ta bort resurserna utan att tillfrågas ytterligare en gång.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Nästa steg
I den här snabbstarten skapade du en grundläggande skalningsuppsättning och använde tillägget för anpassat skript för att installera en grundläggande NGINX-webbserver på VM-instanserna. Om du vill veta mer kan du gå till självstudiekursen om hur man skapar och hanterar VM-skalningsuppsättningar för Azure.

> [!div class="nextstepaction"]
> [Skapa och hantera VM-skalningsuppsättningar för Azure](tutorial-create-and-manage-cli.md)
