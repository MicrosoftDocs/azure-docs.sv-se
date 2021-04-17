---
title: Vanliga frågor och svar om Azure-trafikanalys | Microsoft Docs
description: Få svar på några av de vanligaste frågorna om trafikanalys.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: 98c0a6f88da717256e78a748902317a90a369a9c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533623"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Trafikanalys vanliga frågor och svar

Den här artikeln samlar in många av de vanligaste frågorna om trafikanalys i Azure Network Watcher.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Vilka är kraven för att använda trafikanalys?

Trafikanalys kräver följande:

- En Network Watcher aktiverad prenumeration.
- Flödesloggar för nätverkssäkerhetsgruppen (NSG) har aktiverats för de NSG:er som du vill övervaka.
- Ett Azure Storage-konto för att lagra råflödesloggar.
- En Azure Log Analytics-arbetsyta med läs- och skrivåtkomst.

Ditt konto måste uppfylla något av följande för att aktivera trafikanalys:

- Ditt konto måste ha någon av följande Azure-roller i prenumerationsomfånget: ägare, deltagare, läsare eller nätverksdeltagare.
- Om ditt konto inte har tilldelats någon av de tidigare angivna rollerna måste det tilldelas en anpassad roll som har tilldelats följande åtgärder på prenumerationsnivå.
            
    - Microsoft.Network/applicationGateways/read
    - Microsoft.Network/connections/read
    - Microsoft.Network/loadBalancers/read 
    - Microsoft.Network/localNetworkGateways/read 
    - Microsoft.Network/networkInterfaces/read 
    - Microsoft.Network/networkSecurityGroups/read 
    - Microsoft.Network/publicIPAddresses/read
    - Microsoft.Network/routeTables/read
    - Microsoft.Network/virtualNetworkGateways/read 
    - Microsoft.Network/virtualNetworks/read
        
Så här kontrollerar du roller som tilldelats en användare för en prenumeration:

1. Logga in på Azure med **hjälp av Login-AzAccount**. 

2. Välj den prenumeration som krävs med **hjälp av Select-AzSubscription**. 

3. Om du vill visa en lista över alla roller som har tilldelats en angiven användare använder du  **Get-AzRoleAssignment -SignInName [användarens e-postadress] -IncludeClassicAdministrators**. 

