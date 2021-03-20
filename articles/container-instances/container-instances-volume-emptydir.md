---
title: Montera emptyDir-volym till behållar grupp
description: Lär dig hur du monterar en emptyDir-volym för att dela data mellan behållarna i en behållar grupp i Azure Container Instances
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 64a3c83008f163167528a5e5987fe2316942d5bc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "77117750"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Montera en emptyDir volym i Azure Container Instances

Lär dig hur du monterar en *emptyDir* -volym för att dela data mellan behållarna i en behållar grupp i Azure Container instances. Använd *emptyDir* -volymer som tillfälliga cacheminnen för dina arbets belastningar i behållare.

> [!NOTE]
> Att montera en *emptyDir* volym är för närvarande begränsad till Linux-behållare. Medan vi arbetar för att hämta alla funktioner till Windows-behållare kan du hitta aktuella plattforms skillnader i [översikten](container-instances-overview.md#linux-and-windows-containers).

## <a name="emptydir-volume"></a>emptyDir volym

*EmptyDir* -volymen ger en skrivbar katalog som är tillgänglig för varje behållare i en behållar grupp. Behållare i gruppen kan läsa och skriva samma filer i volymen, och den kan monteras med samma eller olika sökvägar i varje behållare.

Några exempel använder för en *emptyDir* -volym:

* Scratch Space
* Kontroll punkter under långvariga aktiviteter
* Lagra data som hämtats av en sidvagn-behållare och hanteras av en program behållare

Data i en *emptyDir* -volym behålls genom behållar krascher. Behållare som startas om är dock inte garanterade att bevara data i en *emptyDir* volym. Om du stoppar en behållar grupp är *emptyDir* -volymen inte beständig.

Den maximala storleken för en Linux *emptyDir* -volym är 50 GB.

## <a name="mount-an-emptydir-volume"></a>Montera en emptyDir volym

Om du vill montera en emptyDir-volym i en behållar instans kan du distribuera med hjälp av en [Azure Resource Manager mall](/azure/templates/microsoft.containerinstance/containergroups), en [yaml-fil](container-instances-reference-yaml.md)eller andra programmerings metoder för att distribuera en behållar grupp.

Fyll först i `volumes` matrisen i avsnittet behållar grupp i `properties` filen. Sedan, för varje behållare i den behållar grupp där du vill montera *emptyDir* -volymen, fyller du `volumeMounts` i matrisen i `properties` avsnittet i behållar definitionen.

Följande Resource Manager-mall skapar till exempel en behållar grupp bestående av två behållare, som var och en monterar *emptyDir* -volymen:

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Exempel på distribution av container grupper finns i [distribuera en grupp med flera behållare med hjälp av en Resource Manager-mall](container-instances-multi-container-group.md) och [distribuera en grupp med flera behållare med hjälp av en yaml-fil](container-instances-multi-container-yaml.md).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du monterar andra volym typer i Azure Container Instances:

* [Montera en Azure-filresurs i Azure Container Instances](container-instances-volume-azure-files.md)
* [Montera en gitRepo volym i Azure Container Instances](container-instances-volume-gitrepo.md)
* [Montera en hemlig volym i Azure Container Instances](container-instances-volume-secret.md)