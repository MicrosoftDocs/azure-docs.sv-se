---
title: Montera hemlig volym till behållar grupp
description: Lär dig hur du monterar en hemlig volym för att lagra känslig information för åtkomst av dina behållar instanser
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: ea82ba5994feaf102d4622eada284df431e004d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86169569"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Montera en hemlig volym i Azure Container Instances

Använd en *hemlig* volym för att tillhandahålla känslig information till behållarna i en behållar grupp. Den *hemliga* volymen lagrar hemligheterna i filer på volymen, som är tillgängliga för behållarna i behållar gruppen. Genom att lagra hemligheter i en *hemlig* volym kan du undvika att lägga till känsliga data som SSH-nycklar eller autentiseringsuppgifter till din program kod.

* När de har distribuerats med hemligheter i en behållar grupp, är en hemlig volym *skrivskyddad*.
* Alla hemliga volymer backas upp av [tmpfs][tmpfs], ett RAM-baserat fil system. deras innehåll skrivs aldrig till icke-flyktig lagring.

> [!NOTE]
> *Hemliga* volymer är för närvarande begränsade till Linux-behållare. Lär dig hur du skickar säkra miljövariabler för både Windows-och Linux-behållare i [Ange miljövariabler](container-instances-environment-variables.md). Medan vi arbetar för att hämta alla funktioner till Windows-behållare kan du hitta aktuella plattforms skillnader i [översikten](container-instances-overview.md#linux-and-windows-containers).

## <a name="mount-secret-volume---azure-cli"></a>Montera hemlig volym – Azure CLI

Om du vill distribuera en behållare med en eller flera hemligheter med hjälp av Azure CLI inkluderar `--secrets` du `--secrets-mount-path` parametrarna och i kommandot [AZ container Create][az-container-create] . I det här exemplet monteras en *hemlig* volym som består av två filer som innehåller hemligheter, "mysecret1" och "mysecret2", på `/mnt/secrets` :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

Följande [AZ container exec][az-container-exec] -utdata visar hur du öppnar ett gränssnitt i behållaren som körs, listar filerna i den hemliga volymen och visar deras innehåll:

```azurecli
az container exec \
  --resource-group myResourceGroup \
  --name secret-volume-demo --exec-command "/bin/sh"
```

```output
/usr/src/app # ls /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>Montera hemlig volym – YAML

Du kan också distribuera behållar grupper med Azure CLI och en [yaml-mall](container-instances-multi-container-yaml.md). Att distribuera med YAML-mall är den bästa metoden när du distribuerar behållar grupper som består av flera behållare.

När du distribuerar med en YAML-mall måste de hemliga värdena vara **base64-kodade** i mallen. De hemliga värdena visas dock i klartext i filerna i behållaren.

Följande YAML-mall definierar en behållar grupp med en behållare som monterar en *hemlig* volym på `/mnt/secrets` . Den hemliga volymen har två filer som innehåller hemligheter, "mysecret1" och "mysecret2".

```yaml
apiVersion: '2019-12-01'
location: eastus
name: secret-volume-demo
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /mnt/secrets
        name: secretvolume1
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: secretvolume1
    secret:
      mysecret1: TXkgZmlyc3Qgc2VjcmV0IEZPTwo=
      mysecret2: TXkgc2Vjb25kIHNlY3JldCBCQVIK
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Om du vill distribuera med YAML-mallen sparar du föregående YAML till en fil med namnet `deploy-aci.yaml` och kör sedan kommandot [AZ container Create][az-container-create] med `--file` parametern:

```azurecli-interactive
# Deploy with YAML template
az container create \
  --resource-group myResourceGroup \
  --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Montera hemlig volym – Resource Manager

Förutom CLI-och YAML-distribution kan du distribuera en behållar grupp med hjälp av en Azure [Resource Manager-mall](/azure/templates/microsoft.containerinstance/containergroups).

Fyll först i `volumes` matrisen i avsnittet behållar grupp i `properties` mallen. När du distribuerar med en Resource Manager-mall måste de hemliga värdena vara **base64-kodade** i mallen. De hemliga värdena visas dock i klartext i filerna i behållaren.

Sedan, för varje behållare i den behållar grupp där du vill montera den *hemliga* volymen, fyller du `volumeMounts` i matrisen i `properties` avsnittet i behållar definitionen.

Följande Resource Manager-mall definierar en behållar grupp med en behållare som monterar en *hemlig* volym på `/mnt/secrets` . Den hemliga volymen har två hemligheter, "mysecret1" och "mysecret2".

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Om du vill distribuera med Resource Manager-mallen sparar du föregående JSON till en fil med namnet `deploy-aci.json` och kör sedan kommandot [AZ Deployment Group Create][az-deployment-group-create] med `--template-file` parametern:

```azurecli-interactive
# Deploy with Resource Manager template
az deployment group create \
  --resource-group myResourceGroup \
  --template-file deploy-aci.json
```

## <a name="next-steps"></a>Nästa steg

### <a name="volumes"></a>Volymer

Lär dig hur du monterar andra volym typer i Azure Container Instances:

* [Montera en Azure-filresurs i Azure Container Instances](container-instances-volume-azure-files.md)
* [Montera en emptyDir volym i Azure Container Instances](container-instances-volume-emptydir.md)
* [Montera en gitRepo volym i Azure Container Instances](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Säkra miljövariabler

En annan metod för att tillhandahålla känslig information till behållare (inklusive Windows-behållare) är att använda [säkra miljövariabler](container-instances-environment-variables.md#secure-values).

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