Om du inte ser några utdata kontaktar du prenumerationsadministratören för att få åtkomst till att köra kommandona. Mer information finns i Lägga [till eller ta bort Azure-rolltilldelningar med hjälp av Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Kan NSG:erna jag aktiverar flödesloggar för finnas i andra regioner än min arbetsyta?

Ja, dessa NSG:er kan finnas i andra regioner än Din Log Analytics-arbetsyta.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Kan flera NSG:er konfigureras inom en enda arbetsyta?

Ja.

## <a name="can-i-use-an-existing-workspace"></a>Kan jag använda en befintlig arbetsyta?

Ja. Om du väljer en befintlig arbetsyta kontrollerar du att den har migrerats till det nya frågespråket. Om du inte vill uppgradera arbetsytan måste du skapa en ny. Mer information om det nya frågespråket finns i Azure Monitor [till ny loggsökning.](../azure-monitor/logs/log-query-overview.md)

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>Kan mitt Azure Storage konto finnas i en prenumeration och min Log Analytics-arbetsyta finns i en annan prenumeration?

Ja, ditt Azure Storage kan finnas i en prenumeration och Log Analytics-arbetsytan kan finnas i en annan prenumeration.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Kan jag lagra rådataloggar i en annan prenumeration?

Ja. Du kan konfigurera NSG-flödesloggar så att de skickas till ett lagringskonto som finns i en annan prenumeration, förutsatt att du har rätt behörigheter och att lagringskontot finns i samma region som NSG:n. NSG:n och mållagringskontot måste också dela samma Azure Active Directory klientorganisation.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Vad händer om jag inte kan konfigurera en NSG för trafikanalys på grund av felet "Hittades inte"?

Välj en region som stöds. Om du väljer en region som inte stöds visas felmeddelandet "Hittades inte". De regioner som stöds visas tidigare i den här artikeln.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Vad händer om jag får statusen "Det gick inte att läsa in" under sidan NSG-flödesloggar?

Providern Microsoft.Insights måste vara registrerad för att flödesloggningen ska fungera korrekt. Om du inte är säker på om Microsoft.Insights-providern är registrerad för din prenumeration ersätter du *xxxxx-xxxxx-xxxxxx-xxxx* i följande kommando och kör följande kommandon från PowerShell:

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Jag har konfigurerat lösningen. Varför ser jag inte något på instrumentpanelen?

Det kan ta upp till 30 minuter innan instrumentpanelen visas första gången. Lösningen måste först aggregera tillräckligt med data för att kunna härleda meningsfulla insikter. Sedan genererar den rapporter. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Vad händer om jag får det här meddelandet: "Det gick inte att hitta några data på den här arbetsytan för det valda tidsintervallet. Prova att ändra tidsintervallet eller välj en annan arbetsyta."?

Prova följande alternativ:
- Ändra tidsintervallet i det övre fältet.
- Välj en annan Log Analytics-arbetsyta i det övre fältet.
- Försök att komma åt trafikanalys efter 30 minuter, om det nyligen har aktiverats.
    
Om problemen kvarstår kan du skapa frågor i [user voice-forumet.](https://feedback.azure.com/forums/217313-networking?category_id=195844)

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Vad händer om jag får det här meddelandet: "Analyserar dina NSG-flödesloggar för första gången. Den här processen kan ta 20–30 minuter att slutföra. Kom tillbaka efter en stund. 2) Om ovanstående steg inte fungerar och arbetsytan har den kostnadsfria SKU:n kontrollerar du din arbetsyteanvändning här för att verifiera över kvoten. Mer information finns i Vanliga frågor och svar."?

Du kan se det här meddelandet eftersom:
- Trafikanalys har nyligen aktiverats och kanske ännu inte har aggregerat tillräckligt med data för att härleda meningsfulla insikter.
- Du använder den kostnadsfria versionen av Log Analytics-arbetsytan och den överskred kvotgränserna. Du kan behöva använda en arbetsyta med större kapacitet.
    
Om problemen kvarstår kan du skapa frågor i [user voice-forumet.](https://feedback.azure.com/forums/217313-networking?category_id=195844)
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Vad händer om jag får det här meddelandet: "Det verkar som att vi har resursdata (topologi) och ingen flödesinformation. Klicka här om du vill se resursdata och gå till Vanliga frågor och svar om du vill ha mer information."?

Du ser resursinformationen på instrumentpanelen. Det finns dock ingen flödesrelaterad statistik. Data kanske inte finns på grund av att det inte finns några kommunikationsflöden mellan resurserna. Vänta i 60 minuter och kontrollera statusen igen. Om problemet kvarstår och du är säker på att det finns kommunikationsflöden mellan resurser kan du skapa frågor i [User Voice-forumet.](https://feedback.azure.com/forums/217313-networking?category_id=195844)

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Kan jag konfigurera trafikanalys med PowerShell eller en Azure Resource Manager mall eller klient?

Du kan konfigurera trafikanalys med hjälp Windows PowerShell från version 6.2.1 och senare. Information om hur du konfigurerar flödesloggning och trafikanalys för en specifik NSG med hjälp av set-cmdleten finns [i Set-AzNetworkWatcherConfigFlowLog.](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) Information om hur du hämtar status för flödesloggning och trafikanalys för en specifik NSG finns [i Get-AzNetworkWatcherFlowLogStatus](/powershell/module/az.network/get-aznetworkwatcherflowlogstatus).

För närvarande kan du inte använda en Azure Resource Manager för att konfigurera trafikanalys.

Information om hur du konfigurerar trafikanalys med hjälp Azure Resource Manager en klient finns i följande exempel.

**Ange cmdlet-exempel:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<name of NSG>"
$TAstorageId = "/subscriptions/<storage subscription id>/resourcegroups/<storage resource group name> /providers/microsoft.storage/storageaccounts/<storage account name>"
$networkWatcherResourceGroupName = "<network watcher resource group name>"
$networkWatcherName = "<network watcher name>"

$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}',
    'properties': 
    {
        'storageId': '${TAstorageId}',
        'enabled': '<true to enable flow log or false to disable flow log>',
        'retentionPolicy' : 
        {
            days: <enter number of days like to retail flow logs in storage account>,
            enabled: <true to enable retention or false to disable retention>
        }
    },
    'flowAnalyticsConfiguration':
    {
                'networkWatcherFlowAnalyticsConfiguration':
      {
        'enabled':,<true to enable traffic analytics or false to disable traffic analytics>
        'workspaceId':'bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb',
        'workspaceRegion':'<workspace region>',
        'workspaceResourceId':'/subscriptions/<workspace subscription id>/resourcegroups/<workspace resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
        
      }

    }
}
"@
$apiversion = "2016-09-01"

armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/configureFlowlog?api-version=${apiversion}" $requestBody
```
**Hämta cmdlet-exempel:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<NSG name>"


$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}'
}
“@


armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/queryFlowLogStatus?api-version=${apiversion}" $requestBody
```


## <a name="how-is-traffic-analytics-priced"></a>Hur prissätts Trafikanalys pris?

Trafikanalys mäts. Mätningen baseras på bearbetning av flödesloggdata från tjänsten och lagring av de resulterande förbättrade loggarna på en Log Analytics-arbetsyta. 

Enligt prisplanen är [](https://azure.microsoft.com/pricing/details/network-watcher/)till exempel, med tanke på regionen USA, västra centrala, om flödesloggdata som lagras i ett lagringskonto som bearbetas av Trafikanalys är 10 GB och utökade loggar som matas in i Log Analytics-arbetsytan är 1 GB så är de tillämpliga avgifterna: 10 x 2,3$ + 1 x 2,76$ = 25,76$

## <a name="how-frequently-does-traffic-analytics-process-data"></a>Hur ofta Trafikanalys bearbeta data?

Se avsnittet om [dataaggregering i](./traffic-analytics-schema.md#data-aggregation) dokumentet Trafikanalys schema och dataaggregering

## <a name="how-does-traffic-analytics-decide-that-an-ip-is-malicious"></a>Hur avgör Trafikanalys en IP-adress är skadlig? 

Trafikanalys sig på Microsofts interna system för hotinformation för att se en IP-adress som skadlig. Dessa system utnyttjar olika telemetrikällor som Microsofts produkter och tjänster, Microsoft Digital Crimes Unit (DCU), Microsoft Security Response Center (MSRC) och externa flöden och skapar mycket intelligens ovanpå det. Vissa av dessa data är Microsoft Internal. Om en känd IP-adress flaggas som skadlig kan du skapa en supportbiljett för att få information.

## <a name="how-can-i-set-alerts-on-traffic-analytics-data"></a>Hur ställer jag in aviseringar för Trafikanalys data?

Trafikanalys har inte inbyggt stöd för aviseringar. Men eftersom Trafikanalys data lagras i Log Analytics kan du skriva anpassade frågor och ställa aviseringar på dem. Steg:
- Du kan använda kortlänken för Log Analytics i Trafikanalys. 
- Använd schemat [som dokumenteras här](traffic-analytics-schema.md) för att skriva dina frågor 
- Klicka på Ny aviseringsregel för att skapa aviseringen
- Se [loggaviseringsdokumentationen](../azure-monitor/alerts/alerts-log.md) för att skapa aviseringen

## <a name="how-do-i-check-which-vms-are-receiving-most-on-premises-traffic"></a>Hur gör jag för att du vilka virtuella datorer som tar emot mest lokal trafik?

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
| where <Scoping condition>
| mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
| where isnotempty(vm) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
| make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by vm
| render timechart
```

  För IP-adresser:

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
| where isnotempty(IP) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
| make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by IP
| render timechart
```

För tid använder du formatet : yyyy-mm-dd 00:00:00

## <a name="how-do-i-check-standard-deviation-in-traffic-received-by-my-vms-from-on-premises-machines"></a>Hur gör jag för att du standardavvikelsen för trafik som tas emot av mina virtuella datorer från lokala datorer?

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
| where isnotempty(vm) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + utboundBytes_d
| summarize deviation = stdev(traffic)  by vm
```

För IP-adresser:

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
| where isnotempty(IP) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d
| summarize deviation = stdev(traffic)  by IP
```

## <a name="how-do-i-check-which-ports-are-reachable-or-blocked-between-ip-pairs-with-nsg-rules"></a>Hur gör jag för att du vilka portar som kan nås (eller blockeras) mellan IP-par med NSG-regler?

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and TimeGenerated between (startTime .. endTime)
| extend sourceIPs = iif(isempty(SrcIP_s), split(SrcPublicIPs_s, " ") , pack_array(SrcIP_s)),
destIPs = iif(isempty(DestIP_s), split(DestPublicIPs_s," ") , pack_array(DestIP_s))
| mvexpand SourceIp = sourceIPs to typeof(string)
| mvexpand DestIp = destIPs to typeof(string)
| project SourceIp = tostring(split(SourceIp, "|")[0]), DestIp = tostring(split(DestIp, "|")[0]), NSGList_s, NSGRule_s, DestPort_d, L4Protocol_s, FlowStatus_s 
| summarize DestPorts= makeset(DestPort_d) by SourceIp, DestIp, NSGList_s, NSGRule_s, L4Protocol_s, FlowStatus_s
```

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Hur navigerar jag med tangentbordet i geokartvyn?

Sidan geokarta innehåller två huvudavsnitt:
    
- **Banderoll:** Banderollen överst på geokartan innehåller knappar för att välja trafikdistributionsfilter (till exempel Distribution, Trafik från länder/regioner och Skadlig). När du väljer en knapp tillämpas respektive filter på kartan. Om du till exempel väljer knappen Aktiv visas de aktiva datacentren i distributionen på kartan.
- **Karta:** Under banderollen visar kartavsnittet trafikdistribution mellan Azure-datacenter och länder/regioner.
    
### <a name="keyboard-navigation-on-the-banner"></a>Tangentbordsnavigering på banderollen
    
- Som standard är valet på geo-kartsidan för banderollen filtret "Azure CS".
- Om du vill flytta till ett annat filter använder du `Tab` antingen `Right arrow` nyckeln eller . Om du vill flytta bakåt använder du `Shift+Tab` antingen `Left arrow` nyckeln eller . Framåtnavigeringen är från vänster till höger, följt av uppifrån och ned.
- Tryck `Enter` på eller på `Down` piltangenten för att tillämpa det valda filtret. Baserat på filterval och distribution markeras en eller flera noder under kartavsnittet.
- Om du vill växla mellan banderoll och karta trycker du på `Ctrl+F6` .
        
### <a name="keyboard-navigation-on-the-map"></a>Tangentbordsnavigering på kartan
    
- När du har valt ett filter på banderollen och tryckt på flyttas fokus till en av de markerade noderna `Ctrl+F6` (**Azure-datacenter** eller **Land/Region**) i kartvyn.
- Om du vill flytta till andra markerade noder på kartan använder du `Tab` antingen eller `Right arrow` nyckeln för att flytta framåt. Använd `Shift+Tab` eller nyckeln för `Left arrow` bakåtflyttning.
- Om du vill välja en markerad nod på kartan använder du `Enter` `Down arrow` nyckeln eller .
- Vid val av sådana noder flyttas fokus till **informationsverktygsrutan** för noden. Som standard flyttas fokus till den stängda knappen i **informationsverktygsrutan**. Om du vill flytta ytterligare **i Box-vyn** `Right arrow` använder du nycklarna och för att flytta framåt respektive `Left arrow` bakåt. Att `Enter` trycka på har samma effekt som att välja den fokuserade knappen i Information Tool **Box**.
- När du trycker `Tab` medan fokus ligger på **informationsverktygsrutan** flyttas fokus till slutpunkterna på samma kontinent som den valda noden. Använd nycklarna `Right arrow` och för att gå igenom dessa `Left arrow` slutpunkter.
- Om du vill flytta till andra flödesslutpunkter eller kontinentkluster använder `Tab` du för framåtflyttning `Shift+Tab` och bakåtflyttning.
- När fokus ligger på **kontinentkluster** använder du `Enter` piltangenterna eller för att markera `Down` slutpunkterna i kontinentklustret. Om du vill gå igenom slutpunkter och knappen Stäng i informationsrutan för kontinentklustret använder du antingen nyckeln eller för `Right arrow` `Left arrow` framåt- respektive bakåtförflyttning. På valfri slutpunkt kan du använda för `Shift+L` att växla till anslutningsraden från den valda noden till slutpunkten. Du kan trycka på `Shift+L` igen för att gå till den valda slutpunkten.
        
### <a name="keyboard-navigation-at-any-stage"></a>Tangentbordsnavigering i alla steg
    
- `Esc` komprimerar det expanderade valet.
- Nyckeln `Up-arrow` utför samma åtgärd som `Esc` . Nyckeln `Down arrow` utför samma åtgärd som `Enter` .
- Använd `Shift+Plus` för att zooma in och för att zooma `Shift+Minus` ut.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Hur navigerar jag med tangentbordet i topologivyn för virtuella nätverk?

Topologisidan för virtuella nätverk innehåller två huvudavsnitt:
    
- **Banderoll:** Banderollen överst i topologin för virtuella nätverk innehåller knappar för att välja trafikdistributionsfilter (till exempel Anslutna virtuella nätverk, Frånkopplade virtuella nätverk och Offentliga IP-adresser). När du väljer en knapp tillämpas respektive filter på topologin. Om du till exempel väljer knappen Aktiv markerar topologin de aktiva virtuella nätverken i distributionen.
- **Topologi:** Under banderollen visar topologiavsnittet trafikdistribution mellan virtuella nätverk.
    
### <a name="keyboard-navigation-on-the-banner"></a>Tangentbordsnavigering på banderollen
    
- Som standard är valet på topologisidan för virtuella nätverk för banderollen filtret "Anslutna virtuella nätverk".
- Om du vill flytta till ett annat filter använder `Tab` du nyckeln för att gå vidare. Om du vill flytta bakåt använder du `Shift+Tab` nyckeln. Framåtnavigeringen är från vänster till höger, följt av uppifrån och ned.
- Tryck `Enter` på för att tillämpa det valda filtret. Baserat på filterval och distribution markeras en eller flera noder (virtuellt nätverk) under avsnittet topologi.
- Om du vill växla mellan banderollen och topologin trycker du på `Ctrl+F6` .
        
### <a name="keyboard-navigation-on-the-topology"></a>Tangentbordsnavigering i topologin
    
- När du har valt ett filter på banderollen och tryckt på flyttas fokus till en av de markerade noderna `Ctrl+F6` **(VNet)** i topologivyn.
- Om du vill flytta till andra markerade noder i topologivyn använder du `Shift+Right arrow` nyckeln för att flytta framåt. 
- På markerade noder flyttas fokus till **informationsverktygsrutan** för noden. Som standard flyttas fokus till knappen **Mer information** i **informationsverktygsrutan.** Om du vill flytta ytterligare **i Box-vyn** använder du `Right arrow` nycklarna och för att flytta framåt respektive `Left arrow` bakåt. Att `Enter` trycka på har samma effekt som att välja den fokuserade knappen i Information Tool **Box**.
- När du väljer sådana noder kan du besöka alla dess anslutningar, en i steg, genom att trycka på `Shift+Left arrow` tangenten. Fokus flyttas till **informationsverktygsrutan** för anslutningen. Fokus kan när som helst flyttas tillbaka till noden genom att trycka på `Shift+Right arrow` igen.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Hur navigerar jag med tangentbordet i undernätets topologivy?

Topologisidan för virtuella undernätsnätverk innehåller två huvudavsnitt:
    
- **Banderoll:** Banderollen överst i den virtuella undernätstopologin innehåller knappar för att välja trafikdistributionsfilter (till exempel undernäten Aktiv, Medel och Gateway). När du väljer en knapp tillämpas respektive filter på topologin. Om du till exempel väljer knappen Aktiv markerar topologin det aktiva virtuella undernätet i distributionen.
- **Topologi:** Under banderollen visar topologiavsnittet trafikdistribution mellan virtuella undernät.
    
### <a name="keyboard-navigation-on-the-banner"></a>Tangentbordsnavigering på banderollen
    
- Som standard är valet på den virtuella undernätstopologisidan för banderollen filtret "Undernät".
- Om du vill flytta till ett annat filter använder `Tab` du nyckeln för att gå vidare. Om du vill flytta bakåt använder du `Shift+Tab` nyckeln. Framåtnavigeringen är från vänster till höger, följt av uppifrån och ned.
- Tryck `Enter` på för att tillämpa det valda filtret. Baserat på filterval och distribution markeras en eller flera noder (undernät) under avsnittet topologi.
- Om du vill växla mellan banderollen och topologin trycker du på `Ctrl+F6` .
        
### <a name="keyboard-navigation-on-the-topology"></a>Tangentbordsnavigering i topologin
    
- När du har valt ett filter på banderollen och tryckt på flyttas fokus till en av de markerade noderna `Ctrl+F6` (**undernätet**) i topologivyn.
- Om du vill flytta till andra markerade noder i topologivyn använder du `Shift+Right arrow` nyckeln för framåtflyttning. 
- På markerade noder flyttas fokus till **informationsverktygsrutan** för noden. Som standard flyttas fokus till knappen **Mer information** i **informationsverktygsrutan.** Om du vill flytta ytterligare **i Box-vyn** `Right arrow` använder du nycklarna och för att flytta framåt respektive `Left arrow` bakåt. Att `Enter` trycka på har samma effekt som att välja den fokuserade knappen i Information Tool **Box**.
- När du väljer sådana noder kan du besöka alla dess anslutningar, en i steg, genom att trycka på `Shift+Left arrow` tangenten. Fokus flyttas till **informationsverktygsrutan** för anslutningen. Fokus kan när som helst flyttas tillbaka till noden genom att trycka på `Shift+Right arrow` igen.

## <a name="are-classic-nsgs-supported"></a>Stöds klassiska NSG:er?
Nej, Trafikanalys stöder inte klassisk NSG. Vi rekommenderar att du migrerar IaaS-resurser från klassisk Azure Resource Manager eftersom klassiska resurser [kommer att bli inaktuella.](../virtual-machines/classic-vm-deprecation.md) Läs den här artikeln för att [förstå hur du migrerar](../virtual-machines/migration-classic-resource-manager-overview.md).