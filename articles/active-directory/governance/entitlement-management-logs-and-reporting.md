---
title: Arkivera & rapport med Azure Monitor – Azure AD-berättigandehantering
description: Lär dig hur du arkiverar loggar och skapar rapporter Azure Monitor i Azure Active Directory berättigandehantering.
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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 354805b3e2b538c92ba2345df2bcd93968640068
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764395"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Arkivera loggar och rapportering om Azure AD-berättigandehantering i Azure Monitor

Azure AD lagrar granskningshändelser i upp till 30 dagar i granskningsloggen. Du kan dock behålla granskningsdata längre än standardperioden för kvarhållning som beskrivs i Hur länge lagrar [Azure AD rapportdata?](../reports-monitoring/reference-reports-data-retention.md)genom att dirigera dem till ett Azure Storage-konto eller använda Azure Monitor. Du kan sedan använda arbetsböcker och anpassade frågor och rapporter om dessa data.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Konfigurera Azure AD att använda Azure Monitor
Innan du använder Azure Monitor arbetsböcker måste du konfigurera Azure AD att skicka en kopia av dess granskningsloggar till Azure Monitor.

Arkivering av Azure AD-granskningsloggar kräver att du Azure Monitor i en Azure-prenumeration. Du kan läsa mer om förhandskrav och uppskattade kostnader för att använda Azure Monitor [i Azure AD-aktivitetsloggar i Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md).

**Förutsättningsroll:** Global administratör

1. Logga in på Azure Portal som en användare som är global administratör. Kontrollera att du har åtkomst till resursgruppen som innehåller Azure Monitor arbetsytan.
 
1. Välj **Azure Active Directory sedan** på **Diagnostikinställningar** under Övervakning i den vänstra navigeringsmenyn. Kontrollera om det redan finns en inställning för att skicka granskningsloggarna till den arbetsytan.

