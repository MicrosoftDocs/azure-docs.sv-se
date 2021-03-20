---
title: Ange miljövariabler i behållar instansen
description: Lär dig hur du ställer in miljövariabler i de behållare som du kör i Azure Container Instances
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: 92ae59f69b7cb43fee1d3ce8190a85fc20a11f60
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86169773"
---
# <a name="set-environment-variables-in-container-instances"></a>Ange miljövariabler i behållar instanser

Genom att ställa in miljövariabler i dina containerinstanser kan du skapa en dynamisk konfiguration av programmet eller skriptet som körs av containern. Detta liknar `--env` kommando rads argumentet för `docker run` . 

Ange miljövariabler i en behållare genom att ange dem när du skapar en behållar instans. Den här artikeln innehåller exempel på hur du ställer in miljövariabler när du startar en behållare med [Azure CLI](#azure-cli-example), [Azure PowerShell](#azure-powershell-example)och [Azure Portal](#azure-portal-example). 

Om du till exempel kör behållar avbildningen Microsoft [ACI-WORDCOUNT][aci-wordcount] kan du ändra dess beteende genom att ange följande miljövariabler:

*NUMWORDS*: antalet ord som skickats till stdout.

*MinLength*: det minsta antalet tecken i ett ord som ska räknas. Ett högre tal ignorerar vanliga ord som "av" och "."

Om du behöver skicka hemligheter som miljövariabler stöder Azure Container Instances [säkra värden](#secure-values) för både Windows-och Linux-behållare.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Azure CLI-exempel

Om du vill se standardutdata för behållaren [ACI-WORDCOUNT][aci-wordcount] kör du det först med detta [AZ-behållar kommandot CREATE][az-container-create] (inga miljövariabler har angetts):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Om du vill ändra utdata startar du en andra behållare med det `--environment-variables` tillagda argumentet och anger värden för *NUMWORDS* -och *minLength* -variablerna. (Det här exemplet förutsätter att du kör CLI i ett bash-gränssnitt eller Azure Cloud Shell. Om du använder kommando tolken i Windows anger du variablerna med dubbla citat tecken, till exempel `--environment-variables "NumWords"="5" "MinLength"="8"` .)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

När båda behållar status visas som *avslutad* (Använd [AZ container Visa][az-container-show] för att kontrol lera tillstånd) visar deras loggar med [AZ container-loggar][az-container-logs] för att se utdata.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

Utdata från behållarna visar hur du har ändrat den andra behållarens skript beteende genom att ställa in miljövariabler.

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

Att ställa in miljövariabler i PowerShell liknar CLI, men använder `-EnvironmentVariable` kommando rads argumentet.

Börja med att starta behållaren [ACI-WORDCOUNT][aci-wordcount] i standard konfigurationen med detta [nya-AzContainerGroup-][new-Azcontainergroup] kommando:

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Kör nu följande [New-AzContainerGroup-][new-Azcontainergroup] kommando. Detta anger *NUMWORDS* -och *minLength* -miljövariablerna när du har fyllt i en mat ris variabel `envVars` :

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

När båda behållarens tillstånd har *avslut ATS* (Använd [Get-AzContainerInstanceLog][azure-instance-log] för att kontrol lera tillstånd) hämtar du loggarna med kommandot [Get-AzContainerInstanceLog][azure-instance-log] .

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

Utdata för varje behållare visar hur du har ändrat skriptet som körs av behållaren genom att ställa in miljövariabler.

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

Om du vill ställa in miljövariabler när du startar en behållare i Azure Portal anger du dem på sidan **Avancerat** när du skapar behållaren.

1. På sidan **Avancerat** ställer du in principen för att **starta om** *på att Miss lyckas*
2. Under **miljövariabler** anger `NumWords` du ett värde för `5` den första variabeln och anger `MinLength` värdet `8` för den andra variabeln. 
1. Välj **Granska + skapa** för att verifiera och distribuera behållaren.

![Portal sidan visar hur du aktiverar knappar och text rutor i miljö variabeln][portal-env-vars-01]

Om du vill visa behållarens loggar går du till **Inställningar** och väljer **behållare** och **loggar** sedan. På liknande sätt som visas i föregående CLI-och PowerShell-avsnitt kan du se hur skriptets beteende har ändrats av miljövariablerna. Endast fem ord visas, vart och ett får bestå av minst åtta tecken.

![Portal som visar behållar logg utdata][portal-env-vars-02]

## <a name="secure-values"></a>Säkra värden

Objekt med säkra värden är avsedda att innehålla känslig information som lösen ord eller nycklar för ditt program. Att använda säkra värden för miljövariabler är både säkrare och mer flexibelt än att inkludera det i behållarens avbildning. Ett annat alternativ är att använda hemliga volymer, som beskrivs i [montera en hemlig volym i Azure Container instances](container-instances-volume-secret.md).

Miljövariabler med säkra värden visas inte i behållarens egenskaper – deras värden kan bara nås från behållaren. Till exempel visar behållar egenskaper som visas i Azure Portal eller Azure CLI bara en säker variabels namn, inte dess värde.

Ange en säker miljö variabel genom att ange `secureValue` egenskapen i stället för vanlig `value` för variabelns typ. De två variablerna som definieras i följande YAML demonstrerar de två variabel typerna.

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

Kör följande kommando för att distribuera behållar gruppen med YAML (justera resurs gruppens namn efter behov):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Verifiera miljövariabler

Kör kommandot [AZ container show][az-container-show] för att fråga efter behållares miljövariabler:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

JSON-svaret visar både den oskyddade Miljövariabelns nyckel och värde, men endast namnet på den säkra miljövariabeln:

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

Med kommandot [AZ container exec][az-container-exec] , som aktiverar körning av ett kommando i en behållare som körs, kan du kontrol lera att variabeln säker miljö har angetts. Kör följande kommando för att starta en interaktiv bash-session i behållaren:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

När du har öppnat ett interaktivt gränssnitt i behållaren kan du komma åt `SECRET` variabelns värde:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Nästa steg

Uppgiftsbaserade scenarier, t. ex. batchbearbetning av en stor data uppsättning med flera behållare, kan dra nytta av anpassade miljövariabler vid körning. Mer information om att köra uppgiftsbaserade behållare finns i [köra behållare aktiviteter med principer för omstart](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
