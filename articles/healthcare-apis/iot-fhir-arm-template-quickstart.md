---
title: 'Snabb start: Distribuera Azure IoT Connector för FHIR (för hands version) med en ARM-mall'
description: I den här snabb starten lär du dig hur du distribuerar Azure IoT Connector för FHIR (för hands version) med hjälp av en Azure Resource Manager-mall (ARM-mall).
author: rbhaiya
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.author: rabhaiya
ms.date: 01/21/2021
ms.openlocfilehash: b45c63031596c96886a96a21bf693803088cc006
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98745233"
---
# <a name="quickstart-use-an-azure-resource-manager-arm-template-to-deploy-azure-iot-connector-for-fhir-preview"></a>Snabb start: Använd en Azure Resource Manager ARM-mall (ARM) för att distribuera Azure IoT Connector för FHIR (för hands version)

I den här snabb starten får du lära dig hur du använder en Azure Resource Manager mall (ARM-mall) för att distribuera Azure IoT Connector för snabba hälso-och sjukvårds resurser (FHIR&#174;) *, en funktion i Azure API för FHIR. För att distribuera en fungerande instans av Azure IoT Connector för FHIR, distribuerar den här mallen även en överordnad Azure API för FHIR-tjänsten och ett Azure IoT Central-program som exporterar telemetri från en enhets Simulator till Azure IoT Connector för FHIR. Du kan köra ARM-mallen för att distribuera Azure IoT Connector för FHIR via Azure Portal, PowerShell eller CLI.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas i Azure Portal när du loggar in.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera till Azure en Azure IoT-anslutning för FHIR med en ARM-mall i Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/).
* Om du vill köra koden lokalt [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[CLI](#tab/CLI)

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/).
* Om du vill köra koden lokalt:
    * Ett bash-gränssnitt (till exempel git bash, som ingår i [git för Windows](https://gitforwindows.org)).
    * [Azure CLI](/cli/azure/install-azure-cli).

---

## <a name="review-the-template"></a>Granska mallen

[Mallen](https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json) definierar följande Azure-resurser:

* **Microsoft. HealthcareApis/Services**
* **Microsoft. HealthcareApis/Services/iomtconnectors**
* **Microsoft. IoTCentral/IoTApps**

## <a name="deploy-the-template"></a>Distribuera mallen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Välj följande länk för att distribuera Azure IoT-anslutningsprogrammet för FHIR med ARM-mallen i Azure Portal:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera till Azure en Azure IoT-anslutning för FHIR-tjänsten med ARM-mallen i Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

På sidan **Distribuera Azure API för FHIR** :

1. Om du vill kan du ändra **prenumerationen** från standardvärdet till en annan prenumeration.

2. För **resurs grupp** väljer du **Skapa ny**, anger ett namn för den nya resurs gruppen och väljer **OK**.

3. Om du har skapat en ny resurs grupp väljer du en **region** för resurs gruppen.

4. Ange ett namn för din nya Azure API för FHIR-instans i **FHIR-tjänstens namn**.

5. Välj **plats** för ditt Azure API för FHIR. Platsen kan vara samma som eller en annan från regionen för resurs gruppen.

6. Ange ett namn för din Azure IoT-anslutning för FHIR-instansen i **IoT Connector-namnet**.

7. Ange ett namn för en anslutning som skapats i Azure IoT Connector för FHIR i **anslutnings namn**. Den här anslutningen används av Azure IoT Central-programmet för att push-överföra enhets telemetri till Azure IoT Connector för FHIR.

8. Ange ett namn för ditt nya Azure IoT Central-program i **IoT Central-namn**. Det här programmet använder en mall för *kontinuerlig övervakning av patienter* för att simulera en enhet.

9. Välj platsen för IoT Central programmet från **IoT central plats** . 

10. Välj **Granska + skapa**.

11. Läs igenom villkoren och välj sedan **skapa**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Om du vill köra PowerShell-skripten lokalt börjar `Connect-AzAccount` du med att ange dina Azure-autentiseringsuppgifter.

Om `Microsoft.HealthcareApis` resurs leverantören inte redan har registrerats för din prenumeration kan du registrera den med följande interaktiva kod. Om du vill köra koden i Azure Cloud Shell väljer du **testa den** i det övre hörnet av ett kodblock.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

Om `Microsoft.IoTCentral` resurs leverantören inte redan har registrerats för din prenumeration kan du registrera den med följande interaktiva kod. Om du vill köra koden i Azure Cloud Shell väljer du **testa den** i det övre hörnet av ett kodblock.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.IoTCentral
```

Använd följande kod för att distribuera Azure IoT-anslutaren för FHIR-tjänsten med ARM-mallen.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$fhirServiceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$location = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$iotConnectorName = Read-Host -Prompt "Enter a name for the new Azure IoT Connector for FHIR"
$connectionName = Read-Host -Prompt "Enter a name for the connection with Azure IoT Connector for FHIR"
$iotCentralName = Read-Host -Prompt "Enter a name for the new Azure IoT Central Application"
$iotCentralLocation = Read-Host -Prompt "Enter a location for the new Azure IoT Central Application"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure IoT Connector for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json `
    -fhirServiceName $fhirServiceName `
    -location $location
    -iotConnectorName $iotConnectorName
    -connectionName $connectionName
    -iotCentralName $iotCentralName
    -iotCentralLocation $iotCentralLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Om `Microsoft.HealthcareApis` resurs leverantören inte redan har registrerats för din prenumeration kan du registrera den med följande interaktiva kod. Om du vill köra koden i Azure Cloud Shell väljer du **testa den** i det övre hörnet av ett kodblock.

```azurecli-interactive
az extension add --name healthcareapis
```

Om `Microsoft.IoTCentral` resurs leverantören inte redan har registrerats för din prenumeration kan du registrera den med följande interaktiva kod.

```azurecli-interactive
az extension add --name azure-iot
```

Använd följande kod för att distribuera Azure IoT-anslutaren för FHIR-tjänsten med ARM-mallen.

```azurecli-interactive
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter a name for the new Azure API for FHIR service: " fhirServiceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " location &&
read -p "Enter a name for the new Azure IoT Connector for FHIR: " iotConnectorName &&
read -p "Enter a name for the connection with Azure IoT Connector for FHIR: " connectionName &&
read -p "Enter a name for the new Azure IoT Central Application: " iotCentralName &&
read -p "Enter a location for the new Azure IoT Central Application: " iotCentralLocation &&

params='fhirServiceName='$fhirServiceName' location='$location' iotConnectorName='$iotConnectorName' connectionName='$connectionName' iotCentralName='$iotCentralName' iotCentralLocation='$iotCentralLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure IoT Connector for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> Det tar några minuter att slutföra distributionen. Anteckna namnen på Azure API för FHIR-tjänsten, Azure IoT Central-programmet och resurs gruppen som du använder för att granska de distribuerade resurserna senare.

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

# <a name="portal"></a>[Portal](#tab/azure-portal)

Följ dessa steg om du vill se en översikt över din nya Azure API for FHIR-tjänst:

1. I [Azure Portal](https://portal.azure.com)söker du efter och väljer **Azure API för FHIR**.

2. I listan FHIR väljer du den nya tjänsten. **Översikts** sidan för den nya Azure API för FHIR-tjänsten visas.

3. Om du vill kontrol lera att det nya FHIR API-kontot har tillhandahållits väljer du länken bredvid **FHIR för metadata** för att hämta API-funktionen FHIR API. Länken har formatet `https://<service-name>.azurehealthcareapis.com/metadata` . Om kontot har tillhandahållits visas en stor JSON-fil.

4. Om du vill kontrol lera att den nya Azure IoT-anslutningen för FHIR är etablerad väljer du **IoT-anslutningen (förhands granskning)** från den vänstra navigerings menyn för att öppna sidan **IoT-anslutningar** . Sidan måste visa den etablerade Azure IoT-anslutningen för FHIR med *statusvärdet* " *online*", *Connections* -värde *som 1* och både *enhets mappning* och *FHIR mappning* Visa *lyckad* ikon.

5. I [Azure Portal](https://portal.azure.com)söker du efter och väljer **IoT Central program**.

6. I listan över IoT Central program väljer du den nya tjänsten. **Översikts** sidan för det nya IoT Central programmet visas.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Kör följande interaktiva kod om du vill visa information om din Azure API for FHIR-tjänst. Du måste ange namnet på den nya FHIR-tjänsten och resurs gruppen.

```azurepowershell-interactive
$fhirServiceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new FHIR service has been provisioned"

$requestUri="https://" + $fhirServiceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

> [!NOTE]
> Azure IoT Connector för FHIR tillhandahåller inte PowerShell-kommandon just nu. Om du vill verifiera att din Azure IoT-anslutning för FHIR har etablerats korrekt, använder du verifierings processen som finns på **Portal** -fliken.

Kör följande interaktiva kod om du vill visa information om ditt Azure IoT Central-program. Du måste ange namnet på det nya IoT Central programmet och resurs gruppen.

```azurepowershell-interactive
$iotCentralName = Read-Host -Prompt "Enter the name of your Azure IoT Central application"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName" -Verbose
Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName
```

# <a name="cli"></a>[CLI](#tab/CLI)

Kör följande interaktiva kod om du vill visa information om din Azure API for FHIR-tjänst. Du måste ange namnet på den nya FHIR-tjänsten och resurs gruppen.

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " fhirServiceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$fhirServiceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
read -p "Press [ENTER] to continue: "
```

> [!NOTE]
> Azure IoT Connector för FHIR tillhandahåller inte CLI-kommandon för tillfället. Om du vill verifiera att din Azure IoT-anslutning för FHIR har etablerats korrekt, använder du verifierings processen som finns på **Portal** -fliken.

Kör följande interaktiva kod om du vill visa information om ditt Azure IoT Central-program. Du måste ange namnet på det nya IoT Central programmet och resurs gruppen.

```azurecli-interactive
read -p "Enter the name of your IoT Central application: " iotCentralName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az iot central app show -g $resourceGroupName -n $iotCentralName" &&
az iot central app show -g $resourceGroupName -n $iotCentralName &&
```

---

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>Anslut dina IoT-data med Azure IoT Connector för FHIR (för hands version)
> [!IMPORTANT]
> Den enhets mappnings mall som anges i den här guiden är utformad för att fungera med data export (bakåtkompatibelt) i IoT Central.

IoT Central-programmet tillhandahåller för närvarande inte ARM-mall eller PowerShell-och CLI-kommandon för att ställa in data export. Följ anvisningarna nedan med hjälp av Azure Portal.  

När du har distribuerat ditt IoT Central-program börjar du skapa telemetri genom att använda dina två färdiga simulerade enheter. I den här självstudien tar vi in Telemetrin från *smarta viktigare korrigerings* Simulator i FHIR via Azure IoT-anslutningen för FHIR. Om du vill exportera dina IoT-data till Azure IoT-anslutningsprogrammet för FHIR vill vi [Konfigurera en data export (bakåtkompatibelt) i IoT Central](../iot-central/core/howto-export-data-legacy.md). På sidan data export (bakåtkompatibelt):
- Välj *Azure-Event Hubs* som export mål.
- Välj *Använd ett värde för anslutnings sträng* för fältet **Event Hubs namn område** .
- Tillhandahåll Azure IoT Connector för FHIR anslutnings sträng som hämtades i föregående steg för fältet **anslutnings sträng** .
- Behåll **telemetri** - *alternativet* för **data att exportera** fält.

---

## <a name="view-device-data-in-azure-api-for-fhir"></a>Visa enhets data i Azure API för FHIR

Du kan visa de FHIR-baserade observations resurserna som skapats av Azure IoT Connector för FHIR på FHIR-servern med Postman. Konfigurera din [Postman för att få åtkomst till Azure API för FHIR](access-fhir-postman-tutorial.md) och gör en `GET` begäran till `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` att Visa observations FHIR resurser med ett hjärta pris värde.

> [!TIP]
> Se till att användaren har rätt åtkomst till Azure API för FHIR-dataplan. Använd [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](configure-azure-rbac.md) för att tilldela nödvändiga data Plans roller.

---

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs tar du bort resurs gruppen, som tar bort resurserna i resurs gruppen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com)söker du efter och väljer **resurs grupper**.

2. I listan resurs grupp väljer du namnet på din resurs grupp.

3. På sidan **Översikt** i resurs gruppen väljer du **ta bort resurs grupp**.

4. I bekräftelse dialog rutan skriver du namnet på din resurs grupp och väljer sedan **ta bort**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to delete"
Write-Verbose "Remove-AzResourceGroup -Name $resourceGroupName" -Verbose
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
read -p "Enter the name of the resource group to delete: " resourceGroupName &&
echo "DELETE A RESOURCE GROUP (AND ITS RESOURCES):  az group delete --name $resourceGroupName" &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

En steg-för-steg-guide som vägleder dig genom processen att skapa en ARM-mall finns i [självstudien för att skapa och distribuera din första arm-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

## <a name="next-steps"></a>Nästa steg

I den här snabb starts guiden har du distribuerat Azure IoT Connector för FHIR-funktionen i Azure API för FHIR-resursen. Välj mellan nästa steg för att lära dig mer om Azure IoT Connector för FHIR:

Förstå olika stadier i data flödet i Azure IoT Connector för FHIR.

>[!div class="nextstepaction"]
>[Azure IoT-anslutning för FHIR data flöde](iot-data-flow.md)

Lär dig hur du konfigurerar IoT Connector med hjälp av mallar för enhets-och FHIR-mappning.

>[!div class="nextstepaction"]
>[FHIR för Azure IoT Connector för mappning av](iot-mapping-templates.md)

* I Azure Portal kallas Azure IoT Connector för FHIR IoT Connector (för hands version). FHIR är ett registrerat varumärke som tillhör HL7 och används med behörigheten för HL7.
