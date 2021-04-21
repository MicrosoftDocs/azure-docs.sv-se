---
title: Rotationsskurs för resurser med en uppsättning autentiseringsuppgifter som lagras i Azure Key Vault
description: Använd den här självstudien om du vill lära dig hur du automatiserar rotationen av en hemlighet för resurser som använder en uppsättning autentiseringsuppgifter.
services: key-vault
author: msmbaldwin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp
ms.openlocfilehash: e66be3b0e3ecae5caa1a76294cc8b8dc11a5f207
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748672"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-one-set-of-authentication-credentials"></a>Automatisera rotationen av en hemlighet för resurser som använder en uppsättning autentiseringsuppgifter

Det bästa sättet att autentisera till Azure-tjänster är att använda en hanterad identitet [,](../general/authentication.md)men det finns vissa scenarier där det inte är ett alternativ. I dessa fall används åtkomstnycklar eller hemligheter. Du bör regelbundet rotera åtkomstnycklar eller hemligheter.

Den här självstudien visar hur du automatiserar periodisk rotation av hemligheter för databaser och tjänster som använder en uppsättning autentiseringsuppgifter. Mer specifikt roterar den här självstudien SQL Server lösenord som lagras i Azure Key Vault med hjälp av en funktion som utlöses av ett Azure Event Grid meddelande:


:::image type="content" source="../media/rotate-1.png" alt-text="Diagram över rotationslösning":::

1. 30 dagar före förfallodatumet för en hemlighet Key Vault publicerar händelsen "nära att gå ut" Event Grid.
1. Event Grid kontrollerar händelseprenumerationer och använder HTTP POST för att anropa funktionsappens slutpunkt som prenumererar på händelsen.
1. Funktionsappen tar emot den hemliga informationen, genererar ett nytt slumpmässigt lösenord och skapar en ny version för hemligheten med det nya lösenordet i Key Vault.
1. Funktionsappen uppdaterar SQL Server med det nya lösenordet.

