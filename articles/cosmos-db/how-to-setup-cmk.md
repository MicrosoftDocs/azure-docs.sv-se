---
title: Konfigurera Kundhanterade nycklar för ditt Azure Cosmos DB-konto
description: Lär dig hur du konfigurerar Kundhanterade nycklar för ditt Azure Cosmos DB-konto med Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/01/2021
ms.author: thweiss
ms.openlocfilehash: 1b1fc0b51c1cd2a99ec97bec9f588699a893ceca
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222630"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Konfigurera kundhanterade nycklar för ditt Azure Cosmos-konto med Azure Key Vault
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Data som lagras i ditt Azure Cosmos-konto är automatiskt och sömlöst krypterade med nycklar som hanteras av Microsoft (**tjänst hanterade nycklar**). Du kan också välja att lägga till ett andra lager av kryptering med nycklar som du hanterar (**Kundhanterade nycklar**).

:::image type="content" source="./media/how-to-setup-cmk/cmk-intro.png" alt-text="Lager för kryptering runt kund information":::

Du måste lagra Kundhanterade nycklar i [Azure Key Vault](../key-vault/general/overview.md) och tillhandahålla en nyckel för varje Azure Cosmos-konto som är aktiverat med Kundhanterade nycklar. Den här nyckeln används för att kryptera alla data som lagras i kontot.

> [!NOTE]
> För närvarande är Kundhanterade nycklar endast tillgängliga för nya Azure Cosmos-konton. Du bör konfigurera dem när kontot skapas.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a> Registrera Azure Cosmos DB Resource Provider för din Azure-prenumeration

