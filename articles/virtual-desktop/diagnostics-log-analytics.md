---
title: Logg analys för Windows Virtual Desktop-diagnostik – Azure
description: Så här använder du Log Analytics med funktionen Windows-diagnostik för virtuella skriv bord.
author: Heidilohr
ms.topic: how-to
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 37990cc4322717f090c7a35c62512ba0e1a04293
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100576149"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Använd Log Analytics för funktionen diagnostik

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md).

Windows Virtual Desktop använder [Azure Monitor](../azure-monitor/overview.md) för övervakning och aviseringar som många andra Azure-tjänster. Det här gör att administratörerna kan identifiera problem via ett enda gränssnitt. Tjänsten skapar aktivitets loggar för både användar-och administrations åtgärder. Varje aktivitets logg faller under följande kategorier:

- Hanterings aktiviteter:
    - Spåra om försök att ändra virtuella Windows-skrivbordet med API: er eller PowerShell lyckas. Kan någon till exempel skapa en adresspool med PowerShell?
- Tillförsel
    - Kan användare prenumerera på arbets ytor?
    - Ser användarna alla resurser som publicerats i fjärr skrivbords klienten?
- Anslutningar:
    - När användarna initierar och slutför anslutningar till tjänsten.
- Värd registrering:
    - Har sessions värden registrerats med tjänsten vid anslutning?
- Fel:
    - Påträffar användarna eventuella problem med specifika aktiviteter? Den här funktionen kan generera en tabell som spårar aktivitets data så länge informationen är kopplad till aktiviteterna.
- Kontroll punkter
    - Vissa steg i livs längden för en aktivitet som har nåtts. Under en-session har en användare exempelvis belastnings utjämning till en viss värd, sedan var användaren inloggad vid en anslutning och så vidare.

Anslutningar som inte når Windows Virtual Desktop visas inte i diagnostiska resultat, eftersom själva roll tjänsten för diagnostik är en del av det virtuella Windows-skrivbordet. Problem med Windows Virtual Desktop-anslutning kan inträffa när användaren har problem med nätverks anslutningen.

Med Azure Monitor kan du analysera Windows-data för virtuella skriv bord och granska prestanda räknare för virtuella datorer (VM), allt inom samma verktyg. I den här artikeln får du lära dig mer om hur du aktiverar diagnostik för din Windows Virtual Desktop-miljö.

>[!NOTE]
>Information om hur du övervakar dina virtuella datorer i Azure finns i [övervaka virtuella Azure-datorer med Azure Monitor](../azure-monitor/vm/monitor-vm-azure.md). Se även till att [Granska tröskelvärdena för prestanda räknaren](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds) för att få en bättre förståelse för användar upplevelsen på sessionen.

## <a name="before-you-get-started"></a>Innan du börjar

Innan du kan använda Log Analytics måste du skapa en arbets yta. Det gör du genom att följa anvisningarna i någon av följande två artiklar:

- Om du föredrar att använda Azure Portal, se [skapa en Log Analytics arbets yta i Azure Portal](../azure-monitor/logs/quick-create-workspace.md).
- Om du föredrar PowerShell, se [skapa en Log Analytics arbets yta med PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md).

När du har skapat arbets ytan följer du anvisningarna i [Anslut Windows-datorer till Azure Monitor](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key) för att få följande information:

- Arbetsyte-ID
- Den primära nyckeln för din arbets yta

Du behöver den här informationen senare i installations processen.

Se till att granska behörighets hantering för Azure Monitor för att aktivera data åtkomst för de som övervakar och underhåller din Windows-miljö för virtuella skriv bord. Mer information finns i [Kom igång med roller, behörigheter och säkerhet med Azure Monitor](../azure-monitor/roles-permissions-security.md).

## <a name="push-diagnostics-data-to-your-workspace"></a>Skicka diagnostikdata till din arbets yta

Du kan skicka diagnostikdata från dina Windows virtuella Skriv bords objekt till Log Analytics för din arbets yta. Du kan ställa in den här funktionen direkt när du först skapar dina objekt.

Så här konfigurerar du Log Analytics för ett nytt objekt:

1. Logga in på Azure Portal och gå till **det virtuella Windows-skrivbordet**.

2. Navigera till objektet (till exempel en värdstat, en app-grupp eller en arbets yta) som du vill avbilda loggar och händelser för.

3. Välj **diagnostikinställningar** i menyn på vänster sida av skärmen.

4. Välj **Lägg till diagnostisk inställning** på menyn som visas på höger sida av skärmen.

    Vilka alternativ som visas på sidan diagnostikinställningar varierar beroende på vilken typ av objekt du redigerar.

    När du till exempel aktiverar diagnostik för en app-grupp visas alternativ för att konfigurera kontroll punkter, fel och hantering. För arbets ytor konfigurerar de här kategorierna en feed som spårar när användarna prenumererar på listan över appar. Mer information om diagnostikinställningar finns i [skapa diagnostisk inställning för att samla in resurs loggar och mått i Azure](../azure-monitor/essentials/diagnostic-settings.md).

     >[!IMPORTANT]
     >Kom ihåg att aktivera diagnostik för varje Azure Resource Manager objekt som du vill övervaka. Data blir tillgängliga för aktiviteter efter att diagnostik har Aktiver ATS. Det kan ta några timmar efter första insamlingen.

