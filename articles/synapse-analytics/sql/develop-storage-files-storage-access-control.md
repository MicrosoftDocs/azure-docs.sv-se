---
title: Kontrollera åtkomsten till lagringskontot för en serverlös SQL-pool
description: Beskriver hur serverlös SQL-poolåtkomst Azure Storage och hur du kan styra lagringsåtkomsten för en serverlös SQL-pool i Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 06/11/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 266a6c27261107b883fdc0c1cdd274e6345de6db
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483460"
---
# <a name="control-storage-account-access-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Kontrollera åtkomsten till lagringskontot för en serverlös SQL-pool i Azure Synapse Analytics

En serverlös SQL-poolfråga läser filer direkt från Azure Storage. Behörigheter för att komma åt filer i Azure Storage styrs på två nivåer:
- **Lagringsnivå** – Användaren ska ha behörighet att komma åt underliggande lagringsfiler. Lagringsadministratören bör tillåta att Azure AD-huvudkontot läser/skriver filer eller genererar EN SAS-nyckel som används för att få åtkomst till lagring.
- **SQL-tjänstnivå** – Användaren ska ha beviljats behörighet att läsa data [med hjälp av extern](develop-tables-external-tables.md) tabell eller köra `OPENROWSET` funktionen. Läs mer om [de behörigheter som krävs i det här avsnittet.](develop-storage-files-overview.md#permissions)

I den här artikeln beskrivs de typer av autentiseringsuppgifter som du kan använda och hur sökning efter autentiseringsuppgifter används för SQL- och Azure AD-användare.

## <a name="storage-permissions"></a>Lagringsbehörigheter

En serverlös SQL-pool i Synapse Analytics kan läsa innehållet i filer som lagras i Azure Data Lake Storage. Du måste konfigurera behörigheter för lagring så att en användare som kör en SQL-fråga kan läsa filerna. Det finns tre metoder för att aktivera åtkomst till de filer som>
- **[Med rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md)** kan du tilldela en roll till en viss Azure AD-användare i den klientorganisation där lagringen finns. RBAC-roller kan tilldelas till Azure AD-användare. En läsare måste ha `Storage Blob Data Reader` `Storage Blob Data Contributor` rollen , eller `Storage Blob Data Owner` . En användare som skriver data i Azure Storage måste ha `Storage Blob Data Writer` rollen `Storage Blob Data Owner` eller . Observera att `Storage Owner` rollen inte innebär att en användare också är `Storage Data Owner` .
- **Access Control (ACL)** kan du definiera en mer exakt behörighetsmodell för filer och kataloger i Azure Storage. ACL kan tilldelas till Azure AD-användare. Om läsarna vill läsa en fil på en sökväg i Azure Storage måste de ha en körnings-ACL (X) på varje mapp i sökvägen och read(R) ACL på filen. [Läs mer om hur du ställer in ACL-behörigheter i lagringslagret](../../storage/blobs/data-lake-storage-access-control.md#how-to-set-acls)
- **Signatur för delad åtkomst (SAS)** gör det möjligt för en läsare att komma åt filerna i Azure Data Lake-lagringen med hjälp av den tidsbegränsade token. Läsaren behöver inte ens autentiseras som Azure AD-användare. SAS-token innehåller de behörigheter som beviljas till läsaren samt perioden när token är giltig. SAS-token är ett bra alternativ för tidsbegränsad åtkomst till alla användare som inte ens behöver vara i samma Azure AD-klientorganisation. SAS-token kan definieras för lagringskontot eller för specifika kataloger. Läs mer om [att bevilja begränsad åtkomst till Azure Storage resurser med hjälp av signaturer för delad åtkomst.](../../storage/common/storage-sas-overview.md)

## <a name="supported-storage-authorization-types"></a>Lagringsauktoriseringstyper som stöds

En användare som har loggat in på en serverlös SQL-pool måste ha behörighet att komma åt och fråga filerna i Azure Storage om filerna inte är offentligt tillgängliga. Du kan använda tre auktoriseringstyper för åtkomst till icke-offentlig lagring – [användaridentitet,](?tabs=user-identity) [signatur för delad](?tabs=shared-access-signature)åtkomst och [hanterad identitet](?tabs=managed-identity).

> [!NOTE]
> **Azure AD-genomsnimning** är standardbeteendet när du skapar en arbetsyta.

### <a name="user-identity"></a>[Användaridentitet](#tab/user-identity)

**Användaridentitet**, som även kallas "Azure AD-genoms vidare", är en auktoriseringstyp där identiteten för den Azure AD-användare som är inloggad i en serverlös SQL-pool används för att auktorisera dataåtkomst. Innan användaren får åtkomst till data måste Azure Storage bevilja behörigheter till Azure AD-användaren. Som du ser i tabellen nedan stöds den inte för SQL-användartypen.

> [!IMPORTANT]
> AAD-autentiseringstoken kan cachelagras av klientprogrammen. PowerBI cachelagrar till exempel AAD-token och återanvänder samma token i en timme. De långvariga frågorna kan misslyckas om token upphör att gälla mitt i frågekörningen. Om du får frågefel som orsakas av AAD-åtkomsttoken som upphör [](develop-storage-files-storage-access-control.md?tabs=managed-identity#supported-storage-authorization-types) att gälla mitt i frågan kan du överväga att byta till hanterad identitet eller [signatur för delad åtkomst.](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#supported-storage-authorization-types)

Du måste ha rollen Storage Blob Data Owner/Contributor/Reader för att kunna använda din identitet för att komma åt data. Alternativt kan du ange mer precisa ACL-regler för att få åtkomst till filer och mappar. Även om du är ägare till ett lagringskonto måste du fortfarande lägga till dig själv i någon av Storage Blob Data-rollerna.
Mer information om åtkomstkontroll i Azure Data Lake Store Gen2 finns i [artikeln Åtkomstkontroll i Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-access-control.md) artikeln.


### <a name="shared-access-signature"></a>[Signatur för delad åtkomst](#tab/shared-access-signature)

**Signatur för delad åtkomst (SAS) ger** delegerad åtkomst till resurser i ett lagringskonto. Med SAS kan en kund ge klienter åtkomst till resurser i ett lagringskonto utan att dela kontonycklar. SAS ger dig detaljerad kontroll över vilken typ av åtkomst du beviljar till klienter som har en SAS, inklusive giltighetsintervall, beviljade behörigheter, acceptabelt IP-adressintervall och godkänt protokoll (https/http).

Du kan hämta en SAS-token genom att gå **till Azure Portal -> Storage Account -> Shared access signature -> Configure permissions -> Generate SAS and connection string** (Skapa SAS och anslutningssträng).

> [!IMPORTANT]
> När en SAS-token genereras innehåller den ett frågetecken (?) i början av token. Om du vill använda token i en serverlös SQL-pool måste du ta bort frågetecknet (?) när du skapar autentiseringsuppgifter. Exempel:
>
> SAS-token: ?sv=2018-03-28&ss=dpqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHrkNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D

Om du vill aktivera åtkomst med hjälp av en SAS-token måste du skapa en databasbeomfångsbefattning eller serverbeomfångsbefattning för autentiseringsuppgifter 


> [!IMPORTANT]
> Du kan inte komma åt privata lagringskonton med SAS-token. Överväg att byta till [Hanterad identitet](develop-storage-files-storage-access-control.md?tabs=managed-identity#supported-storage-authorization-types) eller [Azure AD-direktautentisering](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) för att få åtkomst till skyddad lagring.

### <a name="managed-identity"></a>[Hanterad identitet](#tab/managed-identity)

**Hanterad** identitet kallas även MSI. Det är en funktion i Azure Active Directory (Azure AD) som tillhandahåller Azure-tjänster för serverlös SQL-pool. Dessutom distribuerar den en automatiskt hanterad identitet i Azure AD. Den här identiteten kan användas för att auktorisera begäran om dataåtkomst i Azure Storage.

Innan dataåtkomsten nås måste Azure Storage bevilja behörigheter till hanterad identitet för åtkomst till data. Du beviljar behörigheter till hanterad identitet på samma sätt som du beviljar behörighet till andra Azure AD-användare.

### <a name="anonymous-access"></a>[Anonym åtkomst](#tab/public-access)

Du kan komma åt offentligt tillgängliga filer som placeras på Azure-lagringskonton [som tillåter anonym åtkomst.](../../storage/blobs/anonymous-read-access-configure.md)

---

### <a name="supported-authorization-types-for-databases-users"></a>Auktoriseringstyper som stöds för databasanvändare

I tabellen nedan hittar du de tillgängliga auktoriseringstyperna:

| Auktoriseringstyp                    | *SQL-användare*    | *Azure AD-användare*     |
| ------------------------------------- | ------------- | -----------    |
| [Användaridentitet](?tabs=user-identity#supported-storage-authorization-types)       | Stöds inte | Stöds      |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)       | Stöds     | Stöds      |
| [Hanterad identitet](?tabs=managed-identity#supported-storage-authorization-types) | Stöds | Stöds      |

### <a name="supported-storages-and-authorization-types"></a>Lagrings- och auktoriseringstyper som stöds

Du kan använda följande kombinationer av auktorisering och Azure Storage typer:

| Auktoriseringstyp  | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)    | Stöds\*      | Stöds inte   | Stöds\*     |
| [Hanterad identitet](?tabs=managed-identity#supported-storage-authorization-types) | Stöds      | Stöds        | Stöds     |
| [Användaridentitet](?tabs=user-identity#supported-storage-authorization-types)    | Stöds\*      | Stöds\*        | Stöds\*     |

\* SAS-token och Azure AD-identitet kan användas för att få åtkomst till lagring som inte skyddas med brandväggen.


### <a name="querying-firewall-protected-storage"></a>Köra frågor mot en lagringsplats som skyddas av en brandvägg

När du använder lagring som skyddas med brandväggen kan du använda **användaridentitet eller** **hanterad identitet.**

> [!NOTE]
> Brandväggsfunktionen i Storage är i offentlig förhandsversion och är tillgänglig i alla offentliga molnregioner. 

#### <a name="user-identity"></a>Användaridentitet

Om du vill komma åt lagring som skyddas med brandväggen via användaridentitet kan du använda Azure Portal användargränssnitt eller PowerShell-modulen Az.Storage.
#### <a name="configuration-via-azure-portal"></a>Konfiguration via Azure Portal

1. Sök efter ditt lagringskonto i Azure Portal.
1. Gå till Nätverk under avsnittet Inställningar.
1. I avsnittet "Resursinstanser" lägger du till ett undantag för Synapse-arbetsytan.
1. Välj Microsoft.Synapse/workspaces som resurstyp.
1. Välj namnet på din arbetsyta som ett instansnamn.
1. Klicka på Spara.

#### <a name="configuration-via-powershell"></a>Konfiguration via PowerShell

Följ de här stegen för att konfigurera brandväggen för lagringskontot och lägga till ett undantag för Synapse-arbetsytan.

1. Öppna PowerShell eller [installera PowerShell](/powershell/scripting/install/installing-powershell-core-on-windows)
2. Installera modulen Az.Storage 3.4.0 och Az.Synapse 0.7.0: 
    ```powershell
    Install-Module -Name Az.Storage -RequiredVersion 3.4.0
    Install-Module -Name Az.Synapse -RequiredVersion 0.7.0
    ```
    > [!IMPORTANT]
    > Kontrollera att du använder **version 3.4.0**. Du kan kontrollera din Az.Storage-version genom att köra det här kommandot:  
    > ```powershell 
    > Get-Module -ListAvailable -Name  Az.Storage | select Version
    > ```
    > 

3. Anslut till din Azure-klientorganisation: 
    ```powershell
    Connect-AzAccount
    ```
4. Definiera variabler i PowerShell: 
    - Namn på resursgrupp – du hittar detta i Azure Portal översikt över Lagringskonto.
    - Kontonamn – namnet på lagringskontot som skyddas av brandväggsregler.
    - Klientorganisations-ID – du hittar detta Azure Portal i Azure Active Directory i klientorganisationsinformation.
    - Namn på arbetsyta – Namnet på Synapse-arbetsytan.

    ```powershell
        $resourceGroupName = "<resource group name>"
        $accountName = "<storage account name>"
        $tenantId = "<tenant id>"
        $workspaceName = "<synapse workspace name>"
        
        $workspace = Get-AzSynapseWorkspace -Name $workspaceName
        $resourceId = $workspace.Id
        $index = $resourceId.IndexOf("/resourceGroups/", 0)
        # Replace G with g - /resourceGroups/ to /resourcegroups/
        $resourceId = $resourceId.Substring(0,$index) + "/resourcegroups/" + $resourceId.Substring($index + "/resourceGroups/".Length)
        $resourceId
    ```
    > [!IMPORTANT]
    > Kontrollera att resurs-ID:t matchar den här mallen i utskriften av resourceId-variabeln.
    >
    > Det är viktigt att skriva **resursgrupper i** gemener.
    > Exempel på ett resurs-ID: 
    > ```
    > /subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Synapse/workspaces/{name-of-workspace}
    > ```
    > 
5. Lägg till regel för lagringsnätverk: 
    ```powershell
        Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId
    ```
6. Kontrollera att regeln tillämpades på ditt lagringskonto: 
    ```powershell
        $rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
        $rule.ResourceAccessRules | ForEach-Object { 
            if ($_.ResourceId -cmatch "\/subscriptions\/(\w\-*)+\/resourcegroups\/(.)+") { 
                Write-Host "Storage account network rule is successfully configured." -ForegroundColor Green
                $rule.ResourceAccessRules
            } else {
                Write-Host "Storage account network rule is not configured correctly. Remove this rule and follow the steps in detail." -ForegroundColor Red
                $rule.ResourceAccessRules
            }
        }
    ```

#### <a name="managed-identity"></a>Hanterad identitet
Du måste tillåta [betrodda Microsoft-tjänster... ange](../../storage/common/storage-network-security.md#trusted-microsoft-services) och uttryckligen [tilldela en Azure-roll till](../../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights) den system tilldelade [hanterade identiteten för](../../active-directory/managed-identities-azure-resources/overview.md) den resursinstansen. I det här fallet motsvarar åtkomstomfånget för instansen den Azure-roll som tilldelats den hanterade identiteten.

## <a name="credentials"></a>Autentiseringsuppgifter

Om du vill fråga en fil Azure Storage serverlös SQL-poolslutpunkt en autentiseringsautentisering som innehåller autentiseringsinformationen. Två typer av autentiseringsuppgifter används:
- Autentiseringsuppgifter på servernivå används för ad hoc-frågor som körs med hjälp av `OPENROWSET` funktionen . Autentiseringsnamnet måste matcha lagrings-URL:en.
- DATABASE SCOPED CREDENTIAL används för externa tabeller. Externa `DATA SOURCE` tabellreferenser med de autentiseringsuppgifter som ska användas för åtkomst till lagring.

Om du vill tillåta en användare att skapa eller ta bort en autentiseringsbehörighet kan administratören bevilja/NEKA ALTER ANY CREDENTIAL-behörighet till en användare:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

Databasanvändare som har åtkomst till extern lagring måste ha behörighet att använda autentiseringsuppgifter.

### <a name="grant-permissions-to-use-credential"></a>Bevilja behörigheter för att använda autentiseringsuppgifter

Om du vill använda autentiseringsbehörigheten måste en användare `REFERENCES` ha behörighet för en specifik autentiseringsbehörighet. Om du vill `REFERENCES` ge en behörighet på storage_credential en specific_user kör du:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

## <a name="server-scoped-credential"></a>Serveromfångsbaserade autentiseringsuppgifter

Serveromfångsbaserade autentiseringsuppgifter används när SQL-inloggning `OPENROWSET` anropar funktionen utan `DATA_SOURCE` att läsa filer på vissa lagringskonto. Namnet på autentiseringsuppgifter med serveromfång måste matcha bas-URL:en för Azure Storage (eventuellt följt av ett containernamn).  En autentiseringsidentifiering läggs till genom att [köra CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true). Du måste ange ett CREDENTIAL NAME-argument.

> [!NOTE]
> Argumentet `FOR CRYPTOGRAPHIC PROVIDER` stöds inte.

Namnet på autentiseringsnamnet på servernivå måste matcha den fullständiga sökvägen till lagringskontot (och eventuellt containern) i följande format: `<prefix>://<storage_account_path>[/<container_name>]` . Sökvägar för lagringskonto beskrivs i följande tabell:

| Extern datakälla       | Prefix | Sökväg till lagringskonto                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | https  | <storage_account>.dfs.core.windows.net              |

Serveromfångsbaserade autentiseringsuppgifter ger åtkomst till Azure Storage med hjälp av följande autentiseringstyper:

### <a name="user-identity"></a>[Användaridentitet](#tab/user-identity)

Azure AD-användare kan komma åt alla filer i Azure Storage om de har `Storage Blob Data Owner` `Storage Blob Data Contributor` , eller `Storage Blob Data Reader` roll. Azure AD-användare behöver inte autentiseringsuppgifter för att få åtkomst till lagring. 

SQL-användare kan inte använda Azure AD-autentisering för att få åtkomst till lagring.

### <a name="shared-access-signature"></a>[Signatur för delad åtkomst](#tab/shared-access-signature)

Följande skript skapar autentiseringsuppgifter på servernivå som kan användas av funktionen för att komma åt alla filer i Azure Storage med `OPENROWSET` hjälp av SAS-token. Skapa den här autentiseringsnyckeln för att aktivera SQL-huvudnamn som kör funktionen för att läsa filer som skyddas med SAS-nyckel på Azure Storage som matchar `OPENROWSET` URL:en i autentiseringsnamnet.

Exchange <*mystorageaccountname*> med det faktiska lagringskontonamnet och <*mystorageaccountcontainername*> med det faktiska containernamnet:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

Om du vill kan du bara använda lagringskontots grundläggande URL utan containernamn.

### <a name="managed-identity"></a>[Hanterad identitet](#tab/managed-identity)

Följande skript skapar autentiseringsuppgifter på servernivå som kan användas av funktionen för att komma åt alla filer i Azure Storage med hjälp `OPENROWSET` av arbetsyte hanterad identitet.

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='Managed Identity'
```

Om du vill kan du bara använda lagringskontots grundläggande URL utan containernamn.

### <a name="public-access"></a>[Offentlig åtkomst](#tab/public-access)

Databasomfångsdelade autentiseringsuppgifter krävs inte för att tillåta åtkomst till offentligt tillgängliga filer. Skapa [en datakälla utan databasomfångsbehörighet för att](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) få åtkomst till offentligt tillgängliga filer i Azure Storage.

---

## <a name="database-scoped-credential"></a>Databasomfångsbaserade autentiseringsuppgifter

Databasomfångsbaserade autentiseringsuppgifter används när huvudnamnsanrop fungerar med eller väljer data från en extern `OPENROWSET` tabell som inte har åtkomst till offentliga `DATA_SOURCE` filer. [](develop-tables-external-tables.md) Databasomfånget för autentiseringsuppgifter behöver inte matcha namnet på lagringskontot. Den används explicit i DATAKÄLLA som definierar lagringsplatsen.

Databasomfångsbaserade autentiseringsuppgifter ger åtkomst till Azure Storage med hjälp av följande autentiseringstyper:

### <a name="azure-ad-identity"></a>[Azure AD-identitet](#tab/user-identity)

Azure AD-användare kan komma åt alla filer i Azure Storage om de har minst `Storage Blob Data Owner` `Storage Blob Data Contributor` , eller `Storage Blob Data Reader` roll. Azure AD-användare behöver inte autentiseringsuppgifter för att få åtkomst till lagring.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
)
```

SQL-användare kan inte använda Azure AD-autentisering för att få åtkomst till lagring.

### <a name="shared-access-signature"></a>[Signatur för delad åtkomst](#tab/shared-access-signature)

Följande skript skapar en autentiseringsbehörighet som används för att komma åt filer på lagring med hjälp av SAS-token som anges i autentiseringssuppgifter. Skriptet skapar en extern exempeldatakälla som använder denna SAS-token för att få åtkomst till lagring.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>'
GO
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SasToken
)
```

### <a name="managed-identity"></a>[Hanterad identitet](#tab/managed-identity)

Följande skript skapar en databasomfattning för autentiseringsuppgifter som kan användas för att personifiera aktuell Azure AD-användare som hanterad identitet för tjänsten. Skriptet skapar en extern exempeldatakälla som använder arbetsytans identitet för att få åtkomst till lagring.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>
CREATE DATABASE SCOPED CREDENTIAL SynapseIdentity
WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SynapseIdentity
)
```

Databasomfånget för autentiseringsuppgifter behöver inte matcha namnet på lagringskontot eftersom det uttryckligen används i DATAKÄLLA som definierar lagringsplatsen.

### <a name="public-access"></a>[Offentlig åtkomst](#tab/public-access)

Databasomfångsdelade autentiseringsuppgifter krävs inte för att tillåta åtkomst till offentligt tillgängliga filer. Skapa [en datakälla utan databasomfångsbehörighet för att](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) få åtkomst till offentligt tillgängliga filer i Azure Storage.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.blob.core.windows.net/<container>/<path>'
)
```
---

Databasomfångsautentiseringsuppgifter används i externa datakällor för att ange vilken autentiseringsmetod som ska användas för åtkomst till lagringen:

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = <name of database scoped credential> 
)
```

## <a name="examples"></a>Exempel

### <a name="access-a-publicly-available-data-source"></a>**Få åtkomst till en offentligt tillgänglig datakälla**

Använd följande skript för att skapa en tabell som har åtkomst till offentligt tillgänglig datakälla.

```sql
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat]
       WITH ( FORMAT_TYPE = PARQUET)
GO
CREATE EXTERNAL DATA SOURCE publicData
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<public_container>/<path>' )
GO

CREATE EXTERNAL TABLE dbo.userPublicData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [publicData],
       FILE_FORMAT = [SynapseParquetFormat] )
```

Databasanvändare kan läsa innehållet i filerna från datakällan med hjälp av en extern tabell eller [openrowset-funktion](develop-openrowset.md) som refererar till datakällan:

```sql
SELECT TOP 10 * FROM dbo.userPublicData;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet',
                                DATA_SOURCE = 'mysample',
                                FORMAT='PARQUET') as rows;
GO
```

### <a name="access-a-data-source-using-credentials"></a>**Få åtkomst till en datakälla med autentiseringsuppgifter**

Ändra följande skript för att skapa en extern tabell som har åtkomst till Azure Storage med hjälp av SAS-token, Azure AD-identitet för användare eller hanterad identitet för arbetsytan.

```sql
-- Create master key in databases with some password (one-off per database)
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Y*********0'
GO

-- Create databases scoped credential that use Managed Identity or SAS token. User needs to create only database-scoped credentials that should be used to access data source:

CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity
WITH IDENTITY = 'Managed Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL SasCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2019-10-1********ZVsTOL0ltEGhf54N8KhDCRfLRI%3D'

-- Create data source that one of the credentials above, external file format, and external tables that reference this data source and file format:

CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO

CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
-- Uncomment one of these options depending on authentication method that you want to use to access data source:
--,CREDENTIAL = WorkspaceIdentity 
--,CREDENTIAL = SasCredential 
)

CREATE EXTERNAL TABLE dbo.userData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [mysample],
       FILE_FORMAT = [SynapseParquetFormat] );

```

Databasanvändare kan läsa innehållet i filerna från datakällan med hjälp av [en extern](develop-tables-external-tables.md) tabell eller [openrowset-funktion](develop-openrowset.md)  som refererar till datakällan:

```sql
SELECT TOP 10 * FROM dbo.userdata;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = 'mysample', FORMAT='PARQUET') as rows;
GO
```

## <a name="next-steps"></a>Nästa steg

Artiklarna nedan hjälper dig att lära dig hur du frågar olika mapptyper, filtyper och skapar och använder vyer:

- [Fråga en enskild CSV-fil](query-single-csv-file.md)
- [Fråga mappar och flera CSV-filer](query-folders-multiple-csv-files.md)
- [Fråga specifika filer](query-specific-files.md)
- [Efterfråga Parquet-filer](query-parquet-files.md)
- [Skapa och använda vyer](create-use-views.md)
- [Efterfråga JSON-filer](query-json-files.md)
- [Efterfråga kapslade Parquet-typer](query-parquet-nested-types.md)
