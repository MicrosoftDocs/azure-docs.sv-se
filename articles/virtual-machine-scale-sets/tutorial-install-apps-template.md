---
title: Självstudie – installera appar i en skalnings uppsättning med Azure-mallar
description: Läs hur du använder Azure Resource Manager-mallar för att installera program på VM-skalningsuppsättningar med det anpassade skripttillägget
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: template
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: d5eba5486e7d26e62379e0112cd4b95322e6dae1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97705242"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-an-azure-template"></a>Självstudie: Installera program i VM-skalningsuppsättningar med en Azure-mall
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

Om du vill se hur det anpassade skripttillägget, skapar du en skalningsuppsättning som installerar NGINX-webbservern och matar ut värddatornamnet för skalningsuppsättningens virtuella datorinstans. Följande anpassade skripttilläggsdefinition laddar ner ett exempelskript från GitHub, installerar nödvändiga paket och skriver sedan den virtuella datorinstansens värddatornamn till en grundläggande HTML-sida.


## <a name="create-custom-script-extension-definition"></a>Skapa en anpassad skripttilläggsdefinition
När du definierar en VM-skalningsuppsättning med en Azure-mall, kan resursprovidern *Microsoft.Compute/virtualMachineScaleSets* inkludera ett avsnitt om tillägg. *extensionsProfile* går igenom vad som tillämpas till de virtuella datorinstanserna i en skalningsuppsättning. Om du vill använda det anpassade skripttillägget så anger du en utgivare för *Microsoft.Azure.Extensions* och en typ av *CustomScript*.

Egenskapen *fileUris* används för att definiera källan för installationsskripten eller paketen. Om du vill starta installationsprocessen, definieras de nödvändiga skripten i *commandToExecute*. Följande exempel definierar ett exempelskript från GitHub som installerar och konfigurerar NGINX-webbservern:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"
          ],
          "commandToExecute": "bash automate_nginx.sh"
        }
      }
    }
  ]
}
```

Ett fullständigt exempel på en Azure-mall som distribuerar en skalnings uppsättning och det anpassade skript tillägget finns i [https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json) . Den här exempelmallen används i nästa avsnitt.


## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning
Vi använder exempelmallen för att skapa en skalningsuppsättning och tillämpa det anpassade skripttillägget. Skapa först en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Skapa nu en skalnings uppsättning för virtuella datorer med [AZ distributions grupp skapa](/cli/azure/deployment/group). När du uppmanas, anger du ditt eget användarnamn och lösenord som används som autentiseringsuppgifter för varje virtuell datorinstans:

```azurecli-interactive
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy.json
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorer.

Varje virtuell datorinstans i skalningsuppsättningen laddar ned och kör skriptet från GitHub. I ett mer avancerat exempel, kan flera programkomponenter och filer installeras. Om skalningsuppsättningen skalas upp, använder de nya virtuella datorinstanserna automatiskt samma definition för anpassade skripttillägg och installerar det program som krävs.


## <a name="test-your-scale-set"></a>Testa din skalningsuppsättning
Om du vill testa webbservern hämtar du den offentliga IP-adressen för lastbalanseraren med [az network public-ip show](/cli/azure/network/public-ip). I följande exempel hämtas IP-adressen för *myScaleSetLBPublicIP* som skapas som en del av skalningsuppsättningen:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetPublicIP \
  --query [ipAddress] \
  --output tsv
```

Skriv den offentliga IP-adressen för lastbalanseraren i en webbläsare. Lastbalanseraren distribuerar trafik till en av dina VM-instanser enligt följande exempel:

![Standardwebbsida i NGINX](media/tutorial-install-apps-template/running-nginx.png)

Lämna webbläsaren öppen så att du kan se en uppdaterad version i nästa steg.


## <a name="update-app-deployment"></a>Uppdatera appdistributionen
Under livscykeln för en skalningsuppsättning, kan du behöva distribuera en uppdaterad version av ditt program. Med det anpassade skripttillägget, kan du referera till ett uppdaterat distributionsskript och sedan tillämpa tillägget till din skalningsuppsättning igen. När skalnings uppsättningen skapades i ett föregående steg var *upgradePolicy* inställd på *Automatisk*. Den här inställningen låter virtuella datorinstanser i skalningsuppsättningen att automatiskt uppdatera och tillämpa den senaste versionen av ditt program.

Om du vill uppdatera definitionen för ditt anpassade skripttillägg, redigerar du din mall för att referera till ett nytt installationsskript. Det nya filnamnet måste användas för att det anpassade skripttillägget ska identifiera ändringen. Det anpassade skripttillägget utvärderar inte innehållet i skriptet för att avgöra om ändringar gjorts. Följande definition använder ett uppdaterat installationsskript med *_v2* i slutet av namnet:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"
          ],
          "commandToExecute": "bash automate_nginx_v2.sh"
        }
      }
    }
  ]
}
```

Tillämpa konfigurationen för det anpassade skript tillägget på de virtuella dator instanserna i din skalnings uppsättning igen med [AZ distributions grupp skapa](/cli/azure/deployment/group). Den här *azuredeployv2.json*-mallen används för att tillämpa den uppdaterade versionen av programmet. I praktiken, redigerar du den befintliga *azuredeploy.json*-mallen så den refererar till det uppdaterade installationsskriptet, som det visas i föregående avsnitt. När du uppmanas, anger du samma användarnamn och lösenord som används när du först skapade skalningsuppsättningen:

```azurecli-interactive
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy_v2.json
```

Alla virtuella datorinstanser i skalningsuppsättningen uppdateras automatiskt med den senaste versionen av exempelwebbsidan. Uppdatera webbplatsen i webbläsaren om du vill se den uppdaterade versionen:

![Uppdaterad webbsida i NGINX](media/tutorial-install-apps-template/running-nginx-updated.png)


## <a name="clean-up-resources"></a>Rensa resurser
Ta bort din skalnings uppsättning och ytterligare resurser genom att ta bort resurs gruppen och alla dess resurser med [AZ Group Delete](/cli/azure/group). Parametern `--no-wait` återför kontrollen till kommandotolken utan att vänta på att uppgiften slutförs. Parametern `--yes` bekräftar att du vill ta bort resurserna utan att tillfrågas ytterligare en gång.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Nästa steg
I den här självstudien, läste du hur du automatiskt kan installera och uppdatera program på din skalningsuppsättning med Azure-mallar:

> [!div class="checklist"]
> * Installerar automatiskt program till din skalningsuppsättning
> * Använd det anpassade Azure-skripttillägget
> * Uppdatera ett program som körs på en skalningsuppsättning

Gå vidare till nästa självstudie för att lära dig hur du automatiskt skalar din skalningsuppsättning.

> [!div class="nextstepaction"]
> [Skala dina skalningsuppsättningar automatiskt](tutorial-autoscale-template.md)
