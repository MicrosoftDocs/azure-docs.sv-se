---
title: Köra init-behållare
description: Kör init-behållare i Azure Container Instances om du vill utföra installations uppgifter i en behållar grupp innan program behållarna körs.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 5a729263ee632eb9227694ec8684eb6889c6324b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85954289"
---
# <a name="run-an-init-container-for-setup-tasks-in-a-container-group"></a>Kör en init-behållare för installations uppgifter i en behållar grupp

Azure Container Instances stöder *init-behållare* i en behållar grupp. Init-behållare körs för att slutföras innan program behållaren eller behållare startar. Liknar [Kubernetes init containers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/), Använd en eller flera init-behållare för att utföra initierings logik för dina program behållare, till exempel att ställa in konton, köra installations skript eller konfigurera databaser.

Den här artikeln visar hur du använder en Azure Resource Manager-mall för att konfigurera en behållar grupp med en init-behållare.

## <a name="things-to-know"></a>Saker att känna till

* **API-version** – du behöver minst Azure Container instances API version 2019-12-01 för att distribuera init-behållare. Distribuera med en `initContainers` egenskap i en [yaml-fil](container-instances-multi-container-yaml.md) eller en [Resource Manager-mall](container-instances-multi-container-group.md).
* **Ordningen för körnings** -init-behållare utförs i den ordning som anges i mallen och före andra behållare. Som standard kan du ange högst 59 init-behållare per behållar grupp. Minst en icke-init-behållare måste finnas i gruppen.
* **Värd miljö** – init-behållare körs på samma maskin vara som resten av behållarna i gruppen.
* **Resurser** – du har inte angett resurser för init-behållare. De beviljas totala resurser, till exempel processorer och minne som är tillgängliga för behållar gruppen. Inga andra behållare körs i gruppen medan en init-behållare körs.
* **Egenskaper som stöds** – init-behållare kan använda grupp egenskaper som volymer, hemligheter och hanterade identiteter. De kan dock inte använda portar eller en IP-adress om de har kon figurer ATS för behållar gruppen. 
* **Starta om princip** – varje init-behållare måste avslutas innan nästa behållare i gruppen startar. Om en init-behållare inte avslutas utan problem är dess omstart beroende av den [princip för omstart](container-instances-restart-policy.md) som har kon figurer ATS för gruppen:

    |Princip i grupp  |Princip i init  |
    |---------|---------|
    |Always     |OnFailure         |
    |OnFailure     |OnFailure         |
    |Aldrig     |Aldrig         |
* **Avgifter** – container gruppen debiteras för den första distributionen av en init-behållare.

## <a name="resource-manager-template-example"></a>Exempel på en Resource Manager-mall

Börja med att kopiera följande JSON till en ny fil med namnet `azuredeploy.json` . Mallen konfigurerar en behållar grupp med en init-behållare och två program behållare:

* Behållaren *init1* kör den [upptaget](https://hub.docker.com/_/busybox) avbildningen från Docker Hub. Den försätts i vilo läge i 60 sekunder och skriver sedan en kommando rads sträng till en fil på en [emptyDir volym](container-instances-volume-emptydir.md).
* Båda program behållarna kör Microsoft- `aci-wordcount` behållar avbildningen:
    * *Hamlet* -behållaren kör WORDCOUNT-appen i standard konfigurationen och räknar ord frekvenser i Shakespeare uppspelnings- *Hamlet*.
    * *Juliet* app-behållaren läser kommando rads strängen från emptDir-volymen för att köra WORDCOUNT-appen i stället för Shakespeares *Romeo och Juliet*.

Mer information och exempel som använder `aci-wordcount` avbildningen finns i [Ange miljövariabler i container instances](container-instances-environment-variables.md).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Standard",
                "initContainers": [
                {
                    "name": "init1",
                    "properties": {
                        "image": "busybox",
                        "environmentVariables": [],
                        "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                         "command": [
                            "/bin/sh",
                            "-c",
                            "sleep 60; echo python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html > /mnt/emptydir/command_line.txt"
                        ]
                    }
                }
            ], 
                "containers": [
                    {
                        "name": "hamlet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    },
                    {
                        "name": "juliet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                            ],
                            "command": [
                                "/bin/sh",
                                "-c",
                                "$(cat /mnt/emptydir/command_line.txt)"
                            ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "OnFailure",
                "osType": "Linux",
                "volumes": [
                    {
                        "name": "emptydir1",
                        "emptyDir": {}
                    }
                ]           
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-the-template"></a>Distribuera mallen

Skapa en resursgrupp med kommandot [az group create][az-group-create].

```azurecli
az group create --name myResourceGroup --location eastus
```

Distribuera mallen med kommandot [AZ Deployment Group Create][az-deployment-group-create] .

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

I en grupp med en init-behållare ökar distributions tiden på grund av den tid det tar för initierings containern eller behållare att slutföras.


## <a name="view-container-logs"></a>Visa containerloggar

För att kontrol lera att initierings containern har körts kan du Visa loggens utdata från appens behållare med hjälp av kommandot [AZ container logs][az-container-logs] . `--container-name`Argumentet anger den behållare från vilken du vill hämta loggar. I det här exemplet ska du hämta loggarna för *Hamlet* -och *Juliet* -behållarna, som visar olika kommandoutdata:

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name hamlet
```

Utdata:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name juliet
```

Utdata:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Nästa steg

Med init-behållare kan du utföra installations-och initierings uppgifter för dina program behållare. Mer information om att köra uppgiftsbaserade behållare finns i [köra behållare aktiviteter med principer för omstart](container-instances-restart-policy.md).

Azure Container Instances innehåller andra alternativ för att ändra beteendet för program behållare. Exempel:

* [Ange miljövariabler i behållar instanser](container-instances-environment-variables.md)
* [Ange kommando raden i en behållar instans för att åsidosätta standard kommando rads åtgärden](container-instances-start-command.md)


[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-container-logs]: /cli/azure/container#az-container-logs