1. Om det inte redan finns en inställning klickar du på Lägg **till diagnostikinställning**. Följ instruktionerna i artikeln Integrera [Azure AD-loggar med Azure Monitor för](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) att skicka Azure AD-granskningsloggen till Azure Monitor arbetsytan.

    ![Fönstret Diagnostikinställningar](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. När loggen har skickats till Azure Monitor väljer du **Log Analytics-arbetsytor** och väljer arbetsytan som innehåller Azure AD-granskningsloggarna.

1. Välj **Användning och uppskattade kostnader och** klicka på **Datalagring.** Ändra skjutreglaget till det antal dagar som du vill behålla data för att uppfylla granskningskraven.

    ![Fönstret Log Analytics-arbetsytor](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. Om du senare vill se datumintervallet som finns på arbetsytan kan du använda arbetsboken *Arkiverat loggdatumintervall:*  
    
    1. Välj **Azure Active Directory** sedan på **Arbetsböcker**. 
    
    1. Expandera avsnittet Azure Active Directory **felsökning och** klicka på **Arkiverat loggdatumintervall.** 


## <a name="view-events-for-an-access-package"></a>Visa händelser för ett åtkomstpaket  

Om du vill visa händelser för ett åtkomstpaket måste du ha åtkomst till den underliggande Azure Monitor-arbetsytan (mer information finns i Hantera åtkomst till loggdata och arbetsytor i [Azure Monitor)](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions) och i någon av följande roller: 

- Global administratör  
- Säkerhetsadministratör  
- Säkerhetsläsare  
- Rapportläsare  
- Programadministratör  

Använd följande procedur för att visa händelser: 

1. I den Azure Portal väljer du **Azure Active Directory sedan** på **Arbetsböcker**. Om du bara har en prenumeration går du vidare till steg 3. 

1. Om du har flera prenumerationer väljer du den prenumeration som innehåller arbetsytan.  

1. Välj arbetsboken med namnet Access Package Activity ( *Åtkomstpaketaktivitet).* 

1. I arbetsboken väljer du ett intervall (ändra till **Alla** om du inte är säker) och väljer ett åtkomstpaket-ID i listrutan med alla åtkomstpaket som hade aktivitet under det tidsperioden. Händelser relaterade till åtkomstpaketet som inträffade under det valda tidsperioden visas.  

    ![Visa åtkomstpakethändelser](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    Varje rad innehåller tid, åtkomstpaket-ID, namnet på åtgärden, objekt-ID, UPN och visningsnamnet för den användare som startade åtgärden.  Ytterligare information ingår i JSON.   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Skapa anpassade Azure Monitor frågor med hjälp av Azure Portal
Du kan skapa egna frågor om Azure AD-granskningshändelser, inklusive berättigandehanteringshändelser.  

1. I Azure Active Directory av Azure Portal klickar du på **Loggar** under avsnittet Övervakning i den vänstra navigeringsmenyn för att skapa en ny frågesida.

1. Din arbetsyta bör visas längst upp till vänster på frågesidan. Om du har flera Azure Monitor arbetsytor och den arbetsyta som du använder för att lagra Azure AD-granskningshändelser inte visas klickar du på **Välj omfång.** Välj sedan rätt prenumeration och arbetsyta.

1. I textområdet för frågan tar du sedan bort strängen "sök *" och ersätter den med följande fråga:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Klicka sedan på **Kör**. 

    ![Klicka på Kör för att starta frågan](./media/entitlement-management-logs-and-reporting/run-query.png)

Tabellen visar som standard granskningslogghändelser för berättigandehantering från den senaste timmen. Du kan ändra inställningen För att visa äldre händelser. Men om du ändrar den här inställningen visas endast händelser som inträffat efter att Azure AD har konfigurerats att skicka händelser till Azure Monitor.

Om du vill veta de äldsta och senaste granskningshändelserna som finns i Azure Monitor använder du följande fråga:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Mer information om de kolumner som lagras för granskningshändelser i Azure Monitor finns i Tolka schemat för [Azure AD-granskningsloggar i Azure Monitor](../reports-monitoring/reference-azure-monitor-audit-log-schema.md).

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Skapa anpassade Azure Monitor med hjälp av Azure PowerShell

Du kan komma åt loggar via PowerShell när du har konfigurerat Azure AD att skicka loggar till Azure Monitor. Skicka sedan frågor från skript eller PowerShell-kommandoraden utan att behöva vara global administratör i klientorganisationen. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Se till att användaren eller tjänstens huvudnamn har rätt rolltilldelning

Kontrollera att användaren eller tjänstens huvudnamn som autentiserar till Azure AD har rätt Azure-roll på Log Analytics-arbetsytan. Rollalternativen är antingen Log Analytics-läsare eller Log Analytics-deltagare. Om du redan har en av dessa roller går du vidare till Hämta [Log Analytics-ID med en Azure-prenumeration.](#retrieve-log-analytics-id-with-one-azure-subscription)

Gör följande för att ange rolltilldelningen och skapa en fråga:

1. I Azure Portal du Log [Analytics-arbetsytan](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
).

1. Välj **Access Control (IAM).**

1. Klicka sedan på **Lägg till** för att lägga till en rolltilldelning.

    ![Lägg till en rolltilldelning](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Installera Azure PowerShell modul

När du har rätt rolltilldelning startar du PowerShell och installerar [Azure PowerShell](/powershell/azure/install-az-ps) (om du inte redan har gjort det) genom att skriva:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
Nu är du redo att autentisera till Azure AD och hämta ID:t för den Log Analytics-arbetsyta som du kör frågor mot.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Hämta Log Analytics-ID med en Azure-prenumeration
Om du bara har en enda Azure-prenumeration och en enda Log Analytics-arbetsyta skriver du följande för att autentisera till Azure AD, ansluta till prenumerationen och hämta arbetsytan:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Hämta Log Analytics-ID med flera Azure-prenumerationer

 [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) fungerar i en prenumeration i taget. Om du har flera Azure-prenumerationer bör du därför ansluta till den som har Log Analytics-arbetsytan med Azure AD-loggarna. 
 
 Följande cmdlets visar en lista över prenumerationer och hittar ID:t för den prenumeration som har Log Analytics-arbetsytan:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
Du kan återauktorera och associera Din PowerShell-session till den prenumerationen med hjälp av ett kommando som `Connect-AzAccount –Subscription $subs[0].id` . Mer information om hur du autentiserar till Azure från PowerShell, inklusive icke-interaktivt, finns i [Logga in med Azure PowerShell](/powershell/azure/authenticate-azureps).

Om du har flera Log Analytics-arbetsytor i den prenumerationen returnerar cmdleten [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) listan över arbetsytor. Sedan kan du hitta den som har Azure AD-loggarna. Fältet som returneras av denna cmdlet är samma som värdet för det "Arbetsyte-ID" som visas i Azure Portal `CustomerId` i Översikt över Log Analytics-arbetsytan.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>Skicka frågan till Log Analytics-arbetsytan
När du har identifierat en arbetsyta kan du slutligen använda [Invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) för att skicka en Kusto-fråga till arbetsytan. De här frågorna är skrivna i [Kusto-frågespråket](/azure/kusto/query/).
 
Du kan till exempel hämta datumintervallet för granskningshändelseposterna från Log Analytics-arbetsytan med PowerShell-cmdlets för att skicka en fråga som:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

Du kan också hämta berättigandehanteringshändelser med hjälp av en fråga som:

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Nästa steg:
- [Skapa interaktiva rapporter med Azure Monitor arbetsböcker](../../azure-monitor/visualize/workbooks-overview.md)
