---
title: Felsöka Azure Image Builder-tjänsten
description: Felsök vanliga problem och fel när du använder tjänsten Azure VM Image Builder
author: cynthn
ms.author: danis
ms.date: 10/02/2020
ms.topic: troubleshooting
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: f76c3e6c739ae4dd13355d350a01b878e4d4f360
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666216"
---
# <a name="troubleshoot-azure-image-builder-service"></a>Felsöka Azure Image Builder-tjänsten

Den här artikeln hjälper dig att felsöka och lösa vanliga problem som kan uppstå när du använder Azure Image Builder-tjänsten.

AIB-problem kan inträffa i två områden:
- Överföring av bildmall
- Avbildnings version

## <a name="troubleshoot-image-template-submission-errors"></a>Felsöka fel vid sändning av bildfil

Fel vid sändning av bild mal len returneras endast vid överföring. Det finns ingen fel logg för sändnings fel för bild mal len. Om det uppstod ett fel vid överföringen kan du returnera felet genom att kontrol lera mallens status, särskilt granska `ProvisioningState` och `ProvisioningErrorMessage` / `provisioningError` .

```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object ProvisioningState, ProvisioningErrorMessage
```
> [!NOTE]
> För PowerShell måste du installera [Azure Image Builder PowerShell-modulerna](../windows/image-builder-powershell.md#prerequisites).

I följande avsnitt finns information om problem lösning för att skicka fel i vanliga avbildnings mallar.

### <a name="updateupgrade-of-image-templates-is-currently-not-supported"></a>Uppdatering/uppgradering av bildmallar stöds inte för närvarande

#### <a name="error"></a>Fel

```text
'Conflict'. Details: Update/Upgrade of image templates is currently not supported
```

#### <a name="cause"></a>Orsak

Mallen finns redan.

#### <a name="solution"></a>Lösning

Om du skickar en mall för avbildnings konfiguration och det inte går att skicka en mall finns det fortfarande en artefakt som misslyckats. Ta bort mallen som misslyckades.

### <a name="the-resource-operation-completed-with-terminal-provisioning-state-failed"></a>Resurs åtgärden som slutfördes med etablerings statusen för terminalen misslyckades

#### <a name="error"></a>Fel

```text
Microsoft.VirtualMachineImages/imageTemplates 'helloImageTemplateforSIG01' failed with message '{
  "status": "Failed",
  "error": {
    "code": "ResourceDeploymentFailure",
    "message": "The resource operation completed with terminal provisioning state 'Failed'.",
    "details": [
      {
        "code": "InternalOperationError",
        "message": "Internal error occurred."
```
#### <a name="cause"></a>Orsak

I de flesta fall uppstår fel i resurs distributionen på grund av saknade behörigheter.

#### <a name="solution"></a>Lösning

Beroende på ditt scenario kan Azure Image Builder behöva behörigheter för att:
- Käll avbildning eller resurs grupp för delade avbildnings Galleri
- Distributions avbildning eller Galleri resurs för delad avbildning
- Lagrings kontot, behållaren eller blobben som fil anpassningen har åtkomst till. 

Mer information om hur du konfigurerar behörigheter finns i [Konfigurera Azure Image Builder-tjänst behörigheter med Azure CLI](image-builder-permissions-cli.md) eller [Konfigurera Azure avbildning Builder-tjänst behörigheter med PowerShell](image-builder-permissions-powershell.md)

### <a name="error-getting-managed-image"></a>Fel vid hämtning av hanterad avbildning

#### <a name="error"></a>Fel

```text
Build (Managed Image) step failed: Error getting Managed Image '/subscriptions/.../providers/Microsoft.Compute/images/mymanagedmg1': Error getting managed image (...): compute.
ImagesClient#Get: Failure responding to request: StatusCode=403 -- Original Error: autorest/azure: Service returned an error.
Status=403 Code="AuthorizationFailed" Message="The client '......' with object id '......' does not have authorization to perform action 'Microsoft.Compute/images/read' over scope 
```
#### <a name="cause"></a>Orsak

Behörigheter saknas.

#### <a name="solution"></a>Lösning

Beroende på ditt scenario kan Azure Image Builder behöva behörigheter för att:
* Käll avbildning eller resurs grupp för delade avbildnings Galleri
* Distributions avbildning eller Galleri resurs för delad avbildning
* Lagrings kontot, behållaren eller blobben som fil anpassningen har åtkomst till. 

Mer information om hur du konfigurerar behörigheter finns i [Konfigurera Azure Image Builder-tjänst behörigheter med Azure CLI](image-builder-permissions-cli.md) eller [Konfigurera Azure avbildning Builder-tjänst behörigheter med PowerShell](image-builder-permissions-powershell.md)

### <a name="build--step-failed-for-image-version"></a>Build-steget misslyckades för avbildnings versionen

#### <a name="error"></a>Fel
```text
Build (Shared Image Version) step failed for Image Version '/subscriptions/.../providers/Microsoft.Compute/galleries/.../images/... /versions/0.23768.4001': Error getting Image Version '/subscriptions/.../resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001': Error getting image version '... :0.23768.4001': compute.GalleryImageVersionsClient#Get: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. 
Status=404 Code="ResourceNotFound" Message="The Resource 'Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001' under resource group '<rgName>' was not found."
```
#### <a name="cause"></a>Orsak

Det går inte att hitta käll bilden i Azure Image Builder.

#### <a name="solution"></a>Lösning

Se till att käll avbildningen är korrekt och finns på platsen för Azure Image Builder-tjänsten.

### <a name="downloading-external-file-to-local-file"></a>Laddar ned extern fil till lokal fil

#### <a name="error"></a>Fel

```text
Downloading external file (<myFile>) to local file (xxxxx.0.customizer.fp) [attempt 1 of 10] failed: Error downloading '<myFile>' to 'xxxxx.0.customizer.fp'..
```

#### <a name="cause"></a>Orsak

Fil namnet eller platsen är felaktig eller också går det inte att hitta platsen.

#### <a name="solution"></a>Lösning

Se till att filen kan kommas åt. Kontrol lera att namnet och platsen stämmer.

## <a name="troubleshoot-build-failures"></a>Felsöka build-fel

För avbildnings Bygg fel kan du hämta felet från och `lastrunstatus` Granska informationen i anpassnings loggen. log.


```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object LastRunStatus, LastRunStatusMessage
```

### <a name="customization-log"></a>Anpassnings logg

När avbildnings versionen körs skapas loggar och lagras i ett lagrings konto. Azure Image Builder skapar lagrings kontot i den tillfälliga resurs gruppen när du skapar en avbildnings mal len artefakt.

Namnet på lagrings kontot använder följande mönster: **IT_ \<ImageResourceGroupName\> _\<TemplateName\>_ \<GUID\>**

Till exempel *IT_aibmdi_helloImageTemplateLinux01*.

Du kan visa anpassning. log i lagrings kontot i resurs gruppen genom att välja **lagrings konto**  >  **blobbar**  >  `packerlogs` .  Välj sedan **katalog > anpassning. log**.


### <a name="understanding-the-customization-log"></a>Förstå anpassnings loggen

Loggen är utförlig. Den täcker avbildnings versionen, inklusive eventuella problem med avbildnings distributionen, t. ex. replikering av delat avbildnings Galleri. Felen visas i fel meddelandet för bildmallens status.

Anpassningen. log innehåller följande steg:

1. Distribuera den virtuella datorn och beroenden med ARM-mallar till IT_ mellanlagring av resurs gruppen. I det här steget ingår flera inlägg i Azure Image Builder-resurs leverantören:
    ```text
    Azure request method="POST" request="https://management.azure.com/subscriptions/<subID>/resourceGroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-cccc-cccc-ccccccc/providers/Microsoft.Storage/storageAccounts
    ..
    PACKER OUT ==> azure-arm: Deploying deployment template ...
    ..
    ```

2. Status för distributions fasen. I det här steget ingår status för varje resurs distribution:
    ```text
    PACKER ERR 2020/04/30 23:28:50 packer: 2020/04/30 23:28:50 Azure request method="GET" request="https://management.azure.com/subscriptions/<subID>/resourcegroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-4505-ae28-6661e43fac48/providers/Microsoft.Resources/deployments/pkrdp51lc0339jg/operationStatuses/08586133176207523519?[REDACTED]" body=""
    ```

3. Anslut till steget Bygg VM.

    Om Windows, tjänsten Azure Image Builder ansluter med WinRM:
    ```text
    PACKER ERR 2020/04/30 23:30:50 packer: 2020/04/30 23:30:50 Waiting for WinRM, up to timeout: 10m0s
    ..
    PACKER OUT     azure-arm: WinRM connected.
    ```

    Om Linux så ansluter Azure Image Builder-tjänsten med SSH:
    ```text
    PACKER OUT ==> azure-arm: Waiting for SSH to become available...
    PACKER ERR 2019/12/10 17:20:51 packer: 2020/04/10 17:20:51 [INFO] Waiting for SSH, up to timeout: 20m0s
    PACKER OUT ==> azure-arm: Connected to SSH!
    ```

4. Kör anpassnings steg. När anpassningar körs kan du identifiera dem genom att granska anpassningen. log. Sök efter *(telemetri)*.
    ```text
    (telemetry) Starting provisioner windows-update
    (telemetry) ending windows-update
    (telemetry) Starting provisioner powershell
    (telemetry) ending powershell
    (telemetry) Starting provisioner file
    (telemetry) ending file
    (telemetry) Starting provisioner windows-restart
    (telemetry) ending windows-restart
    
    (telemetry) Finalizing. - This means the build hasfinished
    ```
5. Avetablerings steg. Azure Image Builder lägger till en dold anpassning. Det här avetablerings steget ansvarar för att förbereda den virtuella datorn för inetablering. Den kör Windows Sysprep (med c:\DeprovisioningScript.ps1) eller i Linux waagent deetablering (med/tmp/DeprovisioningScript.sh). 

    Exempel:
    ```text
    PACKER ERR 2020/03/04 23:05:04 [INFO] (telemetry) Starting provisioner powershell
    PACKER ERR 2020/03/04 23:05:04 packer: 2020/03/04 23:05:04 Found command: if( TEST-PATH c:\DeprovisioningScript.ps1 ){cat c:\DeprovisioningScript.ps1} else {echo "Deprovisioning script [c:\DeprovisioningScript.ps1] could not be found. Image build may fail or the VM created from the Image may not boot. Please make sure the deprovisioning script is not accidentally deleted by a Customizer in the Template."}
    ```

6. Rensa steg. När versionen har slutförts tas Azure Image Builder-resurser bort.
    ```text
    PACKER ERR 2020/02/04 02:04:23 packer: 2020/02/04 02:04:23 Azure request method="DELETE" request="https://management.azure.com/subscriptions/<subId>/resourceGroups/IT_aibDevOpsImg_t_vvvvvvv_yyyyyy-de5f-4f7c-92f2-xxxxxxxx/providers/Microsoft.Network/networkInterfaces/pkrnijamvpo08eo?[REDACTED]" body=""
    ```
## <a name="tips-for-troubleshooting-scriptinline-customization"></a>Tips för fel sökning av skript/infogad anpassning
- Testa koden innan du anger den till Image Builder
- Se till att Azure Policys och brand väggarna tillåter anslutning till fjär resurser.
- Genom att skriva kommentarer till-konsolen, som att använda `Write-Host` eller `echo` , kan du söka i anpassning. log.

## <a name="troubleshoot-common-build-errors"></a>Felsök vanliga build-fel

### <a name="packer-build-command-failure"></a>Kommando haveri för paket version

#### <a name="error"></a>Fel

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-04-30T23:24:06.756985789Z",
   "endTime": "2020-04-30T23:39:14.268729811Z",
   "runState": "Failed",
   "message": "Failed while waiting for packerizer: Microservice has failed: Failed while processing request: Error when executing packerizer: Packer build command has failed: exit status 1. During the image build, a failure has occurred, please review the build log to identify which build/customization step failed. For more troubleshooting steps go to https://aka.ms/azvmimagebuilderts. Image Build log location: https://xxxxxxxxxx.blob.core.windows.net/packerlogs/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/customization.log. OperationId: xxxxxx-5a8c-4379-xxxx-8d85493bc791. Use this operationId to search packer logs."
```

#### <a name="cause"></a>Orsak

Anpassnings problem.

#### <a name="solution"></a>Lösning

Granska loggen för att hitta de anpassade felen. Sök efter *(telemetri)*. 

Exempel:
```text
(telemetry) Starting provisioner windows-update
(telemetry) ending windows-update
(telemetry) Starting provisioner powershell
(telemetry) ending powershell
(telemetry) Starting provisioner file
(telemetry) ending file
(telemetry) Starting provisioner windows-restart
(telemetry) ending windows-restart

(telemetry) Finalizing. - This means the build has finished
```

### <a name="timeout-exceeded"></a>Tids gränsen överskreds

#### <a name="error"></a>Fel

```text
Deployment failed. Correlation ID: xxxxx-xxxx-xxxx-xxxx-xxxxxxxxx. Failed in building/customizing image: Failed while waiting for packerizer: Timeout waiting for microservice to complete: 'context deadline exceeded'
```

#### <a name="cause"></a>Orsak

Bygget överskred tids gränsen. Det här felet visas i "lastrunstatus".

#### <a name="solution"></a>Lösning

1. Granska anpassning. log. Identifiera den sista anpassnings funktionen som ska köras. Sök efter `(telemetry)` Start längst ned i loggen. 

2. Kontrol lera skript anpassningar. Anpassningarna kanske inte undertrycker användar åtgärder för kommandon, till exempel `quiet` alternativ. Till exempel, `apt-get install -y` resulterar i att skript körningen väntar på användar interaktion.

3. Om du använder `File` anpassaren för att hämta artefakter som är större än 20 MB, se avsnittet om lösningar.

4. Granska fel och beroenden i skriptet som kan göra att skriptet väntar.

5. Om du förväntar dig att anpassningarna behöver mer tid ökar du [buildTimeoutInMinutes](image-builder-json.md). Standardvärdet är fyra timmar.

6. Om du har resurs intensiva åtgärder, t. ex. genom att hämta gigabyte av filer, bör du tänka på den underliggande versionen av VM-storleken. Tjänsten använder en Standard_D1_v2 virtuell dator. Den virtuella datorn har 1 vCPU och 3,5 GB minne. Om du hämtar 50 GB kommer detta troligen att belasta VM-resurserna och orsaka kommunikations fel mellan Azure Image Builder-tjänsten och skapa en virtuell dator. Försök skapa igen med en större virtuell minnes dator genom att ange [VM_Size](image-builder-json.md#vmprofile).

### <a name="long-file-download-time"></a>Lång fil hämtnings tid

#### <a name="error"></a>Fel
```text
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 826 B / 826000 B  1.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 1652 B / 826000 B  2.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
..
hours later...
..
myBigFile.zip 826000 B / 826000 B  100.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
```
#### <a name="cause"></a>Orsak 

Fil anpassaren laddar ned en stor fil.

#### <a name="solution"></a>Lösning

Fil anpassningen är bara lämplig för små fil hämtningar som är mindre än 20 MB. Använd ett skript eller ett infogat kommando för större fil hämtningar. I Linux kan du till exempel använda `wget` eller `curl` . I Windows kan du använda `Invoke-WebRequest` .

### <a name="error-waiting-on-shared-image-gallery"></a>Fel vid väntan på delad avbildnings Galleri

#### <a name="error"></a>Fel

```text
Deployment failed. Correlation ID: XXXXXX-XXXX-XXXXXX-XXXX-XXXXXX. Failed in distributing 1 images out of total 1: {[Error 0] [Distribute 0] Error publishing MDI to shared image gallery:/subscriptions/<subId>/resourceGroups/xxxxxx/providers/Microsoft.Compute/galleries/xxxxx/images/xxxxxx, Location:eastus. Error: Error returned from SIG client while publishing MDI to shared image gallery for dstImageLocation: eastus, dstSubscription: <subId>, dstResourceGroupName: XXXXXX, dstGalleryName: XXXXXX, dstGalleryImageName: XXXXXX. Error: Error waiting on shared image gallery future for resource group: XXXXXX, gallery name: XXXXXX, gallery image name: XXXXXX.Error: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded}
```

#### <a name="cause"></a>Orsak

Tids gränsen för bildverktyget nåddes i väntan på att avbildningen skulle läggas till och replikeras till det delade avbildnings galleriet (SIG). Om avbildningen matas in i SIG, kan det antas att image-versionen har slutförts. Den övergripande processen misslyckades dock eftersom Image Builder väntade på ett delat avbildnings Galleri för att slutföra replikeringen. Även om versionen har misslyckats fortsätter replikeringen. Du kan hämta egenskaperna för avbildnings versionen genom att kontrol lera distributionens *runOutput*.

```bash
$runOutputName=<distributionRunOutput>
az resource show \
    --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/$imageTemplateName/runOutputs/$runOutputName"  \
    --api-version=2019-05-01-preview
```

#### <a name="solution"></a>Lösning

Öka **buildTimeoutInMinutes**.
 
### <a name="low-windows-resource-information-events"></a>Låga Windows Resource information-händelser

#### <a name="error"></a>Fel

```text
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 1% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 51% cpu; 35% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 36% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 90% cpu; 32% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for the Windows Modules Installer to exit...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'PowerShell -ExecutionPolicy Bypass -OutputFormat Text -File C:/Windows/Temp/packer-windows-update-elevated.ps1' exited with code: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Restarting the machine...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Waiting for machine to become available...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] starting remote command: shutdown.exe -f -r -t 0 -c "packer restart"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'shutdown.exe -f -r -t 0 -c "packer restart"' exited with code: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: A system shutdown is in progress.(1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] starting remote command: shutdown.exe -f -r -t 60 -c "packer restart test"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] command 'shutdown.exe -f -r -t 60 -c "packer restart test"' exited with code: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 Retryable error: Machine not yet available (exit status 1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT Build 'azure-arm' errored: unexpected EOF
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT 
```
#### <a name="cause"></a>Orsak

Resurs överbelastning. Det här problemet visas vanligt vis med Windows Update som körs med standard storleken för build VM D1_V2.

#### <a name="solution"></a>Lösning

Öka storleken på den virtuella datorn.

### <a name="builds-finished-but-no-artifacts-were-created"></a>Skapar färdiga men inga artefakter skapades

#### <a name="error"></a>Fel

```text
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT Build 'azure-arm' errored: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 machine readable: azure-arm,error []string{"Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded"}
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT --> azure-arm: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 Cancelling builder after context cancellation context canceled
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 [INFO] (telemetry) Finalizing.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:24 waiting for all plugin processes to complete...
Done exporting Packer logs to Azure for Packer prefix: [a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT
```
#### <a name="cause"></a>Orsak

Timeout uppstod i väntan på att nödvändiga Azure-resurser skulle skapas.

#### <a name="solution"></a>Lösning

Kör om versionen för att försöka igen.

### <a name="resource-not-found"></a>Resursen hittades inte

#### <a name="error"></a>Fel

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-05-01T00:13:52.599326198Z",
   "endTime": "2020-05-01T00:15:13.62366898Z",
   "runState": "Failed",
   "message": "network.InterfacesClient#UpdateTags: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. Status=404 Code=\"ResourceNotFound\" Message=\"The Resource 'Microsoft.Network/networkInterfaces/aibpls7lz2e.nic.4609d697-be0a-4cb0-86af-49b6fe877fe1' under resource group 'IT_aibImageRG200_window2019VnetTemplate01_9988723b-af56-413a-9006-84130af0e9df' was not found.\""
  },
```
#### <a name="cause"></a>Orsak

Behörigheter saknas.

#### <a name="solution"></a>Lösning

Kontrol lera att Azure Image Builder har alla behörigheter som krävs. 

Mer information om hur du konfigurerar behörigheter finns i [Konfigurera Azure Image Builder-tjänst behörigheter med Azure CLI](image-builder-permissions-cli.md) eller [Konfigurera Azure avbildning Builder-tjänst behörigheter med PowerShell](image-builder-permissions-powershell.md)

### <a name="sysprep-timing"></a>Sysprep-timing

#### <a name="error"></a>Fel

```text
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service RdAgent) -and ((Get-Service RdAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprepping VM ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: & $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while($true) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Write-Output $imageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Start-Sleep -s 5
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprep complete ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (RdAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Sysprepping VM ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_COMPLETE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: Get-Service : Cannot find any service with service name 'WindowsAzureGuestAgent'.
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: At C:\DeprovisioningScript.ps1:6 char:9
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: + while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service Window ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: +         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + CategoryInfo          : ObjectNotFound: (WindowsAzureGuestAgent:String) [Get-Service], ServiceCommandException
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + FullyQualifiedErrorId : NoServiceFoundForGivenName,Microsoft.PowerShell.Commands.GetServiceCommand
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 Cancelling builder after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT Cancelling build after receiving terminated
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling provisioning due to context cancellation: context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: 
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling hook after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
```
#### <a name="cause"></a>Orsak

Orsaken kan vara ett problem med tids inställningen på grund av D1_V2 storleken på den virtuella datorn. Om anpassningar är begränsade och körs på mindre än tre sekunder körs Sysprep-kommandon av Azure Image Builder för att avetablera. När Azure Image Builder avetablerar, söker Sysprep-kommandot efter *WindowsAzureGuestAgent*, som kanske inte är helt installerat som orsakar tids inställningen. 

#### <a name="solution"></a>Lösning

Öka storleken på den virtuella datorn. Alternativt kan du lägga till en 60-sekunders anpassning av PowerShell-sömn för att undvika tids problemet.

### <a name="cancelling-builder-after-context-cancellation-context-canceled"></a>Avbryter Builder efter att kontexten för avbrutet kontext avbrutits

#### <a name="error"></a>Fel
```text
PACKER ERR 2020/03/26 22:11:23 Cancelling builder after context cancellation context canceled
PACKER OUT Cancelling build after receiving terminated
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling hook after context cancellation context canceled
..
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling provisioning due to context cancellation: context canceled
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [ERROR] Remote command exited without exit status or exit signal.
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [INFO] RPC endpoint: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] 148974 bytes written for 'stdout'
PACKER ERR 2020/03/26 22:11:25 [INFO] 0 bytes written for 'stderr'
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC client: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC endpoint: Communicator ended with: 2300218
```
#### <a name="cause"></a>Orsak
Tjänsten Image Builder använder port 22 (Linux) eller 5986 (Windows) för att ansluta till den virtuella build-datorn, detta inträffar när tjänsten är frånkopplad från den virtuella build-datorn under en avbildnings version. Orsaker till från koppling kan variera, men aktivering eller konfiguration av brand väggar i skript kan blockera portarna ovan.

#### <a name="solution"></a>Lösning
Granska skripten för brand Väggs ändringar/-aktivering eller ändringar av SSH eller WinRM och se till att alla ändringar tillåter konstant anslutning mellan tjänsten och skapa en virtuell dator på portarna ovan. Om du vill ha mer information om nätverk i Image Builder kontrollerar du [kraven](./image-builder-networking.md).

## <a name="devops-task"></a>DevOps-uppgift 

### <a name="troubleshooting-the-task"></a>Felsöka uppgiften
Aktiviteten kommer bara att Miss lyckas om ett fel inträffar under anpassningen, när aktiviteten rapporterar fel och du lämnar resurs gruppen för mellanlagring, med loggarna, så att du kan identifiera problemet. 

Om du vill hitta loggen måste du känna till mallnamnet, gå in i pipeline > det gick inte att bygga > detalj rikedom i AIB DevOps-aktiviteten så kommer du att se loggen och namnet på mallen:
```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
``` 

Gå till portalen, Sök efter mallnamnet i resurs gruppen och leta sedan efter resurs gruppen med IT_ *.
Gå till lagrings kontot > blobbar > behållare > loggar.

### <a name="troubleshooting-successful-builds"></a>Felsöka lyckade versioner
Det kanske finns fall där du behöver undersöka lyckade versioner och vill granska loggen. Om avbildningen har skapats, tas den tillfälliga resurs gruppen som innehåller loggarna bort som en del av rensningen. Men det du kan göra är att införa en ström spar läge efter det infogade kommandot och sedan hämta loggarna när versionen har pausats. Detta gör du genom att följa dessa steg:
 
1. Uppdatera det infogade kommandot och Lägg till: Write-Host/ECHO "vila" – Detta gör att du kan söka i loggen
2. Lägg till en ström spar läge för minst 10mins kan du använda kommandot [Start-sömn](/powershell/module/microsoft.powershell.utility/start-sleep)eller `Sleep` Linux.
3. Använd metoden ovan för att identifiera logg platsen och fortsätt sedan att ladda ned/kontrol lera loggen tills den får ström spar läge.


### <a name="operation-was-canceled"></a>Åtgärden avbröts

#### <a name="error"></a>Fel

```text
2020-05-05T18:28:24.9280196Z ##[section]Starting: Azure VM Image Builder Task
2020-05-05T18:28:24.9609966Z ==============================================================================
2020-05-05T18:28:24.9610739Z Task         : Azure VM Image Builder Test(Preview)
2020-05-05T18:28:24.9611277Z Description  : Build images using Azure Image Builder resource provider.
2020-05-05T18:28:24.9611608Z Version      : 1.0.18
2020-05-05T18:28:24.9612003Z Author       : Microsoft Corporation
2020-05-05T18:28:24.9612718Z Help         : For documentation, and end to end example, please visit: http://aka.ms/azvmimagebuilderdevops
2020-05-05T18:28:24.9613390Z ==============================================================================
2020-05-05T18:28:26.0651512Z start reading task parameters...
2020-05-05T18:28:26.0673377Z found build at:  d:\a\r1\a\_AppsAndImageBuilder\webApp
2020-05-05T18:28:26.0708785Z end reading parameters
2020-05-05T18:28:26.0745447Z getting storage account details for aibstagstor1565047758
2020-05-05T18:28:29.8812270Z created archive d:\a\_temp\temp_web_package_09737279437949953.zip
2020-05-05T18:28:33.1568013Z Source for image:  { type: 'PlatformImage',
2020-05-05T18:28:33.1584131Z   publisher: 'MicrosoftWindowsServer',
2020-05-05T18:28:33.1585965Z   offer: 'WindowsServer',
2020-05-05T18:28:33.1592768Z   sku: '2016-Datacenter',
2020-05-05T18:28:33.1594191Z   version: '14393.3630.2004101604' }
2020-05-05T18:28:33.1595387Z template name:  t_1588703313152
2020-05-05T18:28:33.1597453Z starting put template...
2020-05-05T18:28:52.9278603Z put template:  Succeeded
2020-05-05T18:28:52.9281282Z starting run template...
2020-05-05T19:33:14.3923479Z ##[error]The operation was canceled.
2020-05-05T19:33:14.3939721Z ##[section]Finishing: Azure VM Image Builder Task
```
#### <a name="cause"></a>Orsak

Om versionen inte har avbrutits av en användare avbröts den av Azure DevOps user agent. Förmodligen har tids gränsen på 1 timme uppstått på grund av Azure DevOps-funktioner. Om du använder ett privat projekt och en agent får du 60 minuter för bygg tiden. Om versionen överskrider tids gränsen avbryter DevOps aktiviteten som körs.

Mer information om funktioner och begränsningar för Azure DevOps finns i [Microsoft-värdbaserade agenter](/azure/devops/pipelines/agents/hosted#capabilities-and-limitations)
 
#### <a name="solution"></a>Lösning

Du kan vara värd för dina egna DevOps-agenter eller se till att minska tiden för din version. Om du till exempel distribuerar till det delade avbildnings galleriet replikerar du till en region. Om du vill replikera asynkront. 

### <a name="slow-windows-logon-please-wait-for-the-windows-modules-installer"></a>Långsam Windows-inloggning: vänta medan installations programmet för Windows-moduler

#### <a name="error"></a>Fel
När du har skapat en Windows 10-avbildning med Image Builder skapar du en virtuell dator från avbildningen, sedan RDP och måste vänta minuter vid den första inloggningen se en blå skärm med meddelandet:
```text
Please wait for the Windows Modules Installer
```

#### <a name="solution"></a>Lösning
I första hand i Image-versionen kontrollerar du att det inte finns några väntande omstarter som krävs genom att lägga till en Windows-startanpassare som den senaste anpassningen och att all program varu installation är klar. Slutligen lägger du till [/läge: alternativet för virtuell dator](/windows-hardware/manufacture/desktop/sysprep-command-line-options) till standard-Sysprep som AIB använder, se nedan. virtuella datorer som skapats från AIB-avbildningar skapar inte korrekt > åsidosätter kommandona  

 
## <a name="vms-created-from-aib-images-do-not-create-successfully"></a>Det gick inte att skapa virtuella datorer som skapats från AIB-avbildningar

Som standard körs *avetablerings* kod i Azure Image Builder i slutet av varje bild anpassnings fas för att *generalisera* avbildningen. Generalize är en process där avbildningen har kon figurer ATS att återanvändas för att skapa flera virtuella datorer och du kan överföra inställningar för virtuella datorer, till exempel värdnamn, användar namn, osv. För Windows kör Azure Image Builder *Sysprep* och för Linux Azure Image Builder körs `waagent -deprovision` . 

För Windows använder Azure Image Builder ett allmänt Sysprep-kommando. Detta är dock inte lämpligt för varje lyckad Windows-generalisering. Med Azure Image Builder kan du anpassa Sysprep-kommandot. Obs! Azure Image Builder är ett verktyg för bild automatisering. Det ansvarar för att köra Sysprep-kommandot. Men du kan behöva olika Sysprep-kommandon för att kunna använda avbildningen igen. För Linux använder Azure Image Builder ett allmänt `waagent -deprovision+user` kommando. Mer information finns i [Microsoft Azure Linux Agent-dokumentation](https://github.com/Azure/WALinuxAgent#command-line-options).

Om du migrerar en befintlig anpassning och använder olika Sysprep/waagent-kommandon kan du prova de allmänna kommandona för Image Builder. Om det inte går att skapa en virtuell dator kan du använda dina tidigare Sysprep/waagent-kommandon.

> [!NOTE]
> Om AIB skapar en anpassad Windows-avbildning och du skapar en virtuell dator från den, kommer den virtuella datorn inte att skapas (till exempel om kommandot för att skapa virtuella datorer inte slutförs/tids gräns). du måste gå igenom Windows Server Sysprep-dokumentationen. Eller så kan du göra en supportbegäran med support teamet för Windows Server Sysprep, som kan felsöka och Avisera om rätt Sysprep-kommando.

### <a name="command-locations-and-filenames"></a>Kommando platser och fil namn

Windows:

```
c:\DeprovisioningScript.ps1
```

Linux:
```bash
/tmp/DeprovisioningScript.sh
```

### <a name="sysprep-command-windows"></a>Sysprep-kommando: Windows

```PowerShell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```

### <a name="deprovision-command-linux"></a>Avetablerings kommando: Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

### <a name="overriding-the-commands"></a>Åsidosätta kommandona

Om du vill åsidosätta kommandona använder du PowerShell-eller Shell-skript provisioor för att skapa kommando filerna med det exakta fil namnet och lägga dem i de kataloger som anges ovan. Azure Image Builder läser dessa kommandon och utdata skrivs till *anpassning. log*.

## <a name="getting-support"></a>Få support
Om du har refererat till vägledningen och fortfarande inte kan felsöka problemet kan du öppna ett support ärende. När du gör det kan du välja rätt produkt och support, så att du kan använda Azure VM Image Builder support team.

Välja ärende produkt:
```bash
Product Family: Azure
Product: Virtual Machine Running (Window\Linux)
Support Topic: Azure Features
Support Subtopic: Azure Image Builder
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Översikt över Azure Image Builder](../image-builder-overview.md).
