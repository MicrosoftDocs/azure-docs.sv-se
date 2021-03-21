---
title: Använd Azure-pipelines för att bygga & distribuera HPC-lösningar
description: Lär dig hur du distribuerar en pipeline för att bygga/släppa för ett HPC-program som körs på Azure Batch.
author: chrisreddington
ms.author: chredd
ms.date: 03/04/2021
ms.topic: how-to
ms.openlocfilehash: 7170044af58a508ff5a43751cc376f8b8d498444
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102435553"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Använd Azure-pipelines för att bygga och distribuera HPC-lösningar

Verktyg som tillhandahålls av Azure DevOps kan översätta till automatiserad utveckling och testning av HPC-lösningar (data behandling med höga prestanda). [Azure-pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) innehåller en mängd olika CD-processer (modern kontinuerlig integrering) och kontinuerlig distribution (CD) för att skapa, distribuera, testa och övervaka program. Dessa processer påskyndar din program varu leverans så att du kan fokusera på din kod i stället för att stödja infrastruktur och åtgärder.

Den här artikeln beskriver hur du konfigurerar CI/CD-processer med hjälp av [Azure-pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) för HPC-lösningar som distribueras på Azure Batch.

## <a name="prerequisites"></a>Förutsättningar

För att följa stegen i den här artikeln behöver du en [Azure DevOps-organisation](/azure/devops/organizations/accounts/create-organization). Du måste också [skapa ett projekt i Azure-DevOps](/azure/devops/organizations/projects/create-project).

Det är bra att ha grundläggande kunskaper om [käll kontroll](/azure/devops/user-guide/source-control) och [Azure Resource Manager mallens syntax](../azure-resource-manager/templates/template-syntax.md) innan du börjar.

## <a name="create-an-azure-pipeline"></a>Skapa en Azure-pipeline

I det här exemplet ska du skapa en pipeline för build och release för att distribuera en Azure Batch-infrastruktur och lansera ett programpaket. Förutsatt att koden utvecklas lokalt är detta det allmänna distributions flödet:

![Diagram över distributions flödet i pipelinen](media/batch-ci-cd/DeploymentFlow.png)

I det här exemplet används flera Azure Resource Manager mallar och befintliga binärfiler. Du kan kopiera dessa exempel till din lagrings plats och skicka dem till Azure-DevOps.

### <a name="understand-the-azure-resource-manager-templates"></a>Förstå Azure Resource Manager mallar

I det här exemplet används flera Azure Resource Manager mallar för att distribuera lösningen. Tre Capabilities (liknar enheter eller moduler) används för att implementera en viss funktion. En mall från slut punkt till slut punkt (deployment.jspå) används sedan för att distribuera de underliggande kapacitets mallarna. Med den här [länkade mall strukturen ](../azure-resource-manager/templates/deployment-tutorial-linked-template.md) kan varje kapacitets mall vara individuellt testad och återanvändas mellan lösningar.

![Diagram som visar en länkad mall med Azure Resource Manager mallar.](media/batch-ci-cd/ARMTemplateHierarchy.png)

Den här mallen definierar ett Azure Storage-konto, vilket krävs för att distribuera programmet till batch-kontot. Detaljerad information finns i [referens hand boken för Resource Manager-mallen för resurs typer för Microsoft. Storage](/azure/templates/microsoft.storage/allversions).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

