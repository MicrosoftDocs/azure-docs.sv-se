---
title: Snabb start – konfigurera Azure IoT Hub Device Provisioning Service med Azure CLI
description: Snabb start – konfigurera Azure-IoT Hub Device Provisioning Service (DPS) med Azure CLI
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 611068fa020321be88be6e1d6da663266029c658
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94660193"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Snabb start: Konfigurera IoT Hub Device Provisioning Service med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabb starten beskriver hur du använder Azure CLI för att skapa en IoT-hubb och en IoT Hub Device Provisioning Service och för att länka samman de två tjänsterna. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Både IoT-hubben och etablerings tjänsten som du skapar i den här snabb starten kommer att vara offentligt identifierade som DNS-slutpunkter. Undvik känslig information om du vill ändra de namn som används för dessa resurser.
>

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]


## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *my-sample-resource-group* på platsen *usavästra*.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> I exemplet skapas en resursgrupp på platsen USA, västra. Du kan visa en lista över tillgängliga platser genom att köra kommandot `az account list-locations -o table`.
>
>

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Skapa en IoT-hubb med kommandot [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create).

I följande exempel skapas en IoT-hubb med namnet *my-sample-hub* på platsen *usavästra*. Ett IoT Hub-namn måste vara globalt unikt i Azure, så du kanske vill lägga till ett unikt prefix eller suffix till exempel namnet, eller välja ett nytt namn helt och hållet. Kontrol lera att namnet följer rätt namngivnings konventioner för en IoT-hubb: det ska vara 3-50 tecken långt och får bara innehålla alfanumeriska tecken i versaler eller gemener (-). 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Skapa en enhets etablerings tjänst

Skapa en enhets etablerings tjänst med kommandot [AZ IoT DPS Create](/cli/azure/iot/dps#az-iot-dps-create) . 

I följande exempel skapas en etablerings tjänst med namnet *My-Sample-DPS* på platsen för *västra* USA. Du måste också välja ett globalt unikt namn för etablerings tjänsten. Se till att det följer rätt namngivnings konventioner för en IoT Hub Device Provisioning Service: det ska vara 3-64 tecken långt och får bara innehålla alfanumeriska tecken eller bindestreck ("-") med versaler eller gemener.

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> I exemplet skapas en etableringstjänst på platsen USA, västra. Du kan se en lista med tillgängliga platser genom att köra kommandot `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table`, eller genom att gå till sidan [Azure-status](https://azure.microsoft.com/status/) och söka efter ”enhetsetableringstjänst”. I kommandon kan platser anges antingen i ett ord eller med flera ord. exempel: väst, västra USA, västra USA osv. Värdet är inte Skift läges känsligt. Om du använder flera ord för att ange platsen skriver du värdet inom citattecken; till exempel `--location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>Hämta anslutningssträngen för IoT-hubben

Du behöver IoT-hubbens anslutningssträng för att kunna länka den till enhetsetableringstjänsten. Använd kommandot [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string) för att hämta anslutningssträngen. Använd sedan dess utdata för att ställa in en variabel som ska användas när du länkar de två resurserna. 

I följande exempel anges *hubConnectionString* -variabeln till värdet för anslutnings strängen för den primära nyckeln i hubbens *iothubowner* -princip ( `--policy-name` parametern kan användas för att ange en annan princip). Ta del av *mitt-sampel-hubb* för det unika IoT Hub-namnet som du valde tidigare. Kommandot använder Azure CLI-[frågan](/cli/azure/query-azure-cli) och [utdata](/cli/azure/format-output-azure-cli#tsv-output-format)alternativ till att extrahera anslutningssträngen från kommandots utdata.

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

Du kan använda kommandot `echo` om du vill se anslutningssträngen.

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> Dessa två kommandon är giltiga för en värd som körs under Bash.
> 
> Om du använder ett lokalt Windows-/CMD-gränssnitt eller en PowerShell-värd ändrar du kommandona så att de använder rätt syntax för miljön.
>
> Om du använder Azure Cloud Shell kontrollerar du att List rutan miljö på vänster sida av Shell-fönstret säger **bash**.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>Länka IoT-hubben och etableringstjänsten

Länka IoT-hubben och etableringstjänsten med kommandot [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az-iot-dps-linked-hub-create). 

I följande exempel länkas en IoT-hubb med namnet *My-Sample-Hub* på platsen för *västkusten* och en enhets etablerings tjänst med namnet *My-Sample-DPS*. Ta reda på dessa namn för de unika IoT Hub-och Device Provisioning-tjänstens namn som du valde tidigare. Kommandot använder anslutnings strängen för din IoT-hubb som lagrades i *hubConnectionString* -variabeln i föregående steg.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

Det kan ta några minuter att slutföra kommandot.

## <a name="verify-the-provisioning-service"></a>Kontrollera etableringstjänsten

Hämta information om etableringstjänsten med kommandot [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show).

I följande exempel hämtas information om en etableringstjänst med namnet *my-sample-dps*. Ta reda på det här namnet för din egen enhets etablerings tjänst namn.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
Den länkade IoT-hubben visas i samlingen *properties.iotHubs*.

![Verifiera etablerings tjänsten](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstarterna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta att arbeta med efterföljande snabbstarter eller med självstudierna ska du inte rensa resurserna som skapas i denna snabbstart. Om du inte tänker fortsätta kan du använda följande kommandon för att ta bort etableringstjänsten, IoT-hubben eller resursgruppen och alla dess resurser. Ersätt namnen på resurserna som skrivits nedan med namnen på dina egna resurser.

Ta bort etableringstjänsten genom att köra kommandot [az iot dps delete](/cli/azure/iot/dps#az-iot-dps-delete):

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Ta bort IoT-hubben genom att köra kommandot [az iot hub delete](/cli/azure/iot/hub#az-iot-hub-delete):

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Ta bort en resursgrupp och alla dess resurser genom att köra kommandot [az group delete](/cli/azure/group#az-group-delete):

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat en IoT-hubb och en enhets etablerings tjänst instans och länkat de två resurserna. Om du vill lära dig hur du använder den här installationen för att etablera en simulerad enhet fortsätter du till snabb starten för att skapa en simulerad enhet.

> [!div class="nextstepaction"]
> [Snabb start för att skapa en simulerad enhet](./quick-create-simulated-device.md)
