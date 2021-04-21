---
title: Köra init-containrar
description: Kör init-containrar i Azure Container Instances för att utföra installationsaktiviteter i en containergrupp innan programcontainrarna körs.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 9ccaf1a67d6ca3bcff422acb591b528cc72a9608
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763945"
---
# <a name="run-an-init-container-for-setup-tasks-in-a-container-group"></a>Köra en init-container för installationsaktiviteter i en containergrupp

Azure Container Instances har stöd *för init-containrar* i en containergrupp. Init-containrar körs innan programcontainern eller containrarna startar. På samma [sätt som med Kubernetes init-containrar](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)använder du en eller flera init-containrar för att utföra initieringslogik för dina appcontainrar, till exempel ange konton, köra installationsskript eller konfigurera databaser.

Den här artikeln visar hur du använder en Azure Resource Manager för att konfigurera en containergrupp med en init-container.

## <a name="things-to-know"></a>Saker att känna till

* **API-version** – Du behöver minst Azure Container Instances API-version 2019-12-01 för att distribuera Init-containrar. Distribuera med hjälp `initContainers` av en egenskap i en [YAML-fil](container-instances-multi-container-yaml.md) eller [Resource Manager mall](container-instances-multi-container-group.md).
* **Körningsordning** – Init-containrar körs i den ordning som anges i mallen och före andra containrar. Som standard kan du ange högst 59 init-containrar per containergrupp. Minst en icke-init-container måste finnas i gruppen.
* **Värdmiljö** – Init-containrar körs på samma maskinvara som resten av containrarna i gruppen.
* **Resurser** – Du anger inte resurser för init-containrar. De beviljas totalt antal resurser, till exempel processorer och minne som är tillgängligt för containergruppen. När en init-container körs körs inga andra containrar i gruppen.
* **Egenskaper som** stöds – Init-containrar kan använda gruppegenskaper som volymer, hemligheter och hanterade identiteter. De kan dock inte använda portar eller en IP-adress om de har konfigurerats för containergruppen. 
* **Omstartsprincip** – Varje init-container måste avslutas innan nästa container i gruppen startar. Om en init-container inte avslutas korrekt beror omstartsåtgärden på den [omstartsprincip som](container-instances-restart-policy.md) konfigurerats för gruppen:

    |Princip i grupp  |Princip i init  |
    |---------|---------|
    |Always     |OnFailure         |
    |OnFailure     |OnFailure         |
    |Aldrig     |Aldrig         |
* **Avgifter** – containergruppen debiteras från den första distributionen av en init-container.

## <a name="resource-manager-template-example"></a>Resource Manager mallexempel

Börja med att kopiera följande JSON till en ny fil med namnet `azuredeploy.json` . Mallen uppsättningar en containergrupp med en init-container och två programcontainrar:

* *Init1-containern* kör [busybox-avbildningen](https://hub.docker.com/_/busybox) från Docker Hub. Den förfaller i 60 sekunder och skriver sedan en kommandoradssträng till en fil på en [emptyDir-volym.](container-instances-volume-emptydir.md)
* Båda programcontainrarna kör `aci-wordcount` Microsoft-containeravbildningen:
    * Containern *kör* ordräkningsappen i standardkonfigurationen och räknar ordfrekvenser i Görs *spelpjäsEnd .*
    * Juliet-appcontainern läser kommandoradssträngen från emptDir-volymen för att köra wordcount-appen i stället på Volume's *Volume och Juliet*. 

Mer information och exempel som använder `aci-wordcount` avbildningen finns i [Ange miljövariabler i containerinstanser](container-instances-environment-variables.md).

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

Distribuera mallen med kommandot [az deployment group][az-deployment-group-create] create.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

I en grupp med en init-container ökar distributionstiden på grund av den tid det tar för init-containern eller containrarna att slutföras.


## <a name="view-container-logs"></a>Visa containerloggar

Om du vill kontrollera att init-containern har körts korrekt kan du visa loggutdata för appcontainrarna med [kommandot az container logs.][az-container-logs] Argumentet `--container-name` anger den container som loggar ska hämtas från. I det här exemplet hämtar du loggarna för containrarna *för docka* och *juliet,* som visar olika kommandoutdata:

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

Init-containrar hjälper dig att utföra konfigurations- och initieringsuppgifter för dina programcontainrar. Mer information om hur du kör uppgiftsbaserade containrar finns i [Köra containerbaserade uppgifter med omstartsprinciper.](container-instances-restart-policy.md)

Azure Container Instances andra alternativ för att ändra beteendet för programcontainrar. Exempel:

* [Ange miljövariabler i containerinstanser](container-instances-environment-variables.md)
* [Ange kommandoraden i en containerinstans för att åsidosätta standardkommandoradsåtgärden](container-instances-start-command.md)


[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-container-logs]: /cli/azure/container#az_container_logs
