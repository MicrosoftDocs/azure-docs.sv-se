---
title: Överföra artefakter
description: Överföra samlingar av avbildningar eller andra artefakter från ett containerregister till ett annat register genom att skapa en överföringspipeline med hjälp av Azure Storage-konton
ms.topic: article
ms.date: 10/07/2020
ms.custom: ''
ms.openlocfilehash: e921880eb0b8ae5a38e69c9c0045f6a26d84084d
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497990"
---
# <a name="transfer-artifacts-to-another-registry"></a>Överföra artefakter till ett annat register

Den här artikeln visar hur du överför samlingar av avbildningar eller andra registerartefakter från ett Azure-containerregister till ett annat register. Käll- och målregister kan finnas i samma eller olika prenumerationer, Active Directory-klienter, Azure-moln eller fysiskt frånkopplade moln. 

Om du vill överföra artefakter skapar du *en överföringspipeline* som replikerar artefakter mellan två register med hjälp av [bloblagring:](../storage/blobs/storage-blobs-introduction.md)

* Artefakter från ett källregister exporteras till en blob i ett källlagringskonto 
* Bloben kopieras från källlagringskontot till ett mållagringskonto
* Bloben i mållagringskontot importeras som artefakter i målregistret. Du kan konfigurera importpipelinen så att den utlöses när artefaktbloben uppdateras i mållagringen.

Överföring är perfekt för att kopiera innehåll mellan två Azure-containerregister i fysiskt frånkopplade moln, medlade av lagringskonton i varje moln. Om du i stället vill kopiera avbildningar från containerregister i anslutna moln, [](container-registry-import-images.md) inklusive Docker Hub och andra molnleverantörer, rekommenderas avbildningsimport.

I den här artikeln använder du Azure Resource Manager för att skapa och köra överföringspipelinen. Azure CLI används för att etablera associerade resurser, till exempel lagringshemligheter. Azure CLI version 2.2.0 eller senare rekommenderas. Om du behöver installera eller uppgradera CLI kan du läsa mer i [Installera Azure CLI][azure-cli].

Den här funktionen är tillgänglig på **tjänstnivån** premiumcontainerregister. Information om registertjänstnivåer och begränsningar finns i [Azure Container Registry nivåer](container-registry-skus.md).

> [!IMPORTANT]
> Den här funktionen finns för närvarande som en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="prerequisites"></a>Förutsättningar

* **Containerregister – Du** behöver ett befintligt källregister med artefakter som ska överföras och ett målregister. ACR-överföring är avsedd för förflyttning över fysiskt frånkopplade moln. För testning kan käll- och målregister finnas i samma eller en annan Azure-prenumeration, Active Directory-klient eller moln. 

   Om du behöver skapa ett register kan du gå till [Snabbstart: Skapa ett privat containerregister med hjälp av Azure CLI.](container-registry-get-started-azure-cli.md) 
* **Lagringskonton** – Skapa käll- och mållagringskonton i en prenumeration och valfri plats. I testsyfte kan du använda samma prenumeration eller prenumerationer som dina käll- och målregister. I scenarier mellan moln skapar du vanligtvis ett separat lagringskonto i varje moln. 

  Om det behövs skapar du lagringskontona [med Azure CLI](../storage/common/storage-account-create.md?tabs=azure-cli) eller andra verktyg. 

  Skapa en blobcontainer för artefaktöverföring i varje konto. Skapa till exempel en container med namnet *transfer*. Två eller flera överföringspipelines kan dela samma lagringskonto, men bör använda olika omfång för lagringscontainer.
* **Nyckelvalv – Nyckelvalv** krävs för att lagra SAS-tokenhemligheter som används för åtkomst till käll- och mållagringskonton. Skapa käll- och målnyckelvalven i samma Azure-prenumeration eller prenumerationer som dina käll- och målregister. I demonstrationssyfte förutsätter de mallar och kommandon som används i den här artikeln också att käll- och målnyckelvalven finns i samma resursgrupper som käll- respektive målregister. Den här användningen av vanliga resursgrupper krävs inte, men det förenklar de mallar och kommandon som används i den här artikeln.

   Om det behövs skapar du nyckelvalv med [Azure CLI](../key-vault/secrets/quick-create-cli.md) eller andra verktyg.

