---
title: Klassificera dina Azure SQL-data med hjälp av Azure avdelningens kontroll-etiketter
description: Importera din klassificering från Azure avdelningens kontroll i din Azure SQL Database och Azure Synpase Analytics
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: azurepowershell
ms.topic: sample
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/17/2021
ms.openlocfilehash: 2eab7c535ff0c68da772e8a45ead12420734279c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101714909"
---
# <a name="classify-your-azure-sql-data-using-azure-purview-labels"></a>Klassificera dina Azure SQL-data med hjälp av Azure avdelningens kontroll-etiketter
[!INCLUDE[appliesto-sqldb-asa](../../azure-sql/includes/appliesto-sqldb-asa.md)]

I det här dokumentet beskrivs hur du lägger till Azure avdelningens kontroll-etiketter i din Azure SQL Database och Azure Synapse Analytics (tidigare SQL DW).

## <a name="create-an-application"></a>Skapa ett program

1. Öppna **Azure Active Directory** från Azure Portal.
2. Under **Hantera** väljer du **app-registrering**.
3. Skapa en ny Azure Active Directory-app genom att välja **nytt program**.
4. Ange ett namn för programmet och välj **Registrera**.
5. När ditt program har skapats öppnar du **certifikat & hemligheter** under **hanteraren**.
6. Skapa en ny klient hemlighet genom att välja **ny klient hemlighet** under **klient hemligheter**.
7. Lägg till en beskrivning av din klient hemlighet, Välj en förfallo period och välj sedan **Lägg till**.
8. Behåll **värdet** för framtida användning.

   > [!NOTE]
   > När du stänger sidan maskeras värdet. Du kommer inte att kunna hämta klient hemlighetens värde om du går tillbaka till sidan. Du måste skapa en ny klient hemlighet.

9. Gå tillbaka till sidan Översikt för ditt nya program och kopiera följande värden för framtida bruk:
    1. Program-ID (klient)
    1. Katalog-ID (klient)

## <a name="provide-permissions-to-the-application"></a>Ange behörigheter för programmet

1. Sök efter **avdelningens kontroll-konton** i din Azure Portal.
2. Välj det avdelningens kontroll-konto där dina SQL-databaser och Synapse klassificeras.
3. Öppna **åtkomst kontroll (IAM)** och välj **Lägg till**.

4. Välj **Lägg till rolltilldelning**.
5. I avsnittet **roll** söker du efter **avdelningens kontroll data Reader** och väljer den.
6. I avsnittet **Välj** söker du efter programmet som du skapade tidigare, markerar det och trycker på **Spara**.

## <a name="extract-the-classification-from-azure-purview"></a>Extrahera klassificeringen från Azure-avdelningens kontroll

1. Öppna ditt avdelningens kontroll-konto och Sök efter din Azure SQL Database eller Azure Synapse Analytics på Start sidan där du vill kopiera etiketterna.
2. Kopiera qualifiedName under **Egenskaper** och behåll den för framtida användning.
3. Öppna PowerShell-gränssnittet.

4. Kopiera något av följande skript enligt din SQL-tillgång (Azure SQL Database eller Azure-Synapse).
5. Fyll i parametrarna med de värden som du kopierade ovan:

   a. $TenantID: avsnitt 1, steg 9
   
   b. $ClientID: avsnitt 1, steg 9
   
   c. $SecretID: avsnitt 1, steg 8
   
   d. $purviewAccountName: avsnitt 2, steg 2
   
   e. $sqlDatabaseName: avsnitt 3, steg 2

6. Kopiera utdata från skriptet för framtida bruk.

### <a name="for-azure-sql-database"></a>För Azure SQL Database

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$sqlDatabaseName="mssql://sql_server_name.database.windows.net/db_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://purview.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_db?attr:qualifiedName=" + $sqlDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

### <a name="for-azure-synapse-analytics"></a>För Azure Synapse Analytics

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$dwDatabaseName="mssql://dw_server_name.database.windows.net/dw_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://purview.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_dw?attr:qualifiedName=" + $dwDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_dw_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

## <a name="run-the-t-sql-command-on-your-sql-asset"></a>Kör T-SQL-kommandot på din SQL-till gång

1. Anslut till din Azure SQL Database eller din Azure-Synapse med hjälp av det verktyg du väljer.
2. Kör det T-SQL-kommando som du kopierade från föregående avsnitt.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/).

Mer information om Azure avdelningens kontroll finns i [Azure avdelningens kontroll-dokumentationen](../../purview/index.yml).