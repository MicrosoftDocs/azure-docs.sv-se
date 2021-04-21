---
title: Snabbstart – Konfigurera en Azure IoT Hub Device Provisioning Service med Azure CLI
description: Snabbstart – Konfigurera Azure IoT Hub Device Provisioning Service (DPS) med Azure CLI
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 3d52a83c8c0920c4d85aa5b4b6b89fd8d36e5fea
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774979"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Snabbstart: Konfigurera IoT Hub Device Provisioning Service med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstarten beskriver hur du använder Azure CLI för att skapa en IoT-hubb och en IoT Hub Device Provisioning Service och för att länka ihop de två tjänsterna. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Både IoT-hubben och etableringstjänsten som du skapar i den här snabbstarten kan upptäckas offentligt som DNS-slutpunkter. Undvik känslig information om du vill ändra de namn som används för dessa resurser.
>

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]


## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az_group_create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *my-sample-resource-group* på platsen *usavästra*.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> I exemplet skapas en resursgrupp på platsen USA, västra. Du kan visa en lista över tillgängliga platser genom att köra kommandot `az account list-locations -o table`.
>
>

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Skapa en IoT-hubb med kommandot [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create).

I följande exempel skapas en IoT-hubb med namnet *my-sample-hub* på platsen *usavästra*. Ett IoT-hubbnamn måste vara globalt unikt i Azure, så du kanske vill lägga till ett unikt prefix eller suffix i exempelnamnet eller välja ett nytt namn helt och hållet. Kontrollera att ditt namn följer lämpliga namngivningskonventioner för en IoT-hubb: det ska vara mellan 3 och 50 tecken långt och får endast innehålla alfanumeriska versaler eller gemener eller bindestreck (-). 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Skapa en enhetsetableringstjänst

Skapa en device provisioning-tjänst med [kommandot az iot dps create.](/cli/azure/iot/dps#az_iot_dps_create) 

I följande exempel skapas en etableringstjänst med *namnet my-sample-dps* på *platsen westus.* Du måste också välja ett globalt unikt namn för din egen etableringstjänst. Se till att den följer lämpliga namngivningskonventioner för en IoT Hub Device Provisioning Service: den ska vara 3–64 tecken lång och får bara innehålla alfanumeriska versaler eller gemener eller bindestreck ('-').

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> I exemplet skapas en etableringstjänst på platsen USA, västra. Du kan se en lista med tillgängliga platser genom att köra kommandot `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table`, eller genom att gå till sidan [Azure-status](https://azure.microsoft.com/status/) och söka efter ”enhetsetableringstjänst”. I kommandon kan platser anges antingen i ett eller flera ord. exempel: USA, västra; USA, västra; USA, västra osv. Värdet är inte ärendekänsligt. Om du använder flera ord för att ange platsen skriver du värdet inom citattecken; till exempel `--location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>Hämta anslutningssträngen för IoT-hubben

Du behöver IoT-hubbens anslutningssträng för att kunna länka den till enhetsetableringstjänsten. Använd kommandot [az iot hub show-connection-string](/cli/azure/iot/hub#az_iot_hub_show_connection_string) för att hämta anslutningssträngen. Använd sedan dess utdata för att ställa in en variabel som ska användas när du länkar de två resurserna. 

I följande exempel anges *variabeln hubConnectionString* till värdet för anslutningssträngen för den primära nyckeln för hubbens *iothubowner-princip* (parametern kan användas för att ange en `--policy-name` annan princip). Byta ut *my-sample-hub mot* det unika IoT-hubbnamn som du valde tidigare. Kommandot använder Azure CLI-[frågan](/cli/azure/query-azure-cli) och [utdata](/cli/azure/format-output-azure-cli#tsv-output-format)alternativ till att extrahera anslutningssträngen från kommandots utdata.

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
> Om du använder ett lokalt Windows/CMD-gränssnitt eller en PowerShell-värd ändrar du kommandona så att de använder rätt syntax för den miljön.
>
> Om du använder Azure Cloud Shell kontrollerar du att miljö-listrutan till vänster i gränssnittsfönstret säger **Bash**.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>Länka IoT-hubben och etableringstjänsten

Länka IoT-hubben och etableringstjänsten med kommandot [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az_iot_dps_linked_hub_create). 

I följande exempel länkar en IoT-hubb med  namnet *my-sample-hub* på platsen USA, västra och en Enhetsetableringstjänst med namnet *my-sample-dps*. Byta ut dessa namn mot de unika namnen på IoT-hubben och enhetsetableringstjänsten som du valde tidigare. Kommandot använder anslutningssträngen för din IoT-hubb som lagrades i *variabeln hubConnectionString* i föregående steg.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

Det kan ta några minuter att slutföra kommandot.

## <a name="verify-the-provisioning-service"></a>Kontrollera etableringstjänsten

Hämta information om etableringstjänsten med kommandot [az iot dps show](/cli/azure/iot/dps#az_iot_dps_show).

I följande exempel hämtas information om en etableringstjänst med namnet *my-sample-dps*. Byta ut det här namnet mot ditt eget namn på enhetsetableringstjänsten.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
Den länkade IoT-hubben visas i samlingen *properties.iotHubs*.

![Verifiera etableringstjänsten](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstarterna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta att arbeta med efterföljande snabbstarter eller med självstudierna ska du inte rensa resurserna som skapas i denna snabbstart. Om du inte tänker fortsätta kan du använda följande kommandon för att ta bort etableringstjänsten, IoT-hubben eller resursgruppen och alla dess resurser. Ersätt namnen på resurserna som skrivits nedan med namnen på dina egna resurser.

Ta bort etableringstjänsten genom att köra kommandot [az iot dps delete](/cli/azure/iot/dps#az_iot_dps_delete):

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Ta bort IoT-hubben genom att köra kommandot [az iot hub delete](/cli/azure/iot/hub#az_iot_hub_delete):

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Ta bort en resursgrupp och alla dess resurser genom att köra kommandot [az group delete](/cli/azure/group#az_group_delete):

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat en IoT-hubb och en instans av enhetsetableringstjänsten och länkat de två resurserna. Om du vill lära dig hur du använder den här konfigurationen för att etablera en simulerad enhet fortsätter du till snabbstarten för att skapa en simulerad enhet.

> [!div class="nextstepaction"]
> [Snabbstart för att skapa en simulerad enhet](./quick-create-simulated-device.md)
