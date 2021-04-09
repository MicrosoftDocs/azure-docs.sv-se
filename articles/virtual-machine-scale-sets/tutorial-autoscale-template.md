---
title: Självstudie – Autoskala en skalnings uppsättning med Azure-mallar
description: Läs hur du använder Azure Resource Manager-mallar för att automatiskt skala en VM-skalningsuppsättning allteftersom CPU-kraven varierar
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 03/27/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 88cec878ca5d3ccab3a232888ff3a3c0b0faa1db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705259"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-an-azure-template"></a>Självstudie: Skala en VM-skalningsuppsättning automatiskt med en Azure-mall
När du skapar en skalningsuppsättning, definierar du antalet virtuella datorinstanser som du vill köra. När ditt program behöver ändras, kan du automatiskt öka eller minska antalet virtuella datorinstanser. Möjligheten att skala automatiskt låter dig hålla dig uppdaterad med kundernas behov eller svara på ändringar i programprestandan under hela livscykeln för din app. I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Använd automatisk skalning med en skalningsuppsättning
> * Skapa och använd regler för automatisk skalning
> * Belastningstesta virtuella datorinstanser och utlös regler för automatisk skalning
> * Skala tillbaka automatiskt när efterfrågan minskar

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.29 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad. 


## <a name="define-an-autoscale-profile"></a>Definiera en autoskalningsprofil
Du definierar en profil för automatisk skalning i en Azure-mall med resursprovidern *Microsoft.insights/autoscalesettings*. En *profil* ger information om skalningsuppsättningens kapacitet och alla associerade regler. Följande exempel definierar en profil med namnet *Automatisk skalning efter procent baserat på CPU-användning* och ställer in standard och minimum kapacitet för *2* VM-instanser och högst *10*:

```json
{
"type": "Microsoft.insights/autoscalesettings",
"name": "Autoscale",
"apiVersion": "2015-04-01",
"location": "[variables('location')]",
"scale": null,
"properties": {
  "profiles": [
    {
      "name": "Autoscale by percentage based on CPU usage",
      "capacity": {
        "minimum": "2",
        "maximum": "10",
        "default": "2"
      }
    }
  ]
}
```


## <a name="define-a-rule-to-autoscale-out"></a>Definiera en regel för att automatiskt skala ut
Om dina programkrav ökar, ökar även belastningen på de virtuella datorinstanserna i din skalningsuppsättning. Om den här ökade belastningen är konsekvent istället för bara en kortsiktig efterfrågan, kan du konfigurera regler för automatisk skalning för att öka antalet virtuella datorinstanser i skalningsuppsättningen. När dessa virtuella datorinstanser skapas och dina program distribueras, börjar skalningsuppsättningen att distribuera trafik till dem via lastbalanseraren. Du kan styra vilka mått som ska övervakas, som CPU eller disk, hur länge programbelastningen måste uppfylla ett visst tröskelvärde och hur många virtuella datorinstanser som ska läggas till skalningsuppsättningen.

I följande exempel, definieras en regel som ökar antalet virtuella datorinstanser i en skalningsuppsättning när den genomsnittliga CPU-belastningen är större än 70% över en 5 minutersperiod. När regeln utlöses, ökar antalet virtuella datorinstanser med tre.

Följande parametrar används för den här regeln:

| Parameter         | Förklaring                                                                                                         | Värde           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | Prestandamått för att övervaka och tillämpa åtgärder för skalningsuppsättningar på.                                                   | Processorprocentandel  |
| *timeGrain*       | Hur ofta måtten samlas in för analys.                                                                   | 1 minut        |
| *timeAggregation* | Definierar hur de insamlade mätvärdena ska aggregeras för analys.                                                | Genomsnitt         |
| *timeWindow*      | Tidsperioden som övervakas innan värdena för måttet och tröskelvärdet jämförs.                                   | 5 minuter       |
| *Operator*        | Operator som används för att jämföra måttinformationen mot tröskelvärdet.                                                     | Större än    |
| *fastställd*       | Det värde som får regeln för automatisk skalning att utlösa en åtgärd.                                                      | 70 %             |
| *position*       | Anger om skalningsuppsättningen ska skala in eller ut när regeln gäller.                                              | Öka        |
| *bastyp*            | Anger att antalet virtuella datorinstanser ska ändras med ett specifikt värde.                                    | Ändra antal    |
| *värde*           | Hur många virtuella datorinstanser ska skalas in eller ut när regeln gäller.                                             | 3               |
| *cooldown*        | Hur lång tid ska gå innan regeln tillämpas igen så att de automatiska skalningsåtgärderna har tid att börja gälla. | 5 minuter       |

