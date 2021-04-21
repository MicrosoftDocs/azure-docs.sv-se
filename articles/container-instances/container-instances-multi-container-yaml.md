---
title: Självstudie – Distribuera grupp med flera containrar – YAML
description: I den här självstudien får du lära dig hur du distribuerar en containergrupp med flera containrar i Azure Container Instances med hjälp av en YAML-fil med Azure CLI.
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: 74269440357ee2d7ae36661618a31293346fa712
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771271"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Självstudie: Distribuera en grupp med flera containrar med hjälp av en YAML-fil

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)
>

Azure Container Instances stöd för distribution av flera containrar till en enda värd med hjälp av en [containergrupp](container-instances-container-groups.md). En containergrupp är användbar när du skapar en programsidavagn för loggning, övervakning eller någon annan konfiguration där en tjänst behöver en andra kopplad process.

I den här självstudien följer du stegen för att köra en enkel sidovagnskonfiguration med två containrar genom att distribuera en [YAML-fil](container-instances-reference-yaml.md) med hjälp av Azure CLI. En YAML-fil ger ett koncis format för att ange instansinställningarna. Lär dig att:

> [!div class="checklist"]
> * Konfigurera en YAML-fil
> * Distribuera containergruppen
> * Visa loggarna för containrarna

> [!NOTE]
> Grupper med flera containrar är för närvarande begränsade till Linux-containrar.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="configure-a-yaml-file"></a>Konfigurera en YAML-fil

Om du vill distribuera en grupp med flera containrar med kommandot [az container create][az-container-create] i Azure CLI måste du ange containergruppens konfiguration i en YAML-fil. Skicka sedan YAML-filen som en parameter till kommandot .

Börja med att kopiera följande YAML till en ny fil med namnet **deploy-aci.yaml**. I Azure Cloud Shell kan du använda Visual Studio Code för att skapa filen i arbetskatalogen:

```
code deploy-aci.yaml
```

Den här YAML-filen definierar en containergrupp med namnet "myContainerGroup" med två containrar, en offentlig IP-adress och två exponerade portar. Containrarna distribueras från offentliga Microsoft-avbildningar. Den första containern i gruppen kör ett Internetuppriktad webbprogram. Den andra containern, sidovagnen, gör regelbundet HTTP-begäranden till webbprogrammet som körs i den första containern via containergruppens lokala nätverk.

```YAML
apiVersion: 2019-12-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: mcr.microsoft.com/azuredocs/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 80
    - protocol: tcp
      port: 8080
tags: {exampleTag: tutorial}
type: Microsoft.ContainerInstance/containerGroups
```

Om du vill använda ett privat containeravbildningsregister lägger `imageRegistryCredentials` du till egenskapen i containergruppen med värden som ändrats för din miljö:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>Distribuera containergruppen

Skapa en resursgrupp med [kommandot az group][az-group-create] create:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuera containergruppen med kommandot [az container create][az-container-create] och skicka YAML-filen som ett argument:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

Inom några sekunder bör du få ett första svar från Azure.

## <a name="view-deployment-state"></a>Visa distributionstillstånd

Om du vill visa status för distributionen använder du följande [az container show-kommando:][az-container-show]

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Om du vill visa programmet som körs går du till dess IP-adress i webbläsaren. IP-adressen finns till exempel i `52.168.26.124` följande exempelutdata:

```console
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Visa containerloggar

Visa loggutdata för en container med kommandot [az container logs.][az-container-logs] Argumentet `--container-name` anger den container som loggarna ska hämtas från. I det här exemplet `aci-tutorial-app` anges containern.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Utdata:

```console
listening on port 80
::1 - - [02/Jul/2020:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Om du vill se loggarna för sidovagnscontainern kör du ett liknande kommando som anger `aci-tutorial-sidecar` containern.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Utdata:

```console
Every 3s: curl -I http://localhost                          2020-07-02 20:36:41

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Thu, 02 Jul 2020 20:36:41 GMT
Connection: keep-alive
```

Som du ser gör sidovagnen regelbundet en HTTP-begäran till huvudwebbappen via gruppens lokala nätverk för att säkerställa att den körs. Det här sidovagnsexempel kan utökas för att utlösa en avisering om den tog emot en annan HTTP-svarskod än `200 OK` .

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du en YAML-fil för att distribuera en grupp med flera containrar i Azure Container Instances. Du har lärt dig att:

> [!div class="checklist"]
> * Konfigurera en YAML-fil för en grupp med flera containrar
> * Distribuera containergruppen
> * Visa loggarna för containrarna

Du kan också ange en grupp med flera containrar med hjälp av [Resource Manager mall](container-instances-multi-container-group.md). En Resource Manager-mall kan enkelt anpassas för scenarier när du behöver distribuera ytterligare Azure-tjänstresurser med containergruppen.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az_container_create
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[template-reference]: /azure/templates/microsoft.containerinstance/containergroups
