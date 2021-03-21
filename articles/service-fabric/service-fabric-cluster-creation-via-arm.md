---
title: Skapa ett Azure Service Fabric-kluster
description: Lär dig hur du konfigurerar ett säkert Service Fabric kluster i Azure med Azure Resource Manager.  Du kan skapa ett kluster med hjälp av en standardmall eller med hjälp av en egen kluster mall.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: d4daa75fd9383bc19da1b09104ebddff5712c5b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791819"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Skapa ett Service Fabric kluster med Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure-portalen](service-fabric-cluster-creation-via-portal.md)
>
>

Ett [Azure Service Fabric-kluster](service-fabric-deploy-anywhere.md) är en nätverksansluten uppsättning virtuella datorer där dina mikrotjänster distribueras och hanteras.  Ett Service Fabric kluster som körs i Azure är en Azure-resurs och distribueras med hjälp av Azure Resource Manager. I den här artikeln beskrivs hur du distribuerar ett säkert Service Fabric kluster i Azure med hjälp av Resource Manager. Du kan använda en mall för standard kluster eller en anpassad mall.  Om du inte redan har en anpassad mall kan du [lära dig hur du skapar en](service-fabric-cluster-creation-create-template.md).

Den typ av säkerhet som valts för att skydda klustret (t. ex. Windows-identitet, X509 osv.) måste anges för den inledande skapandet av klustret och kan inte ändras därefter. Innan du konfigurerar ett kluster bör du läsa [Service Fabric kluster säkerhets scenarier][service-fabric-cluster-security]. I Azure använder Service Fabric x509-certifikat för att skydda klustret och dess slut punkter, autentisera klienter och kryptera data. Azure Active Directory rekommenderas också att skydda åtkomsten till hanterings slut punkter. Mer information finns [i Konfigurera Azure AD för att autentisera klienter](service-fabric-cluster-creation-setup-aad.md).

Om du skapar ett produktions kluster för att köra produktions arbets belastningar rekommenderar vi att du först läser igenom [Check listan för produktions beredskap](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar 
I den här artikeln använder du modulerna Service Fabric RM PowerShell eller Azure CLI för att distribuera ett kluster:

* [Azure PowerShell 4,1 och uppåt][azure-powershell]
* [Azure CLI version 2,0 och senare][azure-CLI]

Referens dokumentationen för Service Fabric moduler finns här:
* [Az.ServiceFabric](/powershell/module/az.servicefabric)
* [AZ SF CLI-modul](/cli/azure/sf)

### <a name="sign-in-to-azure"></a>Logga in på Azure

Innan du kör något av kommandona i den här artikeln loggar du först in på Azure.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>Skapa ett nytt kluster med ett självsignerat system som skapats

Använd följande kommandon för att skapa ett kluster som skyddas med ett självsignerat systemgenererat certifikat. Det här kommandot konfigurerar ett primärt kluster certifikat som används för kluster säkerhet och för att konfigurera administratörs åtkomst för att utföra hanterings åtgärder med det certifikatet.  Självsignerade certifikat är användbara för att skydda test kluster.  Produktions kluster bör skyddas med ett certifikat från en certifikat utfärdare (CA).

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>Använd standard kluster mal len som levereras i modulen

Använd följande kommando för att snabbt skapa ett kluster genom att ange minimala parametrar med hjälp av standard mal len.

Den mall som används är tillgänglig i [Azure Service Fabric Template-exempel:](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) mall för Windows-mall och [Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Följande kommando kan skapa antingen Windows-eller Linux-kluster, du måste ange operativ systemet. PowerShell/CLI-kommandona skickar även certifikatet i den angivna *CertificateOutputFolder*. kontrol lera dock att mappen redan har skapats. Kommandot tar även med andra parametrar, till exempel VM SKU.

> [!NOTE]
> Följande PowerShell-kommando fungerar endast med Azure PowerShell- `Az` modulen. Om du vill kontrol lera den aktuella versionen av Azure Resource Manager PowerShell-version kör du följande PowerShell-kommando "Get-module AZ". Följ [den här länken](/powershell/azure/install-Az-ps) om du vill uppgradera din Azure Resource Manager PowerShell-version. 
>
>

Distribuera klustret med PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

Distribuera klustret med Azure CLI:

```azurecli
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser
```

### <a name="use-your-own-custom-template"></a>Använd din egen anpassade mall

Om du behöver skapa en anpassad mall för att passa dina behov, rekommenderar vi starkt att du börjar med en av mallarna som är tillgängliga i [mall exemplen för Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Lär dig hur du [anpassar kluster mal len][customize-your-cluster-template].

Om du redan har en anpassad mall måste du kontrol lera att alla de tre certifikat relaterade parametrarna i mallen och parameter filen heter följande och värden är null enligt följande:

```json
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

Distribuera klustret med PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Distribuera klustret med Azure CLI:

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

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>Skapa ett nytt kluster med ditt eget X. 509-certifikat

Använd följande kommando för att skapa kluster, om du har ett certifikat som du vill använda för att skydda klustret med.

Om det här är ett CA-signerat certifikat som du kommer att använda i andra syfte, rekommenderar vi att du anger en specifik resurs grupp specifikt för ditt nyckel valv. Vi rekommenderar att du sätter nyckel valvet i sin egen resurs grupp. Med den här åtgärden kan du ta bort resurs grupperna för beräkning och lagring, inklusive resurs gruppen som innehåller Service Fabric klustret, utan att förlora nycklar och hemligheter. **Resurs gruppen som innehåller nyckel valvet *måste finnas i samma region* som det kluster som använder det.**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>Använd standardvärdet på fem noder, en mall för nodtyp som levereras i modulen
Den mall som används är tillgänglig i Azure- [exempel:](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) mall-och [Ubuntu-mallen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure) för Windows

Distribuera klustret med PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

Distribuera klustret med Azure CLI:

```azurecli
declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser
```

### <a name="use-your-own-custom-cluster-template"></a>Använd din egen anpassade kluster mall
Om du behöver skapa en anpassad mall för att passa dina behov, rekommenderar vi starkt att du börjar med en av mallarna som är tillgängliga i [mall exemplen för Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Lär dig hur du [anpassar kluster mal len][customize-your-cluster-template].

Om du redan har en anpassad mall måste du kontrol lera att alla de tre certifikatets relaterade parametrar i mallen och parameter filen heter följande och värden är null enligt följande.

```json
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

Distribuera klustret med PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

Distribuera klustret med Azure CLI:

```azurecli
declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>Använda en pekare till en hemlighet som laddats upp till ett nyckel valv

Om du vill använda ett befintligt nyckel valv måste nyckel valvet vara [aktiverat för distribution](../key-vault/general/manage-with-cli2.md#bkmk_KVperCLI) så att Compute Resource-providern kan hämta certifikat från den och installera den på klusternoder.

Distribuera klustret med PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretID -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Distribuera klustret med Azure CLI:

```azurecli
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier $secretID  \
    --template-file $templateFilePath --parameter-file $parameterFilePath 
```

## <a name="next-steps"></a>Nästa steg
Nu har du ett säkert kluster som körs i Azure. Anslut sedan [till klustret](service-fabric-connect-to-secure-cluster.md) och lär dig hur du [hanterar program hemligheter](service-fabric-application-secret-management.md).

En mall för JSON-syntax och egenskaper för att använda en mall finns i [referens för Microsoft. ServiceFabric/Clusters](/azure/templates/microsoft.servicefabric/clusters).

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-Az-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-create-template.md
