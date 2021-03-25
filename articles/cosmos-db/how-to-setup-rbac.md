---
title: Konfigurera rollbaserad åtkomst kontroll för ditt Azure Cosmos DB-konto med Azure AD
description: Lär dig hur du konfigurerar rollbaserad åtkomst kontroll med Azure Active Directory för ditt Azure Cosmos DB-konto
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/24/2021
ms.author: thweiss
ms.openlocfilehash: ec12deb5a7c77674026b849585552fd873aee5c1
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046002"
---
# <a name="configure-role-based-access-control-with-azure-active-directory-for-your-azure-cosmos-db-account-preview"></a>Konfigurera rollbaserad åtkomst kontroll med Azure Active Directory för ditt Azure Cosmos DB-konto (för hands version)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB rollbaserad åtkomst kontroll är för närvarande en för hands version. Den här för hands versionen tillhandahålls utan en Serviceavtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

> [!NOTE]
> Den här artikeln är en rollbaserad åtkomst kontroll för data Plans åtgärder i Azure Cosmos DB. Om du använder hanterings plan åtgärder, se [rollbaserad åtkomst kontroll](role-based-access-control.md) som används i artikeln hanterings Plans åtgärder.

Azure Cosmos DB visar ett inbyggt rollbaserad åtkomst kontroll system (RBAC) som gör att du kan:

- Autentisera dina data begär Anden med en Azure Active Directory (Azure AD)-identitet.
- Auktorisera dina data begär Anden med en detaljerad, rollbaserad behörighets modell.

## <a name="concepts"></a>Begrepp

RBAC-Azure Cosmos DB data planet bygger på begrepp som ofta finns i andra RBAC-system som [Azure RBAC](../role-based-access-control/overview.md):

