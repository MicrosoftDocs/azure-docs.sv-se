---
title: Rotations guide för resurser med två uppsättningar autentiseringsuppgifter
description: I den här självstudien får du lära dig hur du automatiserar rotationen av en hemlighet för resurser som använder två uppsättningar autentiseringsuppgifter för autentisering.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.openlocfilehash: 097b5c7d71076c11cdc30fce618f3a4ac4ef67a1
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655466"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-have-two-sets-of-authentication-credentials"></a>Automatisera rotationen av en hemlighet för resurser som har två uppsättningar autentiseringsuppgifter för autentisering

Det bästa sättet att autentisera till Azure-tjänster är genom att använda en [hanterad identitet](../general/authentication.md), men det finns vissa scenarier där det inte är ett alternativ. I dessa fall används åtkomst nycklar eller lösen ord. Du bör rotera åtkomst nycklar och lösen ord ofta.

Den här självstudien visar hur du automatiserar den periodiska rotationen av hemligheter för databaser och tjänster som använder två uppsättningar autentiseringsuppgifter för autentisering. Mer specifikt visar den här självstudien hur du roterar Azure Storage konto nycklar som lagras i Azure Key Vault som hemligheter. Du använder en funktion som utlöses av Azure Event Grid-avisering. 

> [!NOTE]
> Lagrings konto nycklar kan hanteras automatiskt i Key Vault om du anger signatur-token för delad åtkomst för delegerad åtkomst till lagrings kontot. Det finns tjänster som kräver lagrings kontots anslutnings strängar med åtkomst nycklar. I det scenariot rekommenderar vi den här lösningen.

Här är rotations lösningen som beskrivs i den här självstudien: 

![Diagram som visar rotations lösningen.](../media/secrets/rotation-dual/rotation-diagram.png)

I den här lösningen lagrar Azure Key Vault lagrings konton enskilda åtkomst nycklar som versioner av samma hemlighet, alternerande mellan den primära och den sekundära nyckeln i senare versioner. När en åtkomst nyckel lagras i den senaste versionen av hemligheten, återskapas den alternativa nyckeln och läggs till Key Vault som den nya senaste versionen av hemligheten. Lösningen tillhandahåller programmets hela rotations cykel för att uppdatera till den senaste återskapade nyckeln. 

1. Trettio dagar före utgångs datumet för en hemlighet, Key Vault publicerar händelsen nära förfallo datum till Event Grid.
1. Event Grid kontrollerar händelse prenumerationerna och använder HTTP POST för att anropa den slut punkt för funktionen som prenumererar på händelsen.
1. Function-appen identifierar den alternativa nyckeln (inte den senaste) och anropar lagrings kontot för att återskapa det.
1. Function-appen lägger till den nya återskapade nyckeln till Azure Key Vault som den nya versionen av hemligheten.

