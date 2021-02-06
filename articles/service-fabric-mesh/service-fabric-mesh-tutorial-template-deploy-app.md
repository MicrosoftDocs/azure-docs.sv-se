---
title: Självstudie – distribuera en app till Azure Service Fabric nät
description: I den här självstudiekursen får du lära dig hur du distribuerar ett program till ett Service Fabric Mesh med en mall.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 589e881eb48daf7da9cd2a934b14acfcc76dc5f9
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625423"
---
# <a name="tutorial-deploy-an-application-to-service-fabric-mesh-using-a-template"></a>Självstudie: Distribuera ett program till Service Fabric Mesh med hjälp av en mall

> [!IMPORTANT]
> Förhands granskningen av Azure Service Fabric-nätet har dragits tillbaka. Nya distributioner kommer inte längre att tillåtas via Service Fabric nät-API. Stöd för befintliga distributioner fortsätter till 28 april 2021.
> 
> Mer information finns i [förhands granskning av Azure Service Fabric nät](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Den här självstudien ingår i en serie. Du lär dig hur du distribuerar ett Azure Service Fabric-nätprogram med hjälp av en mall.  Programmet består av ASP.NET-webbtjänst för användarna och en serverdel med ASP.NET Core Web API som finns i Docker Hub.  Du hämtar de två containeravbildningarna från Docker Hub och skickar dem sedan till ditt eget privata register. Du kan sedan skapa en mall i Azure RM för programmet och distribuera program från ditt containerregister till Service Fabric-nät. När du är klar har du en enkel att göra-lista-program som körs i Service Fabric-nät.

I del ett i den här serien lärde du dig att:

> [!div class="checklist"]
> * Skapa en privat Azure Container Registry-instans
> * Push-överföra en containeravbildning till registret
> * Skapa RM-mall och parameterfiler
> * Distribuera ett program till Service Fabric Mesh

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * Distribuera ett program till Service Fabric Mesh med hjälp av en mall
> * [Skala tjänster i ett program som körs i Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Uppgradera ett program som körs i Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [Ta bort ett program](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

* Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* [Installera Docker](service-fabric-mesh-howto-setup-developer-environment-sdk.md#install-docker)

* [Installera Azure CLI och Service Fabric Mesh CLI lokalt](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="create-a-container-registry"></a>Skapa ett containerregister

Containeravbildningar som är knutna till tjänsterna i ditt program för Service Fabric-nät måste lagras i ett containerregister.  I den här kursen använder vi en privat instans av Azure Container Registry. 

Använd följande steg för att skapa en ACR-instans.  Om du redan har en installerad ACR-instans kan gå du vidare.

### <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure och konfigurera den aktiva prenumerationen.

```azurecli
az login
az account set --subscription "<subscriptionName>"
```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Använd följande kommando för att skapa en resursgrupp med namnet *myResourceGroup* på platsen *EastUS*.

```azurecli
az group create --name myResourceGroup --location eastus
```

### <a name="create-the-container-registry"></a>Skapa containerregister

Skapa en ACR-instans med hjälp av kommandot `az acr create`. Registernamnet måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. I följande exempel används namnet *myContainerRegistry*. Om du får ett felmeddelande om att registernamnet redan används väljer du ett annat namn.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry --sku Basic
```

När registret har skapats ser utdata ut ungefär så här:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-09-13T19:43:57.388203+00:00",
  "id": "/subscriptions/<subscription>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry",
  "location": "eastus",
  "loginServer": "mycontainerregistry.azurecr.io",
  "name": "myContainerRegistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="push-the-images-to-azure-container-registry"></a>Push-överför avbildningar till Azure Container Registry

Den här självstudien använder exempelprogrammet att göra-lista som ett exempel.  Containeravbildningar för tjänsterna [WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/) och [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/) finns på Docker Hub. Se [Skapa en webbapp för Service Fabric-nät](service-fabric-mesh-tutorial-create-dotnetcore.md) för information om hur du skapar programmet i Visual Studio. Service Fabric-nät kan köra Windows- eller Linux Docker-containrar.  Om du arbetar med Linux-containrar väljer du **Växla till Linux-containrar** i Docker.  Om du arbetar med Windows-containrar väljer du **Växla till Windows-containrar** i Docker.

Innan du kan push-överföra en avbildning till en Azure Container Registry-instans måste du ha en containeravbildning. Om du ännu inte har några lokala containeravbildningar använder du kommandot [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) för att hämta avbildningarna [WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/) och [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/) från Docker Hub.

>[!NOTE]
> Från och med den 2 november 2020 [gäller nedladdnings hastighets gränserna](https://docs.docker.com/docker-hub/download-rate-limit/) för anonyma och autentiserade begär anden till Docker Hub från Docker-fri Plans konton och tillämpas med IP-adress. 
> 
> De här kommandona använder offentliga avbildningar från Docker Hub. Observera att du kan begränsas till en begränsad hastighet. Mer information finns i [autentisera med Docker Hub](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub).

Hämta Windows-avbildningar:

```bash
docker pull seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker pull seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

Innan du kan push-överföra en avbildning till ditt register måste du tagga den med det fullständiga namnet på din ACR-inloggningsserver.

Kör följande kommando för att hämta det fullständiga namnet på inloggningsservern för ACR-instansen.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table

AcrLoginServer
---------------------------------
mycontainerregistry.azurecr.io
```

Tagga Docker-avbildningen med hjälp av kommandot [docker tag](https://docs.docker.com/engine/reference/commandline/tag/). I följande exempel används taggas avbildningarna seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709 och seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709. Om du använder andra avbildningar ersätter du dessa avbildningsnamn i följande kommando.

```bash
docker tag seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker tag seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

Logga in på Azure Container Registry.

```azurecli
az acr login -n myContainerRegistry
```

Använd slutligen [docker push](https://docs.docker.com/engine/reference/commandline/push/) för att överföra avbildningen till ACR-instansen:

```bash
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

### <a name="list-container-images"></a>Visa lista över containeravbildningar

Om du vill se lagringsplatser i ACR-instansen, kör du följande:

```azurecli
az acr repository list --name myContainerRegistry --output table

Result
-------------------------------
seabreeze/azure-mesh-todo-webfrontend
seabreeze/azure-mesh-todo-service
```

I följande exempel visas taggarna för lagringsplatsen **azure-mesh-todo-service**.

```azurecli
az acr repository show-tags --name myContainerRegistry --repository seabreeze/azure-mesh-todo-service --output table

Result
--------
1.0-nanoserver-1709
```

Föregående resultatet bekräftar förekomsten av `azure-mesh-todo-service:1.0-nanoserver-1709` i det privata containerregistret.  Kontrollera också förekomsten av `azure-mesh-todo-webfrontend:1.0-nanoserver-1709`.

## <a name="retrieve-credentials-for-the-registry"></a>Hämta autentiseringsuppgifterna för registret

> [!IMPORTANT]
> Du rekommenderas inte att aktivera administratörsanvändare på en ACR-instans för produktionsscenarier. Här gör vi det endast för att underlätta. I produktionsscenarier ska du använda [tjänstens huvudnamn](../container-registry/container-registry-auth-service-principal.md) för både användar- och systemautentisering i produktionsscenarier.

Du måste ange autentiseringsuppgifterna för registret när du ska distribuera en containerinstans från registret som du skapat. Aktivera först administratörsanvändaren i registret med följande kommando:

```azurecli
az acr update --name myContainerRegistry --admin-enabled true
```

Hämta sedan namnet på inloggningsservern för registret, användarnamn och lösenord med hjälp av följande kommandon:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name myContainerRegistry --query username
az acr credential show --name myContainerRegistry --query "passwords[0].value"
```

Använd det returnerade inloggningsservernamnet för ACR, användarnamn och lösenord när du skapar RM-mallen och parameterfilerna i följande avsnitt.

## <a name="download-and-explore-the-template-and-parameters-files"></a>Ladda ned och titta närmare på mall- och parameterfiler

Ett program för Service Fabric-nät är en Azure-resurs som du kan distribuera och hantera med hjälp av mallar för Azure Resource Manager (RM). Om du inte är bekant med principerna för att distribuera och hantera dina Azure-lösningar kan du läsa mer i [Översikt över Azure Resource Manager](../azure-resource-manager/management/overview.md) och [Förstå strukturen och syntaxen för RM-mallar](../azure-resource-manager/templates/template-syntax.md).

Den här självstudien använder exempelprogrammet att göra-lista som ett exempel.  Istället för att skapa nya mall- och parameterfiler kan du hämta filerna [mesh_rp.windows.json-distributionsmallen](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) och [mesh_rp.windows.parameter.json-parameterfilen](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).

### <a name="parameters"></a>Parametrar
Om det finns värden i mallen som förväntas ändras när programmet har distribuerats, eller som du vill ha möjlighet att ändra beroende på distribution (om du planerar att återanvända mallen för andra distributioner), bör du parameterisera värdena. Rätt sätt att göra detta på är ett skapa ett ”parameter”-område längst upp i din distributionsmall där du anger parameternamn och -egenskaper som du kan referera till senare i distributionsmallen. Varje parameterdefinition innehåller *typ*, *defaultValue*, och ett valfritt område för *metadata* med en *beskrivning*.

Parameterområdet har definierats överst i din distributionsmall, precis före *resursområdet*:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      ...
    },
    "resources": [
```

I [mesh_rp.windows.json Distributionsmall](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) deklareras följande parametrar: plats, registryPassword, registryUserName, registryServer, frontEndImage, serviceImage, frontEndCpu, serviceCpu, frontEndMemory, serviceMemory, frontEndReplicaCount, serviceReplicaCount.  Beskrivningar för varje parameter kan hittas i distributionsmallfilen.

Dessa parametrar används i parameterfilen [mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).  En separat parameterfil ger dig möjlighet att ändra parametervärden från distribution till distribution utan att uppdatera själva distributionsmallen.

### <a name="overview-of-the-application-and-services"></a>Översikt över program och tjänster

Tjänster anges i mallen som egenskaper för programresursen.  Program distribueras till ett privat nätverk som deklareras som en resurs i mallen.  Tjänster kan använda volymer för att bevara data som deklareras som resurser i mallen.  För varje tjänst anges OS-typ, kod paket, antal repliker och nätverket som egenskaper för tjänsten.  Ange containeravbildning, slutpunkter, minne och CPU-resurser samt autentiseringsuppgifter för avbildningslagringsplatsen för varje kodpaketet. Mallen för ett Service Fabric-nät program med flera tjänster ser ut på följande vis på hög nivå:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "MyMeshApplication",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/MeshAppNetwork",
        "Microsoft.ServiceFabricMesh/volumes/ServiceAVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "ServiceA",
            "properties": {
              "description": "ServiceA description.",
              "osType": "Linux",
              "codePackages": [
                {
                  "name": "ServiceA",
                  "image": "[parameters('frontEndImage')]",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'ServiceAVolume')]",
                      "destinationPath": "/app/data"
                    }
                  ],
                  "endpoints": [
                    {
                      "name": "ServiceAListener",
                      "port": 80
                    }
                  ],
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
              "replicaCount": "[parameters('frontEndReplicaCount')]",
              "networkRefs": [
                {
                  "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'MeshAppNetwork')]"
                }
              ]
            }
          },
          {
            "name": "ServiceB",
            ...
          }
        ],
        "description": "Application description."
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MeshAppNetwork",
      "type": "Microsoft.ServiceFabricMesh/networks",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "MeshAppNetwork description.",
        "addressPrefix": "10.0.0.4/22",
        "ingressConfig": {
          "layer4": [
            {
              "name": "ServiceAIngress",
              "publicPort": "20001",
              "applicationName": "MyMeshApplication",
              "serviceName": "ServiceA",
              "endpointName": "ServiceAListener"
            }
          ]
        }
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "ServiceAVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for counter App.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

Se mallfilen [mesh_rp.windows.json Distributionsmall](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) för information om programmet att göra-lista.

## <a name="deploy-the-application-to-service-fabric-mesh"></a>Distribuera programet till Service Fabric-nät
Skapa program och relaterade resurser med följande kommando och ange autentiseringsuppgifter från det föregående steget [Hämta autentiseringsuppgifter för registret](#retrieve-credentials-for-the-registry).

Uppdatera följande parametervärden i parameterfilen:

|Parameter|Värde|
|---|---|
|location|Regionen som programmet ska distribueras till.  Till exepel usaöstra.|
|registryPassword|Lösenordet som du fick tidigare i [Hämta autentiseringsuppgifter för registret](#retrieve-credentials-for-the-registry). Den här parametern i mallen är en säker sträng och visas inte i distributionens status eller `az mesh service show`-kommandon.|
|registryUserName|Användarnamnet som du fick i [Hämta autentiseringsuppgifter för registret](#retrieve-credentials-for-the-registry).|
|registryServer|Registerservernamnet som du fick i [Hämta autentiseringsuppgifter för registret](#retrieve-credentials-for-the-registry).|
|frontEndImage|Containeravbildning för klientdelstjänsten.  Ett exempel är `<myregistry>.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709`.|
|serviceImage|Containeravbildning för serverdelstjänsten.  Ett exempel är `<myregistry>.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709`.|

Om du vill distribuera programmet kör du följande:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

Det här kommandot skapar ett JSON-kodavsnitt som visas nedan. Under avsnittet ```outputs``` i JSON-utdata kopierar du egenskapen ```publicIPAddress```.

```json
"outputs": {
    "publicIPAddress": {
    "type": "String",
    "value": "40.83.78.216"
    }
}
```

Den här informationen kommer från avsnittet ```outputs``` i ARM-mallen. Enligt det som visas nedan refererar det här avsnittet till Gateway-resursen för att hämta den offentliga IP-adressen. 

```json
  "outputs": {
    "publicIPAddress": {
      "value": "[reference('todolistappGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>Öppna programmet

När programmet har distribuerats hämtar du den offentliga IP-adressen för tjänstslutpunkten. Distributionskommandot returnerar den offentliga IP-adressen för tjänsteslutpunkten. Du kan också begära att nätverksresursen hittar den offentliga IP-adressen för tjänsteslutpunkten. Nätverksresursnamnet för det här programmet är `todolistappNetwork`, hämta information om det med hjälp av följande kommando. 

```azurecli
az mesh gateway show --resource-group myResourceGroup --name todolistappGateway
```

Gå till IP-adressen i en webbläsare.

## <a name="check-application-status"></a>Välj programstatus

Du kan kontrollera programmets status med kommandot Visa app. Programnamn för det distribuerade programmet är ”todolistapp”. Hämta information om det.

```azurecli
az mesh app show --resource-group myResourceGroup --name todolistapp
```

Granska loggarna för det distribuerade programmet med hjälp av kommandot `az mesh code-package-log get`:
```azurecli
az mesh code-package-log get --resource-group myResourceGroup --application-name todolistapp --service-name WebFrontEnd --replica-name 0 --code-package-name WebFrontEnd
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett privat containerregister
> * Push-överföra en containeravbildning till registret
> * Skapa en mall och parameterfiler
> * Distribuera ett program till Service Fabric Mesh

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Skala ett program som körs i Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)