Nästa mall definierar ett [Azure Batch-konto](accounts.md). Batch-kontot fungerar som en plattform för att köra flera program i olika [pooler](nodes-and-pools.md#pools). Detaljerad information finns i [referens hand boken för Resource Manager-mallen för resurs typerna Microsoft.BatCH](/azure/templates/microsoft.batch/allversions).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

Nästa mall skapar en batch-pool i batch-kontot. Detaljerad information finns i [referens hand boken för Resource Manager-mallen för resurs typerna Microsoft.BatCH](/azure/templates/microsoft.batch/allversions).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

Den sista mallen fungerar som en Orchestrator, som distribuerar de tre underliggande mallarna.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resource Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resource Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

### <a name="understand-the-hpc-solution"></a>Förstå HPC-lösningen

Som tidigare nämnts används flera Azure Resource Manager mallar och befintliga binärfiler i det här exemplet. Du kan kopiera dessa exempel till din lagrings plats och skicka dem till Azure-DevOps.

I den här lösningen används ffmpeg som programpaket. Du kan [Ladda ned ffmpeg-paketet](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08) om du inte redan har det.

![Skärm bild av lagrings platsens struktur.](media/batch-ci-cd/git-repository.jpg)

Det finns fyra huvud avsnitt i den här lagrings platsen:

- En mapp för **arm-mallar** som innehåller Azure Resource Manager mallar
- En **HPC-** programmapp som innehåller Windows 64-bitars versionen av [ffmpeg 4.3.1](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08).
- En **pipeline** -mapp som innehåller en yaml-fil som definierar processen för att bygga pipelinen.
- Valfritt: en mapp för **klient program** , som är en kopia av [Azure Batch .net-fil bearbetning med ffmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) -exempel. Det här programmet behövs inte för den här artikeln.


> [!NOTE]
> Detta är bara ett exempel på en struktur för en kodbas. Den här metoden används för att demonstrera att program, infrastruktur och pipeline-kod lagras i samma lagrings plats.

Nu när käll koden har kon figurer ATS kan du börja med den första versionen.

## <a name="continuous-integration"></a>Kontinuerlig integrering

[Azure-pipeliner](/azure/devops/pipelines/get-started/), inom Azure DevOps Services, hjälper dig att implementera en pipeline för build, test och distribution för dina program.

I det här steget av din pipeline körs testerna vanligt vis för att validera kod och bygga lämpliga delar av program varan. Antalet och typerna av tester och eventuella ytterligare aktiviteter som du kör beror på din bredare version av version och version.

## <a name="prepare-the-hpc-application"></a>Förbereda HPC-programmet

I det här avsnittet arbetar du med mappen **HPC-Application** . Den här mappen innehåller den program vara (ffmpeg) som ska köras i Azure Batch-kontot.

1. Gå till avsnittet versioner i Azure-pipelines i din Azure DevOps-organisation. Skapa en **ny pipeline**.

    ![Skärm bild av den nya pipelinen.](media/batch-ci-cd/new-build-pipeline.jpg)

1. Det finns två alternativ för att skapa en pipeline för bygge:

    a. [Använd den visuella designern](/azure/devops/pipelines/get-started-designer). Det gör du genom att välja "Använd den visuella designern" på sidan **ny pipeline** .

    b. [Använd yaml-versioner](/azure/devops/pipelines/get-started-yaml). Du kan skapa en ny YAML-pipeline genom att klicka på alternativet Azure databaser eller GitHub på sidan **ny pipeline** . Du kan också lagra exemplet nedan i käll kontrollen och referera till en befintlig YAML-fil genom att välja Visual designer och sedan använda YAML-mallen.

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. När versionen har kon figurer ATS vid behov väljer du **spara & kö**. Om du har kontinuerlig integrering aktive rad (i avsnittet **utlösare** ) utlöses build automatiskt när en ny incheckning av lagrings platsen görs, vilket uppfyller de villkor som anges i versionen.

    ![Skärm bild av en befintlig versions pipeline.](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Visa direktsända uppdateringar av förloppet för din version av Azure DevOps genom att gå till **Bygg** avsnittet i Azure-pipeliner. Välj lämplig version från bygg definitionen.

    ![Skärm bild av Live-utdata från build i Azure DevOps.](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Om du använder ett klient program för att köra HPC-lösningen måste du skapa en separat build-definition för programmet. Du kan hitta ett antal instruktions guider i dokumentationen för [Azure pipeline](/azure/devops/pipelines/get-started/index) .

## <a name="continuous-deployment"></a>Kontinuerlig distribution

Azure-pipeliner används också för att distribuera ditt program och den underliggande infrastrukturen. [Versions pipelines](/azure/devops/pipelines/release) aktiverar kontinuerlig distribution och automatiserar din versions bearbetning.

### <a name="deploy-your-application-and-underlying-infrastructure"></a>Distribuera ditt program och den underliggande infrastrukturen

Det finns ett antal steg som ingår i distributionen av infrastrukturen. Eftersom den här lösningen använder [länkade mallar](../azure-resource-manager/templates/linked-templates.md)måste de mallarna vara tillgängliga från en offentlig slut punkt (http eller https). Detta kan vara en lagrings plats på GitHub, ett Azure Blob Storage-konto eller en annan lagrings plats. Artefakter för överförda mallar kan vara skyddade, eftersom de kan lagras i ett privat läge, men nås med hjälp av någon form av SAS-token (signatur för delad åtkomst).

Följande exempel visar hur du distribuerar en infrastruktur med mallar från en Azure Storage-blob.

1. Skapa en **ny versions definition** och välj en tom definition. Byt namn på den nyligen skapade miljön till något som är relevant för din pipeline.

    ![Skärm bild av den första versions pipelinen.](media/batch-ci-cd/Release-0.jpg)

1. Skapa ett beroende på Build-pipeline för att hämta utdata för HPC-programmet.

    > [!NOTE]
    > Anteckna **käll Ali Aset** eftersom det behövs när aktiviteter skapas i versions definitionen.

    ![Skärm bild som visar en artefakt länk till HPCApplicationPackage i lämplig build-pipeline.](media/batch-ci-cd/Release-1.jpg)

1. Skapa en länk till en annan artefakt, den här gången, en Azure-lagrings platsen. Detta krävs för att komma åt Resource Manager-mallarna som lagras i din lagrings plats. Eftersom Resource Manager-mallar inte kräver kompilering behöver du inte skicka dem via en pipeline för bygge.

    > [!NOTE]
    > Notera **käll Ali Aset** igen, eftersom detta behövs senare.

    ![Skärm bild som visar en artefakt länk till Azure-databaser.](media/batch-ci-cd/Release-2.jpg)

1. Navigera till avsnittet **Variables** . Du vill skapa ett antal variabler i din pipeline så att du inte behöver ange samma information på nytt i flera aktiviteter. I det här exemplet används följande variabler:

   - **applicationStorageAccountName**: namnet på det lagrings konto som innehåller de binärfiler för HPC-programmet
   - **batchAccountApplicationName**: namnet på programmet i batch-kontot
   - **batchAccountName**: namnet på batch-kontot
   - **batchAccountPoolName**: namnet på poolen med virtuella datorer som utför bearbetningen
   - **batchApplicationId**: unikt ID för batch-programmet
   - **batchApplicationVersion**: semantisk version av batch-programmet (det vill säga binärfilerna för ffmpeg)
   - **plats**: plats för de Azure-resurser som ska distribueras
   - **resourceGroupName**: namnet på den resurs grupp som ska skapas och var dina resurser ska distribueras
   - **storageAccountName**: namnet på det lagrings konto som innehåller de länkade Resource Manager-mallarna

   ![Skärm bild som visar variabler som angetts för Azure pipelines-versionen.](media/batch-ci-cd/Release-4.jpg)

1. Navigera till aktiviteterna för utvecklings miljön. I ögonblicks bilden nedan kan du se sex uppgifter. Dessa uppgifter kommer att: Hämta de zippade ffmpeg-filerna, distribuera ett lagrings konto som värd för de kapslade Resource Manager-mallarna, kopiera dessa Resource Manager-mallar till lagrings kontot, distribuera batch-kontot och nödvändiga beroenden, skapa ett program i Azure Batch-kontot och ladda upp programpaketet till Azure Batch-kontot.

    ![Skärm bild som visar de uppgifter som används för att frigöra HPC-programmet till Azure Batch.](media/batch-ci-cd/Release-3.jpg)

1. Lägg till aktiviteten **Hämta pipeline artefakt (för hands version)** och ange följande egenskaper:
    - **Visnings namn:** Ladda ned ApplicationPackage till agent
    - **Namnet på den artefakt som ska laddas ned:** HPC-Application
    - **Sökväg att ladda ned till**: $ (system. DefaultWorkingDirectory)

1. Skapa ett lagrings konto för att lagra dina Azure Resource Manager-mallar. Ett befintligt lagrings konto från lösningen kan användas, men för att stödja det här fristående exemplet och isolering av innehåll ska du skapa ett dedikerat lagrings konto.

    Lägg till **distributions uppgiften Azure resurs grupp** och ange följande egenskaper:
    - **Visnings namn:** Distribuera lagrings konto för Resource Manager-mallar
    - **Azure-prenumeration:** Välj lämplig Azure-prenumeration
    - **Åtgärd**: skapa eller uppdatera resurs grupp
    - **Resurs grupp**: $ (resourceGroupName)
    - **Plats**: $ (plats)
    - **Mall**: $ (system. ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.jspå
    - **Åsidosätt mallparametrar**:-accountName $ (storageAccountName)

1. Ladda upp artefakterna från käll kontrollen till lagrings kontot med hjälp av Azure-pipeliner. Som en del av denna Azure pipeline-uppgift kan lagrings kontots behållares URI och SAS-token överföras till en variabel i Azure-pipelines, så att de kan återanvändas i den här agent fasen.

    Lägg till **Azure File Copy** -aktiviteten och ange följande egenskaper:
    - **Källa:** $ (system. ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/
    - **Azure-Anslutnings typ**: Azure Resource Manager
    - **Azure-prenumeration:** Välj lämplig Azure-prenumeration
    - **Måltyp**: Azure-Blob
    - **RM-lagrings konto**: $ (storageAccountName)
    - **Behållar namn**: mallar
    - **URI för lagrings behållare**: templateContainerUri
    - **SAS-token för lagrings behållare**: templateContainerSasToken

1. Distribuera Orchestrator-mallen. Den här mallen innehåller parametrar för lagrings kontots behållares URI och SAS-token. Variablerna som krävs i Resource Manager-mallen lagras antingen i avsnittet variabler i versions definitionen eller ställdes in från en annan Azure-pipeline-uppgift (till exempel en del av Azure Blob Copy-aktiviteten).

    Lägg till **distributions uppgiften Azure resurs grupp** och ange följande egenskaper:
    - **Visnings namn:** Distribuera Azure Batch
    - **Azure-prenumeration:** Välj lämplig Azure-prenumeration
    - **Åtgärd**: skapa eller uppdatera resurs grupp
    - **Resurs grupp**: $ (resourceGroupName)
    - **Plats**: $ (plats)
    - **Mall**: $ (system. ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.jspå
    - **Åsidosätt mallparametrar**: `-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)`

   En vanlig metod är att använda Azure Key Vault uppgifter. Om tjänstens huvud namn som är anslutet till din Azure-prenumeration har rätt åtkomst principer, kan den Hämta hemligheter från en Azure Key Vault och användas som variabler i din pipeline. Namnet på hemligheten kommer att anges med det associerade värdet. Till exempel kan en hemlighet för sshPassword refereras till $ (sshPassword) i versions definitionen.

1. Nästa steg är att anropa Azure CLI. Den första används för att skapa ett program i Azure Batch och ladda upp associerade paket.

    Lägg till **Azure CLI** -aktiviteten och ange följande egenskaper:
    - **Visnings namn:** Skapa program i Azure Batch konto
    - **Azure-prenumeration:** Välj lämplig Azure-prenumeration
    - **Skript plats**: infogat skript
    - **Infogat skript**: `az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)`

1. Det andra steget används för att överföra associerade paket till programmet (i det här fallet ffmpeg-filerna).

    Lägg till **Azure CLI** -aktiviteten och ange följande egenskaper:
    - **Visnings namn:** Ladda upp paket till Azure Batch konto
    - **Azure-prenumeration:** Välj lämplig Azure-prenumeration
    - **Skript plats**: infogat skript
    - **Infogat skript**: `az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip`

    > [!NOTE]
    > Programpaketets versions nummer har angetts till en variabel. På så sätt kan du skriva över tidigare versioner av paketet och du kan styra paketets versions nummer manuellt till Azure Batch.

1. Skapa en ny version genom att välja **versions > skapa en ny version**. När den har utlösts väljer du länken till den nya versionen för att visa statusen.

1. Visa Live-utdata från agenten genom att välja knappen **loggar** under din miljö.

    ![Skärm bild som visar status för versionen.](media/batch-ci-cd/Release-5.jpg)

## <a name="test-the-environment"></a>Testa miljön

När miljön har kon figurer ATS bekräftar du att följande test kan slutföras.

Anslut till det nya Azure Batch kontot med hjälp av Azure CLI från en PowerShell-kommandotolk.

- Logga in på ditt Azure-konto med `az login` och följ instruktionerna för att autentisera.
- Autentisera nu batch-kontot: `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Visa en lista över tillgängliga program

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Kontrol lera att poolen är giltig

```azurecli
az batch pool list
```

Anteckna värdet för `currentDedicatedNodes` kommandots utdata. Värdet justeras vid nästa test.

#### <a name="resize-the-pool"></a>Ändra storlek på poolen

Ändra storlek på poolen så att det finns tillgängliga Compute-noder för jobb-och uppgifts testning, kontrol lera med kommandot pool List att visa aktuell status tills storleks ändringen har slutförts och det finns tillgängliga noder

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Nästa steg

I de här självstudierna får du lära dig hur du interagerar med ett batch-konto via ett enkelt program.

- [Köra en parallell arbetsbelastning med Azure Batch med hjälp av Python API](tutorial-parallel-python.md)
- [Köra en parallell arbetsbelastning med Azure Batch med hjälp av .NET API:et](tutorial-parallel-dotnet.md)
