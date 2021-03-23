---
title: Överförings artefakter
description: Överföra samlingar med avbildningar eller andra artefakter från ett behållar register till ett annat register genom att skapa en överförings pipeline med Azure Storage-konton
ms.topic: article
ms.date: 10/07/2020
ms.custom: ''
ms.openlocfilehash: 4fe36366011fb790d25419ac46a54c4bf5ad94bf
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785826"
---
# <a name="transfer-artifacts-to-another-registry"></a>Överföra artefakter till ett annat register

Den här artikeln visar hur du överför samlingar av avbildningar eller andra register artefakter från ett Azure Container Registry till ett annat register. Käll-och mål registren kan finnas i samma eller olika prenumerationer, Active Directory klienter, Azure-moln eller fysiskt frånkopplade moln. 

Om du vill överföra artefakter skapar du en *överförings pipeline* som replikerar artefakter mellan två register med hjälp av [Blob Storage](../storage/blobs/storage-blobs-introduction.md):

* Artefakter från ett käll register exporteras till en BLOB i ett käll lagrings konto 
* Blobben kopieras från käll lagrings kontot till ett mål lagrings konto
* Blobben i mål lagrings kontot importeras som artefakter i mål registret. Du kan ställa in import pipelinen så att den utlöses när artefakt-bloben uppdateras i mål lagringen.

Överföring är perfekt för att kopiera innehåll mellan två Azure-behållar register i fysiskt frånkopplade moln, som åtgärdas av lagrings konton i varje moln. Om du i stället vill kopiera avbildningar från behållar register i anslutna moln, inklusive Docker Hub och andra moln leverantörer, rekommenderas [avbildnings import](container-registry-import-images.md) .

I den här artikeln använder du Azure Resource Manager mallar distributioner för att skapa och köra överförings pipelinen. Azure CLI används för att etablera de associerade resurserna, till exempel lagrings hemligheter. Azure CLI-version 2.2.0 eller senare rekommenderas. Om du behöver installera eller uppgradera CLI kan du läsa mer i [Installera Azure CLI][azure-cli].

Den här funktionen är tillgänglig i tjänst nivån **Premium** container Registry. Information om nivåer och gränser för register tjänster finns i [Azure Container Registry-nivåer](container-registry-skus.md).

> [!IMPORTANT]
> Den här funktionen finns för närvarande som en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="prerequisites"></a>Förutsättningar

* **Behållar** register – du behöver ett befintligt käll register med artefakter att överföra och ett mål register. ACR-överföring är avsedd för förflyttning över fysiskt frånkopplade moln. För testning kan käll-och mål registren vara i samma eller en annan Azure-prenumeration, Active Directory klient organisation eller molnet. 

   Om du behöver skapa ett register, se [snabb start: skapa ett privat behållar register med hjälp av Azure CLI](container-registry-get-started-azure-cli.md). 
* **Lagrings konton** – skapa käll-och mål lagrings konton i en prenumeration och plats som du väljer. I test syfte kan du använda samma prenumeration eller prenumerationer som käll-och mål register. I scenarier med flera moln kan du vanligt vis skapa ett separat lagrings konto i varje moln. 

  Om det behövs skapar du lagrings kontona med [Azure CLI](../storage/common/storage-account-create.md?tabs=azure-cli) eller andra verktyg. 

  Skapa en BLOB-behållare för artefakt överföring i varje konto. Du kan till exempel skapa en behållare med namnet *transfer*. Två eller flera överförings pipeliner kan dela samma lagrings konto, men bör använda olika lagrings behållar omfång.
* **Nyckel valv** – nyckel valv krävs för att lagra SAS-tokens hemligheter som används för att komma åt käll-och mål lagrings konton. Skapa käll-och mål nyckel valven i samma Azure-prenumeration eller prenumerationer som käll-och mål register. I demonstrations syfte förutsätter de mallar och kommandon som används i den här artikeln också att käll-och mål nyckel valven finns i samma resurs grupper som käll-och mål registren. Användning av vanliga resurs grupper krävs inte, men det fören klar de mallar och kommandon som används i den här artikeln.

   Om det behövs skapar du nyckel valv med [Azure CLI](../key-vault/secrets/quick-create-cli.md) eller andra verktyg.

