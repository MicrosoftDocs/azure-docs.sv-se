---
title: Arkivera & rapport med Azure Monitor – hantering av Azure AD-berättigande
description: Lär dig att arkivera loggar och skapa rapporter med Azure Monitor i Azure Active Directory hantering av rättigheter.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a7f0d937d41ee42bf0fe678eb2f49e78882f881
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577856"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Arkivera loggar och rapportera om hantering av Azure AD-berättigande i Azure Monitor

Azure AD lagrar gransknings händelser i upp till 30 dagar i gransknings loggen. Du kan dock behålla gransknings data längre än standard kvarhållningsperioden, som beskrivs i [hur länge Azure AD lagrar rapporterings data?](../reports-monitoring/reference-reports-data-retention.md), genom att dirigera den till ett Azure Storage konto eller använda Azure Monitor. Du kan sedan använda arbets böcker och anpassade frågor och rapporter om dessa data.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Konfigurera Azure AD för att använda Azure Monitor
Innan du använder Azure Monitor arbets böcker måste du konfigurera Azure AD för att skicka en kopia av gransknings loggarna till Azure Monitor.

Att arkivera Azure AD audit-loggar kräver att du har Azure Monitor i en Azure-prenumeration. Du kan läsa mer om kraven och de uppskattade kostnaderna för att använda Azure Monitor i [Azure AD-aktivitets loggarna i Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md).

**Nödvändig roll**: global administratör

1. Logga in på Azure Portal som en användare som är global administratör. kontrol lera att du har åtkomst till den resurs grupp som innehåller arbets ytan Azure Monitor.
 
1. Välj **Azure Active Directory** klicka sedan på **diagnostikinställningar** under övervakning i den vänstra navigerings menyn. Kontrol lera om det redan finns en inställning för att skicka gransknings loggarna till den arbets ytan.

