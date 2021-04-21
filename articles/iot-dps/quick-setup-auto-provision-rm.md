---
title: Snabbstart – Skapa en Azure IoT Hub Device Provisioning Service (DPS) med hjälp Azure Resource Manager (ARM-mall)
description: Azure-snabbstart – Lär dig hur du skapar en Azure IoT Hub Device Provisioning Service (DPS) med hjälp av en Azure Resource Manager mall (ARM-mall).
author: wesmc7777
ms.author: wesmc
ms.date: 01/27/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, subject-armqs, devx-track-azurecli
ms.openlocfilehash: bcb37c624f53d961020de022569a621ca1dfaba1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789005"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-dps-with-an-arm-template"></a>Snabbstart: Konfigurera IoT Hub Device Provisioning Service (DPS) med en ARM-mall

Du kan använda en [Azure Resource Manager](../azure-resource-manager/management/overview.md) (ARM-mall) för att programmatiskt konfigurera de Azure-molnresurser som krävs för att etablera dina enheter. De här stegen visar hur du skapar en IoT-hubb och en ny IoT Hub Device Provisioning Service med en ARM-mall. Iot Hub är också länkad till DPS-resursen med hjälp av mallen. Den här länkningen gör att DPS-resursen kan tilldela enheter till hubben baserat på allokeringsprinciper som du konfigurerar.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Den här snabbstarten använder [Azure Portal](../azure-resource-manager/templates/deploy-portal.md)och [Azure CLI](../azure-resource-manager/templates/deploy-cli.md) för att utföra de programmeringssteg som krävs för att skapa en resursgrupp och distribuera mallen, men du kan enkelt använda [PowerShell](../azure-resource-manager/templates/deploy-powershell.md), .NET, Ruby eller andra programmeringsspråk för att utföra dessa steg och distribuera mallen. 

Om din miljö uppfyller kraven och du redan är bekant med ARM-mallar öppnas mallen för distribution i Azure Portal om du väljer knappen Distribuera till **Azure** nedan.

