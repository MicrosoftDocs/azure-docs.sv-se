---
title: Konfigurera rollbaserad åtkomstkontroll för ditt Azure Cosmos DB med Azure AD
description: Lär dig hur du konfigurerar rollbaserad åtkomstkontroll med Azure Active Directory för ditt Azure Cosmos DB konto
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/19/2021
ms.author: thweiss
ms.openlocfilehash: 9de41835e33d50a670a44089cb10d44cc57e92a7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818718"
---
# <a name="configure-role-based-access-control-with-azure-active-directory-for-your-azure-cosmos-db-account-preview"></a>Konfigurera rollbaserad åtkomstkontroll med Azure Active Directory för ditt Azure Cosmos DB konto (förhandsversion)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB rollbaserad åtkomstkontroll är för närvarande i förhandsversion. Den här förhandsversionen tillhandahålls utan Serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure förhandsversioner.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!NOTE]
> Den här artikeln handlar om rollbaserad åtkomstkontroll för dataplansåtgärder i Azure Cosmos DB. Om du använder hanteringsplanåtgärder kan du läsa [artikeln rollbaserad åtkomstkontroll](role-based-access-control.md) som tillämpas på dina hanteringsplanåtgärder.

Azure Cosmos DB exponerar ett inbyggt RBAC-system (rollbaserad åtkomstkontroll) som gör att du kan:

- Autentisera dina databegäranden med en Azure Active Directory (Azure AD)-identitet.
- Auktorisera dina databegäranden med en finkornig, rollbaserad behörighetsmodell.

## <a name="concepts"></a>Begrepp

Den Azure Cosmos DB RBAC-dataplanet bygger på begrepp som ofta finns i andra RBAC-system som [Azure RBAC:](../role-based-access-control/overview.md)

