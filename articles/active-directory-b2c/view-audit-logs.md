---
title: Åtkomst och granska gransknings loggar
titleSuffix: Azure AD B2C
description: Få åtkomst till Azure AD B2C gransknings loggar program mässigt och i Azure Portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 04d5a989fcaf3cef3eea3b782842fb3255d4b317
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98661110"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Åtkomst till Azure AD B2C gransknings loggar

Azure Active Directory B2C (Azure AD B2C) genererar gransknings loggar som innehåller aktivitets information om B2C-resurser, token som utfärdats och administratörs åtkomst. Den här artikeln innehåller en kort översikt över den information som är tillgänglig i gransknings loggar och instruktioner om hur du kommer åt dessa data för din Azure AD B2C-klient.

Gransknings logg händelser behålls endast i **sju dagar**. Planera att ladda ned och lagra dina loggar med någon av de metoder som visas nedan om du behöver en längre kvarhållningsperiod.

> [!NOTE]
> Du kan inte se användar inloggningar för enskilda Azure AD B2C program under avsnittet **användare** i **Azure Active Directory** eller **Azure AD B2C** sidor i Azure Portal. Inloggnings händelserna visar användar aktivitet, men kan inte korreleras tillbaka till det B2C-program som användaren har loggat in på. Du måste använda gransknings loggarna för den, enligt beskrivningen i den här artikeln.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Översikt över tillgängliga aktiviteter i B2C-kategorin för gransknings loggar

Kategorin **B2C** i gransknings loggar innehåller följande typer av aktiviteter:

|Aktivitetstyp |Beskrivning  |
|---------|---------|
|Auktorisering |Aktiviteter som rör auktorisering av en användare för att få åtkomst till B2C-resurser (till exempel en administratör som har åtkomst till en lista över B2C-principer).         |
|Katalog |Aktiviteter relaterade till katalogattribut som hämtats när en administratör loggar in med hjälp av Azure Portal. |
|Program | Skapa, läsa, uppdatera och ta bort (CRUD) åtgärder på B2C-program. |
|Nyckel |CRUD åtgärder för nycklar som lagras i en B2C Key-behållare. |
|Resurs |CRUD-åtgärder på B2C-resurser. Till exempel principer och identitets leverantörer.
|Autentisering |Validering av användarautentiseringsuppgifter och utfärdande av token.|

För CRUD-aktiviteter för användar objekt, se kategorin **kärn katalog** .

## <a name="example-activity"></a>Exempel aktivitet

I den här exempel bilden från Azure Portal visas de data som registrerats när en användare loggar in med en extern identitetsprovider, i det här fallet Facebook:

![Exempel på informations sidan om gransknings loggs aktiviteter i Azure Portal](./media/view-audit-logs/audit-logs-example.png)

Panelen aktivitets information innehåller följande relevanta information:

|Avsnitt|Fält|Beskrivning|
|-------|-----|-----------|
| Aktivitet | Name | Vilken aktivitet som ägde rum. Du kan till exempel *utfärda en id_token till programmet*, vilket avslutar den faktiska användar inloggningen. |
| Initierad av (aktör) | ObjectId | **Objekt-ID** för det B2C-program som användaren loggar in på. Den här identifieraren visas inte i Azure Portal, men är tillgänglig via Microsoft Graph API. |
| Initierad av (aktör) | Namn | **Program-ID** för det B2C-program som användaren loggar in på. |
| Mål | ObjectId | **Objekt-ID** för den användare som loggar in. |
| Ytterligare information | TenantId | **Klient-ID** för Azure AD B2C klient organisationen. |
| Ytterligare information | PolicyId | **Princip-ID: t** för det användar flöde (princip) som används för att signera användaren i. |
| Ytterligare information | ApplicationId | **Program-ID** för det B2C-program som användaren loggar in på. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Visa gransknings loggar i Azure Portal

Azure Portal ger till gång till Gransknings logg händelser i Azure AD B2C klienten.

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Växla till den katalog som innehåller Azure AD B2C klienten och bläddra sedan till **Azure AD B2C**.
1. Under **aktiviteter** i den vänstra menyn väljer du **gransknings loggar**.

En lista över aktivitets händelser som loggats under de senaste sju dagarna visas.

![Exempel filter med två aktivitets händelser i Azure Portal](./media/view-audit-logs/audit-logs-example-filter.png)

Det finns flera filtrerings alternativ, inklusive:

