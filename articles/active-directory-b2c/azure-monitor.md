---
title: Övervaka Azure AD B2C med Azure Monitor
titleSuffix: Azure AD B2C
description: Lär dig hur du loggar Azure AD B2C händelser med Azure Monitor med hjälp av delegerad resurs hantering.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: mimart
ms.subservice: B2C
ms.date: 01/29/2021
ms.openlocfilehash: bc1dea8121d7986b8394adf6545a0b2c30afb133
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580189"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Övervaka Azure AD B2C med Azure Monitor

Använd Azure Monitor för att dirigera Azure Active Directory B2C (Azure AD B2C) inloggnings-och [gransknings](view-audit-logs.md) loggar till olika övervaknings lösningar. Du kan behålla loggarna för långvarig användning eller integrera dem med SIEM-verktyg (säkerhetsinformation och händelsehantering) för att få insikter om din miljö.

Du kan dirigera logg händelser till:

* Ett Azure [Storage-konto](../storage/blobs/storage-blobs-introduction.md).
* En [Log Analytics arbets yta](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) (för att analysera data, skapa instrument paneler och avisering om vissa händelser).
* En Azure [Event Hub](../event-hubs/event-hubs-about.md) (och integrera med dina Splunk-och Sumo Logic-instanser).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

I den här artikeln får du lära dig hur du överför loggarna till en Azure Log Analytics-arbetsyta. Sedan kan du skapa en instrument panel eller skapa aviseringar som baseras på Azure AD B2C användarnas aktiviteter.

> [!IMPORTANT]
> Tänk på följande när du planerar att överföra Azure AD B2C loggar till olika övervaknings lösningar eller lagrings platser. Azure AD B2C loggar innehåller person uppgifter. Sådana data bör bearbetas på ett sätt som garanterar lämplig säkerhet för person uppgifter, inklusive skydd mot obehörig eller olagligt behandling, med lämpliga tekniska eller organisatoriska åtgärder.


## <a name="deployment-overview"></a>Distributionsöversikt

Azure AD B2C utnyttjar [Azure Active Directory övervakning](../active-directory/reports-monitoring/overview-monitoring.md). Om du vill aktivera *diagnostikinställningar* i Azure Active Directory inom din Azure AD B2C-klient använder du [Azure-Lighthouse](../lighthouse/concepts/azure-delegated-resource-management.md) för att [delegera en resurs](../lighthouse/concepts/azure-delegated-resource-management.md), vilket gör att din Azure AD B2C ( **tjänst leverantören**) kan hantera en Azure AD-resurs ( **kund**). När du har slutfört stegen i den här artikeln har du åtkomst till resurs gruppen *Azure-AD-B2C-Monitor* som innehåller [arbets ytan Log Analytics](../azure-monitor/logs/quick-create-workspace.md) i **Azure AD B2C** portalen. Du kommer också att kunna överföra loggarna från Azure AD B2C till arbets ytan Log Analytics.

Under den här distributionen får du behörighet för en användare eller grupp i Azure AD B2C katalogen för att konfigurera Log Analytics arbets ytans instans i klienten som innehåller din Azure-prenumeration. För att skapa auktoriseringen distribuerar du en [Azure Resource Manager](../azure-resource-manager/index.yml) -mall till Azure AD-klienten som innehåller prenumerationen.

Följande diagram visar de komponenter som du konfigurerar i Azure AD och Azure AD B2C klienter.

![Projektion av resurs grupp](./media/azure-monitor/resource-group-projection.png)

