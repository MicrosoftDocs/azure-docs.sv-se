---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en Azure Container instance-resurs från Azure CLI.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 87007d3df3fe44ab04a330b09b8e495ec4b47e54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "97866092"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Skapa en Azure Container instance-resurs från Azure CLI

I YAML nedan definieras Azure Container instance-resursen. Kopiera och klistra in innehållet i en ny fil med namnet `my-aci.yaml` och ersätt de kommenterade värdena med dina egna. Se [mallformat][template-format] för giltiga yaml. Referera till [behållar databaserna och avbildningarna][repositories-and-images] för de tillgängliga avbildnings namnen och deras motsvarande lagrings plats. Mer information om YAML-referensen för container instances finns i [referens för yaml: Azure Container instances][aci-yaml-ref].

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
> Alla platser har inte samma processor-och minnes tillgänglighet. Se tabellen [plats och resurser][location-to-resource] för att visa en lista över tillgängliga resurser för behållare per plats och operativ system.

Vi förlitar dig på den YAML-fil som vi har skapat för [`az container create`][azure-container-create] kommandot. Kör kommandot från Azure CLI och `az container create` Ersätt `<resource-group>` med ditt eget. För att skydda värden inom en YAML-distribution finns dessutom [säkra värden][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

Kommandots utdata är `Running...` om det är giltigt, efter att utdata har ändrats till en JSON-sträng som representerar den nyligen skapade ACI-resursen. Behållar avbildningen är mer än sannolikt inte tillgänglig för en stund, men resursen distribueras nu.

> [!TIP]
> Var uppmärksam på platserna för de allmänt tillgängliga Azure-tjänsterna för för hands versionen, eftersom YAML krävs för att de ska kunna matcha platsen.

[azure-container-create]: /cli/azure/container#az-container-create
[template-format]: /azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../container-image-tags.md
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