Följande regel skulle läggas till i profilavsnittet för resursprovidern *Microsoft.insights/autoscalesettings* från föregående avsnitt:

```json
"rules": [
  {
    "metricTrigger": {
      "metricName": "Percentage CPU",
      "metricNamespace": "",
      "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', variables('vmssName'))]",
      "metricResourceLocation": "[variables('location')]",
      "timeGrain": "PT1M",
      "statistic": "Average",
      "timeWindow": "PT5M",
      "timeAggregation": "Average",
      "operator": "GreaterThan",
      "threshold": 70
    },
    "scaleAction": {
      "direction": "Increase",
      "type": "ChangeCount",
      "value": "3",
      "cooldown": "PT5M"
    }
  }
]
```


## <a name="define-a-rule-to-autoscale-in"></a>Definiera en regel för att automatiskt skala in
På kvällar eller helger, kan efterfrågan på ditt program minska. Om den här minskade belastningen är konsekvent över en tidsperiod, kan du konfigurera regler för automatisk skalning för att minska antalet virtuella datorinstanser i skalningsuppsättningen. Den här åtgärden för skala in minskar kostnaden för att köra din skalningsuppsättningen eftersom du bara köra de antal instanser som krävs för att uppfylla den aktuella efterfrågan.

Följande exempel definierar en regel som skalar in antalet virtuella datorinstanser med en när den genomsnittliga CPU-belastningen är faller under 30 % över en 5 minutersperiod. Den här regeln skulle läggas till profilen för automatisk skalning efter den föregående regeln för att skala ut:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', variables('vmssName'))]",
    "metricResourceLocation": "[variables('location')]",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT5M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "ChangeCount",
    "value": "1",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-an-autoscaling-scale-set"></a>Skapa en skalningsuppsättning som skalar automatiskt
