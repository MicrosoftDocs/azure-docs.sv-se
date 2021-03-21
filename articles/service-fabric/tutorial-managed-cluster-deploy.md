---
title: Distribuera ett Service Fabric hanterat kluster (för hands version)
description: I den här självstudien ska du distribuera ett Service Fabric hanterat kluster för testning.
ms.topic: tutorial
ms.date: 08/27/2020
ms.custom: references_regions
ms.openlocfilehash: fd54e6f8c5272c3a8fe0e0162ca2e57968b24642
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791718"
---
# <a name="tutorial-deploy-a-service-fabric-managed-cluster-preview"></a>Självstudie: Distribuera ett Service Fabric hanterat kluster (för hands version)

I den här själv studie serien diskuterar vi:

> [!div class="checklist"]
> * Så här distribuerar du ett Service Fabric hanterat kluster 
> * [Så här skalar du ut ett Service Fabric hanterat kluster](tutorial-managed-cluster-scale.md)
> * [Lägga till och ta bort noder i ett Service Fabric hanterat kluster](tutorial-managed-cluster-add-remove-node-type.md)
> * [Så här distribuerar du ett program till ett Service Fabric hanterat kluster](tutorial-managed-cluster-deploy-app.md)

Den här delen av serien handlar om hur du:

> [!div class="checklist"]
> * Anslut till ditt Azure-konto
> * Skapa en ny resursgrupp
> * Distribuera ett Service Fabric hanterat kluster
> * Lägg till en typ av primär nod i klustret

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

* Skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) om du inte redan har en Azure-prenumeration

* Installera [modulen Service Fabric SDK och PowerShell](service-fabric-get-started.md).

* Installera [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) (eller senare).

## <a name="connect-to-your-azure-account"></a>Anslut till ditt Azure-konto

Ersätt `<your-subscription>` med prenumerations strängen för ditt Azure-konto och Anslut:

```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <your-subscription>

```

## <a name="create-a-new-resource-group"></a>Skapa en ny resursgrupp

Skapa sedan resurs gruppen för det hanterade Service Fabric klustret, och Ersätt `<your-rg>` och `<location>` med önskat grupp namn och plats.

> [!NOTE]
> Regioner som stöds för den offentliga för hands versionen är,,,, `centraluseuap` `eastus2euap` `eastasia` `northeurope` `westcentralus` och `eastus2` .

```powershell
$resourceGroup = "myResourceGroup"
$location = "EastUS2"

New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="deploy-a-service-fabric-managed-cluster"></a>Distribuera ett Service Fabric hanterat kluster

### <a name="create-a-service-fabric-managed-cluster"></a>Skapa ett Service Fabric-hanterat kluster

I det här steget ska du skapa ett Service Fabric hanterat kluster med hjälp av New-AzServiceFabricManagedCluster PowerShell-kommandot. I följande exempel skapas ett kluster med namnet IT-kluster i resurs gruppen med namnet myResourceGroup. Den här resurs gruppen skapades i föregående steg i regionen eastus2.

I det här steget anger du dina egna värden för följande parametrar:

* **Kluster namn**: Ange ett unikt namn för klustret, till exempel *mysfcluster*.
* **Administratörs lösen ord**: Ange ett lösen ord för den administratör som ska användas för RDP på de underliggande virtuella datorerna i klustret.
* **Tumavtryck för klient certifikat**: Ange tumavtrycket för det klient certifikat som du vill använda för att få åtkomst till klustret. Om du inte har ett certifikat, följer du [Ange och hämta ett certifikat](../key-vault/certificates/quick-create-portal.md) för att skapa ett självsignerat certifikat.
* **Kluster-SKU**: ange vilken [typ av Service Fabric hanterat kluster](overview-managed-cluster.md#service-fabric-managed-cluster-skus) som ska distribueras. *Basic* SKU-kluster är endast avsedda för test distributioner och tillåter inte tillägg eller borttagning av Node-typer.

```powershell
$clusterName = "<unique cluster name>"
$password = "Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$clientThumbprint = "<certificate thumbprint>"
$clusterSku = "Standard"

New-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroup -Location $location -ClusterName $clusterName -ClientCertThumbprint $clientThumbprint -ClientCertIsAdmin -AdminPassword $password -Sku $clusterSKU -Verbose
```

### <a name="add-a-primary-node-type-to-the-service-fabric-managed-cluster"></a>Lägg till en primär nodtyp i Service Fabric hanterat kluster

I det här steget ska du lägga till en primär nodtyp till klustret som du nyss skapade. Varje Service Fabric kluster måste ha minst en typ av primär nod.

I det här steget anger du dina egna värden för följande parametrar:

* **Namn på nodtyp**: Ange ett unikt namn för nodtypen som ska läggas till i klustret, till exempel "NT1".

> [!NOTE]
> Om nodtypen som läggs till är den första eller enda nodtypen i klustret, måste den primära egenskapen användas.

```powershell
$nodeType1Name = "NT1"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeType1Name -Primary -InstanceCount 5
```

Det kan ta några minuter att slutföra kommandot.

## <a name="validate-the-deployment"></a>Verifiera distributionen

### <a name="review-deployed-resources"></a>Granska distribuerade resurser

När distributionen är klar hittar du Service Fabric Explorer värdet Service Fabric på översikts sidan för hanterade kluster resurser i portalen. När du uppmanas att ange ett certifikat använder du det certifikat som klienten tumavtryck angavs i PowerShell-kommandot.

## <a name="next-steps"></a>Nästa steg

I det här steget har vi skapat och distribuerat vårt första Service Fabric hanterade klustret. Mer information om hur du skalar ett kluster finns i:

> [!div class="nextstepaction"]
> [Skala ut ett Service Fabric hanterat kluster](tutorial-managed-cluster-scale.md)
