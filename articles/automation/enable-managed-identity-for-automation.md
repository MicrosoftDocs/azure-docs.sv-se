---
title: Aktivera en hanterad identitet för ditt Azure Automation konto (förhandsversion)
description: Den här artikeln beskriver hur du ställer in hanterad identitet för Azure Automation konton.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: 93c55c21bf740f2851cac1926bc673cebcd914b0
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514809"
---
# <a name="enable-a-managed-identity-for-your-azure-automation-account-preview"></a>Aktivera en hanterad identitet för ditt Azure Automation konto (förhandsversion)

Det här avsnittet visar hur du skapar en hanterad identitet för ett Azure Automation-konto och hur du använder den för att få åtkomst till andra resurser. Mer information om hur hanterade identiteter fungerar med Azure Automation finns i [Hanterade identiteter.](automation-security-overview.md#managed-identities-preview)

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto och prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. Både den hanterade identiteten och De Azure-målresurser som din runbook hanterar med hjälp av den identiteten måste finnas i samma Azure-prenumeration.

- Den senaste versionen av Azure Automation-kontomoduler. För närvarande är detta 1.6.0. (Mer information [om den här versionen finns i Az.Automation 1.6.0.)](https://www.powershellgallery.com/packages/Az.Automation/1.6.0)

- En Azure-resurs som du vill komma åt från din Automation-runbook. Den här resursen måste ha en roll som definierats för den hanterade identiteten, vilket hjälper Automation-runbooken att autentisera åtkomsten till resursen. Om du vill lägga till roller måste du vara ägare till resursen i motsvarande Azure AD-klientorganisation.

- Om du vill köra hybridjobb med en hanterad identitet uppdaterar du Hybrid Runbook Worker till den senaste versionen. De lägsta versionerna som krävs är:

   - Windows Hybrid Runbook Worker: version 7.3.1125.0
   - Linux Hybrid Runbook Worker: version 1.7.4.0

## <a name="enable-system-assigned-identity"></a>Aktivera system tilldelad identitet

>[!IMPORTANT]
>Den nya Automation-identiteten på kontonivå åsidosätter alla tidigare system tilldelade identiteter på VM-nivå (som beskrivs i Använda [runbook-autentisering med hanterade identiteter).](/automation-hrw-run-runbooks#runbook-auth-managed-identities) Om du kör hybridjobb på virtuella Azure-datorer som använder en virtuell dators system tilldelade identitet för att få åtkomst till Runbook-resurser används Automation-kontoidentiteten för hybridjobben. Det innebär att din befintliga jobbkörning kan påverkas om du har använt funktionen kund hanterade nycklar (CMK) för ditt Automation-konto.<br/><br/>Om du vill fortsätta att använda den virtuella datorns hanterade identitet bör du inte aktivera Automation-identiteten på kontonivå. Om du redan har aktiverat det kan du inaktivera den hanterade identiteten för Automation-kontot. Se [Inaktivera din Azure Automation hanterad identitet.](https://docs.microsoft.com/azure/automation/disable-managed-identity-for-automation)

Du kan konfigurera system tilldelade identiteter för Azure Automation på något av två sätt. Du kan antingen använda Azure Portal eller Azure REST API.

>[!NOTE]
>Användar tilldelade identiteter stöds inte ännu.

### <a name="enable-system-assigned-identity-in-azure-portal"></a>Aktivera system tilldelad identitet i Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Gå till ditt Automation-konto och välj **Identitet** under **Kontoinställningar.**

1. Ange alternativet **System tilldelad** till **På och** tryck på **Spara.** När du uppmanas att bekräfta väljer du **Ja.**

:::image type="content" source="media/managed-identity/managed-identity-on.png" alt-text="Aktivera system tilldelad identitet i Azure Portal.":::

Ditt Automation-konto kan nu använda den system tilldelade identiteten, som är registrerad med Azure Active Directory (Azure AD) och representeras av ett objekt-ID.

:::image type="content" source="media/managed-identity/managed-identity-object-id.png" alt-text="Objekt-ID för hanterad identitet.":::

### <a name="enable-system-assigned-identity-through-the-rest-api"></a>Aktivera system tilldelad identitet via REST API

Du kan konfigurera en system tilldelad hanterad identitet till Automation-kontot med hjälp av följande REST API anrop.

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Begärandetext
```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 .
 .
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
.
.
}
```

|Egenskap (JSON) | Värde | Beskrivning|
|----------|-----------|------------|
| principalid | \<principal-ID\> | Globalt unik identifierare (GUID) för tjänstens huvudnamnsobjekt för den hanterade identitet som representerar ditt Automation-konto i Azure AD-klientorganisationen. Detta GUID visas ibland som ett "objekt-ID" eller objectID. |
| tenantid | \<Azure-AD-tenant-ID\> | Globalt unik identifierare (GUID) som representerar Den Azure AD-klientorganisation där Automation-kontot nu är medlem. I Azure AD-klientorganisationen har tjänstens huvudnamn samma namn som Automation-kontot. |

## <a name="give-identity-access-to-azure-resources-by-obtaining-a-token"></a>Ge identitetsåtkomst till Azure-resurser genom att hämta en token

Ett Automation-konto kan använda sin hanterade identitet för att hämta token för att få åtkomst till andra resurser som skyddas av Azure AD, till exempel Azure Key Vault. Dessa token representerar inte någon specifik användare av programmet. I stället representerar de programmet som har åtkomst till resursen. I det här fallet representerar till exempel token ett Automation-konto.

Innan du kan använda din system tilldelade hanterade identitet för autentisering måste du konfigurera åtkomst för identiteten på den Azure-resurs där du planerar att använda identiteten. För att slutföra den här uppgiften tilldelar du lämplig roll till identiteten på Azure-målresursen.

I det här Azure PowerShell för att visa hur du tilldelar rollen Deltagare i prenumerationen till Azure-målresursen. Rollen Deltagare används som exempel och kanske inte krävs i ditt fall.

```powershell
New-AzRoleAssignment -ObjectId <automation-Identity-object-id> -Scope "/subscriptions/<subscription-id>" -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-managed-identity"></a>Autentisera åtkomst med hanterad identitet

När du aktiverar den hanterade identiteten för ditt Automation-konto och ger en identitet åtkomst till målresursen kan du ange den identiteten i runbooks mot resurser som stöder hanterad identitet. För identitetsstöd använder du cmdleten Az `Connect-AzAccount` cmdlet. Se [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) i PowerShell-referensen.

```powershell
Connect-AzAccount -Identity
```

>[!NOTE]
>Om din organisation fortfarande använder de inaktuella AzureRM-cmdletarna kan du använda `Connect-AzureRMAccount -Identity` .

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>Generera en åtkomsttoken utan att använda Azure-cmdlets

För HTTP-slutpunkter kontrollerar du följande.
- Metadatarubriken måste finnas och ska vara inställd på "true".
- En resurs måste skickas tillsammans med begäran, som en frågeparameter för en GET-begäran och som formulärdata för en POST-begäran.
- X-IDENTITY-HEADER ska vara inställt på värdet för miljövariabeln IDENTITY_HEADER för Hybrid Runbook Workers. 
- Innehållstyp för Post-begäran måste vara "application/x-www-form-urlencoded". 

### <a name="sample-get-request"></a>Exempel på GET-begäran

```powershell
$resource= "?resource=https://management.azure.com/" 
$url = $env:IDENTITY_ENDPOINT + $resource 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token
```

### <a name="sample-post-request"></a>Exempel på POST-begäran
```powershell
$url = $env:IDENTITY_ENDPOINT  
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$headers.Add("Metadata", "True") 
$body = @{resource='https://management.azure.com/' } 
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body 
Write-Output $accessToken.access_token
```

## <a name="sample-runbooks-using-managed-identity"></a>Exempel på runbooks med hanterad identitet

### <a name="sample-runbook-to-access-a-sql-database-without-using-azure-cmdlets"></a>Exempel-runbook för att få åtkomst till en SQL-databas utan att använda Azure-cmdlets

```powershell
$queryParameter = "?resource=https://database.windows.net/" 
$url = $env:IDENTITY_ENDPOINT + $queryParameter
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$content =[System.Text.Encoding]::Default.GetString((Invoke-WebRequest -UseBasicParsing -Uri $url -Method 'GET' -Headers $Headers).RawContentStream.ToArray()) | ConvertFrom-Json 
$Token = $content.access_token 
echo "The managed identities for Azure resources access token is $Token" 
$SQLServerName = "<ServerName>"    # Azure SQL logical server name  
$DatabaseName = "<DBname>"     # Azure SQL database name 
Write-Host "Create SQL connection string" 
$conn = New-Object System.Data.SqlClient.SQLConnection  
$conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30" 
$conn.AccessToken = $Token 
Write-host "Connect to database and execute SQL script" 
$conn.Open()  
$ddlstmt = "CREATE TABLE Person( PersonId INT IDENTITY PRIMARY KEY, FirstName NVARCHAR(128) NOT NULL)" 
Write-host " " 
Write-host "SQL DDL command" 
$ddlstmt 
$command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn) 
Write-host "results" 
$command.ExecuteNonQuery() 
$conn.Close()
```

### <a name="sample-runbook-to-access-a-key-vault-using-azure-cmdlets"></a>Exempel på runbook för att få åtkomst till ett nyckelvalv med hjälp av Azure-cmdlets

```powershell
Write-Output "Connecting to azure via  Connect-AzAccount -Identity" 
Connect-AzAccount -Identity 
Write-Output "Successfully connected with Automation account's Managed Identity" 
Write-Output "Trying to fetch value from key vault using MI. Make sure you have given correct access to Managed Identity" 
$secret = Get-AzKeyVaultSecret -VaultName '<KVname>' -Name '<KeyName>' 

$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue) 
try { 
  $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr) 
    Write-Output $secretValueText 
} finally { 
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr) 
}
```

### <a name="sample-python-runbook-to-get-a-token"></a>Exempel på Python-runbook för att hämta en token
 
```python
#!/usr/bin/env python3 
import os 
import requests  
# printing environment variables 
endPoint = os.getenv('IDENTITY_ENDPOINT')+"?resource=https://management.azure.com/" 
identityHeader = os.getenv('IDENTITY_HEADER') 
payload={} 
headers = { 
  'X-IDENTITY-HEADER': identityHeader,
  'Metadata': 'True' 
} 
response = requests.request("GET", endPoint, headers=headers, data=payload) 
print(response.text) 
```

## <a name="next-steps"></a>Nästa steg

- Om du behöver inaktivera en hanterad identitet kan du gå [till Inaktivera din Azure Automation hanterad identitet (förhandsversion)](disable-managed-identity-for-automation.md).

- En översikt över hur Azure Automation finns i Översikt över [Automation-kontoautentisering.](automation-security-overview.md)