* **Miljövariabler** – till exempel kommandon i den här artikeln anger du följande miljövariabler för käll-och mål miljö. Alla exempel är formaterade för bash-gränssnittet.
  ```console
  SOURCE_RG="<source-resource-group>"
  TARGET_RG="<target-resource-group>"
  SOURCE_KV="<source-key-vault>"
  TARGET_KV="<target-key-vault>"
  SOURCE_SA="<source-storage-account>"
  TARGET_SA="<target-storage-account>"
  ```

## <a name="scenario-overview"></a>Översikt över scenario

Du skapar följande tre pipeline-resurser för bild överföring mellan register. Alla skapas med hjälp av åtgärderna för att införa. Dessa resurser körs på *käll* -och *mål* register och lagrings konton. 

Lagrings autentisering använder SAS-token som hanteras som hemligheter i nyckel valv. Pipelinen använder hanterade identiteter för att läsa hemligheterna i valvena.

* **[ExportPipeline](#create-exportpipeline-with-resource-manager)** -långsiktig resurs som innehåller information på hög nivå om *käll* registret och lagrings kontot. Den här informationen inkluderar käll lagringens BLOB container-URI och nyckel valvet som hanterar käll-SAS-token. 
* **[ImportPipeline](#create-importpipeline-with-resource-manager)** -långsiktig resurs som innehåller information på hög nivå om *mål* registret och lagrings kontot. Den här informationen omfattar URI för mål lagrings-BLOB-behållare och nyckel valvet som hanterar målets SAS-token. En import utlösare är aktive rad som standard, så att pipelinen körs automatiskt när en artefakt-BLOB hamnar i mål lagrings behållaren. 
* **[PipelineRun](#create-pipelinerun-for-export-with-resource-manager)** – resurs som används för att anropa antingen en ExportPipeline-eller ImportPipeline-resurs.  
  * Du kör ExportPipeline manuellt genom att skapa en PipelineRun-resurs och ange de artefakter som ska exporteras.  
  * Om en import utlösare aktive ras körs ImportPipeline automatiskt. Den kan också köras manuellt med hjälp av en PipelineRun. 
  * För närvarande kan högst **50 artefakter** överföras med varje PipelineRun.

### <a name="things-to-know"></a>Saker att känna till
* ExportPipeline och ImportPipeline kommer vanligt vis att finnas i olika Active Directory klienter som är kopplade till käll-och mål molnen. Det här scenariot kräver separata hanterade identiteter och nyckel valv för export-och import resurserna. I test syfte kan dessa resurser placeras i samma moln, vilket delar identiteter.
* Som standard ger ExportPipeline och ImportPipeline-mallarna en systemtilldelad hanterad identitet för att få åtkomst till Key Vault-hemligheter. Mallarna ExportPipeline och ImportPipeline stöder också en användardefinierad identitet som du anger. 

## <a name="create-and-store-sas-keys"></a>Skapa och lagra SAS-nycklar

Överföring använder token för signatur för delad åtkomst (SAS) för att komma åt lagrings kontona i käll-och mål miljöerna. Generera och lagra tokens enligt beskrivningen i följande avsnitt.  

### <a name="generate-sas-token-for-export"></a>Generera SAS-token för export

Kör kommandot [AZ Storage container generate-SAS][az-storage-container-generate-sas] för att generera en SAS-token för behållaren i käll lagrings kontot som används för artefakt export.

*Rekommenderade token-behörigheter*: läsa, skriva, lista, Lägg till. 

I följande exempel tilldelas kommandots utdata till EXPORT_SAS-miljövariabeln, som föregås av "?"-symbolen. Uppdatera `--expiry` värdet för din miljö:

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

Lagra SAS-token i ditt käll Azure Key Vault med [AZ Key Vault Secret set][az-keyvault-secret-set]:

```azurecli
az keyvault secret set \
  --name acrexportsas \
  --value $EXPORT_SAS \
  --vault-name $SOURCE_KV 
```

### <a name="generate-sas-token-for-import"></a>Generera SAS-token för import

Kör kommandot [AZ Storage container generate-SAS][az-storage-container-generate-sas] för att generera en SAS-token för behållaren på mål lagrings kontot, som används för artefakt import.

*Rekommenderade token-behörigheter*: läsa, ta bort, lista

I följande exempel tilldelas kommandots utdata till IMPORT_SAS-miljövariabeln, som föregås av "?"-symbolen. Uppdatera `--expiry` värdet för din miljö:

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

Lagra SAS-token i ditt mål Azure Key Vault med [AZ Key Vault Secret set][az-keyvault-secret-set]:

```azurecli
az keyvault secret set \
  --name acrimportsas \
  --value $IMPORT_SAS \
  --vault-name $TARGET_KV 
```

## <a name="create-exportpipeline-with-resource-manager"></a>Skapa ExportPipeline med Resource Manager

Skapa en ExportPipeline-resurs för ditt käll behållar register med hjälp av Azure Resource Manager mall distribution.

Kopiera ExportPipeline Resource Manager- [mallfiler](https://github.com/Azure/acr/tree/master/docs/image-transfer/ExportPipelines) till en lokal mapp.

Ange följande parameter värden i filen `azuredeploy.parameters.json` :

|Parameter  |Värde  |
|---------|---------|
|registryName     | Namnet på käll behållar registret      |
|exportPipelineName     |  Namn som du väljer för export pipelinen       |
|targetUri     |  URI för lagrings behållaren i käll miljön (målet för export pipelinen).<br/>Exempel: `https://sourcestorage.blob.core.windows.net/transfer`       |
|keyVaultName     |  Namn på käll nyckel valvet  |
|sasTokenSecretName  | Namnet på hemligheten för SAS-token i käll nyckel valvet <br/>Exempel: acrexportsas

### <a name="export-options"></a>Export alternativ

`options`Egenskapen för export pipelines stöder valfria booleska värden. Följande värden rekommenderas:

|Parameter  |Värde  |
|---------|---------|
|alternativ | OverwriteBlobs – Skriv över befintliga mål-blobar<br/>ContinueOnErrors – Fortsätt att exportera återstående artefakter i käll registret om en artefakt export Miss lyckas.

### <a name="create-the-resource"></a>Skapa resursen

Kör [AZ Deployment Group Create][az-deployment-group-create] för att skapa en resurs med namnet *exportPipeline* som visas i följande exempel. Som standard, med det första alternativet, aktiverar exempel mal len en systemtilldelad identitet i ExportPipeline-resursen. 

Med det andra alternativet kan du ange resursen med en tilldelad identitet. (Skapandet av den användardefinierade identiteten visas inte.)

Med båda alternativen konfigurerar mallen identiteten för att få åtkomst till SAS-token i export nyckel valvet. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>Alternativ 1: skapa resurs och aktivera systemtilldelad identitet

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>Alternativ 2: skapa resurs och ge användare tilldelad identitet

I det här kommandot anger du resurs-ID för den användarspecifika identiteten som en ytterligare parameter.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

I kommandot utdata noterar du resurs-ID ( `id` ) för pipelinen. Du kan lagra det här värdet i en miljö variabel för senare användning genom att köra [AZ-distributions gruppen show][az-deployment-group-show]. Exempel:

```azurecli
EXPORT_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-importpipeline-with-resource-manager"></a>Skapa ImportPipeline med Resource Manager 

Skapa en ImportPipeline-resurs i ditt mål behållar register med hjälp av Azure Resource Manager Template Deployment. Som standard är pipelinen aktive rad för att importeras automatiskt när lagrings kontot i mål miljön har en artefakt-blob.

Kopiera ImportPipeline Resource Manager- [mallfiler](https://github.com/Azure/acr/tree/master/docs/image-transfer/ImportPipelines) till en lokal mapp.

Ange följande parameter värden i filen `azuredeploy.parameters.json` :

Parameter  |Värde  |
|---------|---------|
|registryName     | Namn på ditt mål behållar register      |
|importPipelineName     |  Namn du väljer för import pipelinen       |
|sourceUri     |  URI för lagrings behållaren i mål miljön (källan för import pipelinen).<br/>Exempel: `https://targetstorage.blob.core.windows.net/transfer/`|
|keyVaultName     |  Namn på mål nyckel valvet |
|sasTokenSecretName     |  Namnet på hemligheten för SAS-token i mål nyckel valvet<br/>Exempel: ACR-portarna) |

### <a name="import-options"></a>Alternativ för import

`options`Egenskapen för import pipelinen stöder valfria booleska värden. Följande värden rekommenderas:

|Parameter  |Värde  |
|---------|---------|
|alternativ | OverwriteTags – Skriv över befintliga mål etiketter<br/>DeleteSourceBlobOnSuccess – ta bort käll lagrings-bloben efter lyckad import till mål registret<br/>ContinueOnErrors – Fortsätt att importera återstående artefakter i mål registret om en artefakt import Miss lyckas.

### <a name="create-the-resource"></a>Skapa resursen

Kör [AZ Deployment Group Create][az-deployment-group-create] för att skapa en resurs med namnet *importPipeline* som visas i följande exempel. Som standard, med det första alternativet, aktiverar exempel mal len en systemtilldelad identitet i ImportPipeline-resursen. 

Med det andra alternativet kan du ange resursen med en tilldelad identitet. (Skapandet av den användardefinierade identiteten visas inte.)

Med båda alternativen konfigurerar mallen identiteten för att få åtkomst till SAS-token i import Key Vault. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>Alternativ 1: skapa resurs och aktivera systemtilldelad identitet

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json 
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>Alternativ 2: skapa resurs och ge användare tilldelad identitet

I det här kommandot anger du resurs-ID för den användarspecifika identiteten som en ytterligare parameter.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

Om du planerar att köra importen manuellt noterar du resurs-ID ( `id` ) för pipelinen. Du kan lagra det här värdet i en miljö variabel för senare användning genom att köra kommandot [AZ Deployment Group show][az-deployment-group-show] . Exempel:

```azurecli
IMPORT_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-pipelinerun-for-export-with-resource-manager"></a>Skapa PipelineRun för export med Resource Manager 

Skapa en PipelineRun-resurs för ditt käll behållar register med hjälp av Azure Resource Manager mall distribution. Den här resursen kör ExportPipeline-resursen som du skapade tidigare och exporterar angivna artefakter från behållar registret som en blob till ditt käll lagrings konto.

Kopiera PipelineRun Resource Manager- [mallfiler](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Export) till en lokal mapp.

Ange följande parameter värden i filen `azuredeploy.parameters.json` :

|Parameter  |Värde  |
|---------|---------|
|registryName     | Namnet på käll behållar registret      |
|pipelineRunName     |  Namn som du väljer för körningen       |
|pipelineResourceId     |  Resurs-ID för export pipelinen.<br/>Exempel: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/exportPipelines/myExportPipeline`|
|Målnamn     |  Namn som du väljer för artefakter som exporteras till ditt käll lagrings konto, till exempel en *BLOB*
|artefakter | Matris med käll artefakter som ska överföras, som taggar eller manifest sammandrag<br/>Exempel: `[samples/hello-world:v1", "samples/nginx:v1" , "myrepository@sha256:0a2e01852872..."]` |

Om du omdistribuerar en PipelineRun-resurs med identiska egenskaper måste du också använda egenskapen [forceUpdateTag](#redeploy-pipelinerun-resource) .

Kör [AZ Deployment Group Create][az-deployment-group-create] för att skapa PipelineRun-resursen. I följande exempel namnger distributionen *exportPipelineRun*.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json
```

För senare användning lagrar du resurs-ID för pipelinen som körs i en miljö variabel:

```azurecli
EXPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

Det kan ta flera minuter innan artefakter exporteras. När distributionen har slutförts verifierar du artefakt export genom att ange den exporterade blobben i *överförings* containern för käll lagrings kontot. Du kan till exempel köra kommandot [AZ Storage BLOB List][az-storage-blob-list] :

```azurecli
az storage blob list \
  --account-name $SOURCE_SA \
  --container transfer \
  --output table
```

## <a name="transfer-blob-optional"></a>Överför BLOB (valfritt) 

Använd AzCopy-verktyget eller andra metoder för att [överföra BLOB-data](../storage/common/storage-use-azcopy-v10.md#transfer-data) från käll lagrings kontot till mål lagrings kontot.

Följande kommando kopierar t. ex. [`azcopy copy`](../storage/common/storage-ref-azcopy-copy.md) BLOB från *överförings* containern i käll kontot till *överförings* containern i mål kontot. Om blobben finns i mål kontot skrivs den över. Autentisering använder SAS-token med lämpliga behörigheter för käll-och mål behållarna. (Steg för att skapa tokens visas inte.)

```console
azcopy copy \
  'https://<source-storage-account-name>.blob.core.windows.net/transfer/myblob'$SOURCE_SAS \
  'https://<destination-storage-account-name>.blob.core.windows.net/transfer/myblob'$TARGET_SAS \
  --overwrite true
```

## <a name="trigger-importpipeline-resource"></a>Utlös ImportPipeline-resurs

Om du har aktiverat `sourceTriggerStatus` parametern för ImportPipeline (standardvärdet) utlöses pipelinen när blobben har kopierats till mål lagrings kontot. Det kan ta flera minuter innan artefakter importeras. När importen har slutförts verifierar du artefakt import genom att lista databaserna i mål behållar registret. Du kan till exempel köra [AZ ACR databas lista][az-acr-repository-list]:

```azurecli
az acr repository list --name <target-registry-name>
```

Om du inte har aktiverat `sourceTriggerStatus` parametern för import pipelinen kör du ImportPipeline-resursen manuellt, som du ser i följande avsnitt. 

## <a name="create-pipelinerun-for-import-with-resource-manager-optional"></a>Skapa PipelineRun för import med Resource Manager (valfritt) 
 
Du kan också använda en PipelineRun-resurs för att utlösa en ImportPipeline för artefakt import till mål behållar registret.

Kopiera PipelineRun Resource Manager- [mallfiler](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Import) till en lokal mapp.

Ange följande parameter värden i filen `azuredeploy.parameters.json` :

|Parameter  |Värde  |
|---------|---------|
|registryName     | Namn på ditt mål behållar register      |
|pipelineRunName     |  Namn som du väljer för körningen       |
|pipelineResourceId     |  Resurs-ID för import pipelinen.<br/>Exempel: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/importPipelines/myImportPipeline`       |
|sourceName     |  Namnet på den befintliga blobben för exporterade artefakter i ditt lagrings konto, till exempel en *BLOB*

Om du omdistribuerar en PipelineRun-resurs med identiska egenskaper måste du också använda egenskapen [forceUpdateTag](#redeploy-pipelinerun-resource) .

Kör [AZ Deployment Group Create][az-deployment-group-create] för att köra resursen.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json
```

För senare användning lagrar du resurs-ID för pipelinen som körs i en miljö variabel:

```azurecli
IMPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

När distributionen har slutförts verifierar du artefakt import genom att lista databaserna i mål behållar registret. Du kan till exempel köra [AZ ACR databas lista][az-acr-repository-list]:

```azurecli
az acr repository list --name <target-registry-name>
```

## <a name="redeploy-pipelinerun-resource"></a>Distribuera om PipelineRun-resurs

Om du omdistribuerar en PipelineRun-resurs med *identiska egenskaper* måste du utnyttja egenskapen **forceUpdateTag** . Den här egenskapen anger att PipelineRun-resursen ska återskapas även om konfigurationen inte har ändrats. Kontrol lera att forceUpdateTag är olika varje gången du distribuerar om PipelineRun-resursen. Exemplet nedan återskapar en PipelineRun för export. Aktuellt datetime används för att ange forceUpdateTag, vilket säkerställer att egenskapen alltid är unik.

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

## <a name="delete-pipeline-resources"></a>Ta bort pipeline-resurser

I följande exempel kommandon används [AZ Resource Delete][az-resource-delete] för att ta bort pipelines-resurserna som skapats i den här artikeln. Resurs-ID: n var tidigare lagrade i miljövariabler.

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

* **Malldistribution fel eller fel**
  * Om en pipeline-körning Miss lyckas tittar du på `pipelineRunErrorMessage` egenskapen för körnings resursen.
  * Vanliga distributions fel för mallar finns i [Felsöka distributioner av arm-mallar](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
* **Problem med export eller import av Storage-blobbar**
  * SAS-token kan ha upphört att gälla eller har otillräcklig behörighet för den angivna exporten eller import körningen
  * Den befintliga lagrings-bloben i käll lagrings kontot kanske inte skrivs över under flera export körningar. Kontrol lera att alternativet OverwriteBlob är inställt på export körningen och att SAS-token har tillräcklig behörighet.
  * Det går inte att ta bort lagrings-bloben i mål lagrings kontot efter en lyckad import körning. Kontrol lera att alternativet DeleteBlobOnSuccess är inställt i import körningen och att SAS-token har tillräcklig behörighet.
  * Lagrings-BLOB har inte skapats eller tagits bort. Bekräfta att den behållare som anges i export-eller import körning finns eller att det finns en angiven lagrings-BLOB för manuell import körning. 
* **AzCopy problem**
  * Se [Felsöka AzCopy-problem](../storage/common/storage-use-azcopy-configure.md#troubleshoot-issues).  
* **Problem med att överföra artefakter**
  * Alla artefakter eller inga artefakter överförs. Bekräfta stavningen av artefakter i export körningen och namn på BLOB i export-och import körningar. Bekräfta att du överför högst 50 artefakter.
  * Pipeline-körningen kanske inte har slutförts. En export-eller import körning kan ta lite tid. 
  * För andra pipeline-problem anger du distributions [korrelations-ID: t](../azure-resource-manager/templates/deployment-history.md) för export körningen eller import-körningen till Azure Container Registry-teamet.
* **Problem med att hämta avbildningen i en fysiskt isolerad miljö**
  * Om du ser fel som rör externa lager eller försöker lösa mcr.microsoft.com när du försöker hämta en avbildning i en fysiskt isolerad miljö, innehåller avbildnings manifestet troligen icke-distribuerbara lager. På grund av typen av en fysiskt isolerad miljö kan de här avbildningarna ofta inte hämta. Du kan bekräfta att detta är fallet genom att kontrol lera avbildnings manifestet för alla referenser till externa register. Om så är fallet måste du skicka de icke-distribuerbara lagren till ditt offentliga moln ACR innan du distribuerar en export pipeline – kör för avbildningen. Anvisningar om hur du gör detta finns i [Hur gör jag för att push-överförda lager till ett register?](./container-registry-faq.md#how-do-i-push-non-distributable-layers-to-a-registry)

## <a name="next-steps"></a>Nästa steg

Om du vill importera enskilda behållar avbildningar till ett Azure Container Registry från ett offentligt register eller ett annat privat register, se kommando referensen [AZ ACR import][az-acr-import] .

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