1. Om det inte redan finns en inställning klickar du på **Lägg till diagnostisk inställning**. Följ anvisningarna i artikeln [integrera Azure AD-loggar med Azure Monitor loggar](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) för att skicka Azure AD audit-loggen till arbets ytan Azure Monitor.

    ![Fönstret Diagnostikinställningar](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. När loggen har skickats till Azure Monitor väljer du **Log Analytics arbets ytor** och väljer den arbets yta som innehåller Azure AD audit-loggarna.

1. Välj **användning och uppskattade kostnader** och klicka på **data kvarhållning**. Ändra skjutreglaget till det antal dagar som du vill behålla data för att uppfylla dina gransknings krav.

    ![Fönstret Log Analytics arbets ytor](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. Om du senare vill se datum intervallet i arbets ytan kan du använda arbets ytan *Arkiverad logg datum intervall* :  
    
    1. Välj **Azure Active Directory** klicka sedan på **arbets böcker**. 
    
    1. Expandera avsnittet **Azure Active Directory fel sökning** och klicka på **arkiverade logg datum intervall**. 


## <a name="view-events-for-an-access-package"></a>Visa händelser för ett Access-paket  

Om du vill visa händelser för ett Access-paket måste du ha åtkomst till den underliggande Azure Monitor-arbetsytan (se [Hantera åtkomst till loggdata och arbets ytor i Azure Monitor](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions) för information) och i någon av följande roller: 

- Global administratör  
- Säkerhetsadministratör  
- Säkerhetsläsare  
- Rapport läsare  
- Program administratör  

Använd följande procedur för att visa händelser: 

1. I Azure Portal väljer **Azure Active Directory** klickar sedan på **arbets böcker**. Om du bara har en prenumeration går du vidare till steg 3. 

1. Om du har flera prenumerationer väljer du den prenumeration som innehåller arbets ytan.  

1. Välj den arbets bok som heter *Access Package-aktivitet*. 

1. I arbets boken väljer du ett tidsintervall (ändra till **alla** om inte säker) och välj ett paket-ID för åtkomst i list rutan för alla åtkomst paket som hade aktiviteten under det tidsintervallet. De händelser som är relaterade till det åtkomst paket som inträffade under det valda tidsintervallet visas.  

    ![Visa åtkomst paket händelser](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    Varje rad innehåller klock slag, Access-paket-ID, namnet på åtgärden, objekt-ID, UPN och visnings namnet för den användare som startade åtgärden.  Ytterligare information ingår i JSON.   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Skapa anpassade Azure Monitor frågor med hjälp av Azure Portal
Du kan skapa egna frågor om gransknings händelser i Azure AD, inklusive rättighets hanterings händelser.  

1. I Azure Active Directory av Azure Portal klickar du på **loggar** under avsnittet övervakning i den vänstra navigerings menyn för att skapa en ny fråga.

1. Din arbets yta bör visas i det övre vänstra hörnet på sidan fråga. Om du har flera Azure Monitor arbets ytor och den arbets yta som du använder för att lagra Azure AD audit-händelser inte visas, klickar du på **Välj omfång**. Välj sedan rätt prenumeration och arbets yta.

1. Ta sedan bort strängen "search *" i fråge textområdet och ersätt den med följande fråga:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Klicka sedan på **Kör**. 

    ![Starta frågan genom att klicka på Kör](./media/entitlement-management-logs-and-reporting/run-query.png)

I tabellen visas Gransknings logg händelser för rättighets hantering från den senaste timmen som standard. Du kan ändra inställningen "tidsintervall" för att Visa äldre händelser. Men om du ändrar den här inställningen visas bara händelser som har inträffat efter att Azure AD har kon figurer ATS för att skicka händelser till Azure Monitor.

Om du vill veta de äldsta och senaste gransknings händelserna i Azure Monitor använder du följande fråga:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Mer information om de kolumner som lagras för gransknings händelser i Azure Monitor finns i [tolka schemat för gransknings loggar i Azure AD i Azure Monitor](../reports-monitoring/reference-azure-monitor-audit-log-schema.md).

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Skapa anpassade Azure Monitor frågor med Azure PowerShell

Du kan komma åt loggar via PowerShell när du har konfigurerat Azure AD för att skicka loggar till Azure Monitor. Skicka sedan frågor från skript eller PowerShell-kommandoraden, utan att behöva vara en global administratör i klient organisationen. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Se till att användarens eller tjänstens huvud namn har rätt roll tilldelning

Se till att du, användaren eller tjänstens huvud namn som ska autentiseras till Azure AD, finns i lämplig Azure-roll på arbets ytan Log Analytics. Roll alternativen är antingen Log Analytics läsare eller Log Analytics deltagare. Om du redan är i någon av dessa roller kan du gå vidare till [hämta Log Analytics-ID med en Azure-prenumeration](#retrieve-log-analytics-id-with-one-azure-subscription).

Gör så här för att ange roll tilldelningen och skapa en fråga:

1. Leta upp [arbets ytan Log Analytics](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
)i Azure Portal.

1. Välj **Access Control (IAM)**.

1. Klicka sedan på **Lägg** till för att lägga till en roll tilldelning.

    ![Lägg till en rolltilldelning](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Installera Azure PowerShell modul

När du har en lämplig roll tilldelning startar du PowerShell och [installerar Azure PowerShell-modulen](/powershell/azure/install-az-ps?view=azps-3.3.0) (om du inte redan har gjort det) genom att skriva:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
Nu är du redo att autentisera till Azure AD och hämta ID: t för den Log Analytics arbets ytan du frågar.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Hämta Log Analytics-ID med en Azure-prenumeration
Om du bara har en enda Azure-prenumeration och en enda Log Analytics arbets yta skriver du följande för att autentisera till Azure AD, ansluta till den prenumerationen och hämta arbets ytan:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Hämta Log Analytics-ID med flera Azure-prenumerationer

 [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) körs i en prenumeration i taget. Om du har flera Azure-prenumerationer bör du se till att du ansluter till den som har Log Analytics arbets ytan med Azure AD-loggarna. 
 
 Följande cmdletar visar en lista över prenumerationer och hittar ID: t för den prenumeration som har Log Analytics arbets ytan:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
Du kan autentisera och associera PowerShell-sessionen med den prenumerationen med hjälp av ett kommando som `Connect-AzAccount –Subscription $subs[0].id` . Mer information om hur du autentiserar till Azure från PowerShell, inklusive icke-interaktivt, finns i [Logga in med Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
).

Om du har flera Log Analytics arbets ytor i den prenumerationen returnerar cmdleten [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) listan över arbets ytor. Sedan kan du hitta den som har Azure AD-loggarna. `CustomerId`Fältet som returneras av denna cmdlet är detsamma som värdet för "arbetsyte-ID" som visas i Azure Portal i Översikt över arbets ytan Log Analytics.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>Skicka frågan till arbets ytan Log Analytics
När du har identifierat en arbets yta kan du slutligen använda [Invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
) för att skicka en Kusto-fråga till arbets ytan. Dessa frågor skrivs i [Kusto-frågespråk](/azure/kusto/query/).
 
Du kan till exempel hämta datum intervallet för gransknings händelse posterna från arbets ytan Log Analytics, med PowerShell-cmdletar för att skicka en fråga som:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

Du kan också hämta rättighets hanterings händelser med hjälp av en fråga som:

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Nästa steg:
- [Skapa interaktiva rapporter med Azure Monitor arbets böcker](../../azure-monitor/visualize/workbooks-overview.md)