- [Behörighets modellen](#permission-model) består av en uppsättning **åtgärder**. var och en av dessa åtgärder mappar till en eller flera databas åtgärder. Några exempel på åtgärder är att läsa ett objekt, skriva ett objekt eller köra en fråga.
- Azure Cosmos DB användare skapar **[roll definitioner](#role-definitions)** som innehåller en lista över tillåtna åtgärder.
- Roll definitioner tilldelas till vissa Azure AD-identiteter via **[roll tilldelningar](#role-assignments)**. En roll tilldelning definierar också den omfattning som roll definitionen gäller för. för närvarande är tre omfattningar för närvarande:
    - Ett Azure Cosmos DB konto,
    - En Azure Cosmos DB databas,
    - En Azure Cosmos DB behållare.

  :::image type="content" source="./media/how-to-setup-rbac/concepts.png" alt-text="RBAC-koncept":::

> [!NOTE]
> Azure Cosmos DB RBAC exponerar för närvarande inte några inbyggda roll definitioner.

## <a name="permission-model"></a><a id="permission-model"></a> Behörighets modell

I tabellen nedan visas alla åtgärder som exponeras av behörighets modellen.

| Name | Motsvarande databas åtgärd (er) |
|---|---|
| `Microsoft.DocumentDB/databaseAccounts/readMetadata` | Läs kontots metadata. Se [begär Anden om metadata](#metadata-requests) för mer information. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create` | Skapa ett nytt objekt. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read` | Läs ett enskilt objekt med dess ID och partitionsnyckel (punkt-Läs). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/replace` | Ersätt ett befintligt objekt. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/upsert` | "Upsert" ett objekt, vilket innebär att skapa det om det inte finns, eller ersätta det om det finns. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/delete` | Ta bort ett objekt. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery` | Kör en [SQL-fråga](sql-query-getting-started.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` | Läsa från behållarens [ändrings flöde](read-change-feed.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeStoredProcedure` | Kör en [lagrad procedur](stored-procedures-triggers-udfs.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/manageConflicts` | Hantera [konflikter](conflict-resolution-policies.md) för flera Skriv regions konton (det vill säga, lista och ta bort objekt från den här inmatningen). |

Jokertecken stöds på både *behållare* -och *objekt* nivåer:

- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`
- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*`

> [!IMPORTANT]
> Den här behörighets modellen täcker bara databas åtgärder som gör att du kan läsa och skriva data. Den omfattar **inte** någon typ av hanterings åtgärder, t. ex. att skapa behållare eller ändra deras genomflöde. Om du vill autentisera hanterings åtgärder med en AAD-identitet använder du [Azure RBAC](role-based-access-control.md) i stället.

### <a name="metadata-requests"></a><a id="metadata-requests"></a> Begär Anden om metadata

När du använder Azure Cosmos DB SDK: er utfärdar dessa SDK: er för skrivskyddade metadata vid initiering och för att hantera vissa data begär Anden. Dessa metadata-förfrågningar hämtar olika konfigurations information som: 

- Den globala konfigurationen av ditt konto, som innehåller de Azure-regioner som kontot är tillgängligt i.
- Partitionsnyckel för dina behållare eller deras indexerings princip.
- En lista över fysiska partitioner som gör en behållare och deras adresser.

De hämtar *inte* någon av de data som du har lagrat i ditt konto.

För att säkerställa den bästa transparensen i vår behörighets modell omfattas dessa metadata explicit av `Microsoft.DocumentDB/databaseAccounts/readMetadata` åtgärden. Den här åtgärden ska tillåtas i varje situation där ditt Azure Cosmos DB-konto nås via någon av de Azure Cosmos DB SDK: erna. Den kan tilldelas (via en roll tilldelning) på valfri nivå i Azure Cosmos DB hierarkin (det vill säga konto, databas eller behållare).

Faktiska begär Anden om metadata som tillåts av `Microsoft.DocumentDB/databaseAccounts/readMetadata` åtgärden beror på omfattningen som åtgärden är tilldelad till:

| Omfång | Begär Anden som tillåts av åtgärden |
|---|---|
| Konto | – Visar databaserna under kontot<br>– För varje databas under kontot är tillåtna åtgärder i databas omfånget |
| Databas | – Läser metadata för databasen<br>– Visar behållarna under databasen<br>– För varje behållare under databasen är tillåtna åtgärder i container omfånget |
| Container | -Läser metadata för behållare<br>– Visar en lista över fysiska partitioner under behållaren<br>-Matcha adressen för varje fysisk partition |

## <a name="create-role-definitions"></a><a id="role-definitions"></a> Skapa roll definitioner

När du skapar en roll definition måste du ange:

- Namnet på ditt Azure Cosmos DB-konto.
- Resurs gruppen som innehåller ditt konto.
- Typ av roll definition; `CustomRole` stöds för närvarande inte.
- Namnet på roll definitionen.
- En lista med [åtgärder](#permission-model) som du vill att rollen ska tillåta.
- Ett eller flera omfång som roll definitionen kan tilldelas till; omfattningar som stöds är:
    - `/` (konto nivå),
    - `/dbs/<database-name>` (databas nivå),
    - `/dbs/<database-name>/colls/<container-name>` (container nivå).

> [!NOTE]
> De åtgärder som beskrivs nedan är för närvarande tillgängliga i:
> - Azure PowerShell: [AZ. CosmosDB-version 2.0.1-för hands version](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLI: ["cosmosdb-Preview"-tilläggs version 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

Skapa en roll med namnet *MyReadOnlyRole* som endast innehåller Läs åtgärder:

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadOnlyRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed') `
    -AssignableScope "/"
```

Skapa en roll med namnet *MyReadWriteRole* som innehåller alla åtgärder:

```powershell
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadWriteRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*') `
    -AssignableScope "/"
```

Ange de roll definitioner som du har skapat för att hämta deras ID: n:

```powershell
Get-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName
```

```
RoleName         : MyReadWriteRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}

RoleName         : MyReadOnlyRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}
```

### <a name="using-the-azure-cli"></a>Använda Azure CLI

Skapa en roll med namnet *MyReadOnlyRole* som endast innehåller Läs åtgärder:

```json
// role-definition-ro.json
{
    "RoleName": "MyReadOnlyRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ]
    }]
}
```

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-ro.json
```

Skapa en roll med namnet *MyReadWriteRole* som innehåller alla åtgärder:

```json
// role-definition-rw.json
{
    "RoleName": "MyReadWriteRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ]
    }]
}
```

```azurecli
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-rw.json
```

Ange de roll definitioner som du har skapat för att hämta deras ID: n:

```azurecli
az cosmosdb sql role definition list --account-name $accountName --resource-group $resourceGroupName
```

```
[
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadWriteRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  },
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadOnlyRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  }
]
```

## <a name="create-role-assignments"></a><a id="role-assignments"></a> Skapa roll tilldelningar

När du har skapat dina roll definitioner kan du koppla dem till dina AAD-identiteter. När du skapar en roll tilldelning måste du ange:

- Namnet på ditt Azure Cosmos DB-konto.
- Resurs gruppen som innehåller ditt konto.
- ID för den roll definition som ska tilldelas.
- Ägar-ID för identiteten som roll definitionen ska tilldelas.
- Omfånget för roll tilldelningen. omfattningar som stöds är:
    - `/` (konto nivå)
    - `/dbs/<database-name>` (databas nivå)
    - `/dbs/<database-name>/colls/<container-name>` (container nivå)

  Omfånget måste matcha eller vara en under omfattning för en av roll definitionernas tilldelnings bara scope.

> [!NOTE]
> Om du vill skapa en roll tilldelning för ett huvud namn för tjänsten, se till att använda dess **objekt-ID** som finns i avsnittet **företags program** på bladet **Azure Active Directory** Portal.

> [!NOTE]
> De åtgärder som beskrivs nedan är för närvarande tillgängliga i:
> - Azure PowerShell: [AZ. CosmosDB-version 2.0.1-för hands version](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLI: ["cosmosdb-Preview"-tilläggs version 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

Tilldela en roll till en identitet:

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
$readOnlyRoleDefinitionId = "<roleDefinitionId>" // as fetched above
$principalId = "<aadPrincipalId>"
New-AzCosmosDBSqlRoleAssignment -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -RoleDefinitionId $readOnlyRoleDefinitionId `
    -Scope $accountName `
    -PrincipalId $principalId
```

### <a name="using-the-azure-cli"></a>Använda Azure CLI

Tilldela en roll till en identitet:

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
readOnlyRoleDefinitionId = '<roleDefinitionId>' // as fetched above
principalId = '<aadPrincipalId>'
az cosmosdb sql role assignment create --account-name $accountName --resource-group $resourceGroupName --scope "/" --principal-id $principalId --role-definition-id $readOnlyRoleDefinitionId
```

## <a name="initialize-the-sdk-with-azure-ad"></a>Initiera SDK med Azure AD

Om du vill använda Azure Cosmos DB RBAC i ditt program måste du uppdatera hur du initierar Azure Cosmos DB SDK. I stället för att skicka ditt kontos primär nyckel måste du skicka en instans av en `TokenCredential` klass. Den här instansen ger Azure Cosmos DB SDK med den kontext som krävs för att hämta en AAD-token för den identitet som du vill använda.

Hur du skapar en `TokenCredential` instans ligger utanför den här artikelns omfattning. Det finns många sätt att skapa en sådan instans beroende på vilken typ av AAD-identitet du vill använda (användarens huvud namn, tjänstens huvud namn, grupp osv.). Viktigast är att din `TokenCredential` instans måste matcha den identitet (ägar-ID) som du har tilldelat rollerna till. Du hittar exempel på att skapa en `TokenCredential` klass:

- [i .NET](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme#credential-classes)
- [i Java](https://docs.microsoft.com/java/api/overview/azure/identity-readme#credential-classes)
- [i Java Script](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme#credential-classes)

I exemplen nedan används ett huvud namn för tjänsten med en `ClientSecretCredential` instans.

### <a name="in-net"></a>I .NET

Azure Cosmos DB RBAC stöds för närvarande i `preview` versionen av [.NET SDK v3](sql-api-sdk-dotnet-standard.md).

```csharp
TokenCredential servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
CosmosClient client = new CosmosClient("<account-endpoint>", servicePrincipal);
```

### <a name="in-java"></a>I Java

Azure Cosmos DB RBAC stöds för närvarande i [Java SDK v4](sql-api-sdk-java-v4.md).

```java
TokenCredential ServicePrincipal = new ClientSecretCredentialBuilder()
    .authorityHost("https://login.microsoftonline.com")
    .tenantId("<azure-ad-tenant-id>")
    .clientId("<client-application-id>")
    .clientSecret("<client-application-secret>")
    .build();
CosmosAsyncClient Client = new CosmosClientBuilder()
    .endpoint("<account-endpoint>")
    .credential(ServicePrincipal)
    .build();
```

### <a name="in-javascript"></a>I Java Script

Azure Cosmos DB RBAC stöds för närvarande i [Java Script SDK v3](sql-api-sdk-node.md).

```javascript
const servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
const client = new CosmosClient({
    "<account-endpoint>",
    aadCredentials: servicePrincipal
});
```

## <a name="auditing-data-requests"></a>Gransknings data begär Anden

När du använder Azure Cosmos DB RBAC får [diagnostikloggar](cosmosdb-monitor-resource-logs.md) en förstärkt information om identitets-och auktoriseringsinformation för varje data åtgärd. På så sätt kan du utföra detaljerad granskning och hämta AAD-identiteten som används för varje data förfrågan som skickas till ditt Azure Cosmos DB-konto.

Denna ytterligare information flödar i **DataPlaneRequests** -logg kategorin och består av två extra kolumner:

- `aadPrincipalId_g` visar ägar-ID: t för AAD-identiteten som användes för att autentisera begäran.
- `aadAppliedRoleAssignmentId_g` visar den [roll tilldelning](#role-assignments) som har lösts vid auktorisering av begäran.

## <a name="limits"></a>Gränser

- Du kan skapa upp till 100 roll definitioner och 2 000 roll tilldelningar per Azure Cosmos DB konto.
- Azure AD Group-matchning stöds för närvarande inte för identiteter som tillhör fler än 200 grupper.
- Azure AD-token skickas för närvarande som en rubrik med varje enskild begäran som skickas till den Azure Cosmos DB tjänsten, vilket ökar den totala nytto Last storleken.
- Att komma åt dina data med Azure AD via [Azure Cosmos DB Explorer](data-explorer.md) stöds inte ännu. Om du använder Azure Cosmos DB Explorer kräver det fortfarande att användaren har åtkomst till kontots primära nyckel för tillfället.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="which-azure-cosmos-db-apis-are-supported-by-rbac"></a>Vilka Azure Cosmos DB API:er stöds av RBAC?

För närvarande stöds endast SQL API:et.

### <a name="is-it-possible-to-manage-role-definitions-and-role-assignments-from-the-azure-portal"></a>Går det att hantera rolldefinitioner och rolltilldelningar från Azure-portalen?

Stöd för rollhantering från Azure-portalen är inte tillgängligt än.

### <a name="which-sdks-in-azure-cosmos-db-sql-api-support-rbac"></a>Vilka SDK: er i Azure Cosmos DB SQL API stöder RBAC?

[.Net v3](sql-api-sdk-dotnet-standard.md) -och [Java v4](sql-api-sdk-java-v4.md) SDK: er stöds för närvarande.

### <a name="is-the-azure-ad-token-automatically-refreshed-by-the-azure-cosmos-db-sdks-when-it-expires"></a>Uppdateras Azure AD-token automatiskt av Azure Cosmos DB-SDK: er när den upphör att gälla?

Ja.

### <a name="is-it-possible-to-disable-the-usage-of-the-account-primary-key-when-using-rbac"></a>Går det att inaktivera användningen av kontots primära nyckel när RBAC används?

Det går för närvarande inte att inaktivera kontots primära nyckel.

## <a name="next-steps"></a>Nästa steg

- Få en översikt över [säker åtkomst till data i Cosmos DB](secure-access-to-data.md).
- Lär dig mer om [RBAC för Azure Cosmos DB hantering](role-based-access-control.md).
