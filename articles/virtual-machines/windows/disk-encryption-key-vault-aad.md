---
title: Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption med Azure AD (tidigare version)
description: I den här artikeln får du lära dig hur du skapar och konfigurerar ett nyckelvalv för Azure Disk Encryption med Azure AD.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: f2f301556bd24adb5e4a18f15717374ef26c400b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777895"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption med Azure AD (tidigare version)

**Den nya versionen av Azure Disk Encryption eliminerar kravet på att tillhandahålla en Azure AD-programparameter för att aktivera diskkryptering för virtuella datorer. Med den nya versionen behöver du inte längre ange Azure AD-autentiseringsuppgifter under steget aktivera kryptering. Alla nya virtuella datorer måste krypteras utan Azure AD-programparametrarna med den nya versionen. Anvisningar för hur du aktiverar diskkryptering för virtuella datorer med den nya versionen finns [i Azure Disk Encryption](disk-encryption-overview.md). Virtuella datorer som redan har krypterats med Azure AD-programparametrar stöds fortfarande och bör fortsätta att underhållas med AAD-syntaxen.**

Azure Disk Encryption använder Azure Key Vault för att styra och hantera diskkrypteringsnycklar och hemligheter.  Mer information om nyckelvalv finns i Kom [igång med Azure Key Vault](../../key-vault/general/overview.md) och Skydda ditt [nyckelvalv.](../../key-vault/general/security-overview.md) 

Att skapa och konfigurera ett nyckelvalv för användning Azure Disk Encryption med Azure AD (tidigare version) omfattar tre steg:

1. Skapa ett nyckelvalv. 
2. Konfigurera ett Azure AD-program och tjänstens huvudnamn.
3. Ange nyckelvalvets åtkomstprincip för Azure AD-appen
4. Ställa in avancerade åtkomstprinciper för nyckelvalvet
 
Du kan också, om du vill, generera eller importera en nyckelkrypteringsnyckel (KEK).

