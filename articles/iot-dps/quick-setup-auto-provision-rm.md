---
title: Snabb start – skapa en Azure-IoT Hub Device Provisioning Service (DPS) med Azure Resource Manager-mall (ARM-mall)
description: Azure snabb start – lär dig hur du skapar en Azure-IoT Hub Device Provisioning Service (DPS) med Azure Resource Manager-mall (ARM-mall).
author: wesmc7777
ms.author: wesmc
ms.date: 01/27/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, subject-armqs, devx-track-azurecli
ms.openlocfilehash: 505859075ce58c5db6873544123710a11135651a
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198633"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-dps-with-an-arm-template"></a>Snabb start: Konfigurera IoT Hub Device Provisioning Service (DPS) med en ARM-mall

Du kan använda en [Azure Resource Manager](../azure-resource-manager/management/overview.md) mall (arm-mall) för att program mässigt konfigurera de Azure-moln resurser som krävs för att kunna konfigurera dina enheter. De här stegen visar hur du skapar en IoT-hubb och en ny IoT Hub Device Provisioning Service med en ARM-mall. IoT Hub är också länkad till DPS-resursen med hjälp av mallen. Med den här länkningen kan DPS-resursen tilldela enheter till hubben baserat på de allokeringsregler som du konfigurerar.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

I den här snabb starten används [Azure Portal](../azure-resource-manager/templates/deploy-portal.md)och [Azure CLI](../azure-resource-manager/templates/deploy-cli.md) för att utföra de programmerings steg som krävs för att skapa en resurs grupp och distribuera mallen, men du kan enkelt använda [PowerShell](../azure-resource-manager/templates/deploy-powershell.md), .net, ruby eller andra programmeringsspråk för att utföra dessa steg och distribuera mallen. 

Om din miljö uppfyller förutsättningarna och du redan är bekant med att använda ARM-mallar kan du välja knappen **distribuera till Azure** nedan för att öppna mallen för distribution i Azure Portal.