Under den här distributionen konfigurerar du både din Azure AD B2C-klient och Azure AD-klient där Log Analytics-arbetsytan kommer att vara värd. Azure AD B2C kontot bör tilldelas rollen som [Global administratör](../active-directory/roles/permissions-reference.md#limit-use-of-global-administrator) på Azure AD B2C klienten. Det Azure AD-konto som används för att köra distributionen måste tilldelas [ägar](../role-based-access-control/built-in-roles.md#owner) rollen i Azure AD-prenumerationen. Det är också viktigt att se till att du är inloggad i rätt katalog när du Slutför varje steg enligt beskrivningen.

## <a name="1-create-or-choose-resource-group"></a>1. skapa eller Välj en resurs grupp

Börja med att skapa eller välja en resurs grupp som innehåller mål Log Analytics arbets ytan som ska ta emot data från Azure AD B2C. Du anger resurs gruppens namn när du distribuerar Azure Resource Manager-mallen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller din **Azure AD-klient**.
1. [Skapa en resurs grupp](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) eller Välj en befintlig. I det här exemplet används en resurs grupp med namnet *Azure-AD-B2C-Monitor*.

## <a name="2-create-a-log-analytics-workspace"></a>2. skapa en Log Analytics arbets yta

En **Log Analytics-arbetsyta** är en unik miljö för Azure Monitor loggdata. Du använder den här Log Analytics arbets ytan för att samla in data från Azure AD B2C [gransknings loggar](view-audit-logs.md)och sedan visualisera den med frågor och arbets böcker eller skapa aviseringar.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller din **Azure AD-klient**.
1. [Skapa en Log Analytics-arbetsyta](../azure-monitor/logs/quick-create-workspace.md). I det här exemplet används en Log Analytics arbets yta med namnet *AzureAdB2C* i en resurs grupp med namnet *Azure-AD-B2C-Monitor*.

## <a name="3-delegate-resource-management"></a>3. delegera resurs hantering

I det här steget väljer du din Azure AD B2C klient som en **tjänst leverantör**. Du definierar också de auktoriseringar du behöver för att tilldela lämpliga inbyggda Azure-roller till grupper i Azure AD-klienten.

### <a name="31-get-your-azure-ad-b2c-tenant-id"></a>3,1 Hämta ditt Azure AD B2C klient-ID

Börja med att hämta **klient-ID** för din Azure AD B2Cs katalog (även kallat katalog-ID).

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller **Azure AD B2C** klienten.
1. Välj **Azure Active Directory**, Välj **Översikt**.
1. Registrera **klient-ID: t**.

### <a name="32-select-a-security-group"></a>3,2 Välj en säkerhets grupp

Välj nu en Azure AD B2C grupp eller användare som du vill ge behörighet till resurs gruppen som du skapade tidigare i katalogen som innehåller din prenumeration.  

För att förenkla hanteringen rekommenderar vi att du använder Azure AD-användargrupper *för varje* roll, så att du kan lägga till eller ta bort enskilda användare i gruppen i stället för att tilldela behörigheter direkt till den användaren. I den här genom gången ska vi lägga till en säkerhets grupp.

> [!IMPORTANT]
> För att du ska kunna lägga till behörigheter för en Azure AD-grupp måste **grupp typen** anges till **säkerhet**. Det här alternativet väljs när gruppen skapas. Mer information finns i [Skapa en grundläggande grupp och lägga till medlemmar med hjälp av Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

1. Med **Azure Active Directory** fortfarande markerat i **Azure AD B2C** -katalogen väljer du **grupper** och sedan en grupp. Om du inte har någon befintlig grupp kan du skapa en **säkerhets** grupp och sedan lägga till medlemmar. Om du vill ha mer information följer du proceduren [skapa en grundläggande grupp och lägger till medlemmar med Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). 
1. Välj **Översikt** och registrera gruppens **objekt-ID**.

### <a name="33-create-an-azure-resource-manager-template"></a>3,3 skapa en mall för Azure Resource Manager

Därefter skapar du en Azure Resource Manager-mall som ger Azure AD B2C åtkomst till Azure AD-resurs gruppen som du skapade tidigare (till exempel *Azure-AD-B2C-Monitor*). Distribuera mallen från GitHub-exemplet genom att använda knappen **distribuera till Azure** , som öppnar Azure Portal och låter dig konfigurera och distribuera mallen direkt i portalen. För de här stegen ser du till att du är inloggad på Azure AD-klienten (inte Azure AD B2C klient organisationen).

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller din **Azure AD** -klient.
3. Använd knappen **distribuera till Azure** för att öppna Azure Portal och distribuera mallen direkt i portalen. Mer information finns i [skapa en Azure Resource Manager mall](../lighthouse/how-to/onboard-customer.md#create-an-azure-resource-manager-template).

   [![Distribuera till Azure](https://aka.ms/deploytoazurebutton)](   https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure-ad-b2c%2Fsiem%2Fmaster%2Ftemplates%2FrgDelegatedResourceManagement.json)

5. På sidan **Anpassad distribution** anger du följande information:

   | Field   | Definition |
   |---------|------------|
   | Prenumeration |  Välj den katalog som innehåller Azure-prenumerationen där resurs gruppen *Azure-AD-B2C-Monitor* skapades. |
   | Region| Välj den region där resursen ska distribueras.  | 
   | Namn på MSP-erbjudande| Ett namn som beskriver den här definitionen. Till exempel *Azure AD B2C övervakning*.  |
   | Beskrivning av MSP-erbjudandet| En kort beskrivning av ditt erbjudande. Till exempel kan *Azure Monitor i Azure AD B2C*.|
   | Hanteras av klient-ID| **Klient-ID** för Azure AD B2C klient organisation (även kallat katalog-ID). |
   |Auktoriseringar|Ange en JSON-matris med objekt som innehåller Azure AD `principalId` , `principalIdDisplayName` och Azure `roleDefinitionId` . `principalId`Är **objekt-ID: t** för den B2C grupp eller användare som ska ha åtkomst till resurser i den här Azure-prenumerationen. I den här genom gången anger du gruppens objekt-ID som du registrerade tidigare. För `roleDefinitionId` använder du det [inbyggda roll](../role-based-access-control/built-in-roles.md) svärdet för *deltagar rollen* `b24988ac-6180-42a0-ab88-20f7382dd24c` .|
   | RG namn | Namnet på den resurs grupp som du skapade tidigare i Azure AD-klienten. Till exempel *Azure-AD-B2C-Monitor*. |

   Följande exempel visar en auktoriserings mat ris med en säkerhets grupp.

   ```json
   [
       {
           "principalId": "<Replace with group's OBJECT ID>",
           "principalIdDisplayName": "Azure AD B2C tenant administrators",
           "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
       }
   ]
   ```

När du har distribuerat mallen kan det ta några minuter (vanligt vis inte fler än fem) för resurs projektionen. Du kan kontrol lera distributionen i din Azure AD-klient och få information om resurs projektionen. Mer information finns i [Visa och hantera tjänst leverantörer](../lighthouse/how-to/view-manage-service-providers.md).

## <a name="4-select-your-subscription"></a>4. Välj din prenumeration

När du har distribuerat mallen och väntat några minuter tills resurs projektionen har slutförts, följer du dessa steg för att koppla din prenumeration till din Azure AD B2C katalog.

1. Logga ut från Azure Portal om du är inloggad (Detta gör att dina autentiseringsuppgifter för sessionen kan uppdateras i nästa steg).
2. Logga in på [Azure Portal](https://portal.azure.com) med ditt **Azure AD B2C** -administratörs konto. Det här kontot måste vara medlem i säkerhets gruppen du angav i steget [delegera resurs hantering](#3-delegate-resource-management) .
3. Välj ikonen **katalog + prenumeration** i portalens verktygsfält.
4. Välj den Azure AD-katalog som innehåller Azure-prenumerationen och resurs gruppen *Azure-AD-B2C-Monitor* som du har skapat.

    ![Växla katalog](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)

1. Kontrol lera att du har valt rätt katalog och prenumeration. I det här exemplet är alla kataloger och alla prenumerationer markerade.

    ![Alla kataloger som valts i katalog & prenumerations filter](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="5-configure-diagnostic-settings"></a>5. Konfigurera diagnostikinställningar

Diagnostiska inställningar definierar var loggar och mått för en resurs ska skickas. Möjliga destinationer är:

- [Azure Storage-konto](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)
- Lösningar för [Event Hub](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)
- [Log Analytics arbets yta](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)

I det här exemplet använder vi Log Analytics arbets ytan för att skapa en instrument panel.

### <a name="51-create-diagnostic-settings"></a>5,1 Skapa diagnostikinställningar

Du är redo att [skapa diagnostikinställningar](../active-directory/reports-monitoring/overview-monitoring.md) i Azure Portal.

Konfigurera övervaknings inställningar för Azure AD B2C aktivitets loggar:

1. Logga in på [Azure Portal](https://portal.azure.com/) med ditt Azure AD B2C-administratörs konto. Det här kontot måste vara medlem i säkerhets gruppen du angav i steget [Välj en säkerhets grupp](#32-select-a-security-group) .
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. Välj **Azure Active Directory**
1. Under **Övervakning** väljer du **Diagnostikinställningar**.
1. Om det finns befintliga inställningar för resursen visas en lista över inställningar som redan har kon figurer ATS. Välj antingen **Lägg till diagnostisk inställning** för att lägga till en ny inställning eller Välj **Redigera** för att redigera en befintlig inställning. Varje inställning får inte ha fler än en av varje mål typ.

    ![Fönstret diagnostikinställningar i Azure Portal](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Ange ett namn för inställningen om det inte redan har en.
1. Markera kryss rutan för varje mål för att skicka loggarna. Välj **Konfigurera** för att ange inställningarna **enligt beskrivningen i följande tabell**.
1. Välj **Skicka till Log Analytics** och välj sedan **namnet på arbets ytan** som du skapade tidigare ( `AzureAdB2C` ).
1. Välj **AuditLogs** och **SignInLogs**.
1. Välj **Spara**.

> [!NOTE]
> Det kan ta upp till 15 minuter efter att en händelse har spridits för att den ska [visas i en Log Analytics-arbetsyta](../azure-monitor/logs/data-ingestion-time.md). Lär dig också mer om [Active Directory rapporterings fördröjning](../active-directory/reports-monitoring/reference-reports-latencies.md), vilket kan påverka inaktuella data och spela en viktig roll i rapportering.

Om du ser fel meddelandet "om du vill konfigurera diagnostikinställningar för att använda Azure Monitor för din Azure AD B2C katalog måste du konfigurera en delegerad resurs hantering," kontrol lera att du loggar in med en användare som är medlem i [säkerhets gruppen](#32-select-a-security-group) och [väljer din prenumeration](#4-select-your-subscription).

## <a name="6-visualize-your-data"></a>6. visualisera dina data

Nu kan du konfigurera din Log Analytics arbets yta för att visualisera dina data och konfigurera aviseringar. Dessa konfigurationer kan göras både i Azure AD-klienten och på din Azure AD B2C-klient.

### <a name="61-create-a-query"></a>6,1 Skapa en fråga

Med logg frågor kan du utnyttja värdet för de data som samlas in i Azure Monitor loggar. Ett kraftfullt frågespråk gör att du kan koppla data från flera tabeller, aggregera stora mängder data och utföra komplexa åtgärder med minimal kod. I stort sett kan alla frågor besvaras och analyseras så länge som stödjande data har samlats in och du förstår hur du skapar rätt fråga. Mer information finns i [Kom igång med logg frågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md).

1. Från **Log Analytics arbets yta** väljer du **loggar**
1. I Frågeredigeraren klistrar du in följande fråga för [Kusto-fråga](/azure/data-explorer/kusto/query/) . Den här frågan visar princip användning per åtgärd under de senaste x dagarna. Standard varaktigheten anges till 90 dagar (90d). Observera att frågan bara fokuserar på den åtgärd där en token/kod utfärdas av en princip.

    ```kusto
    AuditLogs
    | where TimeGenerated  > ago(90d)
    | where OperationName contains "issue"
    | extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
    | extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
    | summarize SignInCount = count() by Policy, OperationName
    | order by SignInCount desc  nulls last
    ```

1. Välj **Kör**. Frågeresultaten visas längst ned på skärmen.
1. Om du vill spara din fråga för senare användning väljer du **Spara**.

   ![Log Analytics logg redigeraren](./media/azure-monitor/query-policy-usage.png)

1. Fyll i följande information:

    - **Namn** – ange namnet på frågan.
    - **Spara som** -Välj `query` .
    - **Kategori** – Välj `Log` .

1. Välj **Spara**.

Du kan också ändra frågan för att visualisera data med hjälp av [Render](/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) -operatorn.

```kusto
AuditLogs
| where TimeGenerated  > ago(90d)
| where OperationName contains "issue"
| extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
| extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
| summarize SignInCount = count() by Policy
| order by SignInCount desc  nulls last
| render  piechart
```

![Log Analytics logg redigeraren, cirkel](./media/azure-monitor/query-policy-usage-pie.png)

Fler exempel finns i Azure AD B2C [Siem GitHub lagrings platsen](https://aka.ms/b2csiem).

### <a name="62-create-a-workbook"></a>6,2 Skapa en arbets bok

Arbetsböcker ger en flexibel arbetsyta för dataanalys och skapandet av gedigna visuella rapporter i Azure-portalen. De gör att du kan trycka på flera data källor i Azure och kombinera dem till enhetliga interaktiva upplevelser. Mer information finns i [Azure Monitor arbets böcker](../azure-monitor/visualize/workbooks-overview.md).

Följ anvisningarna nedan om du vill skapa en ny arbets bok med en mall för JSON-Galleri. Den här arbets boken innehåller en instrument panel för **användar insikter** och **autentisering** för Azure AD B2C-klient.

1. I arbets **ytan Log Analytics** väljer du **arbets böcker**.
1. Välj **+ nytt** alternativ i verktygsfältet för att skapa en ny arbets bok.
1. På sidan **ny arbets bok** väljer du **avancerad redigerare** med hjälp av **</>** alternativet i verktygsfältet.

     ![Galleri mal len](./media/azure-monitor/wrkb-adv-editor.png)

1. Välj **Galleri mal len**.
1. Ersätt JSON i **Galleri mal len**  med innehållet från [Azure AD B2C Basic-arbets bok](https://raw.githubusercontent.com/azure-ad-b2c/siem/master/workbooks/dashboard.json):
1. Tillämpa mallen med knappen **tillämpa** .
1. Slutför redigeringen av arbets boken genom att välja **klart redigera** -knappen från verktygsfältet.
1. Spara slutligen arbets boken genom att använda knappen **Spara** i verktygsfältet.
1. Ange en **rubrik**, till exempel *Azure AD B2C instrument panel*.
1. Välj **Spara**.

    ![Spara arbets boken](./media/azure-monitor/wrkb-title.png)

Arbets boken visar rapporter i form av en instrument panel.

![Arbets bok första instrument panel](./media/azure-monitor/wkrb-dashboard-1.png)

![Arbets bok andra instrument panel](./media/azure-monitor/wrkb-dashboard-2.png)

![Arbets bok tredje instrument panel](./media/azure-monitor/wrkb-dashboard-3.png)


## <a name="create-alerts"></a>Skapa aviseringar

Aviseringar skapas av aviseringsregler i Azure Monitor och kan automatiskt köra sparade frågor eller anpassade loggsökningar med jämna mellanrum. Du kan skapa aviseringar baserat på specifika prestandamått, när vissa händelser skapas, om en händelse saknas, eller om flera händelser skapas inom ett visst tidsintervall. Aviseringar kan till exempel användas för att meddela dig när det genomsnittliga antalet inloggningar överstiger ett visst tröskelvärde. Mer information finns i [skapa aviseringar](../azure-monitor/alerts/tutorial-response.md).


Följ anvisningarna nedan om du vill skapa en ny Azure-avisering, som skickar ett [e-postmeddelande](../azure-monitor/alerts/action-groups.md#configure-notifications) när det finns 25% Drop i de **totala förfrågningarna** jämför med föregående period. Aviseringen kommer att köras var 5: e minut och leta efter släpp inom de senaste 24 timmarna i Windows. Aviseringarna skapas med Kusto-frågespråk.


1. Välj **loggar** från **Log Analytics arbets yta**. 
1. Skapa en ny **Kusto-fråga** med hjälp av frågan nedan.

    ```kusto
    let start = ago(24h);
    let end = now();
    let threshold = -25; //25% decrease in total requests.
    AuditLogs
    | serialize TimeGenerated, CorrelationId, Result
    | make-series TotalRequests=dcount(CorrelationId) on TimeGenerated in range(start, end, 1h)
    | mvexpand TimeGenerated, TotalRequests
    | where TotalRequests > 0
    | serialize TotalRequests, TimeGenerated, TimeGeneratedFormatted=format_datetime(todatetime(TimeGenerated), 'yyyy-M-dd [hh:mm:ss tt]')
    | project   TimeGeneratedFormatted, TotalRequests, PercentageChange= ((toreal(TotalRequests) - toreal(prev(TotalRequests,1)))/toreal(prev(TotalRequests,1)))*100
    | order by TimeGeneratedFormatted
    | where PercentageChange <= threshold   //Trigger's alert rule if matched.
    ```

1. Välj **Kör** för att testa frågan. Du bör se resultatet om det finns ett Drop of 25% eller mer i den totala antalet begär Anden under de senaste 24 timmarna.
1. Om du vill skapa en varnings regel baserat på frågan ovan använder du alternativet **+ ny aviserings regel** som är tillgängligt i verktygsfältet.
1. På sidan **skapa en aviserings regel** väljer du **villkors namn** 
1. På sidan **Konfigurera signal logik** anger du följande värden och använder sedan knappen **klart** för att spara ändringarna.
    * Aviserings logik: ange **antalet resultat** som är **större än** **0**.
    * Utvärdering baserad på: Välj **1440** för perioden (i minuter) och **5** för frekvens (i minuter) 

    ![Skapa ett varnings regel villkor](./media/azure-monitor/alert-create-rule-condition.png)

När aviseringen har skapats går du till **Log Analytics arbets yta** och väljer **aviseringar**. Den här sidan visar alla aviseringar som har utlösts i alternativet varaktighet som angetts av **tids intervall** .  

### <a name="configure-action-groups"></a>Konfigurera åtgärds grupper

Azure Monitor-och Service Health-aviseringar använder åtgärds grupper för att meddela användare om att en avisering har utlösts. Du kan inkludera att skicka ett röst samtal, SMS, e-post; eller utlöser olika typer av automatiserade åtgärder. Följ anvisningarna [skapa och hantera åtgärds grupper i Azure Portal](../azure-monitor/alerts/action-groups.md)

Här är ett exempel på en e-postavisering om aviseringar. 

   ![E-postavisering](./media/azure-monitor/alert-email-notification.png)

## <a name="multiple-tenants"></a>Flera klienter

Om du vill publicera flera Azure AD B2C klient loggar till samma Log Analytics arbets yta (eller Azure Storage-konto eller händelsehubben) måste du ha separata distributioner med olika **namn** värden för MSP-erbjudandet. Se till att din Log Analytics arbets yta finns i samma resurs grupp som den som du konfigurerade i [skapa eller Välj resurs grupp](#1-create-or-choose-resource-group).

När du arbetar med flera Log Analytics arbets ytor använder du frågor för flera [arbets ytor](../azure-monitor/logs/cross-workspace-query.md) för att skapa frågor som fungerar över flera arbets ytor. Följande fråga utför exempelvis en koppling av två gransknings loggar från olika klienter baserat på samma kategori (till exempel autentisering):

```kusto
workspace("AD-B2C-TENANT1").AuditLogs
| join  workspace("AD-B2C-TENANT2").AuditLogs
  on $left.Category== $right.Category
```

## <a name="change-the-data-retention-period"></a>Ändra kvarhållningsperioden för data

Azure Monitors loggar har utformats för att skala och stödja insamling, indexering och lagring av stora mängder data per dag från vilken källa som helst i företaget eller som distribueras i Azure. Som standard behålls loggar i 30 dagar, men Retentions tiden kan ökas till upp till två år. Lär dig hur du [hanterar användning och kostnader med Azure Monitor loggar](../azure-monitor/logs/manage-cost-storage.md). När du har valt pris nivån kan du [ändra data lagrings perioden](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period).

## <a name="next-steps"></a>Nästa steg

* Hitta fler exempel i [galleriet](https://aka.ms/b2csiem)för Azure AD B2C Siem. 

* Mer information om hur du lägger till och konfigurerar diagnostikinställningar i Azure Monitor finns i [Självstudier: samla in och analysera resurs loggar från en Azure-resurs](../azure-monitor/essentials/monitor-azure-resource.md).

* Information om hur du strömmar Azure AD-loggar till en Event Hub finns i [självstudie: strömma Azure Active Directory loggar till en Azure Event Hub](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
