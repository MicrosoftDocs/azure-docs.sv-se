---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en Azure-containerinstansresurs från Azure CLI.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 32b00e031e3cf865093c267084117a8704b6e272
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800007"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Skapa en Azure Container Instance-resurs från Azure CLI

YAML nedan definierar Azure Container Instance-resursen. Kopiera och klistra in innehållet i en ny fil med namnet `my-aci.yaml` och ersätt de kommenterade värdena med dina egna. Se [mallformatet för][template-format] giltig YAML. Se [containerdatabaserna och avbildningarna för tillgängliga][repositories-and-images] avbildningsnamn och deras motsvarande lagringsplats. Mer information om YAML-referensen för containerinstanser finns i [YAML-referens: Azure Container Instances][aci-yaml-ref].

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
properties:
  imageRegistryCredentials: # This is only required if you are pulling a non-public image that requires authentication to access. For example Text Analytics for health.
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  volumes: # This node, is only required for container instances that pull their model in at runtime, such as LUIS.
  - name: aci-file-share
    azureFile:
      shareName: # < File share name >
      storageAccountName: # < Storage account name>
      storageAccountKey: # < Storage account key >
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> Alla platser har inte samma processor- och minnestillgänglighet. I tabellen plats [och resurser finns][location-to-resource] en lista över tillgängliga resurser för containrar per plats och operativsystem.

Vi förlitar oss på YAML-filen som vi skapade för [`az container create`][azure-container-create] kommandot . Från Azure CLI kör du kommandot `az container create` som ersätter `<resource-group>` med ditt eget. För att skydda värden i en YAML-distribution refererar du dessutom till [säkra värden][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

Kommandots utdata är om de är giltiga. Efter en stund ändras utdata till en `Running...` JSON-sträng som representerar den nyligen skapade ACI-resursen. Containeravbildningen är förmodligen inte tillgänglig på ett tag, men resursen har nu distribuerats.

> [!TIP]
> Var uppmärksam på platserna för den offentliga förhandsversionen av Azure Cognitive Service eftersom YAML måste justeras för att matcha platsen.

[azure-container-create]: /cli/azure/container#az_container_create
[template-format]: /azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../container-image-tags.md
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
