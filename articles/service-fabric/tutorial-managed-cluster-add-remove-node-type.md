---
title: Lägga till och ta bort nodtyper för ett Service Fabric hanterat kluster (för hands version)
description: I den här självstudien får du lära dig hur du lägger till och tar bort nodtyper för ett Service Fabric hanterat kluster.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: bb33512652677fc4e46d8ba3668dca985bbcfe01
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791247"
---
# <a name="tutorial-add-and-remove-node-types-from-a-service-fabric-managed-cluster-preview"></a>Självstudie: lägga till och ta bort nodtyper från ett Service Fabric hanterat kluster (för hands version)

I den här själv studie serien diskuterar vi:

> [!div class="checklist"]
> * [Så här distribuerar du ett Service Fabric hanterat kluster](tutorial-managed-cluster-deploy.md)
> * [Så här skalar du ut ett Service Fabric hanterat kluster](tutorial-managed-cluster-scale.md)
> * Lägga till och ta bort noder i ett Service Fabric hanterat kluster
> * [Så här distribuerar du ett program till ett Service Fabric hanterat kluster](tutorial-managed-cluster-deploy-app.md)

Den här delen av serien handlar om hur du:

> [!div class="checklist"]
> * Lägga till en nodtyp i ett Service Fabric hanterat kluster
> * Ta bort en nodtyp från ett Service Fabric hanterat kluster

## <a name="prerequisites"></a>Krav

* Ett Service Fabric hanterat kluster (se [*distribuera ett hanterat kluster*](tutorial-managed-cluster-deploy.md)).
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) eller senare (se [*Installera Azure PowerShell*](/powershell/azure/install-az-ps)).

## <a name="add-a-node-type-to-a-service-fabric-managed-cluster"></a>Lägga till en nodtyp i ett Service Fabric hanterat kluster

Du kan lägga till en nodtyp till ett Service Fabric hanterat kluster via en Azure Resource Manager mall, PowerShell eller CLI. I den här självstudien kommer vi att lägga till en nodtyp med hjälp av Azure PowerShell.

Om du vill skapa en ny nodtyp måste du definiera tre egenskaper:

* **Nodnamn**: namn som är unikt från alla befintliga nodtyper i klustret.
* **Antal instanser**: det första antalet noder av den nya nodtypen.
* **VM-storlek**: VM SKU för noderna. Om inget värde anges används standardvärdet *Standard_D2* .

> [!NOTE]
> Om nodtypen som läggs till är den första eller enda nodtypen i klustret, måste den primära egenskapen användas.

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "nt2"
$vmSize = "Standard_D2_v2"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeTypeName -InstanceCount 3 -vmSize $vmSize
```

## <a name="remove-a-node-type-from-a-service-fabric-managed-cluster"></a>Ta bort en nodtyp från ett Service Fabric hanterat kluster

Om du vill ta bort en nodtyp från ett Service Fabric hanterat kluster måste du använda PowerShell eller CLI. I den här självstudien tar vi bort en nodtyp med hjälp av Azure PowerShell.

> [!NOTE]
> Det går inte att ta bort en Primary Node-typ om det är den enda primära nodtypen i klustret.  

Så här tar du bort en nodtyp:

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "myCluster"
$nodeTypeName = "nt2"

Remove-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName  -Name $nodeTypeName
```

## <a name="next-steps"></a>Nästa steg

 I det här avsnittet har vi lagt till och tagit bort nodtyper. Information om hur du distribuerar ett program till ett Service Fabric hanterat kluster finns i:

> [!div class="nextstepaction"]
> [Distribuera en app till ett Service Fabric hanterat kluster](tutorial-managed-cluster-deploy-app.md)
