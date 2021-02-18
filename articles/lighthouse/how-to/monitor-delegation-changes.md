---
title: Övervaka Delegerings ändringar i hanterings klienten
description: Lär dig hur du övervakar Delegerings aktivitet från kund klienter till din hanterings klient.
ms.date: 01/27/2021
ms.topic: how-to
ms.openlocfilehash: 3bf6cc044d807d0c830b15c6d9c9a6d507f1a54f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593131"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Övervaka Delegerings ändringar i hanterings klienten

Som tjänst leverantör kanske du vill vara medveten om att kund prenumerationer eller resurs grupper delegeras till din klient organisation via [Azure Lighthouse](../overview.md)eller när tidigare delegerade resurser tas bort.

I hanterings klienten spårar [Azure aktivitets loggen](../../azure-monitor/essentials/platform-logs-overview.md) Delegerings aktivitet på klient nivå. Den här loggade aktiviteten innehåller alla tillagda eller borttagna delegeringar från alla kund klienter.

I det här avsnittet beskrivs de behörigheter som krävs för att övervaka Delegerings aktivitet till din klient (i alla dina kunder). Det innehåller också ett exempel skript som visar en metod för att fråga och rapportera om dessa data.

> [!IMPORTANT]
> Alla de här stegen måste utföras i hanterings klienten i stället för i alla kund klienter.
>
> Även om vi refererar till tjänst leverantörer och kunder i det här avsnittet kan [företag som hanterar flera klienter](../concepts/enterprise.md) använda samma processer.

## <a name="enable-access-to-tenant-level-data"></a>Ge åtkomst till data på klient nivå