> [!NOTE]
> Det kan finnas en fördröjning mellan steg 3 och 4. Under den tiden kommer hemligheten i Key Vault inte att kunna autentisera till SQL Server. Om något av stegen misslyckas kan du Event Grid i två timmar.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration [– skapa en utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure Key Vault
* SQL Server

Distributionslänken nedan kan användas om du inte har några befintliga Key Vault och SQL Server:

[![Bild som visar en knapp med etiketten "Distribuera till Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmain%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. Under **Resursgrupp** väljer du **Skapa ny**. Ge gruppen ett namn. Vi använder **akvrotation i den här** självstudien.
1. Under **Sql Admin Login (Inloggning för SQL-administratör)** skriver du sql-administratörens inloggningsnamn. 
1. Välj **Granska + skapa**.
1. Välj **Skapa**

:::image type="content" source="../media/rotate-2.png" alt-text="Skapa en resursgrupp":::

Nu har du en Key Vault och en SQL Server instans. Du kan kontrollera den här konfigurationen i Azure CLI genom att köra följande kommando:

```azurecli
az resource list -o table -g akvrotation
```

Resultatet ser ut ungefär så här:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation      eastus      Microsoft.Sql/servers/databases
akvrotation-sql2         akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql2/master  akvrotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-and-deploy-sql-server-password-rotation-function"></a>Skapa och distribuera sql server-funktionen för lösenordsrotation
> [!IMPORTANT]
> Mallen nedan kräver Key Vault, SQL Server och Azure Function finns i samma resursgrupp

Skapa sedan en funktionsapp med en system hanterad identitet, utöver de andra nödvändiga komponenterna, och distribuera sql server-lösenordsrotationsfunktioner

Funktionsappen kräver följande komponenter:
- En Azure App Service plan
- En funktionsapp med sql-lösenordsrotationsfunktioner med händelseutlösare och HTTP-utlösare 
- Ett lagringskonto som krävs för hantering av funktionsapputlösare
- En åtkomstprincip för funktionsappens identitet för att få åtkomst till hemligheter i Key Vault
- En EventGrid-händelseprenumeration **för SecretNearExpiry-händelse**

1. Välj länken för Azure-malldistribution: 

   [![Bild som visar en knapp med etiketten "Distribuera till Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmain%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. I listan **Resursgrupp** väljer du **akvrotation**.
1. I **SQL Server-namn skriver** du sql Server-namnet med lösenordet som ska roteras
1. I Key Vault **anger** du namnet på nyckelvalvet
1. I **Funktionsappens namn** anger du funktionsappens namn
1. I **Hemlighetsnamn** skriver du ett hemligt namn där lösenordet ska lagras
1. I **lagringsplatsens URL** skriver du funktionskoden GitHub location ( **https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp.git** )
1. Välj **Granska + skapa**.
1. Välj **Skapa**.

:::image type="content" source="../media/rotate-3.png" alt-text="Välj Granska och skapa":::
  

När du har slutfört föregående steg har du ett lagringskonto, en servergrupp och en funktionsapp. Du kan kontrollera den här konfigurationen i Azure CLI genom att köra följande kommando:

```azurecli
az resource list -o table -g akvrotation
```

Resultatet ser ut ungefär som följande utdata:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation       eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation       eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation       eastus      Microsoft.Sql/servers/databases
cfogyydrufs5wazfunctions akvrotation       eastus      Microsoft.Storage/storageAccounts
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/serverFarms
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/sites
akvrotation-fnapp        akvrotation       eastus      Microsoft.insights/components
```

Information om hur du skapar en funktionsapp och använder hanterad identitet för att få åtkomst till Key Vault finns i Skapa en [funktionsapp](../../azure-functions/functions-create-function-app-portal.md)från Azure Portal , Använda hanterad identitet för App Service och [Azure Functions](../../app-service/overview-managed-identity.md)och Tilldela en [Key Vault-åtkomstprincip](../general/assign-access-policy-portal.md)med hjälp av Azure Portal .

### <a name="rotation-function"></a>Rotationsfunktion
Funktionen som distribuerades i föregående steg använder en händelse för att utlösa rotationen av en hemlighet genom att Key Vault och SQL-databasen. 

#### <a name="function-trigger-event"></a>Funktionsutlösarhändelse

Den här funktionen läser händelsedata och kör rotationslogiken:

```csharp
public static class SimpleRotationEventHandler
{
   [FunctionName("AKVSQLRotation")]
   public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
   {
      log.LogInformation("C# Event trigger function processed a request.");
      var secretName = eventGridEvent.Subject;
      var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
      var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
      log.LogInformation($"Key Vault Name: {keyVaultName}");
      log.LogInformation($"Secret Name: {secretName}");
      log.LogInformation($"Secret Version: {secretVersion}");

      SecretRotator.RotateSecret(log, secretName, keyVaultName);
   }
}
```

#### <a name="secret-rotation-logic"></a>Logik för hemlig rotation
Den här rotationsmetoden läser databasinformation från hemligheten, skapar en ny version av hemligheten och uppdaterar databasen med den nya hemligheten:

```csharp
    public class SecretRotator
    {
        private const string CredentialIdTag = "CredentialId";
        private const string ProviderAddressTag = "ProviderAddress";
        private const string ValidityPeriodDaysTag = "ValidityPeriodDays";

        public static void RotateSecret(ILogger log, string secretName, string keyVaultName)
        {
            //Retrieve Current Secret
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";
            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
            KeyVaultSecret secret = client.GetSecret(secretName);
            log.LogInformation("Secret Info Retrieved");

            //Retrieve Secret Info
            var credentialId = secret.Properties.Tags.ContainsKey(CredentialIdTag) ? secret.Properties.Tags[CredentialIdTag] : "";
            var providerAddress = secret.Properties.Tags.ContainsKey(ProviderAddressTag) ? secret.Properties.Tags[ProviderAddressTag] : "";
            var validityPeriodDays = secret.Properties.Tags.ContainsKey(ValidityPeriodDaysTag) ? secret.Properties.Tags[ValidityPeriodDaysTag] : "";
            log.LogInformation($"Provider Address: {providerAddress}");
            log.LogInformation($"Credential Id: {credentialId}");

            //Check Service Provider connection
            CheckServiceConnection(secret);
            log.LogInformation("Service  Connection Validated");
            
            //Create new password
            var randomPassword = CreateRandomPassword();
            log.LogInformation("New Password Generated");

            //Add secret version with new password to Key Vault
            CreateNewSecretVersion(client, secret, randomPassword);
            log.LogInformation("New Secret Version Generated");

            //Update Service Provider with new password
            UpdateServicePassword(secret, randomPassword);
            log.LogInformation("Password Changed");
            log.LogInformation($"Secret Rotated Successfully");
        }
}
```
Du hittar den fullständiga koden på [GitHub](https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp).

## <a name="add-the-secret-to-key-vault"></a>Lägg till hemligheten i Key Vault
Ange din åtkomstprincip för att bevilja *användare behörighet att* hantera hemligheter:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Skapa en ny hemlighet med taggar som innehåller SQL Server resurs-ID, SQL Server inloggningsnamn och giltighetsperiod för hemligheten i dagar. Ange namnet på hemligheten, det första lösenordet från SQL-databasen (i vårt exempel "Simple123") och inkludera ett förfallodatum som är inställt på imorgon.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqlPassword --vault-name akvrotation-kv --value "Simple123" --tags "CredentialId=sqlAdmin" "ProviderAddress=<sql-database-resource-id>" "ValidityPeriodDays=90" --expires $tomorrowDate
```

Om du skapar en hemlighet med ett kort förfallodatum publiceras en händelse inom 15 minuter, vilket i sin tur utlöser `SecretNearExpiry` funktionen för att rotera hemligheten.

## <a name="test-and-verify"></a>Testa och verifiera

Kontrollera att hemligheten har roterats genom att gå till **Key Vault**  >  **Hemligheter:**

:::image type="content" source="../media/rotate-8.png" alt-text="Skärmbild som visar hur du kommer åt Key Vault > hemligheter.":::

Öppna **hemligheten sqlPassword** och visa de ursprungliga och roterade versionerna:

:::image type="content" source="../media/rotate-9.png" alt-text="Gå till Hemligheter":::

### <a name="create-a-web-app"></a>Skapa en webbapp

Skapa en webbapp för att verifiera SQL-autentiseringsuppgifterna. Den här webbappen hämtar hemligheten från Key Vault, extraherar SQL-databasinformation och autentiseringsuppgifter från hemligheten och testar anslutningen till SQL Server.

Webbappen kräver följande komponenter:
- En webbapp med system hanterad identitet
- En åtkomstprincip för att komma åt hemligheter i Key Vault via hanterad identitet för webbapp

1. Välj länken för Azure-malldistribution: 

   [![Bild som visar en knapp med etiketten "Distribuera till Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp-WebApp%2Fmain%2FARM-Templates%2FWeb-App%2Fazuredeploy.json)

1. Välj **resursgruppen akvrotation.**
1. I **SQL Server-namn skriver** du sql Server-namnet med lösenordet som ska roteras
1. I Key Vault **anger** du namnet på nyckelvalvet
1. I **Hemlighetsnamn** skriver du ett hemligt namn där lösenordet lagras
1. I **lagringsplatsens URL** skriver du webbappkoden GitHub location ( **https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp-WebApp.git** )
1. Välj **Granska + skapa**.
1. Välj **Skapa**.


### <a name="open-the-web-app"></a>Öppna webbappen

Gå till url:en för det distribuerade programmet:
 
'https://akvrotation-app.azurewebsites.net/'

När programmet öppnas i webbläsaren visas genererat **hemlighetsvärde och värdet** **Databasansluten** på *sant.*

## <a name="learn-more"></a>Läs mer

- Självstudie: [Rotation för resurser med två uppsättningar autentiseringsuppgifter](tutorial-rotation-dual.md)
- Översikt: [Övervaka Key Vault med Azure Event Grid](../general/event-grid-overview.md)
- Gör så här: Ta [emot e-post när en nyckelvalvshemlighet ändras](../general/event-grid-logicapps.md)
- [Azure Event Grid händelseschema för Azure Key Vault](../../event-grid/event-schema-key-vault.md)
