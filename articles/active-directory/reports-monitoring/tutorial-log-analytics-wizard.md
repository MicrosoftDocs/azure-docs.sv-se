---
title: Konfigurera Log Analytics-guiden i Azure AD | Microsoft Docs
description: Lär dig hur du konfigurerar Log Analytics.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: markvi
author: MarkusVi
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: cffd72171693499365f31b6eb51fd2586187b98b
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271271"
---
# <a name="tutorial-configure-the-log-analytics-wizard"></a>Självstudie: Konfigurera Log Analytics-guiden


I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera en Log Analytics-arbetsyta för gransknings-och inloggnings loggarna
> * Köra frågor med hjälp av KQL (Kusto Query Language)
> * Skapa en varnings regel som skickar aviseringar när ett speciellt konto används
> * Skapa en anpassad arbets bok med snabb starts mal len
> * Lägg till en fråga till en befintlig arbetsboksmall

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration med minst en P1-licensierad administratör. Om du inte har någon Azure-prenumeration kan du [Registrera dig för en kostnads fri utvärderings version](https://azure.microsoft.com/free/).

- En Azure AD-klientorganisation.

- En användare som är global administratör eller säkerhetsadministratör för Azure AD-klientorganisationen.


Bekanta dig med de här artiklarna:

- [Självstudie: samla in och analysera resurs loggar från en Azure-resurs](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs)

- [Så här integrerar du aktivitets loggar med Log Analytics](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Hantera konto för nöd åtkomst i Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)

- [Snabbreferens för KQL](https://docs.microsoft.com/azure/data-explorer/kql-quick-reference)

- [Azure Monitor arbets böcker](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview)



## <a name="configure-a-workspace"></a>Konfigurera en arbets yta 

Den här proceduren beskriver hur du konfigurerar en Log Analytics-arbetsyta för gransknings-och inloggnings loggarna.
Att konfigurera en Log Analytics-arbetsyta består av två huvud steg:
 
1. Skapa en Log Analytics-arbetsyta
2. Ställer in diagnostikinställningar

**Så här konfigurerar du en arbets yta:** 


1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör.

2. Sök efter **Log Analytics-arbetsytor**.

    ![Sök efter resurser tjänster och dokument](./media/tutorial-log-analytics-wizard/search-services.png)

3. Klicka på **Lägg till**på sidan Log Analytics-arbetsytor.

    ![Lägg till](./media/tutorial-log-analytics-wizard/add.png)

4.  Utför följande steg på sidan **skapa Log Analytics arbets yta** :

    ![Skapa en Log Analytics-arbetsyta](./media/tutorial-log-analytics-wizard/create-log-analytics-workspace.png)

    1. Välj din prenumeration.

    2. Välj en resursgrupp.
 
    3. I text rutan **namn** anger du ett namn (t. ex.: MytestWorkspace1).

    4. Välj din region.

5. Klicka på **Granska + Skapa**.

    ![Granska och skapa](./media/tutorial-log-analytics-wizard/review-create.png)

6. Klicka på **skapa** och vänta tills distributionen har slutförts. Du kan behöva uppdatera sidan för att se den nya arbets ytan.

    ![Skapa](./media/tutorial-log-analytics-wizard/create-workspace.png)

7. Sök efter **Azure Active Directory**.

    ![Azure Active Firectory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

8. I avsnittet **övervakning** klickar du på **diagnostisk inställning**.

    ![Azure Active Firectory](./media/tutorial-log-analytics-wizard/diagnostic-settings.png)

9. På sidan **diagnostikinställningar** klickar du på **Lägg till diagnostisk inställning**.

    ![Lägg till diagnostisk inställning](./media/tutorial-log-analytics-wizard/add-diagnostic-setting.png)

10. Utför följande steg på sidan **diagnostisk inställning** :

    ![Välj diagnostikinställningar](./media/tutorial-log-analytics-wizard/select-diagnostics-settings.png)

    1. Under **kategori information**väljer du **AuditLogs** och **SigninLogs**.

    2. Under **mål information**väljer du **Skicka till Log Analytics**och väljer sedan din nya Log Analytics-arbetsyta. 
   
    3. Klicka på **Spara**. 

## <a name="run-queries"></a>Köra frågor  

Den här proceduren visar hur du kör frågor med hjälp av **KQL (Kusto Query Language)**.


**Köra en fråga:**


1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör.

2. Sök efter **Azure Active Directory**.

    ![Azure Active Firectory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. I avsnittet **övervakning** klickar du på **loggar**.

4. På sidan **loggar** klickar du på **Kom igång**.

5. Skriv frågan i text rutan * i*sökningen* .

6. Klicka på **Kör**.  


### <a name="kql-query-examples"></a>Exempel på KQL-frågor

Ta 10 slumpmässiga poster från indata:

`SigninLogs | take 10`

Titta på de inloggningar där den villkorliga åtkomsten lyckades

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus` 


Räkna hur många lyckade aktiviteter som har gjorts

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus | count`


Sammanlagt antal lyckade inloggningar per användare per dag:

`SigninLogs | where ConditionalAccessStatus == "success" | summarize SuccessfulSign-ins = count() by UserDisplayName, bin(TimeGenerated, 1d)` 


Se hur många gånger en användare utför en viss åtgärd under en viss tids period:

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | summarize count() by OperationName, Identity`


Pivotera resultatet av åtgärds namnet

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | project OperationName, Identity | evaluate pivot(OperationName)`


Sammanfoga gransknings-och inloggnings loggar med en inre koppling:

`AuditLogs |where OperationName contains "Add User" |extend UserPrincipalName = tostring(TargetResources[0].userPrincipalName) | |project TimeGenerated , UserPrincipalName |join kind = inner (SigninLogs) on UserPrincipalName |summarize arg_min(TimeGenerated, *) by UserPrincipalName |extend SigninDate = TimeGenerated` 


Visa antalet signerade inloggningar efter klient program typ:

`SigninLogs | summarize count() by ClientAppUsed`

Räkna inloggnings tillägg per dag:

`SigninLogs | summarize NumberOfEntries=count() by bin(TimeGenerated, 1d)`

Ta 5 slumpmässiga poster och projicera de kolumner som du vill ska visas i resultaten:

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `


Ta den översta 5 i fallande ordning och projicera de kolumner som du vill se

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `

Skapa en ny kolumn genom att kombinera värdena till två andra kolumner:

`SigninLogs | limit 10 | extend RiskUser = strcat(RiskDetail, "-", Identity) | project RiskUser, ClientAppUsed`




## <a name="create-an-alert-rule"></a>Skapa en varningsregel  

Den här proceduren visar hur du skickar aviseringar när BreakGlass-kontot används.

**Så här skapar du en varnings regel:**


1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör.

2. Sök efter **Azure Active Directory**.

    ![Azure Active Firectory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. I avsnittet **övervakning** klickar du på **loggar**.

4. På sidan **loggar** klickar du på **Kom igång**.

5. I text rutan **Sök** skriver du: `SigninLogs |where UserDisplayName contains "BreakGlass" | project UserDisplayName`

6. Klicka på **Kör**.  

7. Klicka på **ny varnings regel**i verktygsfältet.

    ![Ny varnings regel](./media/tutorial-log-analytics-wizard/new-alert-rule.png)

8. På sidan **skapa aviserings regel** kontrollerar du att omfattningen är korrekt.

9. Under **villkor**klickar du på: **när den genomsnittliga anpassade loggs ökningen är större än <logic undefined> Count**

    ![Standard villkor](./media/tutorial-log-analytics-wizard/default-condition.png)

10. På sidan **Konfigurera signal logik** , i avsnittet **aviserings logik** , utför du följande steg:

    ![Aviserings logik](./media/tutorial-log-analytics-wizard/alert-logic.png)

    1. Välj **antal resultat** **baserat på**.

    2. Som **operatör**väljer du **större än**.

    3. Som **tröskelvärde**väljer du **0**. 

11. På sidan **Konfigurera signal logik** , i avsnittet **utvärdera baserat på** , utför du följande steg:

    ![Utvärderas baserat på](./media/tutorial-log-analytics-wizard/evaluated-based-on.png)

    1. Som **period (i minuter)** väljer du **5**.

    2. Som **frekvens (i minuter)** väljer du **5**.

    3. Klicka på **Klar**. 

12. Under **Åtgärds grupp**klickar du på **Välj åtgärds grupp**. 

    ![Åtgärdsgrupp](./media/tutorial-log-analytics-wizard/action-group.png)

13. Klicka på **skapa åtgärds grupp**på sidan **Välj en åtgärds grupp som ska bifogas i aviserings regeln**. 

    ![Skapa åtgärdsgrupp](./media/tutorial-log-analytics-wizard/create-action-group.png)

14. Utför följande steg på sidan **skapa åtgärds grupp** :

    ![Instans information](./media/tutorial-log-analytics-wizard/instance-details.png)

    1. Skriv **min åtgärds grupp**i text rutan **Åtgärds grupp namn** .

    2. Skriv **min åtgärd**i text rutan **visnings namn** .

    3. Klicka på **Granska + skapa**. 

    4. Klicka på **Skapa**.


15. Utför följande steg under **Anpassa åtgärd**:

    ![Anpassa åtgärder](./media/tutorial-log-analytics-wizard/customize-actions.png)

    1. Välj **e-postämne**.

    2. I text rutan **ämnes rad** skriver du: `Breakglass account has been used`

16. Utför följande steg under **aviserings regel information**:

    ![Information om aviserings regel](./media/tutorial-log-analytics-wizard/alert-rule-details.png)

    1. I text rutan **aviserings regel namn** skriver du: `Breakglass account`

    2. I text rutan **Beskrivning** skriver du: `Your emergency access account has been used`

17. Klicka på **Skapa aviseringsregel**.   


## <a name="create-a-custom-workbook"></a>Skapa en anpassad arbets bok

Den här proceduren visar hur du skapar en ny arbets bok med hjälp av snabb starts mal len.




1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör.

2. Sök efter **Azure Active Directory**.

    ![Azure Active Firectory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. I avsnittet **övervakning** klickar du på **arbets böcker**.

    ![Arbetsböcker](./media/tutorial-log-analytics-wizard/workbooks.png)

4. I avsnittet **snabb start** klickar du på **Tom**.

    ![Snabbstart](./media/tutorial-log-analytics-wizard/quick-start.png)

5. Klicka på **Lägg till**.

    ![Lägg till arbets bok](./media/tutorial-log-analytics-wizard/add-workbook.png)

6. Klicka på **Lägg till text**.

    ![Lägg till text](./media/tutorial-log-analytics-wizard/add-text.png)


7. I text rutan skriver du: `# Client apps used in the past week` , och klickar sedan på **klar redigering**.

    ![Arbets boks text](./media/tutorial-log-analytics-wizard/workbook-text.png)

8. I den nya arbets boken klickar du på **Lägg till**och sedan på **Lägg till fråga**.

    ![Lägg till fråga](./media/tutorial-log-analytics-wizard/add-query.png)

9. I text rutan fråga skriver du: `SigninLogs | where TimeGenerated > ago(7d) | project TimeGenerated, UserDisplayName, ClientAppUsed | summarize count() by ClientAppUsed`

10. Klicka på **Kör fråga**.

    ![Kör frågan](./media/tutorial-log-analytics-wizard/run-workbook-query.png)

11. Klicka på **cirkel diagram**under **visualisering**i verktygsfältet.

    ![Cirkeldiagram](./media/tutorial-log-analytics-wizard/pie-chart.png)

12. Klicka på **klar redigering**.

    ![Redigering klar](./media/tutorial-log-analytics-wizard/done-workbook-editing.png)



## <a name="add-a-query-to-a-workbook-template"></a>Lägg till en fråga till en arbetsboksmall  

Den här proceduren visar hur du lägger till en fråga till en befintlig arbetsboksmall. Exemplet baseras på en fråga som visar distributionen av lyckade och misslyckade åtkomst försök.


1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör.

2. Sök efter **Azure Active Directory**.

    ![Azure Active Firectory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. I avsnittet **övervakning** klickar du på **arbets böcker**.

    ![Arbetsböcker](./media/tutorial-log-analytics-wizard/workbooks.png)

4. I avsnittet **villkorlig åtkomst** klickar du på **villkorlig åtkomst insikter och rapportering**.

    ![Mall för villkorsstyrd åtkomst](./media/tutorial-log-analytics-wizard/conditional-access-template.png)

5. I verktygsfältet klickar du på **Redigera**.

    ![Mall för villkorsstyrd åtkomst](./media/tutorial-log-analytics-wizard/edit-workbook-template.png)

6. Klicka på de tre punkterna i verktygsfältet, Lägg sedan **till**och **Lägg till fråga**.

    ![Lägg till arbets bok fråga](./media/tutorial-log-analytics-wizard/add-custom-workbook-query.png)

7. I text rutan fråga skriver du: `SigninLogs | where TimeGenerated > ago(20d) | where ConditionalAccessPolicies != "[]" | summarize dcount(UserDisplayName) by bin(TimeGenerated, 1d), ConditionalAccessStatus`

8. Klicka på **Kör fråga**.

    ![Kör frågan](./media/tutorial-log-analytics-wizard/run-workbook-insights-query.png)

9. Klicka på **tidsintervall**och välj sedan **Ange i fråga**.

10. Klicka på **visualisering**och välj **stapeldiagram**. 

11. Klicka på **Avancerade inställningar**, som diagram rubrik, Skriv `Conditional Access status over the last 20 days` och klicka sedan på **klar redigering**. 

    ![Ange diagram rubrik](./media/tutorial-log-analytics-wizard/set-chart-title.png)








## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du hanterar enhets identiteter med hjälp av Azure Portal.
> [!div class="nextstepaction"]
> [Övervakning](overview-monitoring.md)