## <a name="prerequisites"></a>Förutsättningar
* En Azure-prenumeration. [Skapa ett kostnads fritt.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure Key Vault.
* Två Azure Storage-konton.

Du kan använda den här distributions länken om du inte har ett befintligt nyckel valv och befintliga lagrings konton:

[![Länk som är märkt med att distribuera till Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. Under **resurs grupp** väljer du **Skapa ny**. Namnge gruppen **akvrotation** och välj sedan **OK**.
1. Välj **Granska + skapa**.
1. Välj **Skapa**.

    ![Skärm bild som visar hur du skapar en resurs grupp.](../media/secrets/rotation-dual/dual-rotation-1.png)

Nu har du ett nyckel valv och två lagrings konton. Du kan kontrol lera den här installationen i Azure CLI genom att köra det här kommandot:

```azurecli
az resource list -o table -g akvrotation
```

Resultatet ser ut ungefär så här:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv         akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotationstorage     akvrotation      eastus      Microsoft.Storage/storageAccounts
akvrotationstorage2    akvrotation      eastus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-the-key-rotation-function"></a>Skapa och distribuera nyckel rotations funktionen

Därefter skapar du en Function-app med en Systemhanterad identitet, förutom andra nödvändiga komponenter. Du distribuerar också rotations funktionen för lagrings konto nycklarna.

Funktionen app-rotation kräver följande komponenter och konfiguration:
- En Azure App Service plan
- Ett lagrings konto för att hantera funktionens program utlösare
- En åtkomst princip för åtkomst till hemligheter i Key Vault
- Rollen lagrings konto nyckel operatör som är tilldelad Function-appen så att den kan komma åt åtkomst nycklar för lagrings kontot
- En nyckel rotations funktion med en händelse utlösare och en HTTP-utlösare (rotation på begäran)
- En Event Grid händelse prenumeration för **SecretNearExpiry** -händelsen

1. Välj distributions länk för Azure-mallar: 

   [![Azure Template Deployment-länk.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FFunction%2Fazuredeploy.json)

1. I listan **resurs grupp** väljer du **akvrotation**.
1. I rutan **lagrings konto RG** anger du namnet på resurs gruppen där ditt lagrings konto finns. Behåll standardvärdet **[resourceGroup (). name]** om ditt lagrings konto redan finns i samma resurs grupp där du ska distribuera nyckel rotations funktionen.
1. I rutan **lagrings konto namn** anger du namnet på det lagrings konto som innehåller de åtkomst nycklar som ska roteras.
1. I rutan **Key Vault RG** anger du namnet på den resurs grupp som nyckel valvet finns i. Behåll standardvärdet **[resourceGroup (). name]** om det redan finns ett nyckel valv i samma resurs grupp där du ska distribuera nyckel rotations funktionen.
1. I rutan **Key Vault namn** anger du namnet på nyckel valvet.
1. I rutan **Funktionsapp namn** anger du namnet på Function-appen.
1. I rutan **hemligt namn** anger du namnet på hemligheten där du kommer att lagra åtkomst nycklar.
1. I rutan **LAGRINGS platsen URL** anger du GitHub-platsen för funktions koden: **https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell.git** .
1. Välj **Granska + skapa**.
1. Välj **Skapa**.

   ![Skärm bild som visar hur du skapar det första lagrings kontot.](../media/secrets/rotation-dual/dual-rotation-2.png)

När du har slutfört föregående steg har du ett lagrings konto, en Server grupp, en Function-app och Application Insights. När distributionen är klar visas den här sidan: ![ skärm bild som visar sidan distributionen är klar.](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> Om du stöter på ett problem kan du välja **distribuera** igen för att slutföra distributionen av komponenterna.


Du kan hitta mallar och kod för rotations funktionen på [GitHub](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell).

## <a name="add-the-storage-account-access-keys-to-key-vault"></a>Lägg till lagrings kontots åtkomst nycklar i Key Vault

Börja med att ange åtkomst principen för att bevilja behörighet att **Hantera hemligheter** till användare:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Nu kan du skapa en ny hemlighet med lagrings kontots åtkomst nyckel som värde. Du behöver också lagrings kontots resurs-ID, den hemliga giltighets perioden och nyckel-ID: t som ska läggas till i hemligheten så att rotations funktionen kan återskapa nyckeln i lagrings kontot.

Fastställ resurs-ID för lagrings kontot. Du kan hitta det här värdet i `id` egenskapen.
```azurecli
az storage account show -n akvrotationstorage
```

Visa en lista med åtkomst nycklar för lagrings kontot så att du kan hämta nyckel värden:

```azurecli
az storage account keys list -n akvrotationstorage 
```

Kör det här kommandot med dina hämtade värden för `key1Value` och `storageAccountResourceId` :

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
az keyvault secret set --name storageKey --vault-name akvrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Om du skapar en hemlighet med ett kort utgångs datum `SecretNearExpiry` kommer en händelse att publiceras inom några minuter. Den här händelsen kommer i sin tur att utlösa funktionen för att rotera hemligheten.

Du kan kontrol lera att åtkomst nycklarna har återskapats genom att hämta lagrings konto nyckeln och Key Vault hemligheten och jämföra dem.

Använd det här kommandot för att hämta den hemliga informationen:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey
```
Observera att `CredentialId` har uppdaterats till den alternativa `keyName` och som `value` har återskapats: ![ skärm bild som visar utdata från ett z-valvs hemliga show-kommando för det första lagrings kontot.](../media/secrets/rotation-dual/dual-rotation-4.png)

Hämta åtkomst nycklar för att jämföra värdena:
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Skärm bild som visar utdata från kommandot a z Storage konto Keys-lista för det första lagrings kontot.](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-storage-accounts-for-rotation"></a>Lägg till lagrings konton för rotation

Du kan återanvända samma Function-app för att rotera nycklar för flera lagrings konton. 

Om du vill lägga till lagrings konto nycklar i en befintlig funktion för rotation behöver du:
- Rollen lagrings konto nyckel operatör som är tilldelad till Function-appen så att den kan komma åt åtkomst nycklar för lagrings kontot.
- En Event Grid händelse prenumeration för **SecretNearExpiry** -händelsen.

1. Välj distributions länk för Azure-mallar: 

   [![Azure Template Deployment-länk.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FAdd-Event-Subscriptions%2Fazuredeploy.json)

1. I listan **resurs grupp** väljer du **akvrotation**.
1. I rutan **lagrings konto namn** anger du namnet på det lagrings konto som innehåller de åtkomst nycklar som ska roteras.
1. I rutan **Key Vault namn** anger du namnet på nyckel valvet.
1. I rutan **Funktionsapp namn** anger du namnet på Function-appen.
1. I rutan **hemligt namn** anger du namnet på hemligheten där du kommer att lagra åtkomst nycklar.
1. Välj **Granska + skapa**.
1. Välj **Skapa**.

   ![Skärm bild som visar hur du skapar ett ytterligare lagrings konto.](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Lägg till en annan lagrings konto åtkomst nyckel i Key Vault

Fastställ resurs-ID för lagrings kontot. Du kan hitta det här värdet i `id` egenskapen.
```azurecli
az storage account show -n akvrotationstorage2
```

Visa en lista med åtkomst nycklar för lagrings kontot så att du kan hämta key2-värdet:

```azurecli
az storage account keys list -n akvrotationstorage2 
```

Kör det här kommandot med dina hämtade värden för `key2Value` och `storageAccountResourceId` :

```azurecli
tomorrowDate=`date -d tomorrow -Iseconds -u | awk -F'+' '{print $1"Z"}'`
az keyvault secret set --name storageKey2 --vault-name akvrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Använd det här kommandot för att hämta den hemliga informationen:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey2
```
Observera att `CredentialId` har uppdaterats till den alternativa `keyName` och som `value` har återskapats: ![ skärm bild som visar utdata från ett z-valvs hemliga show-kommando för det andra lagrings kontot.](../media/secrets/rotation-dual/dual-rotation-8.png)

Hämta åtkomst nycklar för att jämföra värdena:
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Skärm bild som visar utdata från kommandot a z Storage konto Keys-lista för det andra lagrings kontot.](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="key-vault-dual-credential-rotation-functions"></a>Key Vault rotations funktioner för dubbla autentiseringsuppgifter

- [Lagringskonto](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Redis-cache](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="next-steps"></a>Nästa steg
- Översikt: [övervaka Key Vault med Azure Event Grid](../general/event-grid-overview.md)
- Gör så här: [skapa din första funktion i Azure Portal](../../azure-functions/functions-create-first-azure-function.md)
- Gör så här: [ta emot e-post när en Key Vault hemliga ändringar](../general/event-grid-logicapps.md)
- Referens: [Azure Event Grid händelse schema för Azure Key Vault](../../event-grid/event-schema-key-vault.md)
