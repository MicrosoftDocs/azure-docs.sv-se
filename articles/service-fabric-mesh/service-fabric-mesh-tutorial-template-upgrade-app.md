---
title: Självstudie – uppgradera en app som körs i Azure Service Fabric nät
description: I den här självstudien lär du dig hur du uppgraderar ett program som körs i Service Fabric Mesh.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 8a71e854f03bee75b757e0a0aa02e7aa2c24469b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626568"
---
# <a name="tutorial-upgrade-a-service-fabric-application-running-in-service-fabric-mesh"></a>Självstudie: uppgradera ett program som körs i Service Fabric Mesh

> [!IMPORTANT]
> Förhands granskningen av Azure Service Fabric-nätet har dragits tillbaka. Nya distributioner kommer inte längre att tillåtas via Service Fabric nät-API. Stöd för befintliga distributioner fortsätter till 28 april 2021.
> 
> Mer information finns i [förhands granskning av Azure Service Fabric nät](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Den här självstudiekursen är den tredje delen i en serie. Du får lära dig hur du uppgraderar ett Service Fabric-program som [tidigare har distribuerat till Service Fabric Mesh](service-fabric-mesh-tutorial-template-deploy-app.md) genom att öka de tilldelade CPU-resurserna.  När du är klar har du en webbtjänst för klientdelen som körs med högre CPU-resurser.

I den tredje delen i serien får du lära dig att:

> [!div class="checklist"]
> * Ändra programkonfigurationerna
> * Uppgradera ett program som körs i Service Fabric Mesh

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Distribuera ett program till Service Fabric Mesh med en mall](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [Skala ett program som körs i Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
> * Uppgradera ett program som körs i Service Fabric Mesh
> * [Ta bort ett program](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

* Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Öppna [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md), eller [installera Azure CLI och Service Fabric Mesh CLI lokalt](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="upgrade-application-configurations"></a>Uppgradera programkonfigurationerna

En av de största fördelarna med att distribuera program till Service Fabric Mesh är att du enkelt kan uppdatera din programkonfiguration.  Till exempel CPU- eller minnesresurser för dina tjänster.

I den här självstudien används exemplet med att göra-listan, som [distribuerades tidigare](service-fabric-mesh-tutorial-template-deploy-app.md) och som nu bör vara igång och köras. Programmet har två tjänster: WebFrontEnd och ToDoService. Varje tjänst distribuerades från början med CPU-resurser på 0,5.  Om du vill visa CPU-resurserna för WebFrontEnd-tjänsten kör du följande:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp
```

I distributionsmallen för programresursen har varje tjänst en *cpu*-egenskap som kan användas för att ange önskade CPU-resurser. Ett program kan bestå av flera tjänster där varje tjänst har en unik *CPU*-inställning som distribueras och hanteras tillsammans. För att öka CPU-resurserna för webbtjänsten för klientdelen ändrar du värdet *cpue* i distributionsmallen eller parameterfilen.  Sedan uppgraderar du programmet.

### <a name="modify-the-deployment-template-parameters"></a>Ändra parametrarna för distributionsmallen

Om det finns värden i mallen som förväntas ändras när programmet har distribuerats, eller som du vill ha möjlighet att ändra beroende på distribution (om du planerar att återanvända mallen för andra distributioner), bör du parameterisera värdena.

Tidigare distribuerades programmet med hjälp av [mesh_rp.windows.json-distributionsmallen](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) och [mesh_rp.windows.parameter.json-parameterfilen](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).

Öppna [mesh_rp.windows.parameter.json-parameterfilen](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) lokalt och ange *frontEndCpu*-värdet till 1:

```json
      "frontEndCpu":{
        "value": "1"
      }
```

Spara dina ändringar i parameterfilen.  

Parametern *frontEndCpu* deklareras i *parametrar*-avsnittet i [distributionsmallen mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json):

```json
"frontEndCpu": {
    "defaultValue": "0.5",
    "type": "string",
    "metadata": {
        "description": "The CPU resources for the front end web service."
    }
}
```

WebFrontEnd-tjänsten *codePackages->resurser->begäranden->cpu* egenskapen refererar till *frontEndCpu*-parametern:

```json
    "services": [
          {
            "name": "WebFrontEnd",
            "properties": {
              "description": "WebFrontEnd description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "WebFrontEnd",
                  "image": "[parameters('frontEndImage')]",
                  ...
                  "resources": {
                    "requests": {
                      "cpu": "[parameters('frontEndCpu')]",
                      "memoryInGB": "[parameters('frontEndMemory')]"
                    }
                  },
                  "imageRegistryCredential": {
                    "server": "[parameters('registryServer')]",
                    "username": "[parameters('registryUserName')]",
                    "password": "[parameters('registryPassword')]"
                  }
                }
              ],
              ...
```

### <a name="upgrade-your-application"></a>Uppgraderar tillämpningen

När programmet körs kan du uppgradera det genom att omdistribuera mallen och den uppdaterade parameterfilen:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

Det här startar en rullande uppgraderingen av ditt program och du bör se hur dina CPU-resurser ökar inom några minuter.  Om du vill visa CPU-resurserna för WebFrontEnd-tjänsten kör du följande:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:

> [!div class="checklist"]
> * Ändra programkonfigurationerna
> * Uppgradera ett program som körs i Service Fabric Mesh

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Ta bort ett Service Fabric Mesh-program](service-fabric-mesh-tutorial-template-remove-app.md)