Se [huvudartikeln Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption](disk-encryption-key-vault.md) anvisningar om hur du installerar verktyg och ansluter till [Azure.](disk-encryption-key-vault.md#install-tools-and-connect-to-azure)

> [!Note]
> Stegen i den här artikeln automatiseras i [cli Azure Disk Encryption skriptet](https://github.com/ejarvi/ade-cli-getting-started) för förhandskrav [och Azure Disk Encryption PowerShell-skript](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).


## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv 
Azure Disk Encryption är integrerad med [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) som hjälper dig att styra och hantera diskkrypteringsnycklar och hemligheter i din nyckelvalvsprenumeration. Du kan skapa ett nyckelvalv eller använda ett befintligt för Azure Disk Encryption. Mer information om nyckelvalv finns i Kom [igång med Azure Key Vault](../../key-vault/general/overview.md) och Skydda ditt [nyckelvalv.](../../key-vault/general/security-overview.md) Du kan använda en Resource Manager, en Azure PowerShell eller Azure CLI för att skapa ett nyckelvalv. 


>[!WARNING]
>För att se till att krypteringshemligheterna inte går över regionala gränser Azure Disk Encryption måste Key Vault och de virtuella datorerna finnas i samma region. Skapa och använd en Key Vault som finns i samma region som den virtuella dator som ska krypteras. 


### <a name="create-a-key-vault-with-powershell"></a>Skapa ett nyckelvalv med PowerShell

Du kan skapa ett nyckelvalv med Azure PowerShell cmdleten [New-AzKeyVault.](/powershell/module/az.keyvault/New-azKeyVault) Ytterligare cmdlets för Key Vault finns i [Az.KeyVault](/powershell/module/az.keyvault/). 

1. Skapa en ny resursgrupp om det behövs med [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Om du vill visa en lista över datacenterplatser använder [du Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. Skapa ett nytt nyckelvalv med [hjälp av New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Observera **valvnamnet,** **resursgruppens namn,** **resurs-ID,** **valv-URI** och objekt-ID:t som returneras för senare användning när du krypterar diskarna.  


### <a name="create-a-key-vault-with-azure-cli"></a>Skapa ett nyckelvalv med Azure CLI
Du kan hantera ditt nyckelvalv med Azure CLI med hjälp av [az keyvault-kommandon.](/cli/azure/keyvault#commands) Om du vill skapa ett nyckelvalv använder [du az keyvault create](/cli/azure/keyvault#az_keyvault_create).

1. Skapa en ny resursgrupp om det behövs med [az group create](/cli/azure/group#az_group_create). Om du vill visa en lista över [platser använder du az account list-locations](/cli/azure/account#az_account_list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Skapa ett nytt nyckelvalv med [az keyvault create](/cli/azure/keyvault#az_keyvault_create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Observera **valvnamnet** (namn), **resursgruppens namn,** resurs-ID (ID), **valv-URI** och objekt-ID:t som returneras för senare användning.   

### <a name="create-a-key-vault-with-a-resource-manager-template"></a>Skapa ett nyckelvalv med en Resource Manager mall

Du kan skapa ett nyckelvalv med hjälp av [Resource Manager mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. I Azure-snabbstartsmallen klickar du **på Distribuera till Azure**.
2. Välj prenumeration, resursgrupp, resursgruppsplats, Key Vault objekt-ID, juridiska villkor och avtal och klicka sedan på **Köp.** 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a>Konfigurera en Azure AD-app och tjänstens huvudnamn 
När du behöver ha kryptering aktiverat på en virtuell dator som körs i Azure Azure Disk Encryption och skriver krypteringsnycklarna till ditt nyckelvalv. Hantering av krypteringsnycklar i nyckelvalvet kräver Azure AD-autentisering. Skapa ett Azure AD-program för detta ändamål. I autentiseringssyfte kan du använda antingen klienthemlig autentisering eller [klientcertifikatbaserad Azure AD-autentisering.](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a>Konfigurera en Azure AD-app och tjänstens huvudnamn med Azure PowerShell 
Kör följande kommandon genom att hämta och använda [Azure AD PowerShell-modulen](/powershell/azure/active-directory/install-adv2). 

1. Använd [PowerShell-cmdleten New-AzADApplication](/powershell/module/az.resources/new-azadapplication) för att skapa ett Azure AD-program. MyApplicationHomePage och MyApplicationUri kan vara valfria värden.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. Den $azureAdApplication.ApplicationId är Azure AD ClientID och $aadClientSecret är den klienthemlighet som du kommer att använda senare för att aktivera Azure Disk Encryption. Skydda Azure AD-klienthemligheten på lämpligt sätt. När `$azureAdApplication.ApplicationId` du kör visas ApplicationID.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a>Konfigurera en Azure AD-app och tjänstens huvudnamn med Azure CLI

Du kan hantera tjänstens huvudnamn med Azure CLI med hjälp av [az ad sp-kommandona.](/cli/azure/ad/sp) Mer information finns i Skapa [ett huvudnamn för Azure-tjänsten.](/cli/azure/create-an-azure-service-principal-azure-cli)

1. Skapa ett nytt huvudnamn för tjänsten.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  Det appId som returneras är det Azure AD ClientID som används i andra kommandon. Det är också det SPN som du använder för az keyvault set-policy. Lösenordet är den klienthemlighet som du bör använda senare för att aktivera Azure Disk Encryption. Skydda Azure AD-klienthemligheten på lämpligt sätt.
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a>Konfigurera en Azure AD-app och tjänstens huvudnamn via Azure Portal
Använd stegen i artikeln Använda [portalen för att skapa](../../active-directory/develop/howto-create-service-principal-portal.md) ett Azure Active Directory och tjänstens huvudnamn som kan komma åt resurser för att skapa ett Azure AD-program. Varje steg som anges nedan tar dig direkt till artikelavsnittet för att slutföra. 

1. [Verifiera nödvändiga behörigheter](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)
2. [Skapa ett Azure Active Directory program](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) 
     - Du kan använda val annat namn och inloggnings-URL som du vill när du skapar programmet.
3. [Hämta program-ID:t och autentiseringsnyckeln](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in). 
     - Autentiseringsnyckeln är klienthemligheten och används som AadClientSecret för Set-AzVMDiskEncryptionExtension. 
        - Autentiseringsnyckeln används av programmet som autentiseringsuppgifter för att logga in på Azure AD. I den Azure Portal den här hemligheten kallas nycklar, men har ingen relation till nyckelvalv. Skydda den här hemligheten på rätt sätt. 
     - Program-ID:t används senare som AadClientId för Set-AzVMDiskEncryptionExtension och som ServicePrincipalName för Set-AzKeyVaultAccessPolicy. 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a>Ange nyckelvalvets åtkomstprincip för Azure AD-appen
Om du vill skriva krypteringshemligheter till en angiven Key Vault Azure Disk Encryption behöver klient-ID och klienthemlighet för Azure Active Directory-programmet som har behörighet att skriva hemligheter till Key Vault. 

> [!NOTE]
> Azure Disk Encryption kräver att du konfigurerar följande åtkomstprinciper till ditt Azure AD-klientprogram: _WrapKey-_ och _Set-behörigheter._

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a>Ange åtkomstprincipen för nyckelvalvet för Azure AD-appen med Azure PowerShell
Ditt Azure AD-program behöver behörighet att komma åt nycklarna eller hemligheterna i valvet. Använd cmdleten [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) för att bevilja behörigheter till programmet med hjälp av klient-ID:t (som genererades när programmet registrerades) som parametervärdet _–ServicePrincipalName._ Mer information finns i blogginlägget [Azure Key Vault steg för steg.](/archive/blogs/kv/azure-key-vault-step-by-step) 

1. Ange åtkomstprincipen för nyckelvalvet för AD-programmet med PowerShell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a>Ange åtkomstprincipen för nyckelvalvet för Azure AD-appen med Azure CLI
Använd [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) för att ange åtkomstprincipen. Mer information finns i Hantera [Key Vault med CLI 2.0](../../key-vault/general/manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

Ge tjänstens huvudnamn som du skapade via Azure CLI åtkomst för att hämta hemligheter och omsluta nycklar med följande kommando:

```azurecli-interactive
az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a>Ange åtkomstprincipen för nyckelvalvet för Azure AD-appen med portalen

1. Öppna resursgruppen med ditt nyckelvalv.
2. Välj ditt nyckelvalv, gå till **Åtkomstprinciper och** klicka sedan på **Lägg till ny.**
3. Under **Välj huvudnamn** söker du efter det Azure AD-program som du skapade och väljer det. 
4. För **Nyckelbehörigheter** markerar **du Wrap Key (Omsluta** **nyckel) under Cryptographic Operations (Kryptografiska åtgärder).**
5. För **Hemliga behörigheter markerar** du **Ange** under **Hemlighetshanteringsåtgärder.**
6. Klicka **på OK** för att spara åtkomstprincipen. 

![Azure Key Vault kryptografiska åtgärder – omsluta nyckel](../media/disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault hemliga behörigheter – Ange](../media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a>Ställa in avancerade åtkomstprinciper för nyckelvalvet
Azure-plattformen behöver åtkomst till krypteringsnycklarna eller hemligheterna i ditt nyckelvalv för att göra dem tillgängliga för den virtuella datorn för start och dekryptering av volymerna. Aktivera diskkryptering på nyckelvalvet, annars misslyckas distributioner.  

### <a name="set-key-vault-advanced-access-policies-with-azure-powershell"></a>Ange avancerade åtkomstprinciper för key vault med Azure PowerShell
 Använd PowerShell-cmdleten [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) för nyckelvalvet för att aktivera diskkryptering för nyckelvalvet.

  - **Aktivera Key Vault för diskkryptering:** EnabledForDiskEncryption krävs för Azure Disk-kryptering.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Aktivera Key Vault för distribution om det behövs:** Gör det möjligt för resursprovidern Microsoft.Compute att hämta hemligheter från det här nyckelvalvet när det här nyckelvalvet refereras till vid resursskapande, till exempel när du skapar en virtuell dator.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Aktivera Key Vault för malldistribution om det behövs:** Gör det Azure Resource Manager att hämta hemligheter från det här nyckelvalvet när det här nyckelvalvet refereras till i en malldistribution.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="set-key-vault-advanced-access-policies-using-the-azure-cli"></a>Ange avancerade åtkomstprinciper för Nyckelvalv med hjälp av Azure CLI
Använd [az keyvault update för](/cli/azure/keyvault#az_keyvault_update) att aktivera diskkryptering för nyckelvalvet. 

 - **Aktivera Key Vault för diskkryptering:** Aktiverat för diskkryptering krävs. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Aktivera Key Vault för distribution om det behövs:** Tillåt Virtual Machines hämta certifikat som lagras som hemligheter från valvet.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Aktivera Key Vault för malldistribution om det behövs:** Tillåt Resource Manager att hämta hemligheter från valvet.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="set-key-vault-advanced-access-policies-through-the-azure-portal"></a>Ange avancerade åtkomstprinciper för nyckelvalvet via Azure Portal

1. Välj ditt nyckelvalv, gå till **Åtkomstprinciper och** Klicka **för att visa avancerade åtkomstprinciper.**
2. Markera rutan Aktivera åtkomst **till Azure Disk Encryption för volymkryptering.**
3. Välj **Aktivera åtkomst till Azure Virtual Machines för distribution** och/eller Aktivera åtkomst till Azure Resource Manager för **malldistribution** om det behövs. 
4. Klicka på **Spara**.

![Avancerade åtkomstprinciper för Azure Key Vault](../media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a>Konfigurera en nyckelkrypteringsnyckel (valfritt)
Om du vill använda en nyckelkrypteringsnyckel (KEK) för ett extra säkerhetslager för krypteringsnycklar lägger du till en KEK i nyckelvalvet. Använd [cmdleten Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) för att skapa en nyckelkrypteringsnyckel i nyckelvalvet. Du kan också importera en KEK från din lokala nyckelhanterings-HSM. Mer information finns i [dokumentationen Key Vault .](../../key-vault/keys/hsm-protected-keys.md) När en nyckelkrypteringsnyckel anges använder Azure Disk Encryption nyckeln för att omsluta krypteringshemligheterna innan de skrivs till Key Vault. 

* Använd en RSA-nyckeltyp när du genererar nycklar. Azure Disk Encryption har ännu inte stöd för att använda Elliptic Curve-nycklar.

* Din nyckelvalvshemlighet och KEK-URL:er måste ha versionshantering. Azure tillämpar den här begränsningen för versionshantering. Giltiga hemligheter och KEK-URL:er finns i följande exempel:

  * Exempel på en giltig hemlig URL:   *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Exempel på en giltig KEK-URL:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption inte att ange portnummer som en del av key vault-hemligheter och KEK-URL:er. Exempel på URL:er för nyckelvalv som inte stöds och som stöds finns i följande exempel:

  * Oacceptabel URL för nyckelvalv  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Godtagbar URL för nyckelvalv:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a>Konfigurera en nyckelkrypteringsnyckel med Azure PowerShell 
Innan du använder PowerShell-skriptet bör du vara bekant med Azure Disk Encryption för att förstå stegen i skriptet. Exempelskriptet kan behöva ändras för din miljö. Det här skriptet skapar Azure Disk Encryption förhandskrav och krypterar en befintlig virtuell IaaS-dator genom att omsluta diskkrypteringsnyckeln med hjälp av en nyckelkrypteringsnyckel. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name  $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName  $KVRGname;
     
 #Step 4: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
      $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="certificate-based-authentication-optional"></a>Certifikatbaserad autentisering (valfritt)
Om du vill använda certifikatautentisering kan du ladda upp ett till nyckelvalvet och distribuera det till klienten. Innan du använder PowerShell-skriptet bör du vara bekant med Azure Disk Encryption för att förstå stegen i skriptet. Exempelskriptet kan behöva ändras för din miljö.

     
 ```powershell

 # Fill in "MyKeyVaultResourceGroup", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="certificate-based-authentication-and-a-kek-optional"></a>Certifikatbaserad autentisering och kek (valfritt)

Om du vill använda certifikatautentisering och omsluta krypteringsnyckeln med en KEK kan du använda skriptet nedan som exempel. Innan du använder PowerShell-skriptet bör du vara bekant med alla tidigare Azure Disk Encryption för att förstå stegen i skriptet. Exempelskriptet kan behöva ändras för din miljö.

     
 ```powershell
# Fill in 'MyKeyVaultResourceGroup', 'MySecureVault', and 'MyLocation' (if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>Nästa steg

[Aktivera Azure Disk Encryption med Azure AD på virtuella Windows-datorer (tidigare version)](disk-encryption-windows-aad.md)
