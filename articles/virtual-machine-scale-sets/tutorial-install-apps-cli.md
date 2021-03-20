---
title: Självstudie – installera program i en skalnings uppsättning med Azure CLI
description: Läs hur du använder Azure CLI för att installera program på VM-skalningsuppsättningar med det anpassade skripttillägget
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: cli
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: d9969cf0fa453f857de421dd10934f63f5773f6c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94516755"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-the-azure-cli"></a>Självstudie: Installera program i VM-skalningsuppsättningar med Azure CLI
Om du vill köra program på virtuella datorinstanser i en skalningsuppsättning, måste du först installera programkomponenter och nödvändiga filer. I en föregående självstudie fick du lära dig hur du skapar och använder en anpassad virtuell datoravbildning för att distribuera dina virtuella datorinstanser. Den här anpassade avbildningen inkluderade programinstallationer och konfigurationer. Du kan också automatisera installationen av programmen till en skalningsuppsättning efter att varje virtuell datorinstans distribueras, eller uppdatera ett program som redan körs på en skalningsuppsättning. I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Installerar automatiskt program till din skalningsuppsättning
> * Använd det anpassade Azure-skripttillägget
> * Uppdatera ett program som körs på en skalningsuppsättning

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.29 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad. 


## <a name="what-is-the-azure-custom-script-extension"></a>Vad är det anpassade Azure-skripttillägget?
Det anpassade skripttillägget laddar ner och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för konfiguration efter distribution, programvaruinstallation eller andra konfigurerings-/hanteringsuppgifter. Skript kan laddas ned från Azure Storage eller GitHub, eller tillhandahållas via Azure Portal vid tilläggskörning.

Det anpassade skripttillägget integreras med Azure Resource Manager-mallar och kan också användas med Azure CLI, Azure PowerShell, Azure-portalen eller REST API:et. Mer information finns i [översikten över tillägget för anpassat skript](../virtual-machines/extensions/custom-script-linux.md).

För att använda det anpassade skripttillägget med Azure CLI, skapar du en JSON-fil som definierar vilka filer som ska hämtas och ger den kommandot att köras. Dessa JSON-definitioner kan återanvändas i distributioner av skalningsuppsättningar för att tillämpa konsekventa programinstallationer.


## <a name="create-custom-script-extension-definition"></a>Skapa en anpassad skripttilläggsdefinition
Om du vill se hur det anpassade skripttillägget funkar, skapar du en skalningsuppsättning som installerar NGINX-webbservern och matar ut värddatornamnet för skalningsuppsättningens virtuella datorinstans. Följande anpassade skripttilläggsdefinition laddar ner ett exempelskript från GitHub, installerar nödvändiga paket och skriver sedan den virtuella datorinstansens värddatornamn till en grundläggande HTML-sida.

I ditt nuvarande gränssnitt, skapar du en fil med namnet *customConfig.json* och klistrar in följande konfiguration. Skapa till exempel inte filen i Cloud Shell på din lokala dator. Du kan använda vilket redigeringsprogram som helst. Ange `sensible-editor customConfig.json` i Cloud Shell för att skapa filen och visa en lista över tillgängliga redigeringsprogram.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