[![Distribuera till Azure i översikt](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-iothub-device-provisioning%2fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]


## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-iothub-device-provisioning/).

> [!NOTE]
> Det finns för närvarande inget stöd för ARM-mallar för att skapa registreringar med nya DPS-resurser. Det här är en vanlig och förstådd begäran som övervägs för implementering.

:::code language="json" source="~/quickstart-templates/101-iothub-device-provisioning/azuredeploy.json":::

Två Azure-resurser definieras i mallen ovan:

* [**Microsoft.Devices/iothubs:**](/azure/templates/microsoft.devices/iothubs)Skapar en ny Azure IoT Hub.
* [**Microsoft.Devices/provisioningservices:**](/azure/templates/microsoft.devices/provisioningservices)Skapar en ny Azure IoT Hub Device Provisioning-tjänsten med den nya IoT Hub redan länkad till den.


## <a name="deploy-the-template"></a>Distribuera mallen

#### <a name="deploy-with-the-portal"></a>Distribuera med portalen

1. Välj följande bild för att logga in på Azure och öppna mallen för distribution. Mallen skapar en ny Iot Hub- och DPS-resurs. Hubben länkas i DPS-resursen.

    [![Steg för att distribuera till Azure i portalen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-iothub-device-provisioning%2fazuredeploy.json)

2. Välj eller ange följande värden och klicka på **Granska + skapa.**

    ![Distributionsparametrar för ARM-mallar i portalen](./media/quick-setup-auto-provision-rm/arm-template-deployment-parameters-portal.png)    

    Om det inte anges nedan använder du standardvärdet för att skapa IoT Hub och DPS-resursen.

    | Fält | Beskrivning |
    | :---- | :---------- |
    | **Prenumeration** | Välj din Azure-prenumeration. |
    | **Resursgrupp** | Klicka **på Skapa** ny, ange ett unikt namn för resursgruppen och klicka sedan på **OK.** |
    | **Region** | Välj en region för dina resurser. Till exempel USA, **östra.** |
    | **Namn på IoT-hubb** | Ange ett namn för IoT Hub som måste vara globalt  unikt inom .azure-devices.net-namnområdet. Du behöver hubbnamnet i nästa avsnitt när du validerar distributionen. |
    | **Etableringstjänstnamn** | Ange ett namn för den nya DPS-resursen (Device Provisioning Service). Namnet måste vara globalt unikt inom *.azure-devices-provisioning.net-namnområdet.* Du behöver DPS-namnet i nästa avsnitt när du verifierar distributionen. |
    
3. Läs villkoren på nästa skärm. Om du godkänner alla villkor klickar du på **Skapa.** 

    Distributionen tar en stund att slutföra. 

    Förutom de här Azure Portal kan du även använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributionsmetoder finns i [Distribuera mallar.](../azure-resource-manager/templates/deploy-powershell.md)


#### <a name="deploy-with-the-azure-cli"></a>Distribuera med Azure CLI

Användning av Azure CLI kräver version 2.6 eller senare. Om du kör Azure CLI lokalt kontrollerar du din version genom att köra: `az --version`

Logga in på ditt Azure-konto och välj din prenumeration.

1. Om du kör Azure CLI lokalt i stället för att köra det i portalen måste du logga in. Logga in i kommandotolken genom att köra [inloggningskommandot](/cli/azure/get-started-with-az-cli2):
    
    ```azurecli
    az login
    ```

    Följ instruktionerna för att autentisera med hjälp av koden och logga in på ditt Azure-konto via en webbläsare.

2. Om du har flera Azure-prenumerationer får du åtkomst till alla Azure-konton som är associerade med dina autentiseringsuppgifter när du loggar in på Azure. Använd följande [-kommando för att lista Azure-konton](/cli/azure/account) som du kan använda:
    
    ```azurecli
    az account list -o table
    ```

    Använd följande kommando för att välja den prenumeration som du vill använda för att köra kommandona för att skapa dina IoT Hub- och DPS-resurser. Du kan antingen använda prenumerationsnamnet eller ID:t från utdata från föregående kommando:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Kopiera och klistra in följande kommandon i CLI-prompten. Kör sedan kommandona genom att trycka på **RETUR.**
   
    > [!TIP]
    > Kommandona frågar efter en resursgruppsplats. Du kan visa en lista över tillgängliga platser genom att först köra kommandot :
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

4. Kommandona uppmanar dig att ange följande information. Ange varje värde och tryck på **RETUR.**

    | Parameter | Beskrivning |
    | :-------- | :---------- |
    | **Projektnamn** | Värdet för den här parametern används för att skapa en resursgrupp som ska innehålla alla resurser. Strängen `rg` läggs till i slutet av värdet för resursgruppens namn. |
    | **Plats** | Det här värdet är den region där alla resurser kommer att finnas. |
    | **iotHubName** | Ange ett namn för IoT Hub som måste vara globalt  unikt inom .azure-devices.net-namnområdet. Du behöver hubbnamnet i nästa avsnitt när du validerar distributionen. |
    | **provisioningServiceName** | Ange ett namn för den nya DPS-resursen (Device Provisioning Service). Namnet måste vara globalt unikt inom *.azure-devices-provisioning.net-namnområdet.* Du behöver DPS-namnet i nästa avsnitt när du verifierar distributionen. |

    AzureCLI används för att distribuera mallen. Förutom Azure CLI kan du även använda Azure PowerShell, Azure Portal och REST API. Mer information om andra distributionsmetoder finns i [Distribuera mallar.](../azure-resource-manager/templates/deploy-powershell.md)


## <a name="review-deployed-resources"></a>Granska distribuerade resurser

1. Kontrollera distributionen genom att köra följande kommando [för](/cli/azure/resource#az_resource_list) att lista resurser och leta efter den nya etableringstjänsten och IoT-hubben i utdata:

    ```azurecli
     az resource list -g "${projectName}rg"
    ```

2. Kontrollera att hubben redan är länkad till DPS-resursen genom att köra följande [kommando för DPS-tillägget show](/cli/azure/iot/dps#az_iot_dps_show).

    ```azurecli
     az iot dps show --name <Your provisioningServiceName>
    ```

    Observera de hubbar som är länkade på `iotHubs` medlemmen.


## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstarterna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta att arbeta med efterföljande snabbstarter eller med självstudierna ska du inte rensa resurserna som skapas i denna snabbstart. Om du inte planerar att fortsätta kan du använda Azure Portal eller Azure CLI för att ta bort resursgruppen och alla dess resurser.

Om du vill ta bort en resursgrupp och alla dess resurser från Azure Portal öppnar du bara resursgruppen och klickar på **Ta bort resursgrupp** och längst upp.

Så här tar du bort resursgruppen som distribuerats med Hjälp av Azure CLI:

```azurecli
az group delete --name "${projectName}rg"
```

Du kan också ta bort resursgrupper och enskilda resurser med hjälp av Azure Portal-, PowerShell- eller REST-API:er, samt med plattforms-SDK:er som har publicerats för Azure Resource Manager eller IoT Hub Device Provisioning Service.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat en IoT-hubb och en instans av enhetsetableringstjänsten och länkat de två resurserna. Om du vill lära dig hur du använder den här konfigurationen för att etablera en enhet fortsätter du till snabbstarten för att skapa en enhet.

> [!div class="nextstepaction"]
> [Snabbstart för att etablera en enhet](./quick-create-simulated-device-symm-key.md)
