---
ms.openlocfilehash: e8d8300d2be7a2a48cb48a51b0f81c96f68c45aa
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750375"
---
1. Klona lagrings platsen från den här platsen: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. Öppna mappen där lagrings platsen har hämtats i Visual Studio Code.
1. I Visual Studio Code går du till mappen *src/Cloud-to-Device-console-app* . Där skapar du en fil och namnger den *appsettings.js*. Den här filen innehåller de inställningar som krävs för att köra programmet.
1. Kopiera innehållet från *~/clouddrive/lva-Sample/-appsettings.jspå* filen som du skapade tidigare i den här snabb starten.

    Texten bör se ut som följande utdata.

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Gå till mappen *src/Edge* och skapa en fil med namnet *. kuvert*.
1. Kopiera innehållet i */clouddrive/lva-Sample/Edge-Deployment/.env* -filen. Texten bör se ut som i följande kod.

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    VIDEO_INPUT_FOLDER_ON_DEVICE="/home/lvaedgeuser/samples/input"  
    VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"      
    ```
    