* **Resurs typ för aktivitet** – filtrera efter de aktivitets typer som visas i tabellen i avsnittet [Översikt över tillgängliga aktiviteter](#overview-of-activities-available-in-the-b2c-category-of-audit-logs) .
* **Datum** – filtrera datum intervallet för de aktiviteter som visas.

Om du väljer en rad i listan visas aktivitets informationen för händelsen.

Om du vill hämta listan över aktivitets händelser i en fil med kommaavgränsade värden (CSV) väljer du **Hämta**.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Hämta gransknings loggar med Azure AD repor ting API

Gransknings loggar publiceras i samma pipeline som andra aktiviteter för Azure Active Directory, så att de kan nås via [rapporterings-API: et för Azure Active Directory](/graph/api/directoryaudit-list). Mer information finns i [Kom igång med API för Azure Active Directory rapportering](../active-directory/reports-monitoring/concept-reporting-api.md).

### <a name="enable-reporting-api-access"></a>Aktivera rapporterings-API-åtkomst

Om du vill tillåta skript-eller programbaserad åtkomst till Azure AD repor ting API måste du ha ett program registrerat i Azure AD B2C-klienten med följande API-behörigheter. Du kan aktivera de här behörigheterna för en befintlig program registrering i B2C-klienten eller skapa en ny som är specifik för användning med gransknings logg automatisering.

* Microsoft Graph > program behörigheter > AuditLog > AuditLog. Read. all

Följ stegen i följande artikel för att registrera ett program med de behörigheter som krävs:

[Hantera Azure AD B2C med Microsoft Graph](microsoft-graph-get-started.md)

När du har registrerat ett program med rätt behörigheter, se avsnittet PowerShell-skript senare i den här artikeln för ett exempel på hur du kan hämta aktivitets händelser med ett skript.

### <a name="access-the-api"></a>Åtkomst till API:et

Du kan hämta Azure AD B2C Gransknings logg händelser via API: et genom att filtrera loggarna på `B2C` kategorin. Om du vill filtrera efter kategori använder `filter` du frågesträngparametern när du anropar slut punkten för Azure AD repor ting API.

```http
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>PowerShell-skript

Följande PowerShell-skript visar ett exempel på hur du frågar Azure AD repor ting-API: et. Efter att ha frågat API: et skrivs de loggade händelserna ut till standardutdata och skriver sedan JSON-utdata till en fil.

Du kan testa det här skriptet i [Azure Cloud Shell](overview.md). Se till att du uppdaterar den med ditt program-ID, klient hemlighet och namnet på din Azure AD B2C klient.

```powershell
# This script requires an application registration that's granted Microsoft Graph API permission
# https://docs.microsoft.com/azure/active-directory-b2c/microsoft-graph-get-started

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant domain name

$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 through n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?`$filter=loggedByService eq 'B2C' and activityDateTime gt  " + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```

Här är en JSON-representation av exempel aktivitets händelsen som visades tidigare i artikeln:

```json
{
    "id": "B2C_DQO3J_4984536",
    "category": "Authentication",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "result": "success",
    "resultReason": "N/A",
    "activityDisplayName": "Issue an id_token to the application",
    "activityDateTime": "2019-09-14T18:13:17.0618117Z",
    "loggedByService": "B2C",
    "operationType": "",
    "initiatedBy": {
        "user": null,
        "app": {
            "appId": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "servicePrincipalId": null,
            "servicePrincipalName": "00000000-0000-0000-0000-000000000000"
        }
    },
    "targetResources": [
        {
            "id": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "type": "User",
            "userPrincipalName": null,
            "groupType": null,
            "modifiedProperties": []
        }
    ],
    "additionalDetails": [
        {
            "key": "TenantId",
            "value": "test.onmicrosoft.com"
        },
        {
            "key": "PolicyId",
            "value": "B2C_1A_signup_signin"
        },
        {
            "key": "ApplicationId",
            "value": "00000000-0000-0000-0000-000000000000"
        },
        {
            "key": "Client",
            "value": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36"
        },
        {
            "key": "IdentityProviderName",
            "value": "facebook"
        },
        {
            "key": "IdentityProviderApplicationId",
            "value": "0000000000000000"
        },
        {
            "key": "ClientIpAddress",
            "value": "127.0.0.1"
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

Du kan automatisera andra administrations uppgifter, till exempel [hantera Azure AD B2C användar konton med Microsoft Graph](microsoft-graph-operations.md).