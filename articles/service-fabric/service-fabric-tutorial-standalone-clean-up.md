---
title: Rensa ett fristående kluster
description: I den här självstudien får du lära dig hur du tar bort AWS eller Azure-resurser för ditt fristående Service Fabric-kluster.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 0d46e9068a311594f779411c3ccee2b408febb3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91842894"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Självstudier: Rensa fristående kluster

Service Fabric fristående kluster ger dig möjlighet att välja din egen miljö som värd för Service Fabric. I den här själv studie serien skapar du ett fristående kluster som finns på AWS eller Azure och distribuerar ett program till det.

Den här självstudien är del fyra i en serie. Den här delen av självstudien visar hur du tar bort de AWS-eller Azure-resurser som du har skapat som värd för ditt Service Fabric-kluster.

I den här artikeln får du lära dig att:

> [!div class="checklist"]
> * Ta bort ett Service Fabric kluster
> * Ta bort dina AWS-eller Azure-resurser

## <a name="remove-a-service-fabric-cluster"></a>Ta bort ett Service Fabric kluster

1. RDP till den virtuella dator som du använde för att installera Service Fabric.
2. Öppna PowerShell.
3. Ändra katalog till den extraherade mappen från den andra självstudien.
4. Kör följande kommando för att ta bort Service Fabric-klustret:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Ange `Y` när du uppmanas till det. Om det lyckas kommer dina utdata att se ut ungefär så här (med dina egna IP-adresser):

  ```powershell
  Best Practices Analyzer completed successfully.
  Removing configuration from machine 172.31.21.141
  Removing configuration from machine 172.31.27.1
  Removing configuration from machine 172.31.20.163
  Configuration removed from machine 172.31.21.141
  Configuration removed from machine 172.31.27.1
  Configuration removed from machine 172.31.20.163
  The cluster is successfully removed.
  ```

## <a name="delete-aws-resources"></a>Ta bort AWS-resurser

1. Logga in på ditt AWS-konto.
2. Gå till EC2-konsolen.
3. Välj de tre noder som du skapade i del ett av självstudien.
4. Välj **åtgärder**  >  **instans tillstånd**  >  **Avsluta**.

## <a name="delete-azure-resources"></a>Ta bort Azure-resurser

1. Logga in på Azure-portalen.
2. Gå till avsnittet **Virtual Machines** .
3. Markera kryss rutorna för de tre noder som du skapade i del ett av självstudien.
4. Välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du tar bort resurserna som du skapade i föregående steg.

> [!div class="checklist"]
> * Rensa dina resurser

> [!div class="nextstepaction"]
> [Tillbaka till början](service-fabric-tutorial-standalone-create-infrastructure.md)
