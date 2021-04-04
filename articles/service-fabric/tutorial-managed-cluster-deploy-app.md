---
title: Distribuera ett program till ett Service Fabric hanterat kluster via PowerShell (för hands version)
description: I den här självstudien ska du ansluta till ett Service Fabric hanterat kluster och distribuera ett program via PowerShell.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 36a91d2852bcda5f958441b48ef4721d6ccc83c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91410475"
---
# <a name="tutorial-deploy-an-app-to-a-service-fabric-managed-cluster-preview"></a>Självstudie: Distribuera en app till ett Service Fabric hanterat kluster (för hands version)

I den här själv studie serien diskuterar vi:

> [!div class="checklist"]
> * [Så här distribuerar du ett Service Fabric hanterat kluster](tutorial-managed-cluster-deploy.md)
> * [Så här skalar du ut ett Service Fabric hanterat kluster](tutorial-managed-cluster-scale.md)
> * [Lägga till och ta bort noder i ett Service Fabric hanterat kluster](tutorial-managed-cluster-add-remove-node-type.md)
> * Så här distribuerar du ett program till ett Service Fabric hanterat kluster

Den här delen av serien handlar om hur du:

> [!div class="checklist"]
> * Ansluta till ditt Service Fabric-hanterade kluster
> * Ladda upp ett program till ett kluster
> * Instansiera ett program i ett kluster
> * Ta bort ett program från ett kluster

## <a name="prerequisites"></a>Förutsättningar

* Ett Service Fabric hanterat kluster (se [*distribuera ett hanterat kluster*](tutorial-managed-cluster-deploy.md)).

## <a name="connect-to-your-cluster"></a>Anslut till klustret

För att ansluta till klustret behöver du tumavtryck för klustrets certifikat. Du hittar det här värdet i kluster egenskapernas utdata från resurs distributionen eller genom att fråga kluster egenskaperna på en befintlig resurs.

Följande kommando kan användas för att fråga kluster resursen efter tumavtrycket för kluster certifikatet.

```powershell
$serverThumbprint = (Get-AzResource -ResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ServiceFabric/managedclusters/mysfcluster).Properties.clusterCertificateThumbprint
```

Med kluster certifikatets tumavtryck är du redo att ansluta till klustret.

```powershell
$connectionEndpoint = "mysfcluster.eastus2.cloudapp.azure.com:19000"
Connect-ServiceFabricCluster -ConnectionEndpoint $connectionEndpoint -KeepAliveIntervalInSec 10 `
      -X509Credential `
      -ServerCertThumbprint $serverThumbprint  `
      -FindType FindByThumbprint `
      -FindValue $clientThumbprint `
      -StoreLocation CurrentUser `
      -StoreName My

```

### <a name="upload-an-application-package"></a>Ladda upp ett programpaket

I den här självstudien kommer vi att använda exemplet på [Service Fabric röstnings program](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/voting-sample-no-reverse-proxy) . Mer information om Service Fabric program distribution via PowerShell finns i [Service Fabric distribuera och ta bort program](service-fabric-deploy-remove-applications.md).

> [!NOTE]
> I förhands granskningen Service Fabric hanterade kluster kan du inte publicera program direkt från Visual Studio.

Du måste först [paketera programmet för distribution](service-fabric-package-apps.md). I den här självstudien följer du stegen för att paketera ett program i Visual Studio. Det är viktigt att anteckna sökvägen där programmet har paketerats eftersom det kommer att användas för sökvägen nedan.

När du har skapat programpaketet kan du ladda upp programpaketet till klustret. Uppdatera `$path` värdet för att representera sökvägen där ditt program paket finns och kör följande:

```powershell
$path = "C:\Users\<user>\Documents\service-fabric-dotnet-quickstart\Voting\pkg\Debug"
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage
Register-ServiceFabricApplicationType -ApplicationPathInImageStore Debug
```

### <a name="create-an-application"></a>Skapa ett program

Du kan instansiera ett program från vilken program typ version som helst som har registrerats med hjälp av New-ServiceFabricApplication-cmdleten. Namnet på varje program måste börja med schemat "Fabric:" och måste vara unikt för varje program instans. Alla standard tjänster som definieras i program manifestet för mål program typen skapas också.

```powershell
New-ServiceFabricApplication fabric:/Voting VotingType 1.0.0
```

När åtgärden har slutförts bör du se dina program instanser som körs i Service Fabric Explorer.

### <a name="remove-an-application"></a>Ta bort ett program

När en program instans inte längre behövs kan du ta bort den permanent med namnet med hjälp av `Remove-ServiceFabricApplication` cmdleten, som även automatiskt tar bort alla tjänster som tillhör programmet, permanent tar bort alla tjänst tillstånd.

```powershell
Remove-ServiceFabricApplication fabric:/Voting
```

## <a name="next-steps"></a>Nästa steg

I det här steget distribuerade vi en app till ett Service Fabric hanterat kluster. Mer information om Service Fabric hanterade kluster finns i:

> [!div class="nextstepaction"]
> [Vanliga frågor och svar om Service Fabric hanterade kluster](faq-managed-cluster.md)