1. Logga in på [Azure Portal](https://portal.azure.com/), gå till din Azure-prenumeration och välj **resurs leverantörer** på fliken **Inställningar** :

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp.png" alt-text="Resurs leverantörs post från den vänstra menyn":::

1. Sök efter **Microsoft.DocumentDB** Resource Provider. Kontrol lera att resurs leverantören redan har marker ATS som registrerad. Annars väljer du resurs leverantören och väljer **Registrera**:

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp-register.png" alt-text="Registrera Microsoft.DocumentDB Resource Provider":::

## <a name="configure-your-azure-key-vault-instance"></a>Konfigurera Azure Key Vault-instansen

Om du använder Kundhanterade nycklar med Azure Cosmos DB måste du ange två egenskaper för Azure Key Vault-instansen som du planerar att använda som värd för dina krypterings nycklar: **mjuk borttagning** och **tömning av skydd**.

Om du skapar en ny Azure Key Vault-instans aktiverar du dessa egenskaper när de skapas:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-prop.png" alt-text="Aktivera mjuk borttagning och rensning av skydd för en ny Azure Key Vault-instans":::

Om du använder en befintlig Azure Key Vault-instans kan du kontrol lera att dessa egenskaper är aktiverade genom att titta i avsnittet **Egenskaper** på Azure Portal. Om någon av dessa egenskaper inte är aktive rad kan du läsa avsnitten "Aktivera mjuk borttagning" och "Aktivera rensnings skydd" i någon av följande artiklar:

- [Använda mjuk borttagning med PowerShell](../key-vault/general/key-vault-recovery.md)
- [Använda mjuk borttagning med Azure CLI](../key-vault/general/key-vault-recovery.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a><a id="add-access-policy"></a> Lägg till en åtkomst princip till Azure Key Vault-instansen

1. Från Azure Portal går du till den Azure Key Vault instans som du planerar att använda som värd för dina krypterings nycklar. Välj **åtkomst principer** på den vänstra menyn:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-ap.png" alt-text="Åtkomst principer från den vänstra menyn":::

1. Välj **+ Lägg till åtkomst princip**.

1. Under List rutan **nyckel behörigheter** väljer du **Hämta**, **packa upp nyckel** och packa upp **nyckel** behörigheter:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png" alt-text="Välja rätt behörigheter":::

1. Under **Välj huvud konto** väljer du **ingen vald**.

1. Sök efter **Azure Cosmos DB** huvud konto och välj det (för att göra det lättare att hitta kan du också söka efter huvud-ID: `a232010e-820c-4083-83bb-3ace5fc29d0b` för alla Azure-regioner utom Azure Government regioner där ägar-ID är `57506a73-e302-42a9-b869-6f12d9ec29e9` ). Om **Azure Cosmos DB** -huvudobjektet inte finns i listan kan du behöva registrera om **Microsoft.DocumentDB** Resource Provider enligt beskrivningen i avsnittet [Registrera resurs leverantören](#register-resource-provider) i den här artikeln.

   > [!NOTE]
   > Detta registrerar Azure Cosmos DB första parts identitet i din Azure Key Vault åtkomst princip. Om du vill ersätta den här identiteten hos den första parten med din Azure Cosmos DB hanterade identitet för kontot, se [använda en hanterad identitet i Azure Key Vault åtkomst princip](#using-managed-identity).

1. Välj **Välj** längst ned. 

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap.png" alt-text="Välj Azure Cosmos DB huvud konto":::

1. Välj **Lägg** till för att lägga till den nya åtkomst principen.

1. Välj **Spara** på Key Vault-instansen för att spara alla ändringar.

## <a name="generate-a-key-in-azure-key-vault"></a>Generera en nyckel i Azure Key Vault

1. Från Azure Portal, gå till den Azure Key Vault instans som du planerar att använda som värd för dina krypterings nycklar. Välj sedan **nycklar** i den vänstra menyn:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keys.png" alt-text="Nyckel inmatning från den vänstra menyn":::

1. Välj **generera/importera**, ange ett namn för den nya nyckeln och välj en nyckel storlek för RSA. Minst 3072 rekommenderas för bästa säkerhet. Välj sedan **skapa**:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-gen.png" alt-text="Skapa en ny nyckel":::

1. När nyckeln har skapats väljer du den nya nyckeln och sedan dess aktuella version.

1. Kopiera nyckelns **nyckel identifierare**, förutom delen efter det sista snedstrecket:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keyid.png" alt-text="Nyckelns nyckel identifierare kopieras":::

## <a name="create-a-new-azure-cosmos-account"></a>Skapa ett nytt Azure Cosmos-konto

### <a name="using-the-azure-portal"></a>Använda Azure Portal

När du skapar ett nytt Azure Cosmos DB konto från Azure Portal väljer du **kundhanterad nyckel** i **krypterings** steget. I fältet **nyckel-URI** klistrar du in URI/nyckel-ID för den Azure Key Vault nyckel som du kopierade från föregående steg:

:::image type="content" source="./media/how-to-setup-cmk/portal-cosmos-enc.png" alt-text="Ange CMK-parametrar i Azure Portal":::

### <a name="using-azure-powershell"></a><a id="using-powershell"></a> Använda Azure PowerShell

När du skapar ett nytt Azure Cosmos DB-konto med PowerShell:

- Skicka URI: n för den Azure Key Vault nyckel som kopierats tidigare under egenskapen **keyVaultKeyUri** i **PropertyObject**.

- Använd **2019-12-12** eller senare som API-version.

> [!IMPORTANT]
> Du måste ange `locations` egenskapen explicit för att kontot ska kunna skapas med Kundhanterade nycklar.

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

När kontot har skapats kan du kontrol lera att Kundhanterade nycklar har Aktiver ATS genom att hämta URI för Azure Key Vault nyckel:

```powershell
Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    | Select-Object -ExpandProperty Properties `
    | Select-Object -ExpandProperty keyVaultKeyUri
```

### <a name="using-an-azure-resource-manager-template"></a>Använda en Azure Resource Manager mall

När du skapar ett nytt Azure Cosmos-konto via en Azure Resource Manager-mall:

- Skicka URI: n för den Azure Key Vault nyckel som du kopierade tidigare under egenskapen **keyVaultKeyUri** i objektet **Properties** .

- Använd **2019-12-12** eller senare som API-version.

> [!IMPORTANT]
> Du måste ange `locations` egenskapen explicit för att kontot ska kunna skapas med Kundhanterade nycklar.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}
```

Distribuera mallen med följande PowerShell-skript:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

### <a name="using-the-azure-cli"></a><a id="using-azure-cli"></a> Använda Azure CLI

När du skapar ett nytt Azure Cosmos-konto via Azure CLI skickar du URI: n för den Azure Key Vault nyckel som du kopierade tidigare under `--key-uri` parametern.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --key-uri $keyVaultKeyUri
```

När kontot har skapats kan du kontrol lera att Kundhanterade nycklar har Aktiver ATS genom att hämta URI för Azure Key Vault nyckel:

```azurecli-interactive
az cosmosdb show \
    -n $accountName \
    -g $resourceGroupName \
    --query keyVaultKeyUri
```

## <a name="using-a-managed-identity-in-the-azure-key-vault-access-policy"></a><a id="using-managed-identity"></a> Använda en hanterad identitet i Azure Key Vault åtkomst princip

Den här åtkomst principen säkerställer att dina krypterings nycklar kan nås av ditt Azure Cosmos DB-konto. Detta görs genom att bevilja åtkomst till en särskild Azure Active Directory identitet (AD). Två typer av identiteter stöds:

- Azure Cosmos DBens identitet från den första parten kan användas för att bevilja åtkomst till tjänsten Azure Cosmos DB.
- Ditt Azure Cosmos DB kontos [hanterade identitet](how-to-setup-managed-identity.md) kan användas för att ge åtkomst till ditt konto specifikt.

Eftersom en systemtilldelad hanterad identitet bara kan hämtas när ditt konto har skapats, måste du först skapa ditt konto med hjälp av identiteten hos den första parten, enligt beskrivningen [ovan](#add-access-policy). Sedan:

1. Om det inte gick att skapa ett konto [aktiverar du en systemtilldelad hanterad identitet](how-to-setup-managed-identity.md) på ditt konto och kopierar den `principalId` som tilldelades.

1. Lägg till en ny åtkomst princip till ditt Azure Key Vault-konto, precis som det beskrivs [ovan](#add-access-policy), men med hjälp av `principalId` du kopierade i föregående steg i stället för Azure Cosmos DBens identitet från första part.

1. Uppdatera ditt Azure Cosmos DB-konto och ange att du vill använda den systemtilldelade hanterade identiteten vid åtkomst till dina krypterings nycklar i Azure Key Vault. Du kan göra detta genom att ange den här egenskapen i kontots Azure Resource Manager mall:

   ```json
   {
       "type": " Microsoft.DocumentDB/databaseAccounts",
       "properties": {
           "defaultIdentity": "SystemAssignedIdentity",
           // ...
       },
       // ...
   }
   ```

1. Du kan också ta bort Azure Cosmos DB identitet från den första parten från din Azure Key Vault åtkomst princip.

## <a name="key-rotation"></a>Nyckelrotation

Att rotera den Kundhanterade nyckeln som används av ditt Azure Cosmos-konto kan göras på två sätt.

- Skapa en ny version av nyckeln som för närvarande används från Azure Key Vault:

  :::image type="content" source="./media/how-to-setup-cmk/portal-akv-rot.png" alt-text="Skapa en ny nyckel version":::

- Byt ut nyckeln som för närvarande används med en helt annan genom att uppdatera nyckel-URI: n på ditt konto. Från Azure Portal går du till ditt Azure Cosmos-konto och väljer **data kryptering** på den vänstra menyn:

    :::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="Meny posten data kryptering":::

    Ersätt sedan nyckel- **URI: n** med den nya nyckel som du vill använda och välj **Spara**:

    :::image type="content" source="./media/how-to-setup-cmk/portal-key-swap.png" alt-text="Uppdatera nyckel-URI: n":::

    Så här gör du för att uppnå samma resultat i PowerShell:

    ```powershell
    $resourceGroupName = "myResourceGroup"
    $accountName = "mycosmosaccount"
    $newKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-new-key>"
    
    $account = Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
        -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    
    $account.Properties.keyVaultKeyUri = $newKeyUri
    
    $account | Set-AzResource -Force
    ```

Föregående nyckel-eller nyckel version kan inaktive ras efter 24 timmar eller efter att [Azure Key Vault gransknings loggarna](../key-vault/general/logging.md) inte visar aktivitet från Azure Cosmos DB på den nyckeln eller nyckel versionen längre.
    
## <a name="error-handling"></a>Felhantering

Om det finns några fel när du använder Customer-Managed nycklar (CMK) i Azure Cosmos DB Returnerar Azure Cosmos DB fel informationen tillsammans med en HTTP-underordnad status kod i svaret. Du kan använda den här under status koden för att felsöka orsaken till problemet. Se [HTTP-statuskod för Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) artikel om du vill hämta en lista över http-understatus koder som stöds.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="is-there-an-additional-charge-to-enable-customer-managed-keys"></a>Finns det ytterligare kostnader för att aktivera Kundhanterade nycklar?

Nej, det finns ingen avgift för att aktivera den här funktionen.

### <a name="how-do-customer-managed-keys-impact-capacity-planning"></a>Hur påverkar kund hanterade nycklar kapacitets planeringen?

När du använder Kundhanterade nycklar kan [enheter](./request-units.md) som används av databas åtgärderna se en ökning som återspeglar den ytterligare bearbetning som krävs för att utföra kryptering och dekryptering av dina data. Detta kan leda till en något högre användning av den allokerade kapaciteten. Använd tabellen nedan för vägledning:

| Åtgärdstyp | Begär enhets ökning |
|---|---|
| Punkt – läser (hämtar objekt med deras ID) | + 5% per åtgärd |
| Eventuell Skriv åtgärd | + 6% per åtgärd<br/>ca. + 0,06 RU per indexerad egenskap |
| Frågor, läsa ändrings flöde eller ett konflikt flöde | + 15% per åtgärd |

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Vilka data krypteras med kundens Kundhanterade nycklar?

Alla data som lagras i ditt Azure Cosmos-konto krypteras med kundens hanterade nycklar, förutom följande metadata:

- Namnen på dina Azure Cosmos DB [-konton, databaser och behållare](./account-databases-containers-items.md#elements-in-an-azure-cosmos-account)

- Namnen på dina [lagrade procedurer](./stored-procedures-triggers-udfs.md)

- Egenskaps Sök vägarna som deklareras i dina [indexerings principer](./index-policy.md)

- Värdena för dina behållar [partitionerings nycklar](./partitioning-overview.md)

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>Stöds kund hanterade nycklar för befintliga Azure Cosmos-konton?

Den här funktionen är för närvarande endast tillgänglig för nya konton.

### <a name="is-it-possible-to-use-customer-managed-keys-in-conjunction-with-the-azure-cosmos-db-analytical-store"></a>Är det möjligt att använda Kundhanterade nycklar tillsammans med Azure Cosmos DB [Analytical Store](analytical-store-introduction.md)?

Ja, men du måste [använda ditt Azure Cosmos DB kontos hanterade identitet](#using-managed-identity) i din Azure Key Vault åtkomst princip innan du aktiverar analys lagret.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Finns det någon plan för att ge bättre granularitet än nycklar på konto nivå?

Inte för närvarande, men nycklar för container nivå beaktas.

### <a name="how-can-i-tell-if-customer-managed-keys-are-enabled-on-my-azure-cosmos-account"></a>Hur kan jag se om Kundhanterade nycklar är aktiverade på mitt Azure Cosmos-konto?

Från Azure Portal går du till ditt Azure Cosmos-konto och tittar på **data krypterings** posten på den vänstra menyn. om posten finns aktive ras Kundhanterade nycklar på ditt konto:

:::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="Meny posten data kryptering":::

Du kan också hämta information om ditt Azure Cosmos-konto via programmering och leta efter `keyVaultKeyUri` egenskapen. Se ovan för olika sätt att göra det [i PowerShell](#using-powershell) och [med Azure CLI](#using-azure-cli).

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>Hur påverkar kund hanterade nycklar en säkerhets kopia?

Azure Cosmos DB använder [regelbundna och automatiska säkerhets kopieringar](./online-backup-and-restore.md) av de data som lagras i ditt konto. Den här åtgärden säkerhetskopierar krypterade data. Om du vill använda den återställda säkerhets kopian krävs den krypterings nyckel som du använde vid tidpunkten för säkerhets kopieringen. Det innebär att ingen åter kallelse görs och den version av nyckeln som användes vid tidpunkten för säkerhets kopieringen fortfarande kommer att aktive ras.

### <a name="how-do-i-revoke-an-encryption-key"></a>Hur gör jag för att återkalla en krypterings nyckel?

Nyckel återkallning görs genom att den senaste versionen av nyckeln inaktive ras:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev2.png" alt-text="Inaktivera en nyckels version":::

Om du vill återkalla alla nycklar från en Azure Key Vault-instans kan du också ta bort åtkomst principen som beviljats till Azure Cosmos DB-huvudobjektet:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev.png" alt-text="Tar bort åtkomst principen för Azure Cosmos DB-huvudobjektet":::

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Vilka åtgärder är tillgängliga när en kundhanterad nyckel har återkallats?

Den enda åtgärd som är möjlig när krypterings nyckeln har återkallats är borttagning av kontot.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [data kryptering i Azure Cosmos DB](./database-encryption-at-rest.md).
- Få en översikt över [säker åtkomst till data i Cosmos DB](secure-access-to-data.md).
