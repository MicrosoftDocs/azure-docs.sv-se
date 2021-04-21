---
title: Övervaka delegeringsändringar i din hanterande klientorganisation
description: Lär dig hur du övervakar delegeringsaktivitet från kundklienter till din hanterande klientorganisation.
ms.date: 02/18/2021
ms.topic: how-to
ms.openlocfilehash: 1a12b916fae9794d6d695191a81ec076917bda31
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814900"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Övervaka delegeringsändringar i din hanterande klientorganisation

Som tjänstleverantör kanske du vill vara medveten om när kundprenumerationer eller resursgrupper delegeras till din klientorganisation [via Azure Lighthouse](../overview.md), eller när tidigare delegerade resurser tas bort.

I den hanterande klientorganisationen spårar [Azure-aktivitetsloggen](../../azure-monitor/essentials/platform-logs-overview.md) delegeringsaktivitet på klientorganisationsnivå. Den här loggade aktiviteten omfattar alla tillagda eller borttagna delegeringar från kundklienter.

I det här avsnittet beskrivs de behörigheter som krävs för att övervaka delegeringsaktiviteten till din klientorganisation (för alla dina kunder). Den innehåller också ett exempelskript som visar en metod för att fråga och rapportera om dessa data.

> [!IMPORTANT]
> Alla dessa steg måste utföras i din hanterande klientorganisation, i stället för i alla kundklienter.
>
> Även om vi refererar till tjänstleverantörer och kunder i det här [avsnittet kan företag som hanterar flera klienter](../concepts/enterprise.md) använda samma processer.

## <a name="enable-access-to-tenant-level-data"></a>Ge åtkomst till data på klientnivå

