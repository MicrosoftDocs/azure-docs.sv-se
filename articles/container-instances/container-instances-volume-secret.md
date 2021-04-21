---
title: Montera hemlighetsvolym till containergrupp
description: Lär dig hur du monterar en hemlig volym för att lagra känslig information för åtkomst av dina containerinstanser
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: cd8bd4d59b5e53a0db2455bdfbaf56c05c93d65f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771007"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Montera en hemlig volym i Azure Container Instances

Använd en *hemlig* volym för att ange känslig information till containrarna i en containergrupp. Den *hemliga* volymen lagrar dina hemligheter i filer på volymen som är tillgängliga för containrarna i containergruppen. Genom att lagra hemligheter på en *hemlig volym* kan du undvika att lägga till känsliga data som SSH-nycklar eller databasautentiseringsuppgifter i programkoden.

* När den har distribuerats med hemligheter i en containergrupp är en hemlig volym *skrivskyddad*.
* Alla hemliga volymer backas upp av [tmpfs][tmpfs], ett RAM-säkerhetskopierat filsystem; deras innehåll skrivs aldrig till icke-beständig lagring.

> [!NOTE]
> *Hemliga* volymer är för närvarande begränsade till Linux-containrar. Lär dig hur du skickar säkra miljövariabler för både Windows- och Linux-containrar [i Ange miljövariabler](container-instances-environment-variables.md). Medan vi arbetar med att ta med alla funktioner i Windows-containrar kan du hitta aktuella plattformsskillnader i [översikten](container-instances-overview.md#linux-and-windows-containers).

## <a name="mount-secret-volume---azure-cli"></a>Montera hemlig volym – Azure CLI

Om du vill distribuera en container med en eller flera hemligheter med hjälp av Azure CLI inkluderar du `--secrets` `--secrets-mount-path` parametrarna och i [kommandot az container create.][az-container-create] Det här exemplet monterar *en hemlighetsvolym* som består av två filer som innehåller hemligheter, "mysecret1" och "mysecret2", på `/mnt/secrets` :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

Följande [az container exec-utdata][az-container-exec] visar hur du öppnar ett gränssnitt i containern som körs, listar filerna i den hemliga volymen och visar sedan innehållet:

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

Du kan också distribuera containergrupper med Azure CLI och en [YAML-mall.](container-instances-multi-container-yaml.md) Att distribuera med en YAML-mall är den bästa metoden när du distribuerar containergrupper som består av flera containrar.

När du distribuerar med en YAML-mall måste de hemliga värdena **vara Base64-kodade** i mallen. De hemliga värdena visas dock i klartext i filerna i containern.

Följande YAML-mall definierar en containergrupp med en container som monterar en *hemlig* volym på `/mnt/secrets` . Den hemliga volymen har två filer som innehåller hemligheter, "mysecret1" och "mysecret2".

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

Om du vill distribuera med YAML-mallen sparar du föregående YAML i en fil med namnet och kör `deploy-aci.yaml` sedan kommandot az container [create][az-container-create] med `--file` parametern :

```azurecli-interactive
# Deploy with YAML template
az container create \
  --resource-group myResourceGroup \
  --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Montera hemlig volym – Resource Manager

Förutom CLI- och YAML-distribution kan du distribuera en containergrupp med hjälp av en Azure [Resource Manager mall](/azure/templates/microsoft.containerinstance/containergroups).

Fyll först i `volumes` matrisen i avsnittet `properties` containergrupp i mallen. När du distribuerar Resource Manager en mall måste de hemliga värdena **vara Base64-kodade** i mallen. De hemliga värdena visas dock i klartext i filerna i containern.

För varje container i containergruppen där du vill montera  den hemliga volymen fyller du sedan i matrisen i avsnittet `volumeMounts` i `properties` containerdefinitionen.

Följande mall Resource Manager en containergrupp med en container som monterar en *hemlig* volym på `/mnt/secrets` . Den hemliga volymen har två hemligheter, "mysecret1" och "mysecret2".

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Om du vill Resource Manager mallen sparar du den föregående JSON-filen i en fil med namnet och kör sedan `deploy-aci.json` kommandot az deployment group [create][az-deployment-group-create] med `--template-file` parametern :

```azurecli-interactive
# Deploy with Resource Manager template
az deployment group create \
  --resource-group myResourceGroup \
  --template-file deploy-aci.json
```

## <a name="next-steps"></a>Nästa steg

### <a name="volumes"></a>Volymer

Lär dig hur du monterar andra volymtyper i Azure Container Instances:

* [Montera en Azure-filresurs i Azure Container Instances](container-instances-volume-azure-files.md)
* [Montera en emptyDir-volym i Azure Container Instances](container-instances-volume-emptydir.md)
* [Montera en gitRepo-volym i Azure Container Instances](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Säkra miljövariabler

En annan metod för att tillhandahålla känslig information till containrar (inklusive Windows-containrar) är att använda [säkra miljövariabler](container-instances-environment-variables.md#secure-values).

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-exec]: /cli/azure/container#az_container_exec
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
