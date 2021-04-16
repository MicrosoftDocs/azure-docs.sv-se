---
title: Rotationsskurs för resurser med två uppsättningar autentiseringsuppgifter
description: Använd den här självstudien om du vill lära dig hur du automatiserar rotationen av en hemlighet för resurser som använder två uppsättningar autentiseringsuppgifter.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 1f656a41b0f447b90f58ec14173e418a2defb72e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484837"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-have-two-sets-of-authentication-credentials"></a>Automatisera rotationen av en hemlighet för resurser som har två uppsättningar autentiseringsuppgifter

Det bästa sättet att autentisera till Azure-tjänster är att använda en hanterad identitet [,](../general/authentication.md)men det finns vissa scenarier där det inte är ett alternativ. I dessa fall används åtkomstnycklar eller lösenord. Du bör rotera åtkomstnycklar och lösenord ofta.

Den här självstudien visar hur du automatiserar periodisk rotation av hemligheter för databaser och tjänster som använder två uppsättningar autentiseringsuppgifter. Mer specifikt visar den här självstudien hur du roterar Azure Storage-kontonycklar som lagras i Azure Key Vault som hemligheter. Du använder en funktion som utlöses av ett Azure Event Grid meddelande. 

> [!NOTE]
> Lagringskontonycklar kan hanteras automatiskt i Key Vault om du anger signaturtoken för delad åtkomst för delegerad åtkomst till lagringskontot. Det finns tjänster som kräver anslutningssträngar för lagringskontot med åtkomstnycklar. För det scenariot rekommenderar vi den här lösningen.

Här är rotationslösningen som beskrivs i den här självstudien: 

![Diagram som visar rotationslösningen.](../media/secrets/rotation-dual/rotation-diagram.png)

I den här Azure Key Vault lagringskontots enskilda åtkomstnycklar som versioner av samma hemlighet, alternerande mellan den primära och sekundära nyckeln i senare versioner. När en åtkomstnyckel lagras i den senaste versionen av hemligheten återskapas den alternativa nyckeln och läggs till i Key Vault som den nya senaste versionen av hemligheten. Lösningen tillhandahåller programmets hela rotationscykel för att uppdatera till den senaste återskapade nyckeln. 

1. 30 dagar före förfallodatumet för en hemlighet Key Vault publicerar händelsen som snart upphör att gälla till Event Grid.
1. Event Grid kontrollerar händelseprenumerationer och använder HTTP POST för att anropa funktionsappens slutpunkt som prenumererar på händelsen.
1. Funktionsappen identifierar den alternativa nyckeln (inte den senaste) och anropar lagringskontot för att återskapa den.
1. Funktionsappen lägger till den nya återskapade nyckeln Azure Key Vault som den nya versionen av hemligheten.

