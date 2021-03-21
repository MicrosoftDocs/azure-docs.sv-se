---
title: Vanliga frågor och svar om Azure Traffic Analytics | Microsoft Docs
description: Få svar på några vanliga frågor om trafik analys.
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
ms.openlocfilehash: f4d97b81bf8f11b8380f04dcbfdb72bd658805ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579246"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Vanliga frågor och svar om Trafikanalys

Den här artikeln samlar in på en plats många av de vanligaste frågorna om trafik analys i Azure Network Watcher.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Vilka är kraven för att använda trafik analys?

Trafikanalys kräver följande krav:

- En Network Watcher aktive rad prenumeration.
- Flödes loggar för nätverks säkerhets grupper (NSG) aktiverat för den NSG: er som du vill övervaka.
- Ett Azure Storage konto för att lagra rå Flow-loggar.
- En Azure Log Analytics-arbetsyta med Läs-och Skriv behörighet.

Ditt konto måste uppfylla något av följande för att aktivera trafik analys:

- Ditt konto måste ha någon av följande Azure-roller i prenumerations omfånget: ägare, deltagare, läsare eller nätverks deltagare.
- Om ditt konto inte har tilldelats någon av de tidigare angivna rollerna, måste det tilldelas en anpassad roll som har tilldelats följande åtgärder på prenumerations nivå.
            
    - Microsoft. Network/applicationGateways/Read
    - Microsoft. Network/Connections/Read
    - Microsoft. Network/belastningsutjämnare/Read 
    - Microsoft. Network/localNetworkGateways/Read 
    - Microsoft. Network/networkInterfaces/Read 
    - Microsoft. Network/networkSecurityGroups/Read 
    - Microsoft.Network/publicIPAddresses/read
    - Microsoft. Network/routeTables/Read
    - Microsoft. Network/virtualNetworkGateways/Read 
    - Microsoft. Network/virtualNetworks/Read
        
Så här kontrollerar du roller som har tilldelats en användare för en prenumeration:

1. Logga in på Azure med hjälp av **login-AzAccount**. 

2. Välj den prenumeration som krävs genom att använda **Select-AzSubscription**. 

3. Om du vill visa en lista över alla roller som har tilldelats en angiven användare använder du  **Get-AzRoleAssignment-SignInName [user email]-IncludeClassicAdministrators**. 

Om du inte ser några utdata kan du kontakta respektive prenumerations administratör för att få åtkomst till att köra kommandona. Mer information finns i [lägga till eller ta bort Azure Role-tilldelningar med Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).


## <a name="in-which-azure-regions-is-traffic-analytics-available"></a>I vilka Azure-regioner är Trafikanalys tillgängliga?

Du kan använda Traffic Analytics för NSG: er i någon av följande regioner:
- Kanada, centrala
- USA, västra centrala
- East US
- USA, östra 2
- USA, norra centrala
- USA, södra centrala
- Central US
- USA, västra
- USA, västra 2
- Frankrike, centrala
- Europa, västra
- Europa, norra
- Brasilien, södra
- Storbritannien, västra
- Storbritannien, södra
- Australien, östra
- Australien, sydöstra 
- Asien, östra
- Sydostasien
- Sydkorea, centrala
- Indien, centrala
- Indien, södra
- Japan, östra
- Japan, västra
- US Gov, Virginia
- Kina, östra 2

Arbets ytan Log Analytics måste finnas i följande regioner:
- Kanada, centrala
- USA, västra centrala
- East US
- USA, östra 2
- USA, norra centrala
- USA, södra centrala
- Central US
- USA, västra
- USA, västra 2
- Frankrike, centrala
- Europa, västra
- Europa, norra
- Storbritannien, västra
- Storbritannien, södra
- Australien, östra
- Australien, sydöstra
- Asien, östra
- Sydostasien 
- Sydkorea, centrala
- Indien, centrala
- Japan, östra
- US Gov, Virginia
- Kina, östra 2

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Kan NSG: er jag aktivera flödes loggar i olika regioner än min arbets yta?

Ja, dessa NSG: er kan finnas i olika regioner än din Log Analytics-arbetsyta.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Kan flera NSG: er konfigureras inom en enda arbets yta?

Ja.

## <a name="can-i-use-an-existing-workspace"></a>Kan jag använda en befintlig arbets yta?

