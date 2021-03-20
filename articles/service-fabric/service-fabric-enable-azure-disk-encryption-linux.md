---
title: Aktivera disk kryptering för Linux-kluster
description: I den här artikeln beskrivs hur du aktiverar disk kryptering för Azure Service Fabric klusternoder i Linux med Azure Resource Manager och Azure Key Vault.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: c600d822d20b0e5a0ca613935b1dfa4be838fcec
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "78252818"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Aktivera disk kryptering för Azure Service Fabric klusternoder i Linux 
> [!div class="op_single_selector"]
> * [Disk kryptering för Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Disk kryptering för Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

I den här självstudien får du lära dig hur du aktiverar disk kryptering på Azure Service Fabric klusternoder i Linux. Du måste följa dessa steg för var och en av nodtypen och skalnings uppsättningar för virtuella datorer. För att kryptera noderna använder vi Azure Disk Encryption-funktionen på virtuella datorers skalnings uppsättningar.

Guiden omfattar följande ämnen:

* Viktiga begrepp som du bör känna till när du aktiverar disk kryptering på Service Fabric skalnings uppsättningar för virtuella kluster datorer i Linux.
* Steg att följa innan du aktiverar disk kryptering på Service Fabric klusternoder i Linux.
* Steg som ska följas för att aktivera disk kryptering på Service Fabric klusternoder i Linux.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

 **Själv registrering**

För hands versionen av disk kryptering för den virtuella datorns skalnings uppsättning krävs själv registrering. Gör så här:

1. Kör följande kommando: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Vänta cirka 10 minuter tills statusen *har lästs.* Du kan kontrol lera statusen genom att köra följande kommando:
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault**

1. Skapa ett nyckel valv i samma prenumeration och region som skalnings uppsättningen. Välj sedan **EnabledForDiskEncryption** -åtkomst principen i nyckel valvet med hjälp av dess PowerShell-cmdlet. Du kan också ange principen med hjälp av Key Vault gränssnittet i Azure Portal med följande kommando:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Installera den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli), som har de nya krypterings kommandona.

3. Installera den senaste versionen av [Azure SDK från Azure PowerShell](https://github.com/Azure/azure-powershell/releases) -versionen. Följande är skalnings uppsättningen för den virtuella datorn Azure Disk Encryption-cmdletar för att aktivera ([Ange](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) kryptering, Hämta ([Hämta](/powershell/module/az.compute/get-azvmssvmdiskencryption)) krypterings status och ta bort ([inaktivera](/powershell/module/az.compute/disable-azvmssdiskencryption)) kryptering på skalnings uppsättnings instansen.


| Kommando | Version |  Källa  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 eller senare | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 eller senare | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 eller senare | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 eller senare | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 eller senare | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 eller senare | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Scenarier som stöds för disk kryptering
* Kryptering för skalnings uppsättningar för virtuella datorer stöds bara för skalnings uppsättningar som skapats med hanterade diskar. Det finns inte stöd för interna (eller ohanterade) disk skalnings uppsättningar.
* Både kryptering och inaktive ring av kryptering stöds för OS-och data volymer i skalnings uppsättningar för virtuella datorer i Linux. 
* Avbildnings-och uppgraderings åtgärder för virtuella datorer (VM) för virtuella datorer stöds inte i den aktuella för hands versionen.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Skapa ett nytt kluster och aktivera disk kryptering

Använd följande kommandon för att skapa ett kluster och aktivera disk kryptering med hjälp av en Azure Resource Manager-mall och ett självsignerat certifikat.

### <a name="sign-in-to-azure"></a>Logga in på Azure  

Logga in med följande kommandon:

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Använd den anpassade mall som du redan har 

Om du behöver skapa en anpassad mall rekommenderar vi starkt att du använder en av mallarna på sidan exempel på [mall för skapande av Azure Service Fabric-kluster](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) . 

Om du redan har en anpassad mall, kontrol lera att alla tre certifikat-relaterade parametrar i mallen och parameter filen har namnet enligt följande. Se också till att värdena är null enligt följande:

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Eftersom endast data disk kryptering stöds för skalnings uppsättningar för virtuella datorer i Linux, måste du lägga till en datadisk med hjälp av en Resource Manager-mall. Uppdatera mallen för data disk etablering på följande sätt:

```Json
   
   "storageProfile": { 
            "imageReference": { 
              "publisher": "[parameters('vmImagePublisher')]", 
              "offer": "[parameters('vmImageOffer')]", 
              "sku": "[parameters('vmImageSku')]", 
              "version": "[parameters('vmImageVersion')]" 
            }, 
            "osDisk": { 
              "caching": "ReadOnly", 
              "createOption": "FromImage", 
              "managedDisk": { 
                "storageAccountType": "[parameters('storageAccountType')]" 
              } 
           }, 
                "dataDisks": [ 
                { 
                    "diskSizeGB": 1023, 
                    "lun": 0, 
                    "createOption": "Empty" 
   
```
 

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Här är motsvarande CLI-kommando. Ändra värdena i declare-instruktionerna till lämpliga värden. CLI stöder alla andra parametrar som stöds av föregående PowerShell-kommando.

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Montera en datadisk till en Linux-instans
Innan du fortsätter med kryptering på en skalnings uppsättning för en virtuell dator måste du se till att den tillagda datadisken är korrekt monterad. Logga in på den virtuella Linux-klustret och kör kommandot **LSBLK** . Utdata ska visa den tillagda datadisken i kolumnen **monterings punkt** .


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Distribuera program till ett Service Fabric kluster i Linux
Om du vill distribuera ett program till klustret följer du stegen och anvisningarna i [snabb start: distribuera Linux-behållare till Service Fabric](service-fabric-quickstart-containers-linux.md).


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Aktivera disk kryptering för de virtuella datorernas skalnings uppsättningar som skapats tidigare
Om du vill aktivera disk kryptering för de skalnings uppsättningar för virtuella datorer som du skapade i föregående steg, kör du följande kommandon:
 
```powershell
$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Verifiera om disk kryptering har Aktiver ATS för en skalnings uppsättning för virtuella datorer i Linux
Kör följande kommandon för att hämta status för en hel skalnings uppsättning för virtuella datorer eller en instans i en skalnings uppsättning.
Dessutom kan du logga in på den virtuella Linux-klustret och köra **LSBLK** -kommandot. Utdata ska visa den tillagda datadisken i kolumnen **monterings punkt** och kolumnen **typ** ska vara Read *crypt*.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Inaktivera disk kryptering för en skalnings uppsättning för virtuella datorer i ett Service Fabric kluster
Inaktivera disk kryptering för en skalnings uppsättning för virtuella datorer genom att köra följande kommandon. Observera att om du inaktiverar disk kryptering gäller hela skalnings uppsättningen för den virtuella datorn och inte en enskild instans.

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Nästa steg
I det här läget bör du ha ett säkert kluster och veta hur du aktiverar och inaktiverar disk kryptering för Service Fabric klusternoder och skalnings uppsättningar för virtuella datorer. För liknande vägledning om Service Fabric klusternoder i Linux, se [disk kryptering för Windows](service-fabric-enable-azure-disk-encryption-windows.md). 
