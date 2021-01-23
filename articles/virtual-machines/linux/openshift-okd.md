---
title: Distribuera OKD i Azure
description: Distribuera OKD i Azure.
author: haroldwongms
manager: joraio
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/15/2019
ms.author: haroldw
ms.openlocfilehash: d0a10c6c700f11e540dc7edaa27baee35396d814
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732402"
---
# <a name="deploy-okd-in-azure"></a>Distribuera OKD i Azure

Du kan använda ett av två sätt för att distribuera OKD (tidigare OpenShift Origin) i Azure:

- Du kan distribuera alla nödvändiga komponenter för Azure-infrastrukturen manuellt och följa OKD- [dokumentationen](https://docs.okd.io).
- Du kan också använda en befintlig [Resource Manager-mall](https://github.com/Microsoft/openshift-origin) som fören klar distributionen av OKD-klustret.

## <a name="deploy-using-the-okd-template"></a>Distribuera med OKD-mallen

Om du vill distribuera med hjälp av Resource Manager-mallen använder du en parameter fil för att ange indataparametrarna. Om du vill anpassa distributionen ytterligare delar du GitHub-lagrings platsen och ändrar lämpliga objekt.

Några vanliga anpassnings alternativ är, men är inte begränsade till:

- Skydds VM-storlek (variabel i azuredeploy.jspå)
- Namngivnings konventioner (variabler i azuredeploy.jspå)
- OpenShift-kluster, information, modifierad via hosts-filen (deployOpenShift.sh)

[OKD-mallen](https://github.com/Microsoft/openshift-origin) har flera grenar som är tillgängliga för olika versioner av OKD.  Utifrån dina behov kan du distribuera direkt från lagrings platsen eller så kan du använda lagrings platsen och göra anpassade ändringar innan du distribuerar.

Använd `appId` värdet från tjänstens huvud namn som du skapade tidigare för `aadClientId` parametern.

Följande är ett exempel på en parameter fil med namnet azuredeploy.parameters.jspå med alla nödvändiga indata.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "storageKind": {
            "value": "managed"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

Ersätt parametrarna med din egen information.

Olika versioner kan ha olika parametrar, så kontrol lera de nödvändiga parametrarna för den gren som du använder.

### <a name="deploy-using-azure-cli"></a>Distribuera med Azure CLI


> [!NOTE] 
> Följande kommando kräver Azure CLI 2.0.8 eller senare. Du kan kontrol lera CLI-versionen med `az --version` kommandot. Information om hur du uppdaterar CLI-versionen finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

I följande exempel distribueras OKD-klustret och alla relaterade resurser till en resurs grupp med namnet openshiftrg, med ett distributions namn på myOpenShiftCluster. Mallen refereras direkt från GitHub-lagrings platsen när en lokal parameter fil med namnet azuredeploy.parameters.json används.

```azurecli 
az deployment group create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Distributionen tar minst 30 minuter att slutföra, baserat på det totala antalet noder som har distribuerats. URL: en för OpenShift-konsolen och DNS-namnet för OpenShift-bakgrunden skrivs till terminalen när distributionen är klar. Du kan också visa avsnittet utdata i distributionen från Azure Portal.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Om du inte vill koppla upp kommando raden som väntar på att distributionen ska slutföras, lägger du till `--no-wait` som ett alternativ för grupp distributionen. Utdata från distributionen kan hämtas från Azure Portal i avsnittet distribution för resurs gruppen.

## <a name="connect-to-the-okd-cluster"></a>Ansluta till OKD-klustret

När distributionen är klar ansluter du till OpenShift-konsolen med webbläsaren med hjälp av `OpenShift Console Url` . Du kan också SSH till OKD-huvudservern. Följande är ett exempel som använder utdata från distributionen:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Rensa resurser

Använd kommandot [AZ Group Delete](/cli/azure/group) för att ta bort resurs gruppen, OpenShift-klustret och alla relaterade resurser när de inte längre behövs.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Nästa steg

- [Uppgifter efter distribution](./openshift-container-platform-3x-post-deployment.md)
- [Felsöka OpenShift-distribution](./openshift-container-platform-3x-troubleshooting.md)
- [Komma igång med OKD](https://docs.okd.io)