Ja. Om du väljer en befintlig arbets yta ser du till att den har migrerats till det nya frågespråket. Om du inte vill uppgradera arbets ytan måste du skapa en ny. Mer information om det nya frågespråket finns [Azure Monitor loggar uppgradera till ny loggs ökning](../azure-monitor/logs/log-query-overview.md).

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>Kan mitt Azure Storage konto finnas i en prenumeration och min Log Analytics arbets yta är i en annan prenumeration?

Ja, ditt Azure Storage konto kan finnas i en prenumeration och din Log Analytics arbets yta kan finnas i en annan prenumeration.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Kan jag lagra obehandlade loggar i en annan prenumeration?

Ja. Du kan konfigurera NSG flödes loggar som ska skickas till ett lagrings konto som finns i en annan prenumeration, förutsatt att du har rätt behörighet och att lagrings kontot finns i samma region som NSG. NSG och mål lagrings kontot måste också dela samma Azure Active Directory-klient.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Vad händer om jag inte kan konfigurera en NSG för trafik analys på grund av ett fel som inte hittas?

Välj en region som stöds. Om du väljer en region som inte stöds visas fel meddelandet "Det gick inte att hitta". De regioner som stöds finns i listan tidigare i den här artikeln.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Vad händer om jag får statusen "Det gick inte att läsa in" på sidan för NSG Flow-loggar?

Microsoft. Insights-providern måste registreras för att flödes loggning ska fungera korrekt. Om du inte är säker på om Microsoft. Insights-providern har registrerats för din prenumeration ersätter du *xxxxx-xxxxx-xxxxxx-xxxx* i följande kommando och kör följande kommandon från PowerShell:

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Jag har konfigurerat lösningen. Varför ser jag inte något på instrument panelen?

Det kan ta upp till 30 minuter innan instrument panelen visas första gången. Lösningen måste först samla in tillräckligt med data för att den ska kunna härleda meningsfulla insikter. Sedan skapas rapporter. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Vad händer om jag får det här meddelandet: "vi kunde inte hitta några data i den här arbets ytan för det valda tidsintervallet. Försök att ändra tidsintervall eller Välj en annan arbets yta. "?

Prova följande alternativ:
- Ändra tidsintervallet i den övre stapeln.
- Välj en annan Log Analytics arbets yta i det övre fältet.
- Försök att komma åt trafik analys efter 30 minuter, om det nyligen har Aktiver ATS.
    
