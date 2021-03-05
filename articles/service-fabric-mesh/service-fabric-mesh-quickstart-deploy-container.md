---
title: Snabb start – distribuera Hello World till Azure Service Fabric-nätet
description: Den här snabbstarten visar hur du distribuerar ett Service Fabric Mesh-program till Azure Service Fabric Mesh.
author: georgewallace
ms.author: gwallace
ms.date: 11/27/2018
ms.topic: quickstart
ms.openlocfilehash: c89cc1972a554cac85ce2a258873f6c810e45167
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102217728"
---
# <a name="quickstart-deploy-hello-world-to-service-fabric-mesh"></a>Snabbstart: Distribuera Hello World till Service Fabric Mesh

> [!IMPORTANT]
> Förhands granskningen av Azure Service Fabric-nätet har dragits tillbaka. Nya distributioner kommer inte längre att tillåtas via Service Fabric nät-API. Stöd för befintliga distributioner fortsätter till 28 april 2021.
> 
> Mer information finns i [förhands granskning av Azure Service Fabric nät](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

[Service Fabric Mesh](service-fabric-mesh-overview.md) gör det enkelt att skapa och hantera mikrotjänstprogram i Azure utan att behöva etablera virtuella datorer. I den här snabbstarten skapar du ett Hello World-program i Azure och gör det tillgängligt på Internet. Den här åtgärden utförs med ett enda kommando. Inom ett par minuter visas den här vyn i webbläsaren:

![Hello World-app i webbläsaren][sfm-app-browser]

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Om du inte redan har ett Azure-konto [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="set-up-service-fabric-mesh-cli"></a>Konfigurera Service Fabric Mesh CLI 
Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att genomföra den här snabbstarten. Installera Azure Service Fabric Mesh CLI-tilläggsmodulen genom att följa de här [instruktionerna](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure och konfigurera din prenumeration.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Skapa resursgrupp
Skapa en resursgrupp som programmet ska distribueras till. Du kan använda en befintlig resursgrupp och hoppa över det här steget. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>Distribuera programmet

>[!NOTE]
> Från och med den 2 november 2020 [gäller nedladdnings hastighets gränserna](https://docs.docker.com/docker-hub/download-rate-limit/) för anonyma och autentiserade begär anden till Docker Hub från Docker-fri Plans konton och tillämpas med IP-adress. 
> 
> De här mallarna använder offentliga avbildningar från Docker Hub. Observera att du kan begränsas till en begränsad hastighet. Mer information finns i [autentisera med Docker Hub](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub).

Skapa ditt program i en resursgrupp med kommandot `az mesh deployment create`.  Kör följande:

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json --parameters "{'location': {'value': 'eastus'}}" 
```

Föregående kommando distribuerar ett Linux-program med hjälp av [linux.json-mallen](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json). Om du vill distribuera ett Windows-program använder du [windows.json-mallen](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.windows.json). Windows-containeravbildningar är större än Linux-containeravbildningar och kan ta längre tid att distribuera.

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
      "value": "[reference('helloWorldGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>Öppna programmet
När programmet har distribuerats kopierar du den offentliga IP-adressen för tjänstslutpunkten från CLI-utdata. Öppna IP-adressen i en webbläsare. En webbplats med logotypen för Azure Service Fabric Mesh visas.

## <a name="check-the-application-details"></a>Kontrollera programinformationen
Du kan kontrollera programmets status med kommandot `az mesh app show`. Det här kommandot ger användbar information som du kan följa upp.

Programnamnet för den här snabbstarten är `helloWorldApp`. Om du vill hämta information om programmet kör du följande kommando:

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

## <a name="see-the-application-logs"></a>Se programloggarna

Granska loggarna för det distribuerade programmet med hjälp av kommandot `az mesh code-package-log get`:

```azurecli-interactive
az mesh code-package-log get --resource-group myResourceGroup --application-name helloWorldApp --service-name helloWorldService --replica-name 0 --code-package-name helloWorldCode
```

## <a name="clean-up-resources"></a>Rensa resurser

När du är redo att ta bort programmet kör du kommandot [az group delete][az-group-delete] för att ta bort resursgruppen och de program- och nätverksresurser som den innehåller.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om att skapa och distribuera Service Fabric Mesh-program fortsätter du till självstudien.
> [!div class="nextstepaction"]
> [Skapa och distribuera ett webbprogram för flera tjänster](service-fabric-mesh-tutorial-create-dotnetcore.md)

<!-- Images -->
[sfm-app-browser]: ./media/service-fabric-mesh-quickstart-deploy-container/HelloWorld.png

<!-- Links / Internal -->
[az-group-delete]: /cli/azure/group
[azure-cli-install]: /cli/azure/install-azure-cli