5. Ange ett namn för konfiguration av inställningar och välj sedan **Skicka till Log Analytics**. Namnet du använder får inte innehålla blank steg och bör följa [namngivnings konventionerna för Azure](../azure-resource-manager/management/resource-name-rules.md). Som en del av loggarna kan du välja alla alternativ som du vill lägga till i Log Analytics, t. ex. kontroll punkt, fel, hantering och så vidare.

6. Välj **Spara**.

>[!NOTE]
>Log Analytics ger dig möjlighet att strömma data till [Event Hubs](../event-hubs/event-hubs-about.md) eller arkivera dem i ett lagrings konto. Mer information om den här funktionen finns i [Stream Azure Monitoring data to a Event Hub](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md) och [arkivera Azure-resurs loggar till lagrings kontot](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

## <a name="how-to-access-log-analytics"></a>Så här kommer du åt Log Analytics

Du kan komma åt Log Analytics arbets ytor på Azure Portal eller Azure Monitor.

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>Åtkomst Log Analytics på en Log Analytics arbets yta

1. Logga in på Azure-portalen.

2. Sök efter **Log Analytics-arbetsyta**.

3. Under tjänster väljer du **Log Analytics arbets ytor**.

4. I listan väljer du den arbets yta som du konfigurerade för ditt Windows Virtual Desktop-objekt.

5. På arbets ytan väljer du **loggar**. Du kan filtrera ut din meny lista med **Sök** funktionen.

### <a name="access-log-analytics-on-azure-monitor"></a>Åtkomst Log Analytics på Azure Monitor

1. Logga in på Azure-portalen

2. Sök efter och välj **övervakare**.

3. Välj **loggar**.

4. Följ instruktionerna på sidan loggning för att ange omfånget för frågan.

5. Du är redo att fråga diagnostik. Alla diagnostiska tabeller har prefixet "WVD".

>[!NOTE]
>Mer detaljerad information om de tabeller som lagras i Azure Monitor loggar finns i [Azure Monitor data](/azure/azure-monitor/reference/)återställningen. Alla tabeller som är relaterade till Windows Virtual Desktop kallas "WVD".

## <a name="cadence-for-sending-diagnostic-events"></a>Takt för att skicka diagnostiska händelser

Diagnostiska händelser skickas till Log Analytics när de är klara.

Log Analytics endast rapporter i dessa mellanliggande tillstånd för anslutnings aktiviteter:

- Startade: när en användare väljer och ansluter till en app eller ett skriv bord i fjärr skrivbords klienten.
- Ansluten: när användaren har anslutit till den virtuella datorn där appen eller Skriv bordet finns.
- Slutfört: när användaren eller servern kopplar från sessionen som aktiviteten ägde rum i.

## <a name="example-queries"></a>Exempelfrågor

Till exempel frågor via Azure Monitor Log Analytics användar gränssnitt:
1. Gå till din Log Analytics arbets yta och välj sedan **loggar**. Exempel frågans användar gränssnitt visas automatiskt.
1. Ändra filtret till **kategori**.
1. Välj **virtuellt Windows-skrivbord** för att granska tillgängliga frågor.
1. Välj **Kör** för att köra den valda frågan.

Läs mer om exempel fråga-gränssnittet i [sparade frågor i Azure Monitor Log Analytics](../azure-monitor/logs/example-queries.md).

I följande lista med frågor kan du granska anslutnings information eller problem för en enskild användare. Du kan köra dessa frågor i [Log Analytics Frågeredigeraren](../azure-monitor/logs/log-analytics-tutorial.md#write-a-query). Ersätt `userupn` med UPN för den användare som du vill söka efter varje fråga.


Så här söker du efter alla anslutningar för en enskild användare:

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
```


Så här tar du reda på hur många gånger en användare ansluter per dag:

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
|summarize dcount(CorrelationId) by bin(TimeGenerated, 1d)
```

Så här hittar du varaktigheten för sessionen per användare:

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId)
on CorrelationId
| project Duration = EndTime - StartTime, ResourceAlias
| sort by Duration asc
```

Så här hittar du fel för en speciell användare:

```kusto
WVDErrors
| where UserName == "userupn"
|take 100
```

Ta reda på om ett fel har inträffat för andra användare:

```kusto
WVDErrors
| where CodeSymbolic =="ErrorSymbolicCode"
| summarize count(UserName) by CodeSymbolic
```


>[!NOTE]
>- När en användare öppnar fullständig skriv bord spåras inte appens användning i sessionen som kontroll punkter i WVDCheckpoints-tabellen.
>- Kolumnen ResourcesAlias i tabellen WVDConnections visar om en användare har anslutit till ett fullständigt skriv bord eller en publicerad app. Kolumnen visar bara den första app som de är öppna under anslutningen. Alla publicerade appar som användaren öppnar spåras i WVDCheckpoints.
>- I WVDErrors-tabellen visas hanterings fel, problem med värd registrering och andra problem som inträffar när användaren prenumererar på en lista över appar eller skriv bord.
>- WVDErrors hjälper dig att identifiera problem som kan lösas av administrativa uppgifter. Värdet på ServiceError står alltid "false" för dessa typer av problem. Om ServiceError = "true" måste du eskalera problemet till Microsoft. Se till att du anger CorrelationID för de fel som du eskalerar.

## <a name="next-steps"></a>Nästa steg

Se [identifiera och diagnostisera problem](diagnostics-role-service.md#common-error-scenarios)för att granska vanliga fel scenarier som kan identifieras av diagnostikprogrammet.