- [Behörighetsmodellen](#permission-model) består av en uppsättning **åtgärder**; Var och en av dessa åtgärder mappar till en eller flera databasåtgärder. Några exempel på åtgärder är att läsa ett objekt, skriva ett objekt eller köra en fråga.
- Azure Cosmos DB skapar **[rolldefinitioner som](#role-definitions)** innehåller en lista över tillåtna åtgärder.
- Rolldefinitioner tilldelas till specifika Azure AD-identiteter via **[rolltilldelningar](#role-assignments)**. En rolltilldelning definierar också det omfång som rolldefinitionen gäller för. för närvarande är tre omfång:
    - Ett Azure Cosmos DB konto,
    - En Azure Cosmos DB databas,
    - En Azure Cosmos DB container.

  :::image type="content" source="./media/how-to-setup-rbac/concepts.png" alt-text="RBAC-begrepp":::

> [!NOTE]
> Den Azure Cosmos DB RBAC exponerar för närvarande inte några inbyggda rolldefinitioner.

## <a name="permission-model"></a><a id="permission-model"></a> Behörighetsmodell

> [!IMPORTANT]
> Den här behörighetsmodellen omfattar endast databasåtgärder som gör att du kan läsa och skriva data. Den omfattar **inte** någon typ av hanteringsåtgärder, som att skapa containrar eller ändra deras dataflöde. Det innebär att du inte **kan använda Azure Cosmos DB SDK för dataplanet för** att autentisera hanteringsåtgärder med en AAD-identitet. I stället måste du använda [Azure RBAC](role-based-access-control.md) via:
> - [ARM-mallar](manage-with-templates.md)
> - [Azure PowerShell skript](manage-with-powershell.md),
> - [Azure CLI-skript](manage-with-cli.md),
> - Azure-hanteringsbibliotek som är tillgängliga i
>   - [.NET](https://www.nuget.org/packages/Azure.ResourceManager.CosmosDB)
>   - [Java](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-cosmos)
>   - [Python](https://pypi.org/project/azure-mgmt-cosmosdb/)

I tabellen nedan visas alla åtgärder som exponeras av behörighetsmodellen.

| Name | Motsvarande databasåtgärd(er) |
|---|---|
| `Microsoft.DocumentDB/databaseAccounts/readMetadata` | Läsa kontometadata. Mer [information finns i Metadatabegäranden.](#metadata-requests) |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create` | Skapa ett nytt objekt. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read` | Läs ett enskilt objekt efter dess ID och partitionsnyckel (punktläsning). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/replace` | Ersätt ett befintligt objekt. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/upsert` | "Upsert" ett objekt, vilket innebär att skapa det om det inte finns eller ersätta det om det finns. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/delete` | Ta bort ett objekt. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery` | Kör en [SQL-fråga](sql-query-getting-started.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` | Läs från containerns [ändringsflöde](read-change-feed.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeStoredProcedure` | Kör en [lagrad procedur](stored-procedures-triggers-udfs.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/manageConflicts` | Hantera [konflikter](conflict-resolution-policies.md) för konton i flera skrivningsregion (det vill säga lista och ta bort objekt från konfliktflödet). |

Jokertecken stöds på både container- *och* *objektnivå:*

- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`
- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*`

### <a name="metadata-requests"></a><a id="metadata-requests"></a> Metadatabegäranden

När du Azure Cosmos DB-SDK:er utfärdar dessa SDK:er skrivskyddade metadatabegäranden under initieringen och för att hantera specifika databegäranden. Dessa metadatabegäranden hämtar olika konfigurationsinformation, till exempel: 

- Den globala konfigurationen för ditt konto, vilket innefattar de Azure-regioner som kontot är tillgängligt i.
- Partitionsnyckeln för dina containrar eller deras indexeringsprincip.
- Listan över fysiska partitioner som utgör en container och deras adresser.

De *hämtar inte* några av de data som du har lagrat i ditt konto.

För att säkerställa bästa möjliga transparens i vår behörighetsmodell omfattas dessa metadatabegäranden uttryckligen av `Microsoft.DocumentDB/databaseAccounts/readMetadata` åtgärden. Den här åtgärden ska tillåtas i alla situationer där ditt Azure Cosmos DB-konto nås via någon av Azure Cosmos DB-SDK:erna. Den kan tilldelas (via en rolltilldelning) på valfri nivå i Azure Cosmos DB hierarkin (det vill säga konto, databas eller container).

De faktiska metadatabegäranden som `Microsoft.DocumentDB/databaseAccounts/readMetadata` tillåts av åtgärden beror på omfånget som åtgärden är tilldelad till:

| Omfång | Begäranden som tillåts av åtgärden |
|---|---|
| Konto | – Lista databaserna under kontot<br>– De tillåtna åtgärderna i databasomfånget för varje databas under kontot |
| Databas | – Läsa databasmetadata<br>– Lista containrarna under databasen<br>– För varje container under databasen tillåts åtgärderna i containeromfånget |
| Container | – Läsa metadata för containrar<br>– Lista fysiska partitioner under containern<br>– Lösa adressen för varje fysisk partition |

## <a name="create-role-definitions"></a><a id="role-definitions"></a> Skapa rolldefinitioner

När du skapar en rolldefinition måste du ange:

- Namnet på ditt Azure Cosmos DB konto.
- Den resursgrupp som innehåller ditt konto.
- Typ av rolldefinition; stöds `CustomRole` endast för närvarande.
- Namnet på rolldefinitionen.
- En lista över [åtgärder](#permission-model) som du vill att rollen ska tillåta.
- Ett eller flera omfång som rolldefinitionen kan tilldelas på. omfång som stöds är:
    - `/` (kontonivå),
    - `/dbs/<database-name>` (databasnivå),
    - `/dbs/<database-name>/colls/<container-name>` (containernivå).

> [!NOTE]
> Åtgärderna som beskrivs nedan är för närvarande tillgängliga i:
> - Azure PowerShell: [Az.CosmosDB version 2.0.1-preview](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLI: ["cosmosdb-preview" tillägg version 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

Skapa en roll med *namnet MyReadOnlyRole* som endast innehåller läsåtgärder:

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

Skapa en roll med *namnet MyReadWriteRole* som innehåller alla åtgärder:

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

Visa en lista över rolldefinitioner som du har skapat för att hämta deras-ID:er:

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

Skapa en roll med *namnet MyReadOnlyRole* som endast innehåller läsåtgärder:

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

Skapa en roll med *namnet MyReadWriteRole* som innehåller alla åtgärder:

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

Visa en lista över rolldefinitioner som du har skapat för att hämta deras-ID:er:

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

## <a name="create-role-assignments"></a><a id="role-assignments"></a> Skapa rolltilldelningar

När du har skapat rolldefinitionerna kan du associera dem med dina AAD-identiteter. När du skapar en rolltilldelning måste du ange:

- Namnet på ditt Azure Cosmos DB konto.
- Resursgruppen som innehåller ditt konto.
- ID för rolldefinitionen som ska tilldelas.
- Huvud-ID för den identitet som rolldefinitionen ska tilldelas till.
- Rolltilldelningens omfattning. omfång som stöds är:
    - `/` (kontonivå)
    - `/dbs/<database-name>` (databasnivå)
    - `/dbs/<database-name>/colls/<container-name>` (containernivå)

  Omfånget måste matcha eller vara ett underomfång för en av rolldefinitionens tilldelningsbara omfång.

> [!NOTE]
> Om du vill skapa en rolltilldelning för ett **huvudnamn** för tjänsten  använder du dess **objekt-ID** enligt avsnittet Företagsprogram på Azure Active Directory portalbladet.

> [!NOTE]
> Åtgärderna som beskrivs nedan är för närvarande tillgängliga i:
> - Azure PowerShell: [Az.CosmosDB version 2.0.1-preview](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLI: ["cosmosdb-preview" tillägg version 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

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

Om du vill Azure Cosmos DB RBAC i ditt program måste du uppdatera hur du initierar Azure Cosmos DB SDK. I stället för att skicka kontots primärnyckel måste du skicka en instans av en `TokenCredential` klass. Den här instansen Azure Cosmos DB SDK med den kontext som krävs för att hämta en AAD-token för den identitet som du vill använda.

Hur du skapar en `TokenCredential` instans ligger utanför omfånget för den här artikeln. Det finns många sätt att skapa en sådan instans beroende på vilken typ av AAD-identitet du vill använda (användarens huvudnamn, tjänstens huvudnamn, grupp osv.). Det viktigaste är att din `TokenCredential` instans måste matcha den identitet (huvudnamns-ID) som du har tilldelat dina roller till. Du kan hitta exempel på hur du skapar en `TokenCredential` klass:

- [I .NET](/dotnet/api/overview/azure/identity-readme#credential-classes)
- [I Java](/java/api/overview/azure/identity-readme#credential-classes)
- [I JavaScript](/javascript/api/overview/azure/identity-readme#credential-classes)
- I REST API

I exemplen nedan används ett huvudnamn för tjänsten med en `ClientSecretCredential` instans.

### <a name="in-net"></a>I .NET

Den Azure Cosmos DB RBAC stöds för närvarande i `preview` versionen av [.NET SDK V3.](sql-api-sdk-dotnet-standard.md)

```csharp
TokenCredential servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
CosmosClient client = new CosmosClient("<account-endpoint>", servicePrincipal);
```

### <a name="in-java"></a>I Java

Den Azure Cosmos DB RBAC stöds för närvarande i [Java SDK V4.](sql-api-sdk-java-v4.md)

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

### <a name="in-javascript"></a>I JavaScript

Den Azure Cosmos DB RBAC stöds för närvarande i [JavaScript SDK V3.](sql-api-sdk-node.md)

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

### <a name="in-rest-api"></a>I REST API

Den Azure Cosmos DB RBAC stöds för närvarande med version 2021-03-15 av REST API. När du skapar [auktoriseringsrubriken](/rest/api/cosmos-db/access-control-on-cosmosdb-resources)anger du type-parametern till **aad** och hashsignaturen **(sig)** till **oauth-token** enligt följande exempel: 

`type=aad&ver=1.0&sig=<token-from-oauth>`

## <a name="auditing-data-requests"></a>Granska databegäranden

När du använder Azure Cosmos DB RBAC [utökas](cosmosdb-monitor-resource-logs.md) diagnostikloggarna med identitets- och auktoriseringsinformation för varje dataåtgärd. På så sätt kan du utföra detaljerad granskning och hämta den AAD-identitet som används för varje databegäran som skickas till ditt Azure Cosmos DB konto.

Den här ytterligare informationen flödar **i loggkategorin DataPlaneRequests** och består av två extra kolumner:

- `aadPrincipalId_g` visar huvud-ID för den AAD-identitet som användes för att autentisera begäran.
- `aadAppliedRoleAssignmentId_g` visar [rolltilldelningen](#role-assignments) som respekterades när begäran auktoriserades.

## <a name="limits"></a>Gränser

- Du kan skapa upp till 100 rolldefinitioner och 2 000 rolltilldelningar per Azure Cosmos DB konto.
- Du kan bara tilldela rolldefinitioner till Azure AD-identiteter som tillhör samma Azure AD-klientorganisation som ditt Azure Cosmos DB konto.
- Gruppmatchning i Azure AD stöds för närvarande inte för identiteter som tillhör fler än 200 grupper.
- Azure AD-token skickas för närvarande som ett huvud där varje enskild begäran skickas till Azure Cosmos DB-tjänsten, vilket ökar den totala nyttolaststorleken.
- Åtkomst till dina data med Azure AD [via Azure Cosmos DB Explorer](data-explorer.md) stöds inte ännu. Med Azure Cosmos DB Explorer fortfarande att användaren har åtkomst till kontots primärnyckel för tillfället.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="which-azure-cosmos-db-apis-are-supported-by-rbac"></a>Vilka Azure Cosmos DB API:er stöds av RBAC?

För närvarande stöds endast SQL API:et.

### <a name="is-it-possible-to-manage-role-definitions-and-role-assignments-from-the-azure-portal"></a>Går det att hantera rolldefinitioner och rolltilldelningar från Azure-portalen?

Stöd för rollhantering från Azure-portalen är inte tillgängligt än.

### <a name="which-sdks-in-azure-cosmos-db-sql-api-support-rbac"></a>Vilka SDK:er i Azure Cosmos DB SQL API stöder RBAC?

[.NET V3- och](sql-api-sdk-dotnet-standard.md) Java V4-SDK:er stöds för närvarande. [](sql-api-sdk-java-v4.md)

### <a name="is-the-azure-ad-token-automatically-refreshed-by-the-azure-cosmos-db-sdks-when-it-expires"></a>Uppdateras Azure AD-token automatiskt av Azure Cosmos DB-SDK: er när den upphör att gälla?

Ja.

### <a name="is-it-possible-to-disable-the-usage-of-the-account-primary-key-when-using-rbac"></a>Går det att inaktivera användningen av kontots primära nyckel när RBAC används?

Det går för närvarande inte att inaktivera kontots primära nyckel.

## <a name="next-steps"></a>Nästa steg

- Få en översikt över [säker åtkomst till data i Cosmos DB](secure-access-to-data.md).
- Läs mer om [RBAC för Azure Cosmos DB hantering](role-based-access-control.md).