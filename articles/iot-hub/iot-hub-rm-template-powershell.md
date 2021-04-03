---
title: Skapa en Azure-IoT Hub med hjälp av en mall (PowerShell) | Microsoft Docs
description: Hur du använder en Azure Resource Manager mall för att skapa en IoT Hub med Azure PowerShell.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 1fedadfa7e5b4ec3d7de30d0ad3ef1b1bfa0e0ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92144389"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Skapa en IoT-hubb med Azure Resource Manager-mall (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Lär dig hur du använder en Azure Resource Manager mall för att skapa en IoT Hub och en konsument grupp. Resource Manager-mallar är JSON-filer som definierar de resurser du behöver för att distribuera lösningen. Mer information om hur du utvecklar Resource Manager-mallar finns i [Azure Resource Manager dokumentation](../azure-resource-manager/index.yml).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Resource Manager-mallen som används i den här snabb starten är från [Azure snabb starts-mallar](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/). Här är en kopia av mallen:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

Mallen skapar en Azure IoT Hub med tre slut punkter (eventhub, Cloud-to-Device och Messaging) och en konsument grupp. Fler mall exempel finns i [mallar för Azure snabb start](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular). Du hittar mall-schemat för IoT Hub  [här](/azure/templates/microsoft.devices/iothub-allversions).

Det finns flera metoder för att distribuera en mall.  Du använder Azure PowerShell i den här självstudien.

Om du vill köra PowerShell-skriptet väljer du **prova** att öppna Azure Cloud Shell. Om du vill klistra in skriptet högerklickar du på gränssnittet och väljer Klistra in:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$iotHubName = Read-Host -Prompt "Enter the IoT Hub name"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-with-consumergroup-create/azuredeploy.json" `
    -iotHubName $iotHubName
```

Som du kan se från PowerShell-skriptet är mallen som används från Azures snabb starts mallar. Om du vill använda din egen måste du först överföra mallfilen till Cloud Shell och sedan använda `-TemplateFile` växeln för att ange fil namnet.  Ett exempel finns i [distribuera mallen](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template).

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat en IoT-hubb med hjälp av en Azure Resource Manager-mall kanske du vill utforska ytterligare:

* Läs om funktionerna i [IoT Hub Resource provider REST API][lnk-rest-api].
* Läs [Azure Resource Manager översikt][lnk-azure-rm-overview] för att lära dig mer om funktionerna i Azure Resource Manager.
* För JSON-syntax och egenskaper som ska användas i mallar, se [resurs typer för Microsoft. Devices](/azure/templates/microsoft.devices/iothub-allversions).

Mer information om hur du utvecklar för IoT Hub finns i följande artiklar:

* [Introduktion till C SDK][lnk-c-sdk]
* [SDK:er för Azure IoT][lnk-sdks]

För att ytterligare utforska funktionerna i IoT Hub, se:

* [Distribuera AI till gränsenheter med Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: /rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-powershell-arm]: ../azure-resource-manager/management/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/quickstart-linux.md