För att få åtkomst till aktivitetsloggdata på klientorganisationsnivå måste ett konto tilldelas den inbyggda [rollen](../../role-based-access-control/built-in-roles.md#monitoring-reader) Övervakningsläsare i Azure i rotomfånget (/). Den här tilldelningen måste utföras av en användare som har rollen Global administratör med ytterligare utökad åtkomst.

### <a name="elevate-access-for-a-global-administrator-account"></a>Utöka åtkomst för ett globalt administratörskonto

Om du vill tilldela en roll i rotomfånget (/) måste du ha rollen Global administratör med förhöjd behörighet. Den här utökade åtkomsten bör endast läggas till när du behöver göra rolltilldelningen och sedan ta bort den när du är klar.

Detaljerade anvisningar om hur du lägger till och tar bort utökade privilegier finns i [Utöka åtkomst för att hantera alla Azure-prenumerationer och hanteringsgrupper.](../../role-based-access-control/elevate-access-global-admin.md)

När du har höjt din behörighet har ditt konto rollen Administratör för användaråtkomst i Azure i rotomfånget. Med den här rolltilldelningen kan du visa alla resurser och tilldela åtkomst i valfri prenumeration eller hanteringsgrupp i katalogen, samt göra rolltilldelningar i rotomfånget.

### <a name="assign-the-monitoring-reader-role-at-root-scope"></a>Tilldela rollen Övervakningsläsare i rotomfånget

När du har höjt din behörighet kan du tilldela lämpliga behörigheter till ett konto så att det kan köra frågor mot aktivitetsloggdata på klientorganisationsnivå. Det här kontot måste ha den inbyggda [rollen Övervakningsläsare](../../role-based-access-control/built-in-roles.md#monitoring-reader) i Azure tilldelad i rotomfånget för din hanterande klientorganisation.

> [!IMPORTANT]
> Att bevilja en rolltilldelning i rotomfånget innebär att samma behörigheter gäller för alla resurser i klientorganisationen. Eftersom det här är en bred åtkomstnivå kanske du vill tilldela den här rollen till ett konto för tjänstens huvudnamn och använda det kontot [för att fråga efter data](#use-a-service-principal-account-to-query-the-activity-log). Du kan också tilldela rollen Övervakningsläsare i rotomfånget till enskilda användare eller användargrupper så att de kan visa [delegeringsinformation](#view-delegation-changes-in-the-azure-portal)direkt i Azure Portal . Om du gör det bör du vara medveten om att det här är en bred åtkomstnivå som bör begränsas till så få användare som möjligt.

Använd någon av följande metoder för att göra rotomfångstilldelningen.

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

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>Ta bort utökad åtkomst för det globala administratörskontot

När du har tilldelat rollen Övervakningsläsare i rotomfånget till [](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) önskat konto måste du ta bort den utökade åtkomsten för det globala administratörskontot eftersom den här åtkomstnivån inte längre behövs.

## <a name="view-delegation-changes-in-the-azure-portal"></a>Visa delegeringsändringar i Azure Portal

Användare som har tilldelats rollen Övervakningsläsare i rotomfånget kan visa delegeringsändringar direkt i Azure Portal.

1. Gå till **sidan Mina kunder** och välj sedan **Aktivitetslogg** i den vänstra navigeringsmenyn.
1. Se till **att Katalogaktivitet** är markerat i filtret längst upp på skärmen.

En lista över delegeringsändringar visas. Du kan välja Redigera **kolumner för** att visa eller dölja **värdena Status,** Händelsekategori, Tid, Tidsstämpel, **Prenumeration,** Händelse **initierad** av **,** Resursgrupp, Resurstyp och **Resurs.** 

:::image type="content" source="../media/delegation-activity-portal.jpg" alt-text="Skärmbild av delegeringsändringar i Azure Portal.":::

## <a name="use-a-service-principal-account-to-query-the-activity-log"></a>Använda ett konto för tjänstens huvudnamn för att köra frågor mot aktivitetsloggen

Eftersom rollen Övervakningsläsare i rotomfånget är en så bred åtkomstnivå kanske du vill tilldela rollen till ett konto för tjänstens huvudnamn och använda det kontot för att köra frågor mot data med hjälp av skriptet nedan.

> [!IMPORTANT]
> För närvarande kan klienter med en stor mängd delegeringsaktivitet få fel när de kör frågor mot dessa data.

När du använder ett tjänsthuvudnamnskonto för att fråga aktivitetsloggen rekommenderar vi följande metodtips:

- [Skapa ett nytt konto för tjänstens](../../active-directory/develop/howto-create-service-principal-portal.md) huvudnamn som endast ska användas för den här funktionen i stället för att tilldela den här rollen till ett befintligt huvudnamn för tjänsten som används för annan automatisering.
- Se till att tjänstens huvudnamn inte har åtkomst till några delegerade kundresurser.
- [Använd ett certifikat för att autentisera](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) och lagra det på ett säkert sätt i [Azure Key Vault](../../key-vault/general/security-features.md).
- Begränsa de användare som har åtkomst att agera för tjänstens huvudnamn.

När du har skapat ett nytt tjänsthuvudnamnskonto med övervakningsläsaråtkomst till rotomfånget för din hanterande klientorganisation kan du använda det för att fråga efter och rapportera om delegeringsaktiviteten i din klientorganisation.

[Det Azure PowerShell skriptet](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) kan användas för att fråga den senaste dagen med aktivitet och rapporter om alla tillagda eller borttagna delegeringar (eller försök som inte lyckades). Den frågar data i [klientaktivitetsloggen](/rest/api/monitor/TenantActivityLogs/List) och skapar sedan följande värden för att rapportera om delegeringar som läggs till eller tas bort:

- **DelegatedResourceId:** ID:t för den delegerade prenumerationen eller resursgruppen
- **CustomerTenantId: Kundens** klientorganisations-ID
- **CustomerSubscriptionId:** Prenumerations-ID:t som delegerades eller som innehåller resursgruppen som delegerades
- **CustomerDelegationStatus:** Statusändringen för den delegerade resursen (lyckades eller misslyckades)
- **EventTimeStamp:** Datum och tid då delegeringsändringen loggades

Tänk på följande när du frågar efter dessa data:

- Om flera resursgrupper delegeras i en enda distribution returneras separata poster för varje resursgrupp.
- Ändringar som gjorts i en tidigare delegering (till exempel uppdatering av behörighetsstrukturen) loggas som en tillagd delegering.
- Som nämnts ovan måste ett konto ha den inbyggda Azure-rollen Övervakningsläsare i rotomfånget (/) för att få åtkomst till dessa data på klientorganisationsnivå.
- Du kan använda dessa data i dina egna arbetsflöden och rapportering. Du kan till exempel använda [HTTP Data Collector API (offentlig förhandsversion)](../../azure-monitor/logs/data-collector-api.md) för att logga data [](../../azure-monitor/alerts/action-groups.md) till Azure Monitor från en REST API-klient och sedan använda åtgärdsgrupper för att skapa meddelanden eller aviseringar.

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

- Lär dig hur du kan registrera kunder för [att Azure Lighthouse](../concepts/azure-delegated-resource-management.md).
- Lär dig [Azure Monitor och](../../azure-monitor/index.yml) [Azure-aktivitetsloggen](../../azure-monitor/essentials/platform-logs-overview.md).
- Granska [arbetsboken Aktivitetsloggar efter](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) domän och lär dig hur du visar Azure-aktivitetsloggar över prenumerationer med ett alternativ för att filtrera dem efter domännamn.