För att få åtkomst till aktivitets logg data på klient nivå måste ett konto tilldelas den inbyggda rollen för [övervaknings läsaren](../../role-based-access-control/built-in-roles.md#monitoring-reader) i rot omfånget (/). Den här tilldelningen måste utföras av en användare som har rollen global administratör med ytterligare utökad åtkomst.

### <a name="elevate-access-for-a-global-administrator-account"></a>Öka åtkomsten för ett globalt administratörs konto

Om du vill tilldela en roll i rot omfånget (/) måste du ha rollen global administratör med utökad åtkomst. Den här utökade åtkomsten bör endast läggas till när du behöver göra roll tilldelningen och sedan ta bort den när du är färdig.

Detaljerade anvisningar om hur du lägger till och tar bort höjning finns i [öka åtkomsten för att hantera alla Azure-prenumerationer och hanterings grupper](../../role-based-access-control/elevate-access-global-admin.md).

När du har tilldelat åtkomsten har ditt konto rollen administratör för användar åtkomst i Azure i rot omfånget. Med den här roll tilldelningen kan du Visa alla resurser och tilldela åtkomst i alla prenumerationer eller hanterings grupper i katalogen, samt för att skapa roll tilldelningar i rot omfånget.

### <a name="assign-the-monitoring-reader-role-at-root-scope"></a>Tilldela övervaknings läsar rollen i rot omfånget

När du har förhöjd åtkomst kan du tilldela rätt behörigheter till ett konto så att det kan fråga efter aktivitets logg data på klient nivå. Det här kontot måste ha den inbyggda rollen för [övervaknings läsaren](../../role-based-access-control/built-in-roles.md#monitoring-reader) som är tilldelad till rot omfånget för hanterings klienten.

> [!IMPORTANT]
> Att bevilja en roll tilldelning i rot omfånget innebär att samma behörigheter gäller för alla resurser i klienten. Eftersom det här är en bred åtkomst nivå kan du vilja [tilldela rollen till ett huvud konto för tjänsten och använda det kontot för att fråga efter data](#use-a-service-principal-account-to-query-the-activity-log). Du kan också tilldela övervaknings läsar rollen i rot omfånget till enskilda användare eller till användar grupper så att de kan [Visa Delegerings information direkt i Azure Portal](#view-delegation-changes-in-the-azure-portal). Om du gör det bör du vara medveten om att detta är en bred åtkomst nivå som bör begränsas till det minsta antalet användare som är möjliga.

Använd någon av följande metoder för att göra tilldelningen av rot omfånget.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ObjectId <objectId> 
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>Ta bort utökad åtkomst för det globala administratörs kontot

När du har tilldelat rollen som övervaknings läsare i rot omfånget till önskat konto måste du [ta bort den utökade åtkomsten](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) för det globala administratörs kontot eftersom den här åtkomst nivån inte längre behövs.

## <a name="view-delegation-changes-in-the-azure-portal"></a>Visa Delegerings ändringar i Azure Portal

Användare som har tilldelats rollen som övervaknings läsare i rot omfånget kan visa Delegerings ändringar direkt i Azure Portal.

1. Gå till sidan **Mina kunder** och välj sedan **aktivitets logg** på den vänstra navigerings menyn.
1. Se till att **katalog aktivitet** är markerad i filtret längst upp på skärmen.

En lista över Delegerings ändringar kommer att visas. Du kan välja **Redigera kolumner** om du vill visa eller **dölja status**, **händelse kategori**, tid **, tidstämpel**, **prenumeration**, **händelse som initieras av**, **resurs grupp**, **resurs typ** och **resurs** värden. 

:::image type="content" source="../media/delegation-activity-portal.jpg" alt-text="Skärm bild av Delegerings ändringar i Azure Portal.":::

## <a name="use-a-service-principal-account-to-query-the-activity-log"></a>Använd ett huvud konto för tjänsten för att fråga aktivitets loggen

Eftersom övervaknings läsar rollen i rot omfånget är en sådan bred åtkomst nivå, kanske du vill tilldela rollen till ett huvud konto för tjänsten och använda det kontot för att fråga efter data med hjälp av skriptet nedan.

> [!IMPORTANT]
> För närvarande kan klienter med en stor mängd Delegerings aktivitet stöta på fel vid frågor mot dessa data.

När du använder ett huvud konto för tjänsten för att skicka frågor till aktivitets loggen rekommenderar vi följande metoder:

- [Skapa ett nytt tjänst huvud konto](../../active-directory/develop/howto-create-service-principal-portal.md) som endast ska användas för den här funktionen, i stället för att tilldela den här rollen till ett befintligt huvud namn för tjänsten som används för annan automatisering.
- Se till att det här tjänstens huvud namn inte har åtkomst till några delegerade kund resurser.
- [Använd ett certifikat för att autentisera](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) och [lagra det på ett säkert sätt i Azure Key Vault](../../key-vault/general/security-overview.md).
- Begränsa de användare som har åtkomst till Act för tjänstens huvud namn.

När du har skapat ett nytt tjänst huvud konto med övervaknings läsaren åtkomst till rot omfånget för din hanterings klient kan du använda det för att fråga efter och rapportera om Delegerings aktivitet i din klient.

[Det här Azure PowerShell skriptet](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) kan användas för att skicka frågor till den senaste 1 dagen av aktivitet och rapporter om eventuella tillagda eller borttagna delegeringar (eller försök som inte lyckades). Den frågar [klient aktivitets logg](/rest/api/monitor/TenantActivityLogs/List) data och skapar sedan följande värden för att rapportera om delegeringar som läggs till eller tas bort:

- **DelegatedResourceId**: ID för den delegerade prenumerationen eller resurs gruppen
- **CustomerTenantId**: kundens klient-ID
- **CustomerSubscriptionId**: det PRENUMERATIONS-ID som har delegerats eller som innehåller den resurs grupp som har delegerats
- **CustomerDelegationStatus**: status ändring för den delegerade resursen (lyckades eller misslyckades)
- **EventTimeStamp**: datum och tid då Delegerings ändringen loggades

Tänk på följande när du frågar efter dessa data:

- Om flera resurs grupper delegeras i en enda distribution returneras separata poster för varje resurs grupp.
- Ändringar som görs i en tidigare delegering (till exempel uppdatering av behörighets strukturen) kommer att loggas som en tillagd delegering.
- Som nämnts ovan måste ett konto ha den inbyggda rollen för övervaknings läsaren i rot omfånget (/) för att få åtkomst till dessa data på klient nivå.
- Du kan använda dessa data i dina egna arbets flöden och rapporter. Du kan till exempel använda [http data Collector-API: t (offentlig för hands version)](../../azure-monitor/logs/data-collector-api.md) för att logga Data till Azure Monitor från en REST API-klient och sedan använda [Åtgärds grupper](../../azure-monitor/alerts/action-groups.md) för att skapa meddelanden eller aviseringar.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the last 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri     = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization  = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method  = 'GET'
}
$list = Invoke-RestMethod @listOperations

# First link can be empty - and point to a next link (or potentially multiple pages)
# While you get more data - continue fetching and add result
while($list.nextLink){
    $list2 = Invoke-RestMethod $list.nextLink -Headers $listOperations.Headers -Method Get
    $data+=$list2.value;
    $list.nextLink = $list2.nextlink;
}

$showOperations = $data;

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action") {
    $registerOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action" }
    foreach ($registerOutput in $registerOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $registerOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has registered delegated resources to your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId; 
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp           = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") {
    $unregisterOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action" }
    foreach ($unregisterOutput in $unregisterOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $unregisterOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has unregistered delegated resources from your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId;
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp           = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else {
    Write-Output "No new delegation events for tenant: $($currentContext.Tenant.TenantId)"
}
```

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du kan publicera kunder till [Azure Lighthouse](../concepts/azure-delegated-resource-management.md).
- Läs mer om [Azure Monitor](../../azure-monitor/index.yml) och [Azure aktivitets logg](../../azure-monitor/essentials/platform-logs-overview.md).
- Granska [aktivitets loggarna efter domän](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) exempel arbets bok för att lära dig hur du visar Azure-aktivitets loggar över prenumerationer med ett alternativ för att filtrera dem efter domän namn.
