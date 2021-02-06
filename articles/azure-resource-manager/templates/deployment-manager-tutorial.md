---
title: Använd Azure Deployment Manager för att distribuera mallar
description: Lär dig hur du använder Resource Manager-mallar med Azure Deployment Manager för att distribuera Azure-resurser.
author: mumian
ms.date: 08/25/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 95d5067eccff5c847588834061db8454f75e55d7
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627590"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-public-preview"></a>Självstudier: Använda Azure Deployment Manager med Resource Manager-mallar (offentlig förhandsversion)

Lär dig hur du använder [Azure Deployment Manager](./deployment-manager-overview.md) för att distribuera dina program i flera regioner. Om du föredrar en snabbare metod skapar [snabb starten av Azure Deployment Manager](https://github.com/Azure-Samples/adm-quickstart) de konfigurationer som krävs i din prenumeration och anpassar artefakterna för att distribuera ett program över flera regioner. Snabb starten utför samma uppgifter som i den här självstudien.

För att använda Deployment Manager måste du skapa två mallar:

* **En topologimall**: Beskriver de Azure-resurser som utgör dina program och var de ska distribueras.
* **En distributionsmall**: Beskriver stegen för att distribuera dina program.

> [!IMPORTANT]
> Om din prenumeration har marker ATS för Kanarie för att testa nya Azure-funktioner kan du bara använda Azure Deployment Manager för att distribuera till Kanarie regionerna.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Förstå scenariot
> * Ladda ned filerna för självstudiekursen
> * Förbereda artefakterna
> * Skapa en användardefinierad hanterad identitet
> * Skapa mallen för tjänsttopologin
> * Skapa distributionsmallen
> * Distribuera mallarna
> * Verifiera distributionen
> * Distribuera den senare versionen
> * Rensa resurser

Ytterligare resurser:

* [Referens för Azure Deployment Manager REST API](/rest/api/deploymentmanager/).
* [Självstudie: Använd hälso kontroll i Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.
* Viss erfarenhet av att utveckla [Azure Resource Manager-mallar](overview.md).
* Azure PowerShell. Mer information finns i [Kom igång med Azure PowerShell](/powershell/azure/get-started-azureps).
* Deployment Manager-cmdlets. För att installera dessa cmdlets i förhandsversionen behöver du den senaste versionen av PowerShellGet. Information om hur du skaffar den senaste versionen finns i [Installera PowerShellGet](/powershell/scripting/gallery/installing-psget). Stäng PowerShell-fönstret när du har installerat PowerShellGet. Öppna ett nytt upphöjt PowerShell-fönster och använd följande kommando:

    ```powershell
    Install-Module -Name Az.DeploymentManager
    ```

## <a name="understand-the-scenario"></a>Förstå scenariot

Mallen för tjänstmall beskriver de Azure-resurser som utgör tjänsten och var de ska distribueras. Definitionen av tjänsttopologin har följande hierarki:

* Tjänsttopologi
  * Tjänster
    * Tjänstenheter

Följande diagram visar tjänsttopologin som används i den här självstudien:

![Diagram över scenario i självstudiekurs om Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

Det finns två tjänster som har allokerats på platser för USA, västra och USA, östra. Varje tjänst har två tjänst enheter: ett klient webb program och ett Server dels lagrings konto. Tjänst enhets definitionerna innehåller länkar till mallen och de parameterstyrda filer som skapar webb program och lagrings konton.

## <a name="download-the-tutorial-files"></a>Ladda ned filerna för självstudiekursen

1. Ladda ned [mallarna och artefakterna](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) som används i den här självstudien.
1. Packa upp filerna till dator på din plats.

Det finns två mappar under rotmappen:

* _ADMTemplates_: Innehåller Deployment Manager-mallarna, som innehåller:
  * _CreateADMServiceTopology.json_
  * _CreateADMServiceTopology.Parameters.json_
  * _CreateADMRollout.json_
  * _CreateADMRollout.Parameters.json_
* _ArtifactStore_: Innehåller både mallartefakterna och de binära artefakterna. Se [Förbereda artefakterna](#prepare-the-artifacts).

Det finns två uppsättningar med mallar. En uppsättning är de Deployment Manager-mallar som används för att distribuera tjänstens topologi och distributionen. Den andra uppsättningen anropas från tjänst enheterna för att skapa webb tjänster och lagrings konton.

## <a name="prepare-the-artifacts"></a>Förbereda artefakterna

Mappen ArtifactStore från nedladdningen innehåller två mappar:

![Diagram över artefaktkällan i självstudiekurs om Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

* Mappen _templates_: Innehåller mallartefakterna. Mapparna _1.0.0.0_ och _1.0.0.1_ representerar de två versionerna av de binära artefakterna. I varje version finns det en mapp för varje tjänst: _ServiceEUS_ (USA, östra USA) och _ServiceWUS_ (USA, västra). Varje tjänst har ett par med mall- och parameterfiler för att skapa ett lagringskonto, och ett annat par för att skapa ett webbprogram. Webbprogrammallen anropar ett komprimerat paket, som innehåller filerna för webbprogrammet. Den komprimerade filen är en binär artefakt som lagras i binaries-mappen.
* Mappen _binaries_: Innehåller de binära artefakterna. Mapparna _1.0.0.0_ och _1.0.0.1_ representerar de två versionerna av de binära artefakterna. I varje version finns det en zip-fil för att skapa webb programmet på platsen västra USA och den andra zip-filen för att skapa webb programmet på platsen USA, östra.

De två versionerna (1.0.0.0 och 1.0.0.1) är avsedda för [ändringsdistributionen](#deploy-the-revision). Även om både mallartefakterna och de binära artefakterna har två versioner, skiljer sig endast de binära artefakterna åt i de två versionerna. I praktiken uppdateras binära artefakter oftare jämfört med mallartefakter.

1. Öppna _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json_ i ett redigeringsprogram. Det är en grundläggande mall för att skapa ett lagrings konto.
1. Öppna _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json_.

    ![Azure Deployment Manager-självstudie om att skapa en webbprogrammall](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    Mallen anropar ett distributionspaket, som innehåller filerna för webbprogrammet. I den här självstudien innehåller det komprimerade paketet bara en _index.html_ -fil.
1. Öppna _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json_.

    ![Azure Deployment Manager-självstudie om att skapa containerRoot och parametrar för en webbprogrammall](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    Värdet för `deployPackageUri` är sökvägen till distributions paketet. Parametern innehåller en `$containerRoot` variabel. Värdet för anges `$containerRoot` i distributions [mal len](#create-the-rollout-template) genom att den sammanfogar ARTEFAKT källans SAS-plats, artefakt roten och `deployPackageUri` .
1. Öppna _\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html_.

    ```html
    <html>
      <head>
        <title>Azure Deployment Manager tutorial</title>
      </head>
      <body>
        <p>Hello world from west U.S.!</p>
        <p>Version 1.0.0.0</p>
      </body>
    </html>
    ```

    HTML-koden visar plats och versions information. Den binära filen i mappen _1.0.0.1_ visar _version 1.0.0.1_. När du har distribuerat tjänsten kan du bläddra till dessa sidor.
1. Granska andra artefaktfiler. Det hjälper dig att förstå scenariot bättre.

Mallartefakter används av tjänsttopologimallen och binära artefakter används av distributionsmallen. Både topologimallen och distributionsmallen definierar en Azure-resurs för en artefaktkälla, som är en resurs som används för att peka Resource Manager på mallen och de binära artefakterna som används i distributionen. För att förenkla självstudien används ett lagringskonto för att lagra både mallartefakterna och de binära artefakterna. Båda artefaktkällorna peka på samma lagringskonto.

Kör följande PowerShell-skript för att skapa en resurs grupp, skapa en lagrings behållare, skapa en BLOB-behållare, ladda upp de hämtade filerna och skapa sedan en SAS-token.

> [!IMPORTANT]
> `projectName` i PowerShell-skriptet används för att generera namn för de Azure-tjänster som distribueras i den här självstudien. Olika Azure-tjänster har olika krav på namnen. Om du vill se till att distributionen lyckas väljer du ett namn med färre än 12 tecken och bara gemena bokstäver och siffror.
> Spara en kopia av projekt namnet. Du använder samma `projectName` under självstudierna.

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$filePath = Read-Host -Prompt "Enter the folder that contains the downloaded files"


$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$containerName = "admfiles"
$filePathArtifacts = "${filePath}\ArtifactStore"

New-AzResourceGroup -Name $resourceGroupName -Location $location

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$storageContext = $storageAccount.Context

$storageContainer = New-AzStorageContainer -Name $containerName -Context $storageContext -Permission Off


$filesToUpload = Get-ChildItem $filePathArtifacts -Recurse -File

foreach ($x in $filesToUpload) {
    $targetPath = ($x.fullname.Substring($filePathArtifacts.Length + 1)).Replace("\", "/")

    Write-Verbose "Uploading $("\" + $x.fullname.Substring($filePathArtifacts.Length + 1)) to $($storageContainer.CloudBlobContainer.Uri.AbsoluteUri + "/" + $targetPath)"
    Set-AzStorageBlobContent -File $x.fullname -Container $storageContainer.Name -Blob $targetPath -Context $storageContext | Out-Null
}

$token = New-AzStorageContainerSASToken -name $containerName -Context $storageContext -Permission rl -ExpiryTime (Get-date).AddMonths(1)

$url = $storageAccount.PrimaryEndpoints.Blob + $containerName + $token

Write-Host $url
```

Gör en kopia av URL: en med SAS-token. URL-adressen krävs för att fylla i ett fält i de två parametervärdena: Topology Parameters File och distribution Parameters File.

Öppna behållaren från Azure Portal och kontrol lera att både _binärfilerna_ och _mallarna_ för mappar och filerna har laddats upp.

## <a name="create-the-user-assigned-managed-identity"></a>Skapa den användartilldelade hanterade identiteten

Senare i självstudien ska du distribuera en distribution. En användartilldelad hanterad identitet behövs för att utföra distributionsåtgärderna (till exempel för att distribuera webbprogrammen och lagringskontot). Den här identiteten måste beviljas åtkomst till Azure-prenumerationen som du distribuerar tjänsten till samt ha tillräcklig behörighet för att slutföra artefaktdistributionen.

Du måste skapa en användartilldelad hanterad identitet och konfigurera åtkomstkontrollen för din prenumeration.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Skapa en [användardefinierad hanterad identitet](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
1. Från portalen väljer du **Prenumerationer** på den vänstra menyn och väljer sedan din prenumeration.
1. Välj **Åtkomstkontroll (IAM)** och sedan **Lägg till rolltilldelning**.
1. Ange eller välj följande värden:

    ![Azure Deployment Manager-självstudie om åtkomstkontroll med användartilldelade hanterade identiteter](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    * **Roll**: Ge tillräcklig behörighet för att slutföra artefaktdistributionen (webbprogrammen och lagringskontona). Välj **Deltagare** i den här självstudien. I praktiken vill du begränsa behörigheterna till de minsta nödvändiga.
    * **Tilldela åtkomst till**: Välj **användare tilldelad hanterad identitet**.
    * Välj den användartilldelade hanterade identitet som du skapade tidigare i självstudien.
1. Välj **Spara**.

## <a name="create-the-service-topology-template"></a>Skapa mallen för tjänsttopologin

Öppna _\ADMTemplates\CreateADMServiceTopology.json_.

### <a name="the-parameters"></a>Parametrarna

Mallen innehåller följande parametrar:

* `projectName`: Det här namnet används för att skapa namnen på Deployment Manager-resurserna. Om du till exempel använder **demo** är namnet på tjänstens topologi **demo** ServiceTopology. Resurs namnen definieras i mallens `variables` avsnitt.
* `azureResourcelocation`: För att förenkla självstudien delar alla resurser den här platsen om den inte anges på annat sätt.
* `artifactSourceSASLocation`: SAS-URI: n till BLOB-behållaren där enhets mal len och parameter-filerna lagras för distribution. Se [Förbereda artefakterna](#prepare-the-artifacts).
* `templateArtifactRoot`: Förskjutnings Sök vägen från BLOB-behållaren där mallarna och parametrarna lagras. Standardvärdet är _templates/1.0.0.0_. Ändra inte det här värdet såvida du inte vill ändra mappstrukturen som beskrivs i [Förbereda artefakterna](#prepare-the-artifacts). Relativa sökvägar används i den här självstudien. Den fullständiga sökvägen är konstruerad genom att sammanfoga `artifactSourceSASLocation` , `templateArtifactRoot` och `templateArtifactSourceRelativePath` (eller `parametersArtifactSourceRelativePath` ).
* `targetSubscriptionID`: Prenumerations-ID: t som Deployment Manager-resurserna ska distribueras till och faktureras. Använd ditt prenumerations-ID i den här självstudien.

### <a name="the-variables"></a>Variablerna

Avsnittet med variabler definierar namnen på resurserna, Azure-platserna för de två tjänsterna: och och `ServiceWUS` `ServiceEUS` artefakt Sök vägar:

![Azure Deployment Manager-självstudie om variabler för topologimallen](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

Jämför artefaktsökvägarna med mappstrukturen som du laddade upp till lagringskontot. Observera att artefaktsökvägarna är relativa sökvägar. Den fullständiga sökvägen är konstruerad genom att sammanfoga `artifactSourceSASLocation` , `templateArtifactRoot` och `templateArtifactSourceRelativePath` (eller `parametersArtifactSourceRelativePath` ).

### <a name="the-resources"></a>Resurserna

På rotnivå definieras två resurser: *en artefakt källa* och *en topologi för tjänster*.

Definitionen av artefaktkällan är:

![Azure Deployment Manager-självstudie om artefaktkällan för resurser för topologimallen](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

Följande skärmbild visar endast vissa delar av definitionerna av tjänsttopologin, tjänsterna och tjänstenheterna:

![Azure Deployment Manager-självstudie om tjänsttopologin för resurser för topologimallen](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

* `artifactSourceId`: Används för att koppla artefakt käll resursen till service Topology-resursen.
* `dependsOn`: Alla tjänstens Topology-resurser är beroende av artefakt käll resursen.
* `artifacts`: Peka på mallens artefakter. Relativa sökvägar används här. Den fullständiga sökvägen konstrueras genom sammanfogning `artifactSourceSASLocation` (definieras i artefakt källan), `artifactRoot` (definieras i artefakt källan) och `templateArtifactSourceRelativePath` (eller `parametersArtifactSourceRelativePath` ).

### <a name="topology-parameters-file"></a>Topologiparameterfil

Du skapar en parameterfil som används med topologimallen.

1. Öppna _\ADMTemplates\CreateADMServiceTopology.Parameters.js_ i Visual Studio Code eller text redigerare.
1. Ange parameter värden:

    * `projectName`: Ange en sträng med 4-5 tecken. Det här namnet används för att skapa unika Azure-resurs namn.
    * `azureResourceLocation`: Om du inte är bekant med Azure-platser kan **du använda den** i den här självstudien.
    * `artifactSourceSASLocation`: Ange SAS-URI: n till rot katalogen (BLOB-behållaren) där mall-och parameter-filer för enheter lagras för distribution.  Se [Förbereda artefakterna](#prepare-the-artifacts).
    * `templateArtifactRoot`: Om du inte ändrar mappstrukturen för artefakterna använder du _mallar/1.0.0.0_ i den här självstudien.

> [!IMPORTANT]
> Topologimallen och distributionsmallen delar vissa vanliga parametrar. Dessa parametrar måste ha samma värden. Dessa parametrar är: `projectName` , `azureResourceLocation` och `artifactSourceSASLocation` (båda artefakt källorna delar samma lagrings konto i den här självstudien).

## <a name="create-the-rollout-template"></a>Skapa distributionsmallen

Öppna _\ADMTemplates\CreateADMRollout.json_.

### <a name="the-parameters"></a>Parametrarna

Mallen innehåller följande parametrar:

![Azure Deployment Manager-självstudie om parametrar för distributionsmallen](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

* `projectName`: Det här namnet används för att skapa namnen på Deployment Manager-resurserna. Om du exempelvis använder **demo** är distributions namnet **demo** distributionen. Namnen definieras i mallens `variables` avsnitt.
* `azureResourcelocation`: För att förenkla självstudien delar alla Deployment Manager-resurser på den här platsen om den inte anges på annat sätt.
* `artifactSourceSASLocation`: SAS-URI: n till rot katalogen (BLOB-behållaren) där mall-och parameter-filer för enheter lagras för distribution. Se [Förbereda artefakterna](#prepare-the-artifacts).
* `binaryArtifactRoot`: Standardvärdet är _binärfiler/1.0.0.0_. Ändra inte det här värdet såvida du inte vill ändra mappstrukturen som beskrivs i [Förbereda artefakterna](#prepare-the-artifacts). Relativa sökvägar används i den här självstudien. Den fullständiga sökvägen skapas genom att sammanfoga `artifactSourceSASLocation` , `binaryArtifactRoot` och den som `deployPackageUri` anges i _CreateWebApplicationParameters.jspå_. Se [Förbereda artefakterna](#prepare-the-artifacts).
* `managedIdentityID`: Den användarspecifika hanterade identiteten som utför distributions åtgärderna. Se [Skapa den användartilldelade hanterade identiteten](#create-the-user-assigned-managed-identity).

### <a name="the-variables"></a>Variablerna

I `variables` avsnittet definieras namnen på resurserna. Kontrollera att namnet på tjänsttopologin, tjänstnamnen och namnen på tjänstenheterna matchar namnen som definieras i [topologimallen](#create-the-service-topology-template).

![Azure Deployment Manager-självstudie om variabler för distributionsmallen](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>Resurserna

Tre resurser definieras på rotnivå: en artefaktkälla, ett steg och en distribution.

Artefaktkällans definition är identisk med den som anges i topologimallen. Mer information finns i [Skapa mallen för tjänsttopologin](#create-the-service-topology-template).

Följande skärm bild visar `wait` steg definitionen:

![Azure Deployment Manager-självstudie om wait-steget i resurserna för distributionsmallen](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

Varaktigheten använder [ISO 8601-standarden](https://en.wikipedia.org/wiki/ISO_8601#Durations). **PT1M** (versaler krävs) är ett exempel på en 1-minuters väntan.

I följande skärmbild visas endast vissa delar av distributionsdefinitionen:

![Azure Deployment Manager-självstudie om distribution av resurser för distributionsmallen](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

* `dependsOn`: Distributions resursen är beroende av artefakt käll resursen och alla definierade steg.
* `artifactSourceId`: Används för att koppla artefakt käll resursen till distributions resursen.
* `targetServiceTopologyId`: Används för att koppla tjänstens topologi till distributions resursen.
* `deploymentTargetId`: Det är tjänst enhetens resurs-ID för service Topology-resursen.
* `preDeploymentSteps` och `postDeploymentSteps` : innehåller installations stegen. I mallen `wait` kallas ett steg.
* `dependsOnStepGroups`: Konfigurera beroenden mellan steg grupperna.

### <a name="rollout-parameters-file"></a>Fil för distributionsparametrar

Du skapar en parameterfil som används med distributionsmallen.

1. Öppna _\ADMTemplates\CreateADMRollout.Parameters.js_ i Visual Studio Code eller text redigerare.
1. Ange parameter värden:

    * `projectName`: Ange en sträng med 4-5 tecken. Det här namnet används för att skapa unika Azure-resurs namn.
    * `azureResourceLocation`: Ange en Azure-plats.
    * `artifactSourceSASLocation`: Ange SAS-URI: n till rot katalogen (BLOB-behållaren) där mall-och parameter-filer för enheter lagras för distribution.  Se [Förbereda artefakterna](#prepare-the-artifacts).
    * `binaryArtifactRoot`: Om du inte ändrar mappstrukturen för artefakterna ska du använda _binärfiler/1.0.0.0_ i den här självstudien.
    * `managedIdentityID`: Ange den användarspecifika hanterade identiteten. Se [Skapa den användartilldelade hanterade identiteten](#create-the-user-assigned-managed-identity). Syntax:

        ```json
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> Topologimallen och distributionsmallen delar vissa vanliga parametrar. Dessa parametrar måste ha samma värden. Dessa parametrar är: `projectName` , `azureResourceLocation` och `artifactSourceSASLocation` (båda artefakt källorna delar samma lagrings konto i den här självstudien).

## <a name="deploy-the-templates"></a>Distribuera mallarna

Azure PowerShell kan användas för att distribuera mallarna.

1. Kör skriptet för att distribuera tjänsttopologin.

    ```azurepowershell
    # Create the service topology
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
    ```

    Om du kör skriptet från en annan PowerShell-session än den som du körde för att [förbereda artefakter](#prepare-the-artifacts) -skriptet måste du fylla i variablerna först, som innehåller `$resourceGroupName` och `$filePath` .

    > [!NOTE]
    > `New-AzResourceGroupDeployment` är ett asynkront anrop. Meddelandet lyckades innebär att distributionen har startats. För att verifiera distributionen, se steg 2 och steg 4 i den här proceduren.

1. Kontrollera att tjänsttopologin och de angivna resurserna har skapats på Azure-portalen:

    ![Azure Deployment Manager-självstudie om resurser för en distribuerad tjänsttopologi](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    **Visa dolda typer** måste väljas för att resurserna ska visas.

1. <a id="deploy-the-rollout-template"></a>Distribuera distributionsmallen:

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

1. Kontrollera distributionsförloppet med hjälp av följande PowerShell-skript:

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "${projectName}Rollout" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    Du måste installera PowerShell-cmdlets för Deployment Manager innan du kan köra denna cmdlet. Se [krav](#prerequisites). `-Verbose`Parametern kan användas för att se hela utdata.

    Följande exempel visar körningsstatusen:

    ```Output
    VERBOSE:

    Status: Succeeded
    ArtifactSourceId: /subscriptions/<AzureSubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    BuildVersion: 1.0.0.0

    Operation Info:
        Retry Attempt: 0
        Skip Succeeded: False
        Start Time: 03/05/2019 15:26:13
        End Time: 03/05/2019 15:31:26
        Total Duration: 00:05:12

    Service: adm0925ServiceEUS
        TargetLocation: EastUS
        TargetSubscriptionId: <AzureSubscriptionID>

        ServiceUnit: adm0925ServiceEUSStorage
            TargetResourceGroup: adm0925ServiceEUSrg

            Step: Deploy
                Status: Succeeded
                StepGroup: stepGroup3
                Operation Info:
                    DeploymentName: 2F535084871E43E7A7A4CE7B45BE06510adm0925ServiceEUSStorage
                    CorrelationId: 0b6f030d-7348-48ae-a578-bcd6bcafe78d
                    Start Time: 03/05/2019 15:26:32
                    End Time: 03/05/2019 15:27:41
                    Total Duration: 00:01:08
                Resource Operations:

                    Resource Operation 1:
                    Name: txq6iwnyq5xle
                    Type: Microsoft.Storage/storageAccounts
                    ProvisioningState: Succeeded
                    StatusCode: OK
                    OperationId: 64A6E6EFEF1F7755

    ...

    ResourceGroupName       : adm0925rg
    BuildVersion            : 1.0.0.0
    ArtifactSourceId        : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    TargetServiceTopologyId : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/serviceTopologies/adm0925ServiceTopology
    Status                  : Running
    TotalRetryAttempts      : 0
    OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
    Services                : {adm0925ServiceEUS, adm0925ServiceWUS}
    Name                    : adm0925Rollout
    Type                    : Microsoft.DeploymentManager/rollouts
    Location                : centralus
    Id                      : /subscriptions/<SubscriptionID>/resourcegroups/adm0925rg/providers/Microsoft.DeploymentManager/rollouts/adm0925Rollout
    Tags                    :
    ```

    När distributionen har distribuerats visas två fler resurs grupper som skapats, en för varje tjänst.

## <a name="verify-the-deployment"></a>Verifiera distributionen

1. Öppna [Azure-portalen](https://portal.azure.com).
1. Bläddra till de nyligen skapade webb programmen under de nya resurs grupper som skapats av distributions distributionen.
1. Öppna webbprogrammet i en webbläsare. Kontrol lera platsen och versionen i _index.html_ -filen.

## <a name="deploy-the-revision"></a>Distribuera ändringen

När du har en ny version (1.0.0.1) av webbprogrammet kan du använda följande procedur för att distribuera om webbprogrammet.

1. Öppna _CreateADMRollout.Parameters.jspå_.
1. Uppdatera `binaryArtifactRoot` till _binärfiler/1.0.0.1_.
1. Distribuera om distributionen enligt anvisningarna i [Distribuera mallarna](#deploy-the-rollout-template).
1. Verifiera distributionen genom att följa anvisningarna i [Verifiera distributionen](#verify-the-deployment). Version 1.0.0.1 bör visas på webbsidan.

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure Portal väljer du **resurs grupp** på den vänstra menyn.
1. Använd fältet **Filtrera efter namn** för att visa resursgrupperna som skapats i den här självstudien.

    * **&lt; projectName>RG**: innehåller Deployment Manager-resurser.
    * **&lt; ProjectName>ServiceWUSrg**: innehåller de resurser som definierats av ServiceWUS.
    * **&lt; ProjectName>ServiceEUSrg**: innehåller de resurser som definierats av ServiceEUS.
    * Resursgruppen för den användardefinierade hanterade identiteten.
1. Välj resursgruppens namn.
1. Välj **ta bort resurs grupp** på den översta menyn.
1. Upprepa de två sista stegen för att ta bort andra resursgrupper som skapats av den här självstudien.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder Azure Deployment Manager. Information om hur du integrerar hälso övervakning i Azure Deployment Manager finns i [Självstudier: använda hälso kontroll i Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).