Vi använder en exempelmall för att skapa en skalningsuppsättning och tillämpa regler för automatisk skalning. Du kan [granska den fullständiga mallen](https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/autoscale.json), eller [se resourceprovider-avsnittet *Microsoft.insights/autoscalesettings*](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/autoscale.json#L220) för mallen.

Skapa först en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Skapa nu en skalnings uppsättning för virtuella datorer med [AZ distributions grupp skapa](/cli/azure/deployment/group). När du uppmanas så anger du ditt eget användarnamn som *azureuser* och det lösenord som används som autentiseringsuppgift för varje virtuell datorinstans:

```azurecli-interactive
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/autoscale.json
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorer.


## <a name="generate-cpu-load-on-scale-set"></a>Generera CPU-belastning på skalningsuppsättningen
Om du vill testa reglerna för automatisk skalning kan du generera lite CPU-belastning på de virtuella datorinstanserna i skalningsuppsättningen. Den här simulerade CPU-belastningen gör att reglerna för automatisk skalning skalar ut och ökar antalet virtuella datorinstanser. När den simulerade CPU-belastningen sedan minskar så skalar reglerna för automatisk skalning in och minskar antalet virtuella datorinstanser.

Först, listar du adressen och portarna för att ansluta till virtuella datorinstanser i en skalningsuppsättning med [az vmss list-instance-connection-info](/cli/azure/vmss):

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

Följande exempelutdata visar instansnamnet, den offentliga IP-adressen för lastbalanseraren och portnummer som NAT (Network Address Translation)-regler vidarebefordrar trafik till:

```json
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

SSH till din första virtuella datorinstans. Ange din egen offentliga IP-adress och portnummer med parametern `-p`, som det visas i föregående kommando:

```console
ssh azureuser@13.92.224.66 -p 50001
```

När du är inloggad, installerar du **stress**-verktyget. Starta *10* **stress**-arbetare som genererar CPU-belastning. De här arbetarna kör i *420* sekunder, vilket räcker för att få reglerna för automatisk skalning att implementera den önskade åtgärden.

```console
sudo apt-get update
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

När **stress** visar utdata som liknar *stress: info: [2688] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, trycker du på *Retur* för att återgå till prompten.

Kontrollera att **stress** genererar CPU-belastning genom att granska den aktiva systembelastningen med **top**-verktyget:

```console
top
```

Avsluta **top**, stäng sedan anslutningen till den virtuella datorinstansen. **stress** fortsätter att köras på den virtuella datorinstansen.

```console
Ctrl-c
exit
```

Anslut till en andra virtuell datorinstans med det portnummer som listas från den föregående [az vmss list-instance-connection-info](/cli/azure/vmss):

```console
ssh azureuser@13.92.224.66 -p 50003
```

Installera och kör **stress** och starta sedan tio arbetare på den här andra virtuella datorinstansen.

```console
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

När **stress** återigen visar utdata som liknar *stress: info: [2713] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, trycker du på *Retur* för att återgå till prompten.

Stäng din anslutning till den andra virtuella datorinstansen. **stress** fortsätter att köras på den virtuella datorinstansen.

```console
exit
```

## <a name="monitor-the-active-autoscale-rules"></a>Övervaka de aktiva reglerna för automatisk skalning
Du övervakar antalet virtuella datorinstanser i din skalningsuppsättning med **watch**. Det tar 5 minuter för reglerna för automatisk skalning att börja utskalningsprocessen till svar på den CPU-belastning som skapas av **stress** på var och en av de virtuella datorinstanserna:

```azurecli-interactive
watch az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

När CPU-tröskelvärdet har uppnåtts, ökar reglerna för automatisk skalning antalet virtuella datorinstanser i skalningsuppsättningen. Följande utdata visar tre virtuella datorer som skapats när skalningsuppsättningen skalar ut automatiskt:

```output
Every 2.0s: az vmss list-instances --resource-group myResourceGroup --name myScaleSet --output table

  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            MYRESOURCEGROUP  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             MYRESOURCEGROUP  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             MYRESOURCEGROUP  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

När **stress** stoppar på den första virtuella datorn, återgår den genomsnittliga CPU-belastningen till normal. Efter 5 minuter till, skalar reglerna för automatisk skalning in antalet virtuella datorinstanser. Skalan in-åtgärder tar först bort de virtuella datorinstanserna med de högsta ID. När en skalningsuppsättning använder tillgänglighetsuppsättningar eller tillgänglighetszoner fördelas skala in-åtgärder jämnt över dessa VM-instanser. Följande exempelutdata visar en VM-instans som tas bort eftersom skalningsuppsättningen skalar in automatiskt:

```output
           6  True                  eastus      myScaleSet_6  Deleting             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Avsluta *watch* med `Ctrl-c`. Skalningsuppsättningen fortsätter att skala in var 5:e minut och tar bort en VM-instans tills att det lägsta instansantalet på två har uppnåtts.


## <a name="clean-up-resources"></a>Rensa resurser
Om du vill ta bort din skalningsuppsättning och ytterligare resurser så tar du bort resursgruppen och alla dess resurser med [az group delete](/cli/azure/group):

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Nästa steg
I den här självstudien läste du om hur du automatiskt kan skala in eller ut en skalningsuppsättning med Azure CLI:

> [!div class="checklist"]
> * Använd automatisk skalning med en skalningsuppsättning
> * Skapa och använd regler för automatisk skalning
> * Belastningstesta virtuella datorinstanser och utlös regler för automatisk skalning
> * Skala tillbaka automatiskt när efterfrågan minskar
