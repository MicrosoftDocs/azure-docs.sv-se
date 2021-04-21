---
title: Ange miljövariabler i containerinstansen
description: Lär dig hur du anger miljövariabler i de containrar som du kör i Azure Container Instances
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: 9d95ee3d64460aa5e11f450c9e582cdc0de4f0ae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790805"
---
# <a name="set-environment-variables-in-container-instances"></a>Ange miljövariabler i containerinstanser

Genom att ställa in miljövariabler i dina containerinstanser kan du skapa en dynamisk konfiguration av programmet eller skriptet som körs av containern. Detta liknar `--env` kommandoradsargumentet för `docker run` . 

Om du vill ange miljövariabler i en container anger du dem när du skapar en containerinstans. Den här artikeln visar exempel på hur du anger miljövariabler när du startar en container med [Azure CLI,](#azure-cli-example) [Azure PowerShell](#azure-powershell-example)och [Azure Portal](#azure-portal-example). 

Om du till exempel kör containeravbildningen Microsoft [aci-wordcount][aci-wordcount] kan du ändra dess beteende genom att ange följande miljövariabler:

*NumWords:* Det antal ord som skickas till STDOUT.

*MinLength:* Det minsta antalet tecken i ett ord som det ska räknas. Ett högre tal ignorerar vanliga ord som "av" och "the".

Om du behöver skicka hemligheter som miljövariabler stöder Azure Container Instances [säkra värden för](#secure-values) både Windows- och Linux-containrar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Azure CLI-exempel

Om du vill se standardutdata för [containern aci-wordcount][aci-wordcount] kör du den först med kommandot [az container create][az-container-create] (inga miljövariabler har angetts):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Om du vill ändra utdata startar du en andra container med argumentet tillagt och anger värden för `--environment-variables` *variablerna NumWords* *och MinLength.* (Det här exemplet förutsätter att du kör CLI i ett Bash-gränssnitt eller Azure Cloud Shell. Om du använder Kommandotolken i Windows anger du variablerna med dubbla citattecken, till exempel `--environment-variables "NumWords"="5" "MinLength"="8"` .)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

När båda containrarnas tillstånd visas som *Avslutad* (använd [az container show][az-container-show] för att kontrollera tillståndet) visar du loggarna med az container logs [för][az-container-logs] att se utdata.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

Utdata från containrarna visar hur du har ändrat den andra containerns skriptbeteende genom att ange miljövariabler.

**mycontainer1**
```output
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

**mycontainer2**
```output
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Azure PowerShell exempel

Att ställa in miljövariabler i PowerShell liknar CLI, men använder `-EnvironmentVariable` kommandoradsargumentet.

Starta först [containern aci-wordcount][aci-wordcount] i standardkonfigurationen med det här [kommandot New-AzContainerGroup:][new-Azcontainergroup]

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Kör nu följande [new-AzContainerGroup-kommando.][new-Azcontainergroup] Den här anger *miljövariablerna NumWords* *och MinLength* när du har fyllt i en matrisvariabel, `envVars` :

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

När båda containrarnas tillstånd har avslutats *(använd* [Get-AzContainerInstanceLog][azure-instance-log] för att kontrollera tillståndet) hämtar du deras loggar med kommandot [Get-AzContainerInstanceLog.][azure-instance-log]

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

Utdata för varje container visar hur du har ändrat skriptet som körs av containern genom att ange miljövariabler.

```console
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

Azure:\
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Azure Portal exempel

Om du vill ange miljövariabler när du startar en  container i Azure Portal anger du dem på sidan Avancerat när du skapar containern.

1. På sidan **Avancerat** ställer du in **omstartsprincipen på** *På fel*
2. Under **Miljövariabler** anger du med värdet för för den första variabeln och `NumWords` anger med värdet för för den andra `5` `MinLength` `8` variabeln. 
1. Välj **Granska + skapa för** att verifiera och distribuera sedan containern.

![Portalsida som visar miljövariabeln Aktivera knapp och textrutor][portal-env-vars-01]

Om du vill visa containerns loggar går du till **Inställningar och** **väljer Containrar** och sedan **Loggar**. Precis som i föregående CLI- och PowerShell-avsnitt kan du se hur skriptets beteende har ändrats av miljövariablerna. Endast fem ord visas, var och en med en minsta längd på åtta tecken.

![Portal som visar utdata från containerloggen][portal-env-vars-02]

## <a name="secure-values"></a>Säkra värden

Objekt med säkra värden är avsedda att lagra känslig information som lösenord eller nycklar för ditt program. Att använda säkra värden för miljövariabler är både säkrare och mer flexibelt än att inkludera det i containerns avbildning. Ett annat alternativ är att använda hemliga volymer som beskrivs i [Montera en hemlig volym i Azure Container Instances](container-instances-volume-secret.md).

Miljövariabler med säkra värden visas inte i containerns egenskaper – deras värden kan bara nås inifrån containern. Containeregenskaper som visas i Azure Portal Azure CLI visar till exempel bara namnet på en säker variabel, inte dess värde.

Ange en säker miljövariabel genom att `secureValue` ange egenskapen i stället för den vanliga för `value` variabelns typ. De två variablerna som definieras i följande YAML visar de två variabeltyperna.

### <a name="yaml-deployment"></a>YAML-distribution

Skapa en `secure-env.yaml` fil med följande kodfragment.

```yaml
apiVersion: 2019-12-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Kör följande kommando för att distribuera containergruppen med YAML (justera resursgruppens namn efter behov):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Verifiera miljövariabler

Kör kommandot [az container show][az-container-show] för att köra frågor mot containerns miljövariabler:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

JSON-svaret visar både nyckeln och värdet för den osäkra miljövariabeln, men bara namnet på den säkra miljövariabeln:

```json
[
  [
    {
      "name": "NOTSECRET",
      "secureValue": null,
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET",
      "secureValue": null,
      "value": null
    }
  ]
]
```

Med kommandot [az container exec,][az-container-exec] som gör det möjligt att köra ett kommando i en container som körs, kan du kontrollera att variabeln säker miljö har angetts. Kör följande kommando för att starta en interaktiv bash-session i containern:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

När du har öppnat ett interaktivt gränssnitt i containern kan du komma `SECRET` åt variabelns värde:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Nästa steg

Uppgiftsbaserade scenarier, till exempel batchbearbetning av en stor datamängd med flera containrar, kan dra nytta av anpassade miljövariabler vid körning. Mer information om hur du kör uppgiftsbaserade containrar finns i [Köra containerbaserade uppgifter med omstartsprinciper.](container-instances-restart-policy.md)

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-exec]: /cli/azure/container#az_container_exec
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
