---
title: Azure Traffic Analytics-schema | Microsoft Docs
description: Förstå schemat för Trafikanalys för att analysera flödes loggar för Azure nätverks säkerhets grupper.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 765a2728c5de49787a64fff723625e53e100e450
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017790"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Schema-och data agg regering i Trafikanalys

Trafikanalys är en molnbaserad lösning som ger insyn i användar-och program aktivitet i moln nätverk. Trafikanalys analyserar Network Watcher nätverks säkerhets grupps flödes loggar (NSG) för att ge insikter i trafikflödet i ditt Azure-moln. Med trafik analys kan du:

- Visualisera nätverks aktivitet i dina Azure-prenumerationer och identifiera aktiva punkter.
- Identifiera säkerhetshot till och skydda nätverket, med information som öppna portar, program som försöker ansluta till Internet och virtuella datorer (VM) som ansluter till falska nätverk.
- Förstå trafik flödes mönster i Azure-regioner och på Internet för att optimera nätverks distributionen för prestanda och kapacitet.
- Hitta felaktiga nätverks konfigurationer som leder till misslyckade anslutningar i nätverket.
- Känner till nätverks användningen i byte, paket eller flöden.

### <a name="data-aggregation"></a>Dataaggregering

1. Alla flödes loggar på en NSG mellan "FlowIntervalStartTime_t" och "FlowIntervalEndTime_t" samlas in med en minuters intervall i lagrings kontot som blobbar innan de bearbetas av Trafikanalys.
2. Standard bearbetnings intervallet för Trafikanalys är 60 minuter. Det innebär att var 60: e minut Trafikanalys plocka blobbar från lagring för agg regering. Om det valda bearbetnings intervallet är 10 minuter, kommer Trafikanalys att välja blobbar från lagrings kontot efter var tionde minut.
3. Flöden som har samma käll-IP, mål-IP, målport, NSG namn, NSG-regel, Flow-riktning och Transport Layer Protocol (TCP eller UDP) (Obs! käll porten är exkluderad för agg regering) är sammanställas i ett enda flöde genom Trafikanalys
4. Den här posten är dekorerad (information i avsnittet nedan) och matas in i Log Analytics av Trafikanalys. den här processen kan ta upp till 1 timme max.
5. FlowStartTime_t fältet anger den första förekomsten av ett sådant sammanställt flöde (samma fyra tuple) i flödes logg bearbetnings intervallet mellan "FlowIntervalStartTime_t" och "FlowIntervalEndTime_t".
6. För alla resurser i TA är flödena som anges i användar gränssnittet det totala antalet flöden som visas av NSG, men i Log Analytics användaren ser bara den enda, nedsänkta posten. Om du vill se alla flöden använder du fältet blob_id som kan refereras från Storage. Det totala antalet flöden för den posten matchar de enskilda flödena som visas i blobben.

I nedanstående fråga får du hjälp att titta på alla undernät som samverkar med icke-offentliga offentliga IP-adresser under de senaste 30 dagarna.
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet1_s, Subnet2_s  
```
Om du vill visa BLOB-sökvägen för flödena i ovanstående fråga använder du frågan nedan:

```
let TableWithBlobId =
(AzureNetworkAnalytics_CL
   | where SubType_s == "Topology" and ResourceType == "NetworkSecurityGroup" and DiscoveryRegion_s == Region_s and IsFlowEnabled_b
   | extend binTime = bin(TimeProcessed_t, 6h),
            nsgId = strcat(Subscription_g, "/", Name_s),
            saNameSplit = split(FlowLogStorageAccount_s, "/")
   | extend saName = iif(arraylength(saNameSplit) == 3, saNameSplit[2], '')
   | distinct nsgId, saName, binTime)
| join kind = rightouter (
   AzureNetworkAnalytics_CL
   | where SubType_s == "FlowLog"  
   | extend binTime = bin(FlowEndTime_t, 6h)
) on binTime, $left.nsgId == $right.NSGList_s  
| extend blobTime = format_datetime(todatetime(FlowIntervalStartTime_t), "yyyy MM dd hh")
| extend nsgComponents = split(toupper(NSGList_s), "/"), dateTimeComponents = split(blobTime, " ")
| extend BlobPath = strcat("https://", saName,
                        "@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/", nsgComponents[0],
                        "/RESOURCEGROUPS/", nsgComponents[1],
                        "/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/", nsgComponents[2],
                        "/y=", dateTimeComponents[0], "/m=", dateTimeComponents[1], "/d=", dateTimeComponents[2], "/h=", dateTimeComponents[3],
                        "/m=00/macAddress=", replace(@"-", "", MACAddress_s),
                        "/PT1H.json")
| project-away nsgId, saName, binTime, blobTime, nsgComponents, dateTimeComponents;

TableWithBlobId
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s , BlobPath
```

Frågan ovan konstruerar en URL för att få åtkomst till bloben direkt. URL: en med plats hållare är nedan:

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>Fält som används i Trafikanalys schema
  > [!IMPORTANT]
  > Trafikanalys schemat har uppdaterats den 22 augusti 2019. Det nya schemat ger käll-och mål-IP-adresser som tar bort separat för att kunna tolka FlowDirection-fält som gör frågor enklare. </br>
  > FASchemaVersion_s uppdaterades från 1 till 2. </br>
  > Föråldrade fält: VMIP_s, Subscription_s, Region_s, NSGRules_s, Subnet_s, VM_s, NIC_s, PublicIPs_s, FlowCount_d </br>
  > Nya fält: SrcPublicIPs_s, DestPublicIPs_s, NSGRule_s </br>
  > Föråldrade fält kommer att vara tillgängliga fram till 22 november 2019.

Trafikanalys skapas ovanpå Log Analytics, så att du kan köra anpassade frågor om data som skapats av Trafikanalys och ange aviseringar på samma sätt.

Nedan visas fälten i schemat och vad de betecknar

| Fält | Format | Kommentarer |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Tabell för Trafikanalys data
| SubType_s | Logg | Undertyp för flödes loggarna. Använd endast "logg", andra värden för SubType_s är för interna arbeten av produkten |
| FASchemaVersion_s |   2   | Schema version. Visar inte NSG Flow log-version |
| TimeProcessed_t   | Datum och tid i UTC  | Tiden då Trafikanalys bearbetade rå Flow-loggar från lagrings kontot |
| FlowIntervalStartTime_t | Datum och tid i UTC |  Start tid för flödes logg bearbetnings intervallet. Detta är den tid från vilken flödes intervall mäts |
| FlowIntervalEndTime_t | Datum och tid i UTC | Slut tid för flödes logg bearbetnings intervall |
| FlowStartTime_t | Datum och tid i UTC |  Den första förekomsten av flödet (som sammanställs) i bearbetnings intervallet för flödes loggen mellan "FlowIntervalStartTime_t" och "FlowIntervalEndTime_t". Det här flödet får aggregeras baserat på agg regerings logik |
| FlowEndTime_t | Datum och tid i UTC | Den sista förekomsten av flödet (som sammanställs) i bearbetnings intervallet för flödes loggen mellan "FlowIntervalStartTime_t" och "FlowIntervalEndTime_t". I den här typen av flödes logg v2, innehåller det här fältet den tidpunkt då det sista flödet med samma fyra tuple-start (markerat som "B" i rå Flow-posten) |
| FlowType_s |  * IntraVNet <br> * Anslutningar <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * Okänd privat <br> * Okänd | Definition i anteckningar under tabellen |
| SrcIP_s | Källans IP-adress | Kommer att vara tomt i händelse av AzurePublic-och ExternalPublic-flöden |
| DestIP_s | Mål-IP-adress | Kommer att vara tomt i händelse av AzurePublic-och ExternalPublic-flöden |
| VMIP_s | IP-adress för den virtuella datorn | Används för AzurePublic-och ExternalPublic-flöden |
| PublicIP_s | Offentliga IP-adresser | Används för AzurePublic-och ExternalPublic-flöden |
| DestPort_d | Målport | Port där trafiken är inkommande |
| L4Protocol_s  | * T <br> * U  | Transport protokoll. T = TCP <br> U = UDP |
| L7Protocol_s  | Protokoll namn | Härledd från målport |
| FlowDirection_s | * I = inkommande<br> * O = utgående | Riktningen på flödet in/ut ur NSG som per flödes logg |
| FlowStatus_s  | * A = tillåts av NSG-regeln <br> * D = nekad av NSG-regeln  | Status för flöde som tillåts/nblocked av NSG som per flödes logg |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | Nätverks säkerhets grupp (NSG) som är associerad med flödet |
| NSGRules_s | \<Index value 0)>\|\<NSG_RULENAME>\|\<Flow Direction>\|\<Flow Status>\|\<FlowCount ProcessedByRule> |  NSG-regel som tillåter eller nekar det här flödet |
| NSGRule_s | NSG_RULENAME |  NSG-regel som tillåter eller nekar det här flödet |
| NSGRuleType_s | * Användardefinierad * standard |   Typ av NSG-regel som används av flödet |
| MACAddress_s | MAC-adress | MAC-adressen för NÄTVERKSKORTet som flödet registrerades med |
| Subscription_s | Prenumerationen på det virtuella Azure-nätverket/nätverks gränssnittet/den virtuella datorn fylls i i det här fältet | Endast tillämpligt för FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow och UnknownPrivate flödes typer (flödes typer där bara en sida är Azure) |
| Subscription1_s | Prenumerations-ID:t | Prenumerations-ID för virtuellt nätverk/nätverks gränssnitt/virtuell dator som käll-IP i flödet tillhör |
| Subscription2_s | Prenumerations-ID:t | Prenumerations-ID för virtuellt nätverk/nätverks gränssnitt/virtuell dator som mål-IP i flödet tillhör |
| Region_s | Azure-region för virtuellt nätverk/nätverks gränssnitt/virtuell dator som IP-adressen i flödet tillhör | Endast tillämpligt för FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow och UnknownPrivate flödes typer (flödes typer där bara en sida är Azure) |
| Region1_s | Azure-region | Azure-region för virtuellt nätverk/nätverks gränssnitt/virtuell dator som käll-IP i flödet tillhör |
| Region2_s | Azure-region | Azure-regionen för det virtuella nätverk som mål-IP i flödet tillhör |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  NÄTVERKSKORT som är kopplat till den virtuella datorn som skickar eller tar emot trafiken |
| NIC1_s | <resourcegroup_Name>/\<NetworkInterfaceName> | NÄTVERKSKORT som är kopplat till käll-IP i flödet |
| NIC2_s | <resourcegroup_Name>/\<NetworkInterfaceName> | NÄTVERKSKORT som är associerat med mål-IP i flödet |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | Virtuell dator som är associerad med nätverks gränssnittet NIC_s |
| VM1_s | <resourcegroup_Name>/\<VirtualMachineName> | Virtuell dator som är associerad med käll-IP i flödet |
| VM2_s | <resourcegroup_Name>/\<VirtualMachineName> | Virtuell dator som är associerad med mål-IP i flödet |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Undernät som är associerat med NIC_s |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Undernät som är associerat med käll-IP i flödet |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName>    | Undernät som är associerat med mål-IP i flödet |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Programgateway kopplad till käll-IP i flödet |
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Programgateway kopplad till mål-IP i flödet |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Belastningsutjämnare som är associerad med käll-IP i flödet |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Belastningsutjämnare som är associerad med mål-IP i flödet |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Lokal nätverksgateway kopplad till käll-IP i flödet |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Lokal nätverksgateway kopplad till mål-IP i flödet |
| ConnectionType_s | Möjliga värden är VNetPeering, VpnGateway och ExpressRoute |    Anslutningstyp |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | Anslutnings namn. För FlowType-P2S formateras detta som <gateway name> _<VPN Client IP> |
| ConnectingVNets_s | Blankstegsavgränsad lista över virtuella nätverks namn | I händelse av hubb och eker-topologi kommer hubbens virtuella nätverk att fyllas här |
| Country_s | Landskod med två bokstäver (ISO 3166-1 alpha-2) | Ifyllt för flödes typen ExternalPublic. Alla IP-adresser i PublicIPs_s fält kommer att dela samma landskod |
| AzureRegion_s | Azure-region platser | Ifyllt för flödes typen AzurePublic. Alla IP-adresser i PublicIPs_s fält kommer att dela Azure-regionen |
| AllowedInFlows_d | | Antal inkommande flöden som var tillåtna. Detta representerar antalet flöden som delade samma fyra tuple-inkommande till nätverks gränssnittet som flödet registrerades i |
| DeniedInFlows_d |  | Antal inkommande flöden som nekats. (Inkommande till nätverks gränssnittet som flödet registrerades i) |
| AllowedOutFlows_d | | Antal utgående flöden som tilläts (utgående till nätverks gränssnittet som flödet registrerades i) |
| DeniedOutFlows_d  | | Antal utgående flöden som nekades (utgående till nätverks gränssnittet som flödet registrerades i) |
| FlowCount_d | Inaktuellt. Totalt antal flöden som matchar samma fyra tuple. I händelse av flödes typer ExternalPublic och AzurePublic kommer antalet flöden från olika PublicIP-adresser också att inkluderas.
| InboundPackets_d | Paket som tagits emot som avbildningar i nätverks gränssnittet där NSG-regeln tillämpades | Detta är endast ifyllt för version 2 av NSG flödes logg schema |
| OutboundPackets_d  | Paket som skickas som avbildningar i nätverks gränssnittet där NSG-regeln tillämpades | Detta är endast ifyllt för version 2 av NSG flödes logg schema |
| InboundBytes_d |  Byte som tagits emot som avbildning i nätverks gränssnittet där NSG-regeln tillämpades | Detta är endast ifyllt för version 2 av NSG flödes logg schema |
| OutboundBytes_d | Byte som har skickats som avbildningar i nätverks gränssnittet där NSG-regeln tillämpades | Detta är endast ifyllt för version 2 av NSG flödes logg schema |
| CompletedFlows_d  |  | Detta fylls med ett värde som inte är noll för logg schema för version 2 av NSG Flow |
| PublicIPs_s | <PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Poster avgränsade med staplar |
| SrcPublicIPs_s | <SOURCE_PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Poster avgränsade med staplar |
| DestPublicIPs_s | <DESTINATION_PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Poster avgränsade med staplar |

### <a name="notes"></a>Kommentarer

1. I händelse av AzurePublic-och ExternalPublic-flöden fylls den kund som äger den virtuella Azure-datorns IP-adress i VMIP_s-fältet, medan de offentliga IP-adresserna fylls i fältet PublicIPs_s. För dessa två flödes typer bör vi använda VMIP_s och PublicIPs_s i stället för SrcIP_s och DestIP_s fält. För AzurePublic-och ExternalPublicIP-adresser aggregerar vi ytterligare, så att antalet poster som matas in till kund Log Analytics-arbetsytan är minimalt. (Det här fältet kommer att inaktive ras snart och vi bör använda SrcIP_ och DestIP_s beroende på om den virtuella Azure-datorn är källan eller målet i flödet)
1. Information om flödes typer: baserat på de IP-adresser som ingår i flödet, kategoriseras flödena i till följande flödes typer:
1. IntraVNet – båda IP-adresserna i flödet finns i samma Azure-Virtual Network.
1. Anslutningar-IP-adresser i flödet finns i de två olika virtuella Azure-nätverken.
1. S2S – (plats till plats) en av IP-adresserna tillhör Azure Virtual Network medan den andra IP-adressen tillhör kund nätverket (platsen) som är ansluten till Azure-Virtual Network via VPN-gateway eller Express Route.
1. P2S – (peka på plats) en av IP-adresserna tillhör Azure Virtual Network medan den andra IP-adressen tillhör kund nätverket (platsen) som är ansluten till Azure-Virtual Network via VPN-gateway.
1. AzurePublic – en av IP-adresserna tillhör Azure Virtual Network medan den andra IP-adressen tillhör Azures interna offentliga IP-adresser som ägs av Microsoft. Kundägda offentliga IP-adresser ingår inte i den här flödes typen. Till exempel kommer alla kundägda datorer som skickar trafik till en Azure-tjänst (lagrings slut punkt) att kategoriseras under den här flödes typen.
1. ExternalPublic – en av IP-adresserna tillhör Azure Virtual Network medan den andra IP-adressen är en offentlig IP-adress som inte finns i Azure, rapporteras inte som skadlig i de ASC-flöden som Trafikanalys använder för bearbetnings intervallet mellan "FlowIntervalStartTime_t" och "FlowIntervalEndTime_t".
1. MaliciousFlow – en av IP-adresserna tillhör Azure Virtual Network medan den andra IP-adressen är en offentlig IP-adress som inte finns i Azure och som rapporteras som skadlig i de ASC-flöden som Trafikanalys använder sig av bearbetnings intervallet mellan "FlowIntervalStartTime_t" och "FlowIntervalEndTime_t".
1. UnknownPrivate – en av IP-adresserna tillhör Azure Virtual Network medan den andra IP-adressen tillhör det privata IP-intervallet som definieras i RFC 1918 och inte kunde mappas av Trafikanalys till en kundägda webbplats eller Azure-Virtual Network.
1. Okänd – det går inte att mappa någon av IP-adresserna i flödena med kundens topologi i Azure och lokalt (plats).
1. Vissa fält namn läggs till i \_ s eller \_ d. Dessa säger inte källa och mål, men anger sträng och decimaler för data typerna.

### <a name="next-steps"></a>Efterföljande moment
För att få svar på vanliga frågor, se [vanliga frågor och svar om trafik analys](traffic-analytics-faq.md) för att se information om funktioner, se [Traffic Analytics-dokumentation](traffic-analytics.md)
