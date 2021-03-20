---
title: Kryptera program källan i vila
description: Lär dig hur du krypterar dina program data i Azure Storage och distribuerar dem som en paketfil.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 5524b749b1e15342dd0133920d7190e33ced18ad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92146040"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>Kryptering i vila med Kundhanterade nycklar

Att kryptera webbappens program data på rest kräver ett Azure Storage-konto och ett Azure Key Vault. Dessa tjänster används när du kör din app från ett distributions paket.

  - [Azure Storage tillhandahåller kryptering i vila](../storage/common/storage-service-encryption.md). Du kan använda de nycklar som anges av systemet eller dina egna Kundhanterade nycklar. Detta är den plats där dina program data lagras när de inte körs i en webbapp i Azure.
  - Att [köra från ett distributions paket](deploy-run-package.md) är en distributions funktion i App Service. Du kan distribuera plats innehållet från ett Azure Storage-konto med hjälp av en URL för signatur för delad åtkomst (SAS).
  - [Key Vault referenser](app-service-key-vault-references.md) är en säkerhetsfunktion i App Service. Du kan importera hemligheter vid körning som program inställningar. Använd detta för att kryptera SAS-URL: en för ditt Azure Storage konto.

## <a name="set-up-encryption-at-rest"></a>Konfigurera kryptering i vila

### <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto

Skapa först [ett Azure Storage-konto](../storage/common/storage-account-create.md) och [kryptera det med Kundhanterade nycklar](../storage/common/customer-managed-keys-overview.md). När lagrings kontot har skapats använder du [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) för att ladda upp paketfiler.

Använd sedan Storage Explorer för att [skapa en SAS](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer). 

> [!NOTE]
> Spara denna SAS-URL. används senare för att aktivera säker åtkomst till distributions paketet vid körning.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>Konfigurera körning från ett paket från ditt lagrings konto
  
När du har laddat upp filen till Blob Storage och har en SAS-URL för filen, anger du `WEBSITE_RUN_FROM_PACKAGE` program inställningen till SAS-URL: en. I följande exempel används Azure CLI:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

Om du lägger till den här program inställningen startas din webbapp om. När appen har startats om, bläddrar du till den och kontrollerar att appen har startats på rätt sätt med distributions paketet. Om programmet inte startades korrekt, se [fel söknings guiden för att köra från paket](deploy-run-package.md#troubleshooting).

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Kryptera program inställningen med hjälp av Key Vault referenser

Nu kan du ersätta värdet för `WEBSITE_RUN_FROM_PACKAGE` program inställningen med en Key Vault referens till den SAS-kodade URL: en. Detta sparar SAS-URL: en krypterad i Key Vault, vilket ger ett extra säkerhets lager.

1. Använd följande [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create) kommando för att skapa en Key Vault-instans.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Följ [de här anvisningarna för att ge appen åtkomst](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) till ditt nyckel valv:

1. Använd följande [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) kommando för att lägga till din externa URL som en hemlighet i ditt nyckel valv:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  Använd följande [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) kommando för att skapa `WEBSITE_RUN_FROM_PACKAGE` program inställningen med värdet som en Key Vault referens till den externa URL: en:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    `<secret-version>`Kommer att finnas i resultatet från föregående `az keyvault secret set` kommando.

Att uppdatera den här program inställningen gör att din webbapp startar om. När appen har startats om, bläddrar du till den och kontrollerar att den har startats korrekt med Key Vault referens.

## <a name="how-to-rotate-the-access-token"></a>Så här roterar du åtkomsttoken

Vi rekommenderar att du regelbundet roterar SAS-nyckeln för ditt lagrings konto. För att säkerställa att webbappen inte oavsiktligt löst åtkomst måste du också uppdatera SAS-URL: en i Key Vault.

1. Rotera SAS-nyckeln genom att gå till ditt lagrings konto i Azure Portal. Under **Inställningar**  >  **åtkomst nycklar** klickar du på ikonen för att rotera SAS-nyckeln.

1. Kopiera den nya SAS-webbadressen och Använd följande kommando för att ange den uppdaterade SAS-URL: en i ditt nyckel valv:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Uppdatera Key Vault-referensen i din program inställning till den nya hemliga versionen:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    `<secret-version>`Kommer att finnas i resultatet från föregående `az keyvault secret set` kommando.

## <a name="how-to-revoke-the-web-apps-data-access"></a>Så här återkallar du webbappens data åtkomst

Det finns två metoder för att återkalla webbappens åtkomst till lagrings kontot. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Rotera SAS-nyckeln för det Azure Storage kontot

Om SAS-nyckeln för lagrings kontot är roterad kommer webbappen inte längre att ha åtkomst till lagrings kontot, men den fortsätter att köras med den senast hämtade versionen av paket filen. Ta bort den senast nedladdade versionen genom att starta om webb programmet.

### <a name="remove-the-web-apps-access-to-key-vault"></a>Ta bort webbappens åtkomst till Key Vault

Du kan återkalla webbappens åtkomst till-plats data genom att inaktivera webbappens åtkomst till Key Vault. Det gör du genom att ta bort åtkomst principen för webbappens identitet. Detta är samma identitet som du skapade tidigare när du konfigurerade Key Vault-referenser.

## <a name="summary"></a>Sammanfattning

Programfilerna är nu krypterade i vila i ditt lagrings konto. När din webbapp startar hämtar den SAS-URL: en från ditt nyckel valv. Slutligen läser webb programmet programfilerna från lagrings kontot. 

Om du behöver återkalla webbappens åtkomst till ditt lagrings konto kan du antingen återkalla åtkomst till nyckel valvet eller rotera lagrings konto nycklarna, vilket gör SAS-webbadressen ogiltig.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="is-there-any-additional-charge-for-running-my-web-app-from-the-deployment-package"></a>Finns det någon extra avgift för att köra min webbapp från distributions paketet?

Endast kostnaden som är kopplad till Azure Storage kontot och eventuella tillämpliga utgående kostnader.

### <a name="how-does-running-from-the-deployment-package-affect-my-web-app"></a>Hur påverkar körningen från distributions paketet min webbapp?

- Att köra din app från distributions paketet är `wwwroot/` skrivskyddad. Din app får ett fel meddelande när den försöker skriva till den här katalogen.
- TAR-och GZIP-format stöds inte.
- Den här funktionen är inte kompatibel med lokal cache.

## <a name="next-steps"></a>Nästa steg

- [Key Vault referenser för App Service](app-service-key-vault-references.md)
- [Azure Storage-kryptering av vilande data](../storage/common/storage-service-encryption.md)