* **Miljövariabler** – Till exempel kommandon i den här artikeln anger du följande miljövariabler för käll- och målmiljöerna. Alla exempel är formaterade för Bash-gränssnittet.
  ```console
  SOURCE_RG="<source-resource-group>"
  TARGET_RG="<target-resource-group>"
  SOURCE_KV="<source-key-vault>"
  TARGET_KV="<target-key-vault>"
  SOURCE_SA="<source-storage-account>"
  TARGET_SA="<target-storage-account>"
  ```

## <a name="scenario-overview"></a>Översikt över scenario

Du skapar följande tre pipelineresurser för avbildningsöverföring mellan register. Alla skapas med put-åtgärder. De här resurserna fungerar på *dina käll-* *och målregister* och lagringskonton. 

Lagringsautentisering använder SAS-token, som hanteras som hemligheter i nyckelvalv. Pipelines använder hanterade identiteter för att läsa hemligheterna i valven.

* **[ExportPipeline](#create-exportpipeline-with-resource-manager)** – långvarig resurs som innehåller information på hög nivå om *källregistret och* lagringskontot. Den här informationen omfattar blobcontainerns källlagrings-URI och nyckelvalvet som hanterar SAS-källtoken. 
* **[ImportPipeline](#create-importpipeline-with-resource-manager)** – långvarig resurs som innehåller information på hög nivå om *målregistret och* lagringskontot. Den här informationen omfattar mållagringsblobcontainerns URI och nyckelvalvet som hanterar sas-måltoken. En importutlösare är aktiverad som standard, så pipelinen körs automatiskt när en artefaktblob hamnar i mållagringscontainern. 
* **[PipelineRun](#create-pipelinerun-for-export-with-resource-manager)** – Resurs som används för att anropa en ExportPipeline- eller ImportPipeline-resurs.  
  * Du kör ExportPipeline manuellt genom att skapa en PipelineRun-resurs och ange de artefakter som ska exporteras.  
  * Om en importutlösare är aktiverad körs ImportPipeline automatiskt. Den kan också köras manuellt med hjälp av en PipelineRun. 
  * För närvarande kan **högst 50 artefakter överföras** med varje PipelineRun.

### <a name="things-to-know"></a>Saker att känna till
* ExportPipeline och ImportPipeline finns vanligtvis i olika Active Directory-klienter som är associerade med käll- och målmoln. Det här scenariot kräver separata hanterade identiteter och nyckelvalv för export- och importresurserna. I testsyfte kan dessa resurser placeras i samma moln och dela identiteter.
* Som standard gör exportpipeline- och ImportPipeline-mallarna att en system tilldelad hanterad identitet kan komma åt nyckelvalvshemligheter. ExportPipeline- och ImportPipeline-mallarna stöder också en användar tilldelad identitet som du anger. 

## <a name="create-and-store-sas-keys"></a>Skapa och lagra SAS-nycklar

Överföringen använder SAS-token (signatur för delad åtkomst) för att komma åt lagringskontona i käll- och målmiljöerna. Generera och lagra token enligt beskrivningen i följande avsnitt.  

### <a name="generate-sas-token-for-export"></a>Generera SAS-token för export

Kör kommandot [az storage container generate-sas][az-storage-container-generate-sas] för att generera en SAS-token för containern i källlagringskontot som används för artefaktexport.

*Rekommenderade tokenbehörigheter:* Läsa, Skriva, Lista, Lägg till. 

I följande exempel tilldelas kommandoutdata till EXPORT_SAS miljövariabeln med prefixet "?". Uppdatera `--expiry` värdet för din miljö:

```azurecli
EXPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $SOURCE_SA \
  --expiry 2021-01-01 \
  --permissions alrw \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-export"></a>Lagra SAS-token för export

Lagra SAS-token i azure-nyckelvalvet med [az keyvault secret set:][az-keyvault-secret-set]

```azurecli
az keyvault secret set \
  --name acrexportsas \
  --value $EXPORT_SAS \
  --vault-name $SOURCE_KV 
```

### <a name="generate-sas-token-for-import"></a>Generera SAS-token för import

Kör kommandot [az storage container generate-sas][az-storage-container-generate-sas] för att generera en SAS-token för containern i mållagringskontot som används för artefaktimport.

*Rekommenderade tokenbehörigheter:* Läsa, ta bort, lista

I följande exempel tilldelas kommandoutdata till IMPORT_SAS miljövariabeln med prefixet "?". Uppdatera `--expiry` värdet för din miljö:

```azurecli
IMPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $TARGET_SA \
  --expiry 2021-01-01 \
  --permissions dlr \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-import"></a>Lagra SAS-token för import

Lagra SAS-token i Azure-målnyckelvalvet med [az keyvault secret set][az-keyvault-secret-set]:

```azurecli
az keyvault secret set \
  --name acrimportsas \
  --value $IMPORT_SAS \
  --vault-name $TARGET_KV 
```

## <a name="create-exportpipeline-with-resource-manager"></a>Skapa ExportPipeline med Resource Manager

Skapa en ExportPipeline-resurs för ditt källcontainerregister med hjälp Azure Resource Manager en malldistribution.

Kopiera ExportPipeline Resource Manager [till](https://github.com/Azure/acr/tree/master/docs/image-transfer/ExportPipelines) en lokal mapp.

Ange följande parametervärden i filen `azuredeploy.parameters.json` :

|Parameter  |Värde  |
|---------|---------|
|registryName     | Namnet på ditt källcontainerregister      |
|exportPipelineName     |  Namn som du väljer för exportpipelinen       |
|targetUri     |  URI för lagringscontainern i källmiljön (målet för exportpipelinen).<br/>Exempel: `https://sourcestorage.blob.core.windows.net/transfer`       |
|keyVaultName     |  Namnet på källnyckelvalvet  |
|sasTokenSecretName  | Namnet på SAS-tokenhemligheten i källnyckelvalvet <br/>Exempel: acrexportsas

### <a name="export-options"></a>Exportalternativ

Egenskapen `options` för exportpipelines stöder valfria booleska värden. Följande värden rekommenderas:

|Parameter  |Värde  |
|---------|---------|
|alternativ | OverwriteBlobs – Skriva över befintliga målblobar<br/>ContinueOnErrors – Fortsätt exportera återstående artefakter i källregistret om en artefaktexport misslyckas.

### <a name="create-the-resource"></a>Skapa resursen

Kör [az deployment group create][az-deployment-group-create] för att skapa en resurs med namnet *exportPipeline* enligt följande exempel. Med det första alternativet aktiverar exempelmallen som standard en systemtilldelningsidentitet i resursen ExportPipeline. 

Med det andra alternativet kan du ge resursen en användar tilldelad identitet. (Det går inte att skapa den användar tilldelade identiteten.)

Med båda alternativen konfigurerar mallen identiteten för åtkomst till SAS-token i nyckelvalvet för export. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>Alternativ 1: Skapa resurs och aktivera systemtilldelningsidentitet

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>Alternativ 2: Skapa en resurs och ange en användartilldelningsidentitet

I det här kommandot anger du resurs-ID:t för den användar tilldelade identiteten som en ytterligare parameter.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

Anteckna resurs-ID:t ( ) för `id` pipelinen i kommandoutdata. Du kan lagra det här värdet i en miljövariabel för senare användning genom att köra [az deployment group show][az-deployment-group-show]. Exempel:

```azurecli
EXPORT_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-importpipeline-with-resource-manager"></a>Skapa ImportPipeline med Resource Manager 

Skapa en ImportPipeline-resurs i målcontainerregistret med hjälp Azure Resource Manager malldistribution. Som standard är pipelinen aktiverad för att importera automatiskt när lagringskontot i målmiljön har en artefaktblob.

Kopiera ImportPipeline Resource Manager [till](https://github.com/Azure/acr/tree/master/docs/image-transfer/ImportPipelines) en lokal mapp.

Ange följande parametervärden i filen `azuredeploy.parameters.json` :

Parameter  |Värde  |
|---------|---------|
|registryName     | Namnet på målcontainerregistret      |
|importPipelineName     |  Namn som du väljer för importpipelinen       |
|sourceUri     |  URI för lagringscontainern i målmiljön (källan för importpipelinen).<br/>Exempel: `https://targetstorage.blob.core.windows.net/transfer/`|
|keyVaultName     |  Namnet på målnyckelvalvet |
|sasTokenSecretName     |  Namnet på SAS-tokenhemligheten i målnyckelvalvet<br/>Exempel: acr importsas |

### <a name="import-options"></a>Alternativ för import

Egenskapen `options` för importpipelinen stöder valfria booleska värden. Följande värden rekommenderas:

|Parameter  |Värde  |
|---------|---------|
|alternativ | OverwriteTags – Skriva över befintliga måltaggar<br/>DeleteSourceBlobOnSuccess – Ta bort källlagringsbloben efter lyckad import till målregistret<br/>ContinueOnErrors – Fortsätt importera återstående artefakter i målregistret om en artefaktimport misslyckas.

### <a name="create-the-resource"></a>Skapa resursen

Kör [az deployment group create][az-deployment-group-create] för att skapa en resurs med namnet *importPipeline* enligt följande exempel. Med det första alternativet aktiverar exempelmallen som standard en systemtilldelningsidentitet i resursen ImportPipeline. 

Med det andra alternativet kan du ge resursen en användar tilldelad identitet. (Det går inte att skapa den användar tilldelade identiteten.)

Med båda alternativen konfigurerar mallen identiteten för åtkomst till SAS-token i importnyckelvalvet. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>Alternativ 1: Skapa resurs och aktivera systemtilldelningsidentitet

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json 
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>Alternativ 2: Skapa en resurs och ange en användar tilldelad identitet

I det här kommandot anger du resurs-ID:t för den användar tilldelade identiteten som en ytterligare parameter.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

Om du planerar att köra importen manuellt anteckningen av resurs-ID :t ( `id` ) för pipelinen. Du kan lagra det här värdet i en miljövariabel för senare användning genom att köra [kommandot az deployment group show.][az-deployment-group-show] Exempel:

```azurecli
IMPORT_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-pipelinerun-for-export-with-resource-manager"></a>Skapa PipelineKör för export med Resource Manager 

Skapa en PipelineRun-resurs för ditt källcontainerregister med hjälp Azure Resource Manager en malldistribution. Den här resursen kör den ExportPipeline-resurs som du skapade tidigare och exporterar angivna artefakter från containerregistret som en blob till ditt källlagringskonto.

Kopiera PipelineKör Resource Manager [till](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Export) en lokal mapp.

Ange följande parametervärden i filen `azuredeploy.parameters.json` :

|Parameter  |Värde  |
|---------|---------|
|registryName     | Namnet på ditt källcontainerregister      |
|pipelineRunName     |  Namn som du väljer för körningen       |
|pipelineResourceId     |  Resurs-ID för exportpipelinen.<br/>Exempel: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/exportPipelines/myExportPipeline`|
|targetName     |  Namn som du väljer för artefaktbloben som exporterades till ditt källlagringskonto, till exempel *myblob*
|Artefakter | Matris med källartefakter som ska överföras, som taggar eller manifest<br/>Exempel: `[samples/hello-world:v1", "samples/nginx:v1" , "myrepository@sha256:0a2e01852872..."]` |

Om du omdistribuerar en PipelineRun-resurs med identiska egenskaper måste du också använda [egenskapen forceUpdateTag.](#redeploy-pipelinerun-resource)

Kör [az deployment group create][az-deployment-group-create] för att skapa PipelineRun-resursen. Följande exempel ger distributionsexporten *namnetPipelineRun*.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json
```

Lagra resurs-ID:t för pipelinekörningen i en miljövariabel för senare användning:

```azurecli
EXPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

Det kan ta flera minuter för artefakter att exporteras. När distributionen är klar verifierar du artefaktexporten genom att visa den exporterade bloben i *överföringscontainern* för källlagringskontot. Kör till exempel kommandot [az storage blob list:][az-storage-blob-list]

```azurecli
az storage blob list \
  --account-name $SOURCE_SA \
  --container transfer \
  --output table
```

## <a name="transfer-blob-optional"></a>Överföra blob (valfritt) 

Använd Verktyget AzCopy eller andra metoder för att [överföra blobdata](../storage/common/storage-use-azcopy-v10.md#transfer-data) från källlagringskontot till mållagringskontot.

Följande kommando kopierar till [`azcopy copy`](../storage/common/storage-ref-azcopy-copy.md) exempel myblob från överföringscontainern i källkontot till *överföringscontainern* i målkontot.  Om bloben finns i målkontot skrivs den över. Autentiseringen använder SAS-token med lämpliga behörigheter för käll- och målcontainrarna. (Steg för att skapa token visas inte.)

```console
azcopy copy \
  'https://<source-storage-account-name>.blob.core.windows.net/transfer/myblob'$SOURCE_SAS \
  'https://<destination-storage-account-name>.blob.core.windows.net/transfer/myblob'$TARGET_SAS \
  --overwrite true
```

## <a name="trigger-importpipeline-resource"></a>Utlösa en ImportPipeline-resurs

Om du har aktiverat parametern för ImportPipeline (standardvärdet) utlöses pipelinen när bloben har kopierats `sourceTriggerStatus` till mållagringskontot. Det kan ta flera minuter för artefakter att importeras. När importen är klar kontrollerar du artefaktimporten genom att visa lagringsplatsen i målcontainerregistret. Kör till exempel [az acr repository list][az-acr-repository-list]:

```azurecli
az acr repository list --name <target-registry-name>
```

Om du inte har aktivera parametern för importpipelinen kör du `sourceTriggerStatus` resursen ImportPipeline manuellt, som du ser i följande avsnitt. 

## <a name="create-pipelinerun-for-import-with-resource-manager-optional"></a>Skapa PipelineRun för import med Resource Manager (valfritt) 
 
Du kan också använda en PipelineRun-resurs för att utlösa en ImportPipeline för artefaktimport till målcontainerregistret.

Kopiera PipelineKör Resource Manager [till](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Import) en lokal mapp.

Ange följande parametervärden i filen `azuredeploy.parameters.json` :

|Parameter  |Värde  |
|---------|---------|
|registryName     | Namnet på målcontainerregistret      |
|pipelineRunName     |  Namn som du väljer för körningen       |
|pipelineResourceId     |  Resurs-ID för importpipelinen.<br/>Exempel: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/importPipelines/myImportPipeline`       |
|sourceName     |  Namnet på den befintliga bloben för exporterade artefakter i ditt lagringskonto, till exempel *myblob*

Om du omdistribuerar en PipelineRun-resurs med identiska egenskaper måste du också använda [egenskapen forceUpdateTag.](#redeploy-pipelinerun-resource)

Kör [az deployment group create][az-deployment-group-create] för att köra resursen.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json
```

Lagra resurs-ID:t för pipelinekörningen i en miljövariabel för senare användning:

```azurecli
IMPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

När distributionen är klar verifierar du artefaktimporten genom att visa lagringsplatsen i målcontainerregistret. Kör till exempel [az acr repository list][az-acr-repository-list]:

```azurecli
az acr repository list --name <target-registry-name>
```

## <a name="redeploy-pipelinerun-resource"></a>Distribuera om pipelineKör resurs

Om du omdistribuerar en PipelineRun-resurs *med identiska* egenskaper måste du använda **egenskapen forceUpdateTag.** Den här egenskapen anger att PipelineRun-resursen ska återskapas även om konfigurationen inte har ändrats. Kontrollera att forceUpdateTag är olika varje gång du distribuerar om PipelineRun-resursen. Exemplet nedan återskapar en PipelineRun för export. Aktuell datetime används för att ange forceUpdateTag, vilket säkerställer att den här egenskapen alltid är unik.

```console
CURRENT_DATETIME=`date +"%Y-%m-%d:%T"`
```

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json \
  --parameters forceUpdateTag=$CURRENT_DATETIME
```

## <a name="delete-pipeline-resources"></a>Ta bort pipelineresurser

I följande exempelkommandon används [az resource delete för][az-resource-delete] att ta bort pipelineresurserna som skapades i den här artikeln. Resurs-ID:erna lagrades tidigare i miljövariabler.

```
# Delete export resources
az resource delete \
--resource-group $SOURCE_RG \
--ids $EXPORT_RES_ID $EXPORT_RUN_RES_ID \
--api-version 2019-12-01-preview

# Delete import resources
az resource delete \
--resource-group $TARGET_RG \
--ids $IMPORT_RES_ID $IMPORT_RUN_RES_ID \
--api-version 2019-12-01-preview
```

## <a name="troubleshooting"></a>Felsökning

* **Malldistribution eller fel**
  * Om en pipelinekörning misslyckas kan du titta på `pipelineRunErrorMessage` egenskapen för körningsresursen.
  * Vanliga fel vid malldistribution finns i Felsöka [distributioner av ARM-mallar](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
* **Problem med export eller import av lagringsblobar**
  * SAS-token kan ha upphört att gälla eller ha otillräcklig behörighet för den angivna export- eller importkörningen
  * Befintlig lagringsblob i källlagringskontot kanske inte skrivs över under flera exportkörningar. Bekräfta att alternativet OverwriteBlob är inställt i exportkörningen och att SAS-token har tillräcklig behörighet.
  * Lagringsblob i mållagringskontot kanske inte tas bort efter en lyckad importkörning. Bekräfta att alternativet DeleteBlobOnSuccess har angetts i importkörningen och att SAS-token har tillräcklig behörighet.
  * Lagringsblob har inte skapats eller tagits bort. Bekräfta att containern som anges i export- eller importkörningen finns, eller att den angivna lagringsbloben finns för manuell importkörning. 
* **AzCopy-problem**
  * Se [Felsöka AzCopy-problem.](../storage/common/storage-use-azcopy-configure.md)  
* **Problem med att överföra artefakter**
  * Inte alla artefakter, eller inga, överförs. Bekräfta stavning av artefakter i exportkörningen och namnet på bloben vid export- och importkörningar. Bekräfta att du överför högst 50 artefakter.
  * Pipelinekörningen kanske inte har slutförts. En export- eller importkörning kan ta lite tid. 
  * För andra pipelineproblem anger du [distributionskorrelations-ID:t](../azure-resource-manager/templates/deployment-history.md) för exportkörningen eller importkörningen till Azure Container Registry teamet.
* **Problem med att hämta avbildningen i en fysiskt isolerad miljö**
  * Om du ser fel angående externa lager eller försöker lösa mcr.microsoft.com när du försöker hämta en avbildning i en fysiskt isolerad miljö har ditt bildmanifest sannolikt icke-distributable lager. På grund av typen av fysiskt isolerad miljö kan dessa avbildningar ofta inte hämta. Du kan bekräfta att detta är fallet genom att kontrollera avbildningsmanifestet efter eventuella referenser till externa register. Om så är fallet måste du push-distribuera de icke-distributable-lagren till ditt offentliga moln-ACR innan du distribuerar en exportpipelinekörning för avbildningen. Vägledning om hur du gör detta finns i Hur gör jag för att [push-meddelanden av icke-distributable lager till ett register?](./container-registry-faq.md#how-do-i-push-non-distributable-layers-to-a-registry)

## <a name="next-steps"></a>Nästa steg

Information om hur du importerar enskilda containeravbildningar till ett Azure-containerregister från ett offentligt register eller ett annat privat register finns i [kommandoreferensen az acr import.][az-acr-import]

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/



<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-login]: /cli/azure/reference-index#az-login
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-secret-show]: /cli/azure/keyvault/secret#az-keyvault-secret-show
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-storage-container-generate-sas]: /cli/azure/storage/container#az-storage-container-generate-sas
[az-storage-blob-list]: /cli/azure/storage/blob#az-storage-blob-list
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-deployment-group-delete]: /cli/azure/deployment/group#az-deployment-group-delete
[az-deployment-group-show]: /cli/azure/deployment/group#az-deployment-group-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-import]: /cli/azure/acr#az-acr-import
[az-resource-delete]: /cli/azure/resource#az-resource-delete