## <a name="prerequisites"></a>Förutsättningar
* En Azure-prenumeration. [Skapa ett kostnadsfritt.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure [Cloud Shell](https://shell.azure.com/). Den här självstudien använder Cloud Shell med PowerShell-miljö
* Azure Key Vault.
* Två Azure Storage-konton.

Du kan använda den här distributionslänken om du inte har ett befintligt nyckelvalv och befintliga lagringskonton:

[![Länk som är märkt Distribuera till Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. Under **Resursgrupp** väljer du **Skapa ny**. Ge gruppen namnet **vaultrotation** och välj **sedan OK.**
1. Välj **Granska + skapa**.
1. Välj **Skapa**.

    ![Skärmbild som visar hur du skapar en resursgrupp.](../media/secrets/rotation-dual/dual-rotation-1.png)

Nu har du ett nyckelvalv och två lagringskonton. Du kan kontrollera den här konfigurationen i Azure CLI eller Azure PowerShell genom att köra följande kommando:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az resource list -o table -g vaultrotation
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzResource -Name 'vaultrotation*' | Format-Table
```
---

Resultatet ser ut ungefär så här:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
vaultrotation-kv         vaultrotation      westus      Microsoft.KeyVault/vaults
vaultrotationstorage     vaultrotation      westus      Microsoft.Storage/storageAccounts
vaultrotationstorage2    vaultrotation      westus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-the-key-rotation-function"></a>Skapa och distribuera nyckelrotationsfunktionen

Därefter skapar du en funktionsapp med en system hanterad identitet, utöver andra nödvändiga komponenter. Du distribuerar även rotationsfunktionen för lagringskontonycklarna.

Roteringsfunktionen för funktionsappen kräver följande komponenter och konfiguration:
- En Azure App Service plan
- Ett lagringskonto för att hantera utlösare för funktionsappen
- En åtkomstprincip för åtkomst till hemligheter i Key Vault
- Rollen nyckeloperatör för lagringskonto som tilldelats funktionsappen så att den kan komma åt åtkomstnycklar för lagringskontot
- En nyckelrotationsfunktion med en händelseutlösare och en HTTP-utlösare (rotation på begäran)
- En Event Grid händelseprenumeration **för SecretNearExpiry-händelsen**

1. Välj distributionslänken för Azure-mallar: 

   [![Distributionslänk för Azure-mallar.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. I listan **Resursgrupp** väljer du **vaultrotation**.
1. I rutan **Lagringskonto-RG** anger du namnet på den resursgrupp där lagringskontot finns. Behåll standardvärdet **[resourceGroup().name]** om ditt lagringskonto redan finns i samma resursgrupp där du distribuerar nyckelroteringsfunktionen.
1. I rutan **Lagringskontonamn** anger du namnet på det lagringskonto som innehåller de åtkomstnycklar som ska roteras. Behåll standardvärdet **[concat(resourceGroup().name, 'storage')]** om du använder [lagringskontot](#prerequisites)som skapades i Krav .
1. I rutan **Key Vault RG** anger du namnet på den resursgrupp där nyckelvalvet finns. Behåll standardvärdet **[resourceGroup().name]** om ditt nyckelvalv redan finns i samma resursgrupp där du distribuerar nyckelroteringsfunktionen.
1. I rutan **Key Vault** namn anger du namnet på nyckelvalvet. Behåll standardvärdet **[concat(resourceGroup().name, '-kv')]** om du använder nyckelvalvet som skapades [i Krav](#prerequisites).
1. I rutan **App Service väljer** du Värdplan. **Premium-plan** behövs bara när ditt nyckelvalv ligger bakom brandväggen.
1. I rutan **Funktionsappens** namn anger du namnet på funktionsappen.
1. I rutan **Hemligt namn** anger du namnet på hemligheten där du ska lagra åtkomstnycklar.
1. I rutan **Lagringsplats-URL** anger du GitHub-platsen för funktionskoden. I den här självstudien kan du använda **https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell.git** .
1. Välj **Granska + skapa**.
1. Välj **Skapa**.

   ![Skärmbild som visar hur du skapar och distribuerar en funktion.](../media/secrets/rotation-dual/dual-rotation-2.png)

När du har slutfört föregående steg har du ett lagringskonto, en servergrupp, en funktionsapp och Application Insights. När distributionen är klar visas den här sidan:

   ![Skärmbild som visar sidan Distributionen är klar.](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> Om det uppstår ett fel kan du välja **Distribuera om för** att slutföra distributionen av komponenterna.


Du hittar distributionsmallar och kod för rotationsfunktionen i [Azure Samples](https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell).

## <a name="add-the-storage-account-access-keys-to-key-vault"></a>Lägg till lagringskontots åtkomstnycklar till Key Vault

Först anger du din åtkomstprincip för att bevilja **behörigheter för att hantera** hemligheter till användarens huvudnamn:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault set-policy --upn <email-address-of-user> --name vaultrotation-kv --secret-permissions set delete get list
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Set-AzKeyVaultAccessPolicy -UserPrincipalName <email-address-of-user> --name vaultrotation-kv -PermissionsToSecrets set,delete,get,list
```
---

Nu kan du skapa en ny hemlighet med en åtkomstnyckel för lagringskontot som värde. Du behöver också lagringskontots resurs-ID, hemlighetens giltighetsperiod och nyckel-ID som ska läggas till i hemligheten så att rotationsfunktionen kan återskapa nyckeln i lagringskontot.

Fastställ lagringskontots resurs-ID. Du hittar det här värdet i `id` egenskapen .

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account show -n vaultrotationstorage
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccount -Name vaultrotationstorage -ResourceGroupName vaultrotation | Select-Object -Property *
```
---

Visa en lista över åtkomstnycklarna för lagringskontot så att du kan hämta nyckelvärdena:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Lägg till hemlighet till nyckelvalvet med förfallodatum inställt på imorgon, giltighetsperiod i 60 dagar och resurs-ID för lagringskonto. Kör det här kommandot med dina hämtade värden för `key1Value` och `storageAccountResourceId` :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
az keyvault secret set --name storageKey --vault-name vaultrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$tomorrowDate = (Get-Date).AddDays(+1).ToString('yyy-MM-ddTHH:mm:ssZ')
$secretVaule = ConvertTo-SecureString -String '<key1Value>' -AsPlainText -Force
$tags = @{
    CredentialId='key1'
    ProviderAddress='<storageAccountResourceId>'
    ValidityPeriodDays='60'
}
Set-AzKeyVaultSecret -Name storageKey -VaultName vaultrotation-kv -SecretValue $secretVaule -Tag $tags -Expires $tomorrowDate
```
---

Ovanstående hemlighet utlöser `SecretNearExpiry` en händelse inom några minuter. Den här händelsen utlöser i sin tur funktionen för att rotera hemligheten med en förfallotid på 60 dagar. I den konfigurationen utlöses "SecretNearExpiry"-händelsen var 30:e dag (30 dagar innan förfallodatumet) och rotationsfunktionen växlar rotation mellan key1 och key2.

Du kan kontrollera att åtkomstnycklarna har återskapats genom att hämta lagringskontonyckeln och Key Vault hemligheten och jämföra dem.

Använd det här kommandot för att hämta den hemliga informationen:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzKeyVaultSecret -VaultName vaultrotation-kv -Name storageKey -AsPlainText
```
---

Observera att `CredentialId` har uppdaterats till den alternativa `keyName` och `value` som återskapas:

![Skärmbild som visar utdata från kommandot z keyvault secret show för det första lagringskontot.](../media/secrets/rotation-dual/dual-rotation-4.png)

Hämta åtkomstnycklarna för att jämföra värdena:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage 
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Observera att `value` för nyckeln är samma som hemligheten i nyckelvalvet:

![Skärmbild som visar utdata från kommandot z storage account keys list för det första lagringskontot.](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-storage-accounts-for-rotation"></a>Lägga till lagringskonton för rotation

Du kan återanvända samma funktionsapp för att rotera nycklar för flera lagringskonton. 

Om du vill lägga till lagringskontonycklar i en befintlig funktion för rotation behöver du:
- Rollen Nyckeloperatör för lagringskonto som tilldelats funktionsappen så att den kan komma åt åtkomstnycklar för lagringskontot.
- En Event Grid händelseprenumeration **för händelsen SecretNearExpiry.**

1. Välj distributionslänken för Azure-mallar: 

   [![Distributionslänk för Azure-mallar.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FAdd-Event-Subscriptions%2Fazuredeploy.json)

1. I listan **Resursgrupp** väljer du **vaultrotation**.
1. I rutan **Lagringskonto-RG** anger du namnet på den resursgrupp där lagringskontot finns. Behåll standardvärdet **[resourceGroup().name]** om ditt lagringskonto redan finns i samma resursgrupp där du distribuerar nyckelroteringsfunktionen.
1. I rutan **Lagringskontonamn** anger du namnet på det lagringskonto som innehåller de åtkomstnycklar som ska roteras.
1. I rutan **Key Vault RG** anger du namnet på den resursgrupp där nyckelvalvet finns. Behåll standardvärdet **[resourceGroup().name]** om ditt nyckelvalv redan finns i samma resursgrupp där du distribuerar nyckelroteringsfunktionen.
1. I rutan **Key Vault** namn anger du namnet på nyckelvalvet.
1. I rutan **Funktionsappens** namn anger du namnet på funktionsappen.
1. I rutan **Hemligt namn** anger du namnet på hemligheten där du ska lagra åtkomstnycklar.
1. Välj **Granska + skapa**.
1. Välj **Skapa**.

   ![Skärmbild som visar hur du skapar ett ytterligare lagringskonto.](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Lägg till ytterligare en åtkomstnyckel för lagringskontot i Key Vault

Fastställ lagringskontots resurs-ID. Du hittar det här värdet i `id` egenskapen .
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account show -n vaultrotationstorage2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccount -Name vaultrotationstorage -ResourceGroupName vaultrotation | Select-Object -Property *
```
---

Visa en lista över åtkomstnycklarna för lagringskontot så att du kan hämta key2-värdet:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage2 -ResourceGroupName vaultrotation
```
---

Lägg till hemlighet till nyckelvalvet med förfallodatum inställt på imorgon, giltighetsperiod i 60 dagar och resurs-ID för lagringskonto. Kör det här kommandot med dina hämtade värden för `key2Value` och `storageAccountResourceId` :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
$tomorrowDate = (Get-Date).AddDays(+1).ToString('yyy-MM-ddTHH:mm:ssZ')
az keyvault secret set --name storageKey2 --vault-name vaultrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
$secretVaule = ConvertTo-SecureString -String '<key1Value>' -AsPlainText -Force
$tags = @{
    CredentialId='key2';
    ProviderAddress='<storageAccountResourceId>';
    ValidityPeriodDays='60'
}
Set-AzKeyVaultSecret -Name storageKey2 -VaultName vaultrotation-kv -SecretValue $secretVaule -Tag $tags -Expires $tomorrowDate
```
---

Använd det här kommandot för att hämta den hemliga informationen:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzKeyVaultSecret -VaultName vaultrotation-kv -Name storageKey2 -AsPlainText
```
---

Observera att `CredentialId` uppdateras till den alternativa och som `keyName` `value` återskapas:

![Skärmbild som visar utdata från kommandot z keyvault secret show för det andra lagringskontot.](../media/secrets/rotation-dual/dual-rotation-8.png)

Hämta åtkomstnycklarna för att jämföra värdena:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage 
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Observera att `value` för nyckeln är samma som hemligheten i nyckelvalvet:

![Skärmbild som visar utdata från kommandot a z storage account keys list för det andra lagringskontot.](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="key-vault-rotation-functions-for-two-sets-of-credentials"></a>Key Vault rotationsfunktioner för två uppsättningar autentiseringsuppgifter

Mall för rotationsfunktioner för två uppsättningar autentiseringsuppgifter och flera färdiga funktioner:

- [Projektmall](https://serverlesslibrary.net/sample/bc72c6c3-bd8f-4b08-89fb-c5720c1f997f)
- [Redis Cache](https://serverlesslibrary.net/sample/0d42ac45-3db2-4383-86d7-3b92d09bc978)
- [Lagringskonto](https://serverlesslibrary.net/sample/0e4e6618-a96e-4026-9e3a-74b8412213a4)
- [Cosmos DB](https://serverlesslibrary.net/sample/bcfaee79-4ced-4a5c-969b-0cc3997f47cc)

> [!NOTE]
> Rotationsfunktionerna ovan skapas av en medlem i communityn och inte av Microsoft. Community Azure Functions stöds inte under något Microsoft-supportprogram eller -tjänster och görs tillgängliga i DESS DESS-form utan garantier av något slag.

## <a name="next-steps"></a>Nästa steg

- Självstudie: [Rotering av hemligheter för en uppsättning autentiseringsuppgifter](./tutorial-rotation.md)
- Översikt: [Övervaka Key Vault med Azure Event Grid](../general/event-grid-overview.md)
- Gör så [här: Skapa din första funktion i Azure Portal](../../azure-functions/functions-get-started.md)
- Gör så här: [Ta emot e-post när Key Vault en hemlighet ändras](../general/event-grid-logicapps.md)
- Referens: [Azure Event Grid händelseschema för Azure Key Vault](../../event-grid/event-schema-key-vault.md)
