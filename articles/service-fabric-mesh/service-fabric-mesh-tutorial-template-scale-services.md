---
title: Självstudie – skala en app som körs i Azure Service Fabric nät
description: I den här självstudien lär du dig hur du skalar tjänsterna i ett program som körs i Service Fabric Mesh.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 02dc5d43a23c572d441da2bbb7386885bf66ece7
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625390"
---
# <a name="tutorial-scale-an-application-running-in-service-fabric-mesh"></a>Självstudie: Skala ett program som körs i Service Fabric Mesh

> [!IMPORTANT]
> Förhands granskningen av Azure Service Fabric-nätet har dragits tillbaka. Nya distributioner kommer inte längre att tillåtas via Service Fabric nät-API. Stöd för befintliga distributioner fortsätter till 28 april 2021.
> 
> Mer information finns i [förhands granskning av Azure Service Fabric nät](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Den här självstudien är del två i en serie. Lär dig hur du manuellt skalar antalet tjänstinstanser för ett program som [tidigare har distribuerats till Service Fabric Mesh](service-fabric-mesh-tutorial-template-deploy-app.md). När du är klar har du en klienttjänst som kör tre instanser och en datatjänst som kör två instanser.

I del två i serien lär du dig hur du:

> [!div class="checklist"]
> * Konfigurera önskat antal tjänstinstanser
> * Utföra en uppgradering

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Distribuera ett program till Service Fabric Mesh med en mall](service-fabric-mesh-tutorial-template-deploy-app.md)
> * Skala ett program som körs i Service Fabric Mesh
> * [Uppgradera ett program som körs i Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [Ta bort ett program](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

* Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* [Installera Azure CLI och Service Fabric Mesh CLI lokalt](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="manually-scale-your-services-in-or-out"></a>Skala in eller ut dina tjänster manuellt

En av de största fördelarna med att distribuera program till Service Fabric nät är möjligheten att enkelt skala dina tjänster i eller ut. Detta bör användas för att hantera olika belastnings mängder på dina tjänster eller förbättra tillgängligheten.

I den här självstudien används exemplet med att göra-listan, som [distribuerades tidigare](service-fabric-mesh-tutorial-template-deploy-app.md) och som nu bör vara igång och köras. Programmet har två tjänster: WebFrontEnd och ToDoService. Varje tjänst distribuerades från början med replikantal 1.  Om du vill visa antalet repliker som körs för WebFrontEnd-tjänsten kör du följande:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

Om du vill visa antalet repliker som körs för ToDoService-tjänsten kör du följande:

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

I distributionsmallen för programresursen har varje tjänst en *replicaCount*-egenskap som kan användas för att ange hur många gånger du vill att tjänsten distribueras. Ett program kan bestå av flera tjänster, och varje tjänst kan ha ett unikt *replicaCount*-värde, som distribueras och hanteras tillsammans. Om du vill skala antalet tjänstrepliker ändra du *replicaCount*-värdet för varje tjänst som du vill skala i distributionsmallen eller parameterfilen.  Sedan uppgraderar du programmet.

### <a name="modify-the-deployment-template-parameters"></a>Ändra parametrarna för distributionsmallen

Om det finns värden i mallen som förväntas ändras när programmet har distribuerats, eller som du vill ha möjlighet att ändra beroende på distribution (om du planerar att återanvända mallen för andra distributioner), bör du parameterisera värdena.

Tidigare distribuerades programmet med hjälp av [mesh_rp.windows.json-distributionsmallen](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) och [mesh_rp.windows.parameter.json-parameterfilen](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).

Öppna [mesh_rp.windows.parameter.json-parameterfilen](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) lokalt och ange *frontEndReplicaCount*-värdet till 3 och *serviceReplicaCount*-värdet till 2:

```json
      "frontEndReplicaCount":{
        "value": "3"
      },
      "serviceReplicaCount":{
        "value": "2"
      }
```

Spara dina ändringar i parameterfilen.  Parametrarna *frontEndReplicaCount* och *serviceReplicaCount* deklareras i avsnittet *parameters* i [mesh_rp.windows.json-distributionsmallen](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json):

```json
"frontEndReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    },
    "serviceReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    }
```

*replicaCount*-egenskapen för WebFrontEnd-tjänsten refererar till *frontEndReplicaCount*-parametern och *replicaCount*-egenskapen för ToDoService-tjänsten refererar till *serviceReplicaCount*-parametern:

```json
    "services": [
    {
    "name": "WebFrontEnd",
    "properties": {
        "description": "WebFrontEnd description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('frontEndReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    },
    {
    "name": "ToDoService",
    "properties": {
        "description": "ToDoService description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('serviceReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    }
],
```

När mallen har ändrats uppgraderar du programmet.

### <a name="upgrade-your-application"></a>Uppgraderar tillämpningen

När programmet körs kan du uppgradera det genom att omdistribuera mallen och den uppdaterade parameterfilen:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

När du gör det startar en löpande uppgradering av ditt program och du bör se hur tjänstinstanserna ökar inom några minuter.  Om du vill visa antalet repliker som körs för WebFrontEnd-tjänsten kör du följande:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

Om du vill visa antalet repliker som körs för ToDoService-tjänsten kör du följande:

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera önskat antal tjänstinstanser
> * Utföra en uppgradering

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Uppgradera ett program som körs i Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)