> [!CAUTION]
> Du kan behöva Invertera användningen av enkla (') och dubbla citat tecken (") i JSON-blocket om du bestämmer dig för att referera till JSON direkt (i stället för att referera till *customConfig.jspå* filen) i parametern *--Settings* nedan. 


## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning
Skapa en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Skapa nu en skalningsuppsättning för en virtuell dator med [az vmss create](/cli/azure/vmss). I följande exempel skapas en skalningsuppsättning med namnet *myScaleSet* som genererar SSH-nycklar om det inte redan finns:

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


## <a name="apply-the-custom-script-extension"></a>Tillämpa det anpassade skripttillägget
Tillämpa konfigurationen för det anpassade skripttillägget till de virtuella datorinstanserna i din skalningsuppsättning med [az vmss extension set](/cli/azure/vmss/extension). Följande exempel tillämpar konfigurationen *customConfig.json* till de virtuella datorinstanserna *myScaleSet* i resursgruppen med namnet *myResourceGroup*:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings @customConfig.json
```

Varje virtuell datorinstans i skalningsuppsättningen laddar ned och kör skriptet från GitHub. I ett mer avancerat exempel, kan flera programkomponenter och filer installeras. Om skalningsuppsättningen skalas upp, använder de nya virtuella datorinstanserna automatiskt samma definition för anpassade skripttillägg och installerar det program som krävs.


## <a name="test-your-scale-set"></a>Testa din skalningsuppsättning
Skapa en regel för lastbalanserare med [az network lb rule create](/cli/azure/network/lb/rule) för att tillåta trafik till webbservern. I följande exempel skapas en regel med namnet *myLoadBalancerRuleWeb*:

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

Om du vill testa webbservern hämtar du den offentliga IP-adressen för lastbalanseraren med [az network public-ip show](/cli/azure/network/public-ip). I följande exempel hämtas IP-adressen för *myScaleSetLBPublicIP* som skapas som en del av skalningsuppsättningen:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Skriv den offentliga IP-adressen för lastbalanseraren i en webbläsare. Lastbalanseraren distribuerar trafik till en av dina VM-instanser enligt följande exempel:

![Standardwebbsida i NGINX](media/tutorial-install-apps-cli/running-nginx.png)

Lämna webbläsaren öppen så att du kan se en uppdaterad version i nästa steg.


## <a name="update-app-deployment"></a>Uppdatera appdistributionen
Under livscykeln för en skalningsuppsättning, kan du behöva distribuera en uppdaterad version av ditt program. Med det anpassade skripttillägget, kan du referera till ett uppdaterat distributionsskript och sedan tillämpa tillägget till din skalningsuppsättning igen. När skalnings uppsättningen skapades i ett föregående steg `--upgrade-policy-mode` angavs värdet *automatiskt*. Den här inställningen låter virtuella datorinstanser i skalningsuppsättningen att automatiskt uppdatera och tillämpa den senaste versionen av ditt program.

I ditt nuvarande gränssnitt, skapar du en fil med namnet *cusomConfigv2.json* och klistrar in följande konfiguration. Den här definitionen kör en uppdaterad *v2*-version av installationsskriptet för programmet:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"],
  "commandToExecute": "./automate_nginx_v2.sh"
}
```

Tillämpa konfigurationen för det anpassade skripttillägget till de virtuella datorinstanserna i din skalningsuppsättning med [az vmss extension set](/cli/azure/vmss/extension). *customConfigv2.json* används för att tillämpa den uppdaterade versionen av programmet:

```azurecli-interactive
az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroup \
    --vmss-name myScaleSet \
    --settings @customConfigv2.json
```

Alla virtuella datorinstanser i skalningsuppsättningen uppdateras automatiskt med den senaste versionen av exempelwebbsidan. Uppdatera webbplatsen i webbläsaren om du vill se den uppdaterade versionen:

![Uppdaterad webbsida i NGINX](media/tutorial-install-apps-cli/running-nginx-updated.png)


## <a name="clean-up-resources"></a>Rensa resurser
Ta bort din skalnings uppsättning och ytterligare resurser genom att ta bort resurs gruppen och alla dess resurser med [AZ Group Delete](/cli/azure/group). Parametern `--no-wait` återför kontrollen till kommandotolken utan att vänta på att uppgiften slutförs. Parametern `--yes` bekräftar att du vill ta bort resurserna utan att tillfrågas ytterligare en gång.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Nästa steg
I den här självstudien fick du lära dig hur du automatiskt installerar och uppdaterar program på din skalningsuppsättning med Azure CLI:

> [!div class="checklist"]
> * Installerar automatiskt program till din skalningsuppsättning
> * Använd det anpassade Azure-skripttillägget
> * Uppdatera ett program som körs på en skalningsuppsättning

Gå vidare till nästa självstudie för att lära dig hur du automatiskt skalar din skalningsuppsättning.

> [!div class="nextstepaction"]
> [Skala dina skalningsuppsättningar automatiskt](tutorial-autoscale-cli.md)
