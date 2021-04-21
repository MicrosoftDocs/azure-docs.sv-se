---
title: Kryptera programmets vilokälla
description: Lär dig hur du krypterar dina programdata i Azure Storage och distribuerar dem som en paketfil.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: a26660723fbe96a9b765401af1f0c9cfc80dbc3f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779442"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>Kryptering i vila med kund hanterade nycklar

Kryptering av webbappens programdata i vila kräver ett Azure Storage konto och en Azure Key Vault. Dessa tjänster används när du kör din app från ett distributionspaket.

  - [Azure Storage tillhandahåller kryptering i vila](../storage/common/storage-service-encryption.md). Du kan använda systemtilldeade nycklar eller dina egna, kund-hanterade nycklar. Det är här dina programdata lagras när de inte körs i en webbapp i Azure.
  - [Att köra från ett distributionspaket](deploy-run-package.md) är en distributionsfunktion i App Service. Det gör att du kan distribuera ditt webbplatsinnehåll från ett Azure Storage-konto med hjälp av en SAS-URL (signatur för delad åtkomst).
  - [Key Vault referenser](app-service-key-vault-references.md) är en säkerhetsfunktion i App Service. Det gör att du kan importera hemligheter vid körning som programinställningar. Använd detta för att kryptera SAS-URL:en för ditt Azure Storage konto.

## <a name="set-up-encryption-at-rest"></a>Konfigurera kryptering i vila

### <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto

Skapa först [ett Azure Storage och](../storage/common/storage-account-create.md) kryptera det med [kund-hanterade nycklar](../storage/common/customer-managed-keys-overview.md). När lagringskontot har skapats använder du Azure Storage Explorer [för att](../vs-azure-tools-storage-manage-with-storage-explorer.md) ladda upp paketfiler.

Använd sedan -Storage Explorer för att [generera en SAS](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer). 

> [!NOTE]
> Spara SAS-URL:en. Den används senare för att aktivera säker åtkomst till distributionspaketet vid körning.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>Konfigurera körning från ett paket från ditt lagringskonto
  
När du har laddat upp filen till Blob Storage och har en SAS-URL för filen anger du `WEBSITE_RUN_FROM_PACKAGE` programinställningen till SAS-URL:en. I följande exempel används Azure CLI:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

Om du lägger till den här programinställningen startas webbappen om. När appen har startats om bläddrar du till den och kontrollerar att appen har startats korrekt med hjälp av distributionspaketet. Om programmet inte har börjat korrekt kan du gå till [felsökningsguiden Kör från paket.](deploy-run-package.md#troubleshooting)

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Kryptera programinställningen med hjälp av Key Vault referenser

Nu kan du ersätta värdet för `WEBSITE_RUN_FROM_PACKAGE` programinställningen med en Key Vault till den SAS-kodade URL:en. Detta håller SAS-URL:en krypterad Key Vault, vilket ger ett extra säkerhetslager.

1. Använd följande kommando [`az keyvault create`](/cli/azure/keyvault#az_keyvault_create) för att skapa en Key Vault instans.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Följ [de här anvisningarna för att ge din app åtkomst](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) till ditt nyckelvalv:

1. Använd följande kommando [`az keyvault secret set`](/cli/azure/keyvault/secret#az_keyvault_secret_set) för att lägga till din externa URL som en hemlighet i nyckelvalvet:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  Använd följande kommando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) för att skapa `WEBSITE_RUN_FROM_PACKAGE` programinställningen med värdet som en Key Vault till den externa URL:en:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    `<secret-version>`kommer att finnas i utdata från föregående `az keyvault secret set` kommando.

Om du uppdaterar den här programinställningen startas webbappen om. När appen har startats om bläddrar du till den och kontrollerar att den har startat korrekt med hjälp av Key Vault referensen.

## <a name="how-to-rotate-the-access-token"></a>Så här roterar du åtkomsttoken

Det är bästa praxis att regelbundet rotera SAS-nyckeln för ditt lagringskonto. För att säkerställa att webbappen inte oavsiktligt förlorar åtkomst måste du också uppdatera SAS-URL:en i Key Vault.

1. Rotera SAS-nyckeln genom att gå till ditt lagringskonto i Azure Portal. Under **Inställningar**  >  **Åtkomstnycklar klickar** du på ikonen för att rotera SAS-nyckeln.

1. Kopiera den nya SAS-URL:en och använd följande kommando för att ange den uppdaterade SAS-URL:en i nyckelvalvet:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Uppdatera key vault-referensen i programinställningen till den nya hemliga versionen:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    `<secret-version>`kommer att finnas i utdata från föregående `az keyvault secret set` kommando.

## <a name="how-to-revoke-the-web-apps-data-access"></a>Så här återkallar du webbappens dataåtkomst

Det finns två metoder för att återkalla webbappens åtkomst till lagringskontot. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Rotera SAS-nyckeln för Azure Storage konto

Om SAS-nyckeln för lagringskontot roteras har webbappen inte längre åtkomst till lagringskontot, men den fortsätter att köras med den senast nedladdade versionen av paketfilen. Starta om webbappen för att rensa den senaste nedladdade versionen.

### <a name="remove-the-web-apps-access-to-key-vault"></a>Ta bort webbappens åtkomst till Key Vault

Du kan återkalla webbappens åtkomst till webbplatsdata genom att inaktivera webbappens åtkomst till Key Vault. Det gör du genom att ta bort åtkomstprincipen för webbappens identitet. Det här är samma identitet som du skapade tidigare när du konfigurerade nyckelvalvsreferenser.

## <a name="summary"></a>Sammanfattning

Dina programfiler krypteras nu i vila på ditt lagringskonto. När webbappen startar hämtas SAS-URL:en från nyckelvalvet. Slutligen läser webbappen in programfilerna från lagringskontot. 

Om du behöver återkalla webbappens åtkomst till ditt lagringskonto kan du antingen återkalla åtkomsten till nyckelvalvet eller rotera lagringskontonycklarna, vilket gör SAS-URL:en ogiltig.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="is-there-any-additional-charge-for-running-my-web-app-from-the-deployment-package"></a>Tillkommer några ytterligare avgifter för att köra min webbapp från distributionspaketet?

Endast den kostnad som är associerad Azure Storage kontot och eventuella utgående avgifter.

### <a name="how-does-running-from-the-deployment-package-affect-my-web-app"></a>Hur påverkar körning från distributionspaketet min webbapp?

- Om du kör appen från distributionspaketet blir `wwwroot/` det skrivskyddade. Appen får ett felmeddelande när den försöker skriva till den här katalogen.
- TAR- och GZIP-format stöds inte.
- Den här funktionen är inte kompatibel med lokal cache.

## <a name="next-steps"></a>Nästa steg

- [Key Vault referenser för App Service](app-service-key-vault-references.md)
- [Azure Storage-kryptering av vilande data](../storage/common/storage-service-encryption.md)