Om problemen kvarstår kan du lösa problemet i [User Voice-forumet](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Vad händer om jag får det här meddelandet: "analyserar dina NSG Flow-loggar för första gången. Den här processen kan ta 20-30 minuter att slutföra. Kom tillbaka efter en stund. 2) om steget ovan inte fungerar och din arbets yta är under den kostnads fria SKU: n kontrollerar du arbets ytans användning här för att validera kvoten. mer information finns i vanliga frågor och svar om ytterligare information. "

Du kan se det här meddelandet på grund av följande:
- Trafikanalys nyligen har Aktiver ATS och kanske ännu inte har samlat ihop tillräckligt med data för att kunna härleda meningsfulla insikter.
- Du använder den kostnads fria versionen av Log Analytics-arbetsytan och överskrider kvot gränsen. Du kan behöva använda en arbets yta med en större kapacitet.
    
Om problemen kvarstår kan du lösa problemet i [User Voice-forumet](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Vad händer om jag får det här meddelandet: "det verkar som vi har resurs data (topologi) och ingen information om flöden. Klicka här för att visa information om resurser och Läs vanliga frågor och svar om du vill ha mer information.

Du ser resurs informationen på instrument panelen. Det finns dock ingen flödes relaterad statistik. Det kan finnas data som inte finns på grund av att det inte finns några kommunikations flöden mellan resurserna. Vänta i 60 minuter och kontrol lera statusen igen. Om problemet kvarstår och du är säker på att kommunikations flöden mellan resurser finns, kan du ta upp problem i [användar röst forumet](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Kan jag konfigurera trafik analys med PowerShell eller en Azure Resource Manager mall eller klient?

Du kan konfigurera trafik analys med hjälp av Windows PowerShell från version 6.2.1 och senare. Om du vill konfigurera flödes loggning och trafik analys för en speciell NSG med hjälp av Set-cmdleten, se [set-AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog). För att hämta flödes loggning och trafik analys status för en speciell NSG, se [Get-AzNetworkWatcherFlowLogStatus](/powershell/module/az.network/get-aznetworkwatcherflowlogstatus).

För närvarande kan du inte använda en Azure Resource Manager mall för att konfigurera trafik analys.

Information om hur du konfigurerar trafik analys genom att använda en Azure Resource Manager-klient finns i följande exempel.

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


## <a name="how-is-traffic-analytics-priced"></a>Hur kostar det att Trafikanalys?

Trafikanalys mäts. Mätningen baseras på bearbetning av flödes logg data från tjänsten och lagrar de resulterande, utökade loggarna på en Log Analytics-arbetsyta. 

Till exempel, enligt [pris Planen](https://azure.microsoft.com/pricing/details/network-watcher/), som beaktar regionen västra centrala USA, om flöde loggar data som lagras i ett lagrings konto som bearbetas av trafikanalys är 10 GB och utökade loggar som matas in i Log Analytics arbets ytan är 1 GB, är tillämpliga kostnader: 10 x $2,3 + 1 x $2,76 = $25,76

## <a name="how-frequently-does-traffic-analytics-process-data"></a>Hur ofta Trafikanalys bearbetar data?

Referera till [avsnittet data agg regering](./traffic-analytics-schema.md#data-aggregation) i trafikanalys schema och data agg regerings dokument

## <a name="how-does-traffic-analytics-decide-that-an-ip-is-malicious"></a>Hur avgör Trafikanalys att en IP-adress är skadlig? 

Trafikanalys är beroende av Microsofts interna hot informations system för att anse en IP-adress som skadlig. De här systemen använder olika typer av telemetri, t. ex. Microsofts produkter och tjänster, Microsoft Digital brottslighet Unit (DCU), Microsoft Security Response Center (MSRC) och externa feeds och bygger mycket information på den. En del av dessa data är Microsoft Internal. Om en känd IP-adress har flaggats som skadlig, kan du generera ett support ärende om du vill veta mer.

## <a name="how-can-i-set-alerts-on-traffic-analytics-data"></a>Hur kan jag ange aviseringar för Trafikanalys data?

Trafikanalys har inte inbyggt stöd för aviseringar. Men eftersom Trafikanalys data lagras i Log Analytics kan du skriva anpassade frågor och ange aviseringar för dem. Sätt
- Du kan använda kortlänk för Log Analytics i Trafikanalys. 
- Använd [schemat som beskrivs här](traffic-analytics-schema.md) för att skriva dina frågor 
- Klicka på ny varnings regel för att skapa aviseringen
- Använd [logg aviserings dokumentationen](../azure-monitor/alerts/alerts-log.md) för att skapa aviseringen

## <a name="how-do-i-check-which-vms-are-receiving-most-on-premises-traffic"></a>Hur gör jag för att kontrol lera vilka virtuella datorer som tar emot den mesta lokala trafiken?

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

För tid, Använd format: åååå-mm-dd 00:00:00

## <a name="how-do-i-check-standard-deviation-in-traffic-received-by-my-vms-from-on-premises-machines"></a>Hur gör jag för att kontrol lera standard avvikelsen i trafik som tagits emot av mina virtuella datorer från lokala datorer?

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

## <a name="how-do-i-check-which-ports-are-reachable-or-blocked-between-ip-pairs-with-nsg-rules"></a>Hur gör jag för att kontrol lera vilka portar som kan kommas åt (eller blockerade) mellan IP-par med NSG-regler?

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

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Hur kan jag navigera med hjälp av tangent bordet i Geo Map-vyn?

Den geografiska kart sidan innehåller två huvud avsnitt:
    
- **Banderoll**: banderollen överst i Geo-kartan innehåller knappar för att välja filter för trafik distribution (till exempel distribution, trafik från länder/regioner och skadlig). När du väljer en knapp används respektive filter på kartan. Om du till exempel väljer knappen aktiv, visar kartan de aktiva data centren i distributionen.
- **Karta**: under banderollen visar kart avsnittet trafik distribution mellan Azure-datacenter och länder/regioner.
    
### <a name="keyboard-navigation-on-the-banner"></a>Tangent bords navigering på banderollen
    
- Som standard är valet på sidan geo Map för banderollen "Azure DCs"-filtret.
- Om du vill flytta till ett annat filter använder du antingen `Tab` `Right arrow` tangenten eller. Om du vill flytta bakåt använder du antingen `Shift+Tab` eller `Left arrow` . Framåt-navigeringen är vänster till höger, följt av uppifrån och ned.
- Tryck på `Enter` `Down` tangenten eller piltangenten för att använda det valda filtret. Baserat på filter val och distribution är en eller flera noder under kart avsnittet markerade.
- Om du vill växla mellan banderoll och karta trycker du på `Ctrl+F6` .
        
### <a name="keyboard-navigation-on-the-map"></a>Tangent bords navigering på kartan
    
- När du har valt ett filter på banderollen och tryckte på `Ctrl+F6` , flyttas fokus till en av de markerade noderna (**Azure-datacenter** eller **land/region**) i vyn karta.
- Om du vill flytta till andra markerade noder i kartan använder du antingen `Tab` eller `Right arrow` nyckeln för vidarebefordran. Använd `Shift+Tab` eller `Left arrow` för att flytta bakåt.
- Om du vill välja en markerad nod i kartan använder du `Enter` `Down arrow` nyckeln eller.
- Vid val av sådana noder flyttas fokus till **rutan information-verktyget** för noden. Fokus flyttas som standard till knappen stängd i **rutan information-verktyg**. Om du vill flytta i **rutan** ytterligare, använder `Right arrow` `Left arrow` du och nycklar för att flytta framåt respektive bakåt. Att trycka på `Enter` har samma resultat som om du väljer knappen fokuserad i **rutan information-verktyg**.
- När du trycker `Tab` på när fokus är i **rutan information-verktyget**, flyttas fokus till slut punkterna i samma kontinent som den valda noden. Använd- `Right arrow` och- `Left arrow` tangenterna för att flytta genom de här slut punkterna.
- Om du vill flytta till andra flödes slut punkter eller kontinent-kluster använder du `Tab` för att vidarebefordra rörelser och `Shift+Tab` för omvänd rörelse.
- När fokus är på **kontinents kluster** använder du piltangenterna `Enter` `Down` för att markera slut punkterna i det kontinents klustret. Om du vill flytta genom slut punkter och knappen Stäng i informations rutan i det kontinent klustret använder du antingen `Right arrow` -eller `Left arrow` -nyckeln för framåt och bakåt. På alla slut punkter kan du använda `Shift+L` för att växla till anslutnings linjen från den valda noden till slut punkten. Du kan `Shift+L` gå tillbaka till den valda slut punkten genom att trycka på igen.
        
### <a name="keyboard-navigation-at-any-stage"></a>Tangent bords navigering i alla steg
    
- `Esc` döljer den utökade markeringen.
- `Up-arrow`Nyckeln utför samma åtgärd som `Esc` . `Down arrow`Nyckeln utför samma åtgärd som `Enter` .
- Använd `Shift+Plus` för att zooma in och `Shift+Minus` Zooma ut.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Hur kan jag navigera med hjälp av tangent bordet i vyn för topologi för virtuella nätverk?

Sidan topologi för virtuella nätverk innehåller två huvud delar:
    
- **Banderoll**: banderollen längst upp i topologin för virtuella nätverk innehåller knappar för att välja filter för trafik distribution (till exempel anslutna virtuella nätverk, frånkopplade virtuella nätverk och offentliga IP-adresser). När du väljer en knapp används respektive filter i topologin. Om du till exempel väljer knappen aktiv, visar topologin de aktiva virtuella nätverken i distributionen.
- **Topologi**: under banderollen visar avsnittet topologi trafik distribution mellan virtuella nätverk.
    
### <a name="keyboard-navigation-on-the-banner"></a>Tangent bords navigering på banderollen
    
- Som standard är valet på sidan topologi för virtuella nätverk för banderollen "anslutet virtuella nätverk".
- Om du vill flytta till ett annat filter använder du `Tab` nyckeln för att flytta framåt. Om du vill flytta bakåt använder du `Shift+Tab` nyckeln. Framåt-navigeringen är vänster till höger, följt av uppifrån och ned.
- Tryck på `Enter` för att använda det valda filtret. Baserat på filter val och distribution markeras en eller flera noder (virtuellt nätverk) under avsnittet topologi.
- Om du vill växla mellan banderollen och topologin trycker du på `Ctrl+F6` .
        
### <a name="keyboard-navigation-on-the-topology"></a>Tangent bords navigering i topologin
    
- När du har valt ett filter på banderollen och tryckte på `Ctrl+F6` , flyttas fokus till en av de markerade noderna (**VNet**) i vyn topologi.
- Om du vill flytta till andra markerade noder i vyn topologi använder du `Shift+Right arrow` nyckeln för Forward transport. 
- På markerade noder flyttar fokus till **rutan informations verktyg** för noden. Fokus flyttar som standard till knappen **Mer information** i **rutan information-verktyg**. Om du vill flytta i **rutan** ytterligare, använder du `Right arrow` och `Left arrow` för att flytta framåt och bakåt. Att trycka på `Enter` har samma resultat som om du väljer knappen fokuserad i **rutan information-verktyg**.
- Vid val av sådana noder kan du gå till alla dess anslutningar, en i taget, genom att trycka på `Shift+Left arrow` nyckeln. Fokus flyttas till **rutan informations verktyg** i anslutningen. I någon punkt kan fokus flyttas tillbaka till noden genom att trycka på `Shift+Right arrow` igen.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Hur kan jag navigera med hjälp av tangent bordet i vyn under näts topologi?

Sidan topologier för virtuella under nätverk innehåller två huvud avsnitt:
    
- **Banderoll**: banderollen längst upp i topologin för virtuella under nätverk innehåller knappar för att välja trafik distributions filter (till exempel aktiva, medel och gateway-undernät). När du väljer en knapp används respektive filter i topologin. Om du till exempel väljer knappen aktiv, markerar topologin det aktiva virtuella under nätverket i distributionen.
- **Topologi**: under banderollen visar avsnittet topologi trafik distribution mellan virtuella under nätverk.
    
### <a name="keyboard-navigation-on-the-banner"></a>Tangent bords navigering på banderollen
    
- Som standard är valet på sidan topologier för virtuella under nätverk för banderollen "undernät"-filtret.
- Om du vill flytta till ett annat filter använder du `Tab` nyckeln för att flytta framåt. Om du vill flytta bakåt använder du `Shift+Tab` nyckeln. Framåt-navigeringen är vänster till höger, följt av uppifrån och ned.
- Tryck på `Enter` för att använda det valda filtret. Baserat på filter val och distribution är en eller flera noder (undernät) i avsnittet topologi markerat.
- Om du vill växla mellan banderollen och topologin trycker du på `Ctrl+F6` .
        
### <a name="keyboard-navigation-on-the-topology"></a>Tangent bords navigering i topologin
    
- När du har valt ett filter på banderollen och tryckte på `Ctrl+F6` , flyttas fokus till en av de markerade noderna (**undernät**) i vyn topologi.
- Om du vill flytta till andra markerade noder i vyn topologi använder du `Shift+Right arrow` nyckeln för Forward transport. 
- På markerade noder flyttar fokus till **rutan informations verktyg** för noden. Fokus flyttar som standard till knappen **Mer information** i **rutan information-verktyg**. Om du vill flytta i **rutan** ytterligare, använder `Right arrow` `Left arrow` du och nycklar för att flytta framåt respektive bakåt. Att trycka på `Enter` har samma resultat som om du väljer knappen fokuserad i **rutan information-verktyg**.
- Vid val av sådana noder kan du gå till alla dess anslutningar, en i taget, genom att trycka på `Shift+Left arrow` nyckel. Fokus flyttas till **rutan informations verktyg** i anslutningen. I någon punkt kan fokus flyttas tillbaka till noden genom att trycka på `Shift+Right arrow` igen.

## <a name="are-classic-nsgs-supported"></a>Stöds klassiska NSG: er?
Nej, Trafikanalys stöder inte klassisk NSG. Vi rekommenderar att du migrerar IaaS-resurser från klassisk till Azure Resource Manager eftersom de klassiska resurserna är [inaktuella](../virtual-machines/classic-vm-deprecation.md). Läs den här artikeln för att förstå [hur du migrerar](../virtual-machines/migration-classic-resource-manager-overview.md).