[![Distribuera till Azure i Översikt](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-iothub-device-provisioning%2fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]


## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-iothub-device-provisioning/).

> [!NOTE]
> För närvarande finns det inget stöd för ARM-mallar för att skapa registreringar med nya DPS-resurser. Detta är en gemensam och tolkad begäran som beaktas för implementering.

:::code language="json" source="~/quickstart-templates/101-iothub-device-provisioning/azuredeploy.json":::

Två Azure-resurser definieras i mallen ovan:

* [**Microsoft. Devices/iothubs**](/azure/templates/microsoft.devices/iothubs): skapar en ny Azure-IoT Hub.
* [**Microsoft. Devices/provisioningservices**](/azure/templates/microsoft.devices/provisioningservices): skapar en ny Azure-IoT Hub Device Provisioning service med den nya IoT Hub som redan är länkad till den.


## <a name="deploy-the-template"></a>Distribuera mallen

#### <a name="deploy-with-the-portal"></a>Distribuera med portalen

1. Välj följande bild för att logga in på Azure och öppna mallen för distribution. Mallen skapar en ny IoT-hubb och DPS-resurs. Hubben kommer att länkas i DPS-resursen.

    [![Distribuera till Azure i Portal steg](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-iothub-device-provisioning%2fazuredeploy.json)

2. Välj eller ange följande värden och klicka på **Granska + skapa**.

    ![Distributions parametrar för ARM-mall på portalen](./media/quick-setup-auto-provision-rm/arm-template-deployment-parameters-portal.png)    

    Om den inte anges nedan använder du standardvärdet för att skapa IoT-hubben och DPS-resursen.

    | Fält | Beskrivning |
    | :---- | :---------- |
    | **Prenumeration** | Välj din Azure-prenumeration. |
    | **Resursgrupp** | Klicka på **Skapa nytt** och ange ett unikt namn för resurs gruppen och klicka sedan på **OK**. |
    | **Region** | Välj en region för dina resurser. Till exempel **USA, östra**. |
    | **Namn på IoT Hub** | Ange ett namn för IoT Hub som måste vara globalt unikt inom namn området *. Azure-Devices.net* . Du behöver Hub-namnet i nästa avsnitt när du validerar distributionen. |
    | **Namn på etablerings tjänst** | Ange ett namn för den nya DPS-resursen (Device Provisioning service). Namnet måste vara globalt unikt inom namn området *. Azure-Devices-Provisioning.net* . Du behöver DPS-namnet i nästa avsnitt när du validerar distributionen. |
    
3. Läs villkoren på nästa skärm. Om du godkänner alla villkor klickar du på **skapa**. 

    Det tar en stund att slutföra distributionen. 

    Förutom Azure Portal kan du också använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../azure-resource-manager/templates/deploy-powershell.md).


#### <a name="deploy-with-the-azure-cli"></a>Distribuera med Azure CLI

Om du använder Azure CLI krävs version 2,6 eller senare. Om du kör Azure CLI lokalt kontrollerar du din version genom att köra: `az --version`

Logga in på ditt Azure-konto och välj din prenumeration.

1. Om du kör Azure CLI lokalt i stället för att köra det i portalen måste du logga in. Logga in i kommando tolken genom att köra [inloggnings kommandot](/cli/azure/get-started-with-az-cli2):
    
    ```azurecli
    az login
    ```

    Följ instruktionerna för att autentisera med hjälp av koden och logga in på ditt Azure-konto via en webbläsare.

2. Om du har flera Azure-prenumerationer får du åtkomst till alla Azure-konton som är associerade med dina autentiseringsuppgifter när du loggar in på Azure. Använd följande [-kommando för att lista Azure-konton](/cli/azure/account) som du kan använda:
    
    ```azurecli
    az account list -o table
    ```

    Använd följande kommando för att välja den prenumeration som du vill använda för att köra kommandona för att skapa din IoT-hubb och dina DPS-resurser. Du kan antingen använda prenumerationsnamnet eller ID:t från utdata från föregående kommando:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Kopiera och klistra in följande kommandon i CLI-prompten. Kör sedan kommandona genom att trycka på **RETUR**.
   
    > [!TIP]
    > Kommandona kommer att uppmanas att ange en resurs grupps plats. Du kan visa en lista över tillgängliga platser genom att först köra kommandot:
    >
    > `az account list-locations -o table`
    >
    >
    
    ```azurecli-interactive
    read -p "Enter a project name that is used for generating resource names:" projectName &&
    read -p "Enter the location (i.e. centralus):" location &&
    templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-device-provisioning/azuredeploy.json" &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
    echo "Press [ENTER] to continue ..." &&
    read
    ```

4. Kommandona kommer att uppmana dig att ange följande information. Ange varje värde och tryck på **RETUR**.

    | Parameter | Beskrivning |
    | :-------- | :---------- |
    | **Projektnamn** | Värdet för den här parametern används för att skapa en resurs grupp som innehåller alla resurser. Strängen `rg` läggs till i slutet av värdet för resurs gruppens namn. |
    | **sökvägen** | Det här värdet är den region där alla resurser kommer att finnas. |
    | **iotHubName** | Ange ett namn för IoT Hub som måste vara globalt unikt inom namn området *. Azure-Devices.net* . Du behöver Hub-namnet i nästa avsnitt när du validerar distributionen. |
    | **provisioningServiceName** | Ange ett namn för den nya DPS-resursen (Device Provisioning service). Namnet måste vara globalt unikt inom namn området *. Azure-Devices-Provisioning.net* . Du behöver DPS-namnet i nästa avsnitt när du validerar distributionen. |

    AzureCLI används för att distribuera mallen. Förutom Azure CLI kan du också använda Azure PowerShell, Azure Portal och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../azure-resource-manager/templates/deploy-powershell.md).


## <a name="review-deployed-resources"></a>Granska distribuerade resurser

1. Verifiera distributionen genom att köra följande [kommando för att visa en lista över resurser](/cli/azure/resource#az-resource-list) och leta efter den nya etablerings tjänsten och IoT Hub i utdata:

    ```azurecli
     az resource list -g "${projectName}rg"
    ```

2. Kontrol lera att navet redan är länkat till DPS-resursen genom att köra följande [kommando för DPS-tillägget show](/cli/azure/iot/dps#az_iot_dps_show).

    ```azurecli
     az iot dps show --name <Your provisioningServiceName>
    ```

    Lägg märke till de hubbar som är länkade till `iotHubs` medlemmen.


## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstarterna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta att arbeta med efterföljande snabbstarter eller med självstudierna ska du inte rensa resurserna som skapas i denna snabbstart. Om du inte planerar att fortsätta kan du använda Azure Portal eller Azure CLI för att ta bort resurs gruppen och alla dess resurser.

Om du vill ta bort en resurs grupp och alla dess resurser från Azure Portal öppnar du bara resurs gruppen och klickar på **ta bort resurs grupp** och överst.

Ta bort resurs gruppen som distribueras med Azure CLI:

```azurecli
az group delete --name "${projectName}rg"
```

Du kan också ta bort resurs grupper och enskilda resurser med hjälp av Azure Portal, PowerShell eller REST-API: er, och med de plattforms-SDK: er som stöds publicerade för Azure Resource Manager eller IoT Hub Device Provisioning Service.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat en IoT-hubb och en enhets etablerings tjänst instans och länkat de två resurserna. Om du vill lära dig hur du använder den här installationen för att etablera en enhet fortsätter du till snabb starten för att skapa en enhet.

> [!div class="nextstepaction"]
> [Snabb start för att etablera en enhet](./quick-create-simulated-device-symm-key.md)

