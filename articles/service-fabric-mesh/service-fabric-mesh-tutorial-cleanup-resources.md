---
title: Självstudie – rensa Azure Service Fabric nät resurser
description: Lär dig hur du tar bort Azure Service Fabric Mesh-resurser så att du inte debiteras för resurser som du inte längre använder.
author: georgewallace
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 4d78e1e27d70b147bb52dff13675e63b79335d62
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625472"
---
# <a name="tutorial-remove-azure-resources"></a>Självstudie: Ta bort Azure-resurser

> [!IMPORTANT]
> Förhands granskningen av Azure Service Fabric-nätet har dragits tillbaka. Nya distributioner kommer inte längre att tillåtas via Service Fabric nät-API. Stöd för befintliga distributioner fortsätter till 28 april 2021.
> 
> Mer information finns i [förhands granskning av Azure Service Fabric nät](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Den här självstudien är del fem i en serie och beskriver hur du tar bort appen och dess resurser så att du inte debiteras för dem.

I den här guiden får du lära du dig hur man:
> [!div class="checklist"]
> * Rensa de resurser som används av appen så att du inte debiteras för dem.

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Skapa en Service Fabric Mesh-app i Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Felsöka en Service Fabric Mesh-app som körs i ditt lokala utvecklingskluster](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Distribuera en Service Fabric Mesh-app](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Uppgradera en Service Fabric Mesh-app](service-fabric-mesh-tutorial-upgrade.md)
> * Rensa Service Fabric Mesh-resurser

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

* Om du inte har distribuerat att göra-appen följer du anvisningarna i [Publicera ett Service Fabric Mesh-webbprogram](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="clean-up-resources"></a>Rensa resurser

Det här är slutet av självstudiekursen. När du är klar med de resurser som du har skapat tar du bort dem så att du inte debiteras för resurser som du inte längre använder. Detta är särskilt viktigt eftersom Mesh är en serverlös tjänst som debiteras per sekund. Mer information om priserna för Mesh finns i https://aka.ms/sfmeshpricing.

En av fördelarna med Azure är att när du skapar resurser som är kopplade till en viss resursgrupp tas alla associerade resurser bort när du tar bort resursgruppen. Det betyder att du inte behöver ta bort dem en i taget.

Eftersom du har skapat en ny resursgrupp för att skapa att göra-appen kan du ta bort den här resursgruppen, vilket gör att alla associerade resurser tas bort.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Du kan också ta bort resursgruppen **sfmeshTutorial1RG**[från portalen](../azure-resource-manager/management/manage-resource-groups-portal.md#delete-resource-groups). 

## <a name="next-steps"></a>Nästa steg

Nu när du har slutfört publiceringen av Service Fabric Mesh-programmet till Azure kan du prova följande:

* Om du vill se ett annat exempel på tjänst-till-tjänst-kommunikation kan du utforska [exempelröstningsprogrammet](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp)
* Mer information om Service Fabric-resursmodellen finns i avsnittet om [Service Fabric Mesh-resursmodellen](service-fabric-mesh-service-fabric-resources.md).
* Mer information om Service Fabric Mesh finns i [översikten över Service Fabric Mesh](service-fabric-mesh-overview.md).
* Läs mer om [Cloud Shell](../cloud-shell/overview.md)
