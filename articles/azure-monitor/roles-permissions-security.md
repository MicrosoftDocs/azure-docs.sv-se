---
title: Roller, behörigheter och säkerhet i Azure Monitor
description: Lär dig hur du använder Azure Monitor inbyggda roller och behörigheter för att begränsa åtkomsten till övervakning av resurser.
services: azure-monitor
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 64e770ea328516bdc9dbe1a2e17070c15a252fe7
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490037"
---
# <a name="roles-permissions-and-security-in-azure-monitor"></a>Roller, behörigheter och säkerhet i Azure Monitor

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Många team behöver strikt reglera åtkomsten till övervaknings data och inställningar. Om du till exempel har grupp medlemmar som arbetar exklusivt för övervakning (support tekniker, DevOps-tekniker) eller om du använder en hanterad tjänst leverantör kanske du vill ge dem åtkomst till endast övervaknings data och begränsa deras möjlighet att skapa, ändra eller ta bort resurser. Den här artikeln visar hur du snabbt kan använda en inbyggd övervakning av Azure-rollen till en användare i Azure eller skapa en egen anpassad roll för en användare som behöver begränsad övervaknings behörighet. Den diskuterar sedan säkerhets överväganden för dina Azure Monitor-relaterade resurser och hur du kan begränsa åtkomsten till de data de innehåller.

## <a name="built-in-monitoring-roles"></a>Inbyggda övervakningsroller
Azure Monitor inbyggda roller är utformade för att hjälpa till att begränsa åtkomsten till resurser i en prenumeration samtidigt som den ansvariga för övervakning av infrastruktur för att hämta och konfigurera de data de behöver. Azure Monitor ger två färdiga roller: en övervaknings läsare och en övervaknings deltagare.

### <a name="monitoring-reader"></a>Övervaknings läsare
Personer som har tilldelats rollen övervaknings läsare kan visa alla övervaknings data i en prenumeration, men kan inte ändra någon resurs eller redigera inställningar som rör övervakning av resurser. Den här rollen lämpar sig för användare i en organisation, till exempel support-eller drift tekniker, som behöver kunna:

* Visa övervaknings instrument paneler i portalen och skapa egna instrument paneler för privat övervakning.
* Visa aviserings regler som definierats i [Azure-aviseringar](alerts/alerts-overview.md)
* Fråga efter mått med hjälp av [Azure Monitor REST API](/rest/api/monitor/metrics), [PowerShell-cmdletar](powershell-samples.md)eller [plattforms oberoende CLI](cli-samples.md).
* Fråga aktivitets loggen med hjälp av portalen, Azure Monitor REST API, PowerShell-cmdletar eller plattforms oberoende CLI.
* Visa [diagnostikinställningar](essentials/diagnostic-settings.md) för en resurs.
* Visa [logg profilen](essentials/activity-log.md#legacy-collection-methods) för en prenumeration.
* Visa inställningar för autoskalning.
* Visa aviserings aktivitet och inställningar.
* Åtkomst Application Insights data och visa data i AI-analys.
* Sök Log Analytics arbets ytans data inklusive användnings data för arbets ytan.
* Visa Log Analytics hanterings grupper.
* Hämta sökschemat i Log Analytics-arbetsytan.
* Visa övervaknings paket i Log Analytics arbets ytan.
* Hämta och köra sparade sökningar i Log Analytics-arbetsytan.
* Hämta lagrings konfigurationen för Log Analytics-arbetsytan.

> [!NOTE]
> Den här rollen ger inte Läs behörighet för att logga data som har strömmats till en händelsehubben eller lagras i ett lagrings konto. [Nedan finns](#security-considerations-for-monitoring-data) information om hur du konfigurerar åtkomst till dessa resurser.
> 
> 

### <a name="monitoring-contributor"></a>Övervaknings deltagare
Personer som har tilldelats rollen övervaknings deltagare kan visa alla övervaknings data i en prenumeration och skapa eller ändra övervaknings inställningar, men kan inte ändra andra resurser. Den här rollen är en supermängd av övervaknings läsar rollen och är lämplig för medlemmar i en organisations övervaknings team eller hanterade tjänst leverantörer som, utöver de behörigheter som anges ovan, också måste kunna:

* Publicera övervaknings instrument paneler som en delad instrument panel.
* Ange [diagnostiska inställningar](essentials/diagnostic-settings.md) för en resurs.\*
* Ange [logg profilen](essentials/activity-log.md#legacy-collection-methods) för en prenumeration.\*
* Ange aviserings regel aktivitet och inställningar via [Azure-aviseringar](alerts/alerts-overview.md).
* Skapa Application Insights webbtest och-komponenter.
* Visar Log Analytics delade nycklar för arbets ytan.
* Aktivera eller inaktivera övervaknings paket i Log Analytics arbets ytan.
* Skapa och ta bort och kör sparade sökningar i Log Analytics arbets ytan.
* Skapa och ta bort lagrings konfigurationen för Log Analytics arbets ytan.

\*användaren måste också separat beviljas Listnycklar-behörighet för mål resursen (lagrings konto-eller Event Hub-namnrymden) för att ställa in en logg profil eller diagnostisk inställning.

> [!NOTE]
> Den här rollen ger inte Läs behörighet för att logga data som har strömmats till en händelsehubben eller lagras i ett lagrings konto. [Nedan finns](#security-considerations-for-monitoring-data) information om hur du konfigurerar åtkomst till dessa resurser.
> 
> 

## <a name="monitoring-permissions-and-azure-custom-roles"></a>Övervaknings behörigheter och Azure-anpassade roller
Om de inbyggda rollerna ovan inte uppfyller de exakta behoven för ditt team, kan du [skapa en anpassad Azure-roll](../role-based-access-control/custom-roles.md) med mer detaljerade behörigheter. Nedan visas vanliga Azure RBAC-åtgärder för Azure Monitor med deras beskrivningar.

| Åtgärd | Beskrivning |
| --- | --- |
| Microsoft. Insights/ActionGroups/[läsa, skriva, ta bort] |Läs-/skriv-/ta bort åtgärds grupper. |
| Microsoft. Insights/ActivityLogAlerts/[läsa, skriva, ta bort] |Läs/skriv/ta bort aktivitets logg aviseringar. |
| Microsoft. Insights/AlertRules/[läsa, skriva, ta bort] |Läsa/skriva/ta bort aviserings regler (från varningar Classic). |
| Microsoft. Insights/AlertRules/incidenter/läsning |Visa incidenter (historik för den varnings regel som utlöses) för varnings regler. Detta gäller endast för portalen. |
| Microsoft. Insights/AutoscaleSettings/[läsa, skriva, ta bort] |Läsa/skriva/ta bort inställningar för autoskalning. |
| Microsoft. Insights/DiagnosticSettings/[läsa, skriva, ta bort] |Läsa/skriva/ta bort diagnostikinställningar. |
| Microsoft. Insights/EventCategories/Read |Räkna upp alla kategorier som är möjliga i aktivitets loggen. Används av Azure Portal. |
| Microsoft. Insights/eventtypes/digestevents/Read |Den här behörigheten krävs för användare som behöver åtkomst till aktivitets loggar via portalen. |
| Microsoft. Insights/eventtypes/värden/Read |Visa lista över aktivitets logg händelser (hanterings händelser) i en prenumeration. Den här behörigheten gäller för både program mässig och Portal åtkomst till aktivitets loggen. |
| Microsoft. Insights/ExtendedDiagnosticSettings/[läsa, skriva, ta bort] | Läsa/skriva/ta bort diagnostikinställningar för nätverks flödes loggar. |
| Microsoft. Insights/LogDefinitions/Read |Den här behörigheten krävs för användare som behöver åtkomst till aktivitets loggar via portalen. |
| Microsoft. Insights/LogProfiles/[läsa, skriva, ta bort] |Läsa/skriva/ta bort logg profiler (logg profiler för strömmande aktivitet till händelsehubben eller lagrings konto). |
| Microsoft. Insights/MetricAlerts/[läsa, skriva, ta bort] |Läsa/skriva/ta bort nära real tids aviseringar |
| Microsoft. Insights/MetricDefinitions/Read |Läs mått definitioner (lista över tillgängliga mått typer för en resurs). |
| Microsoft. Insights/Metrics/Read |Läs mått för en resurs. |
| Microsoft. Insights/register/åtgärd |Registrera Azure Monitor Resource Provider. |
| Microsoft. Insights/ScheduledQueryRules/[läsa, skriva, ta bort] |Läs/skriv/ta bort logg aviseringar i Azure Monitor. |



> [!NOTE]
> Åtkomst till aviseringar, diagnostikinställningar och mått för en resurs kräver att användaren har Läs behörighet till resurs typen och resursens omfattning. Genom att skapa ("skriva") en diagnostisk inställning eller logg profil som arkiverar till ett lagrings konto eller strömmar till Event Hub måste användaren också ha Listnycklar-behörighet på mål resursen.
> 
> 

Med tabellen ovan kan du exempelvis skapa en anpassad Azure-roll för en "aktivitets logg läsare" så här:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Säkerhetsöverväganden vid övervakning av data
Övervaknings data – särskilt loggfiler – kan innehålla känslig information, till exempel IP-adresser eller användar namn. Övervakning av data från Azure levereras i tre grundläggande former:

1. Aktivitets loggen som beskriver alla kontroll Plans åtgärder i din Azure-prenumeration.
2. resurs loggar, vilka loggar som genereras av en resurs.
3. Mått, som genereras av resurser.

Alla tre dessa data typer kan lagras i ett lagrings konto eller strömmas till Händelsehubben, som båda är allmänna Azure-resurser. Eftersom dessa är generella resurser, skapa, ta bort och komma åt dem är en privilegie rad åtgärd som är reserverad för en administratör. Vi rekommenderar att du använder följande metoder för att övervaka relaterade resurser för att förhindra missbruk:

* Använd ett enda dedikerat lagrings konto för att övervaka data. Om du behöver separera övervaknings data till flera lagrings konton kan du aldrig dela användning av ett lagrings konto mellan övervaknings-och icke-övervaknings data, eftersom detta kan oavsiktligt ge dem som bara behöver åtkomst till övervaknings data (t. ex. en SIEM-åtkomst till data som inte övervakas).
* Använd en enda, dedikerad Service Bus eller Event Hub-namnrymd i alla diagnostikinställningar av samma skäl som ovan.
* Begränsa åtkomsten till övervaknings-relaterade lagrings konton eller händelse nav genom att hålla dem i en separat resurs grupp och [Använd omfång](../role-based-access-control/overview.md#scope) i övervaknings rollerna för att begränsa åtkomsten till endast den resurs gruppen.
* Bevilja aldrig Listnycklar-behörighet för antingen lagrings konton eller händelse nav i prenumerations omfattning när en användare bara behöver åtkomst till övervaknings data. Ge i stället dessa behörigheter till användaren vid en resurs eller resurs grupp (om du har en dedikerad övervaknings resurs grupp) omfattning.

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Begränsa åtkomsten till övervaknings-relaterade lagrings konton
När en användare eller ett program behöver åtkomst till övervaknings data i ett lagrings konto bör du [skapa en konto säkerhets Association](/rest/api/storageservices/create-account-sas) på det lagrings konto som innehåller övervaknings data med skrivskyddad åtkomst på tjänst nivå till Blob Storage. I PowerShell kan detta se ut så här:

```powershell
$context = New-AzStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Du kan sedan ge token till den entitet som behöver läsa från det lagrings kontot, och den kan visa och läsa från alla blobbar i det lagrings kontot.

Alternativt, om du behöver kontrol lera den här behörigheten med Azure RBAC, kan du bevilja den entiteten Microsoft. Storage/storageAccounts/listnycklar/åtgärd-behörighet för just det lagrings kontot. Detta är nödvändigt för användare som behöver kunna ange en diagnostisk inställning eller logg profil för att arkivera till ett lagrings konto. Du kan till exempel skapa följande anpassade Azure-roll för en användare eller ett program som bara behöver läsa från ett lagrings konto:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzRoleDefinition -Role $role 
```

> [!WARNING]
> Listnycklar-behörighet gör det möjligt för användaren att lista de primära och sekundära lagrings konto nycklarna. Dessa nycklar ger användaren alla signerade behörigheter (läsa, skriva, skapa blobbar, ta bort blobar osv.) över alla signerade tjänster (BLOB, kö, tabell, fil) i det lagrings kontot. Vi rekommenderar att du använder ett konto SAS som beskrivs ovan när det är möjligt.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Begränsa åtkomsten till övervaknings-relaterade händelse hubbar
Ett liknande mönster kan följas av Event Hub, men först måste du skapa en dedikerad regel för lyssning. Om du vill bevilja åtkomst till ett program som bara behöver lyssna på övervaknings-relaterade Event Hub, gör du följande:

1. Skapa en princip för delad åtkomst för de händelse hubbar som har skapats för strömmande övervaknings data med endast lyssnande anspråk. Detta kan göras i portalen. Du kan till exempel kalla det "monitoringReadOnly". Om möjligt ska du ge den nyckeln direkt till konsumenten och hoppa över nästa steg.
2. Om konsumenten behöver kunna hämta nyckeln ad hoc ger du användaren Listnycklar-åtgärden för den händelsehubben. Detta är också nödvändigt för användare som behöver kunna ange en diagnostisk inställning eller logg profil för att strömma till händelse nav. Du kan till exempel skapa en Azure RBAC-regel:
   
   ```powershell
   $role = Get-AzRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzRoleDefinition -Role $role 
   ```

## <a name="monitoring-within-a-secured-virtual-network"></a>Övervakning inom ett skyddat virtuellt nätverk

Azure Monitor behöver åtkomst till dina Azure-resurser för att tillhandahålla de tjänster som du aktiverar. Om du vill övervaka dina Azure-resurser samtidigt som du fortfarande skyddar dem från åtkomst till det offentliga Internet, kan du aktivera följande inställningar.

### <a name="secured-storage-accounts"></a>Skyddade lagrings konton 

Övervaknings data skrivs ofta till ett lagrings konto. Du kanske vill se till att data som kopieras till ett lagrings konto inte kan nås av obehöriga användare. För ytterligare säkerhet kan du låsa nätverks åtkomsten för att bara tillåta att dina auktoriserade resurser och betrodda Microsoft-tjänster har åtkomst till ett lagrings konto genom att begränsa ett lagrings konto så att det använder valda nätverk.
![Dialog Azure Monitor Azure Storage inställningar ](./media/roles-permissions-security/secured-storage-example.png) betraktas som en av de här "betrodda Microsoft-tjänsterna" om du tillåter att betrodda Microsoft-tjänster får åtkomst till din skyddade lagring, kommer Azure monitor att ha åtkomst till ditt skyddade lagrings konto, aktivera skrivning Azure Monitor resurs loggar, aktivitets logg och mått till ditt lagrings konto under dessa skyddade villkor. Detta aktiverar även Log Analytics att läsa loggar från säker lagring.   


Mer information finns i [nätverks säkerhet och Azure Storage](../storage/common/storage-network-security.md)

## <a name="next-steps"></a>Nästa steg
* [Läs om Azure RBAC och behörigheter i Resource Manager](../role-based-access-control/overview.md)
* [Läs översikten över övervakning i Azure](overview.md)

