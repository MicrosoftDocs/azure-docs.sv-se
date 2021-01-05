---
title: SAP-konfigurationer för arbets belastning med Azure-tillgänglighetszoner | Microsoft Docs
description: Arkitektur och scenarier med hög tillgänglighet för SAP NetWeaver med hjälp av Azure-tillgänglighetszoner
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7418e5578450367e9fa37a87adb6e7036619877b
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97827457"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>SAP-arbetsbelastningskonfigurationer med Azure-tillgänglighetszoner
Förutom distributionen av de olika SAP-arkitekturerna i tillgänglighets uppsättningarna i Azure, kan den mer nyligen introducerade [Azure-tillgänglighetszoner](../../../availability-zones/az-overview.md) användas för distributioner av SAP-arbetsbelastningar. En tillgänglighets zon för Azure definieras som: "unika fysiska platser inom en region. Varje zon består av ett eller flera data Center som är utrustade med oberoende strömförsörjning, kylning och nätverk. Azure-tillgänglighetszoner är inte tillgängliga i alla regioner. För Azure-regioner som tillhandahåller Tillgänglighetszoner, kontrol lera [Azures regions karta](https://azure.microsoft.com/global-infrastructure/geographies/). Den här kartan visar vilka regioner som innehåller eller är meddelade att tillhandahålla Tillgänglighetszoner. 

Från och med den typiska SAP-NetWeaver eller S/4HANA-arkitekturen måste du skydda tre olika lager:

- SAP-programnivå, som kan vara ett till några dussin virtuella datorer. Du vill minimera risken för att virtuella datorer får distribueras på samma värd Server. Du vill också att de virtuella datorerna ska ha en acceptabel närhet till DBMS-lagret för att hålla nätverks fördröjningen i ett godkänt fönster
- SAP ASCS/SCS-lager som representerar en enskild felpunkt i SAP NetWeaver-och S/4HANA-arkitekturen. Du kan vanligt vis titta på två virtuella datorer som du vill ska omfattas av ett ramverk för växling vid fel. Dessa virtuella datorer bör därför allokeras i olika infrastruktur fel och uppdaterings domäner
- SAP DBMS Layer, som representerar en enskild fel källa. I vanliga fall består det av två virtuella datorer som omfattas av ett ramverk för växling vid fel. De virtuella datorerna bör därför tilldelas i olika infrastruktur fel och uppdaterings domäner. Undantag är SAP HANA skalbara distributioner där fler än två virtuella datorer kan användas

De största skillnaderna mellan att distribuera dina kritiska virtuella datorer via tillgänglighets uppsättningar eller Tillgänglighetszoner är:

- Att distribuera med en tillgänglighets uppsättning bevarar de virtuella datorerna i uppsättningen i en enskild zon eller data Center (oavsett vilken som gäller för den specifika regionen). Det leder till att distributionen via tillgänglighets uppsättningen inte skyddas av strömförsörjnings-, kylnings-eller nätverks problem som påverkar dataceter (er) i zonen som helhet. På plus-sidan är de virtuella datorerna justerade med uppdaterings-och fel domäner i den zonen eller data centret. Specifikt för SAP ASCS-eller DBMS-skiktet där vi skyddar två virtuella datorer per tillgänglighets uppsättning, förhindrar justeringen av fel-och uppdaterings domäner att båda virtuella datorerna slutar på samma värd maskin vara 
- Genom att distribuera virtuella datorer via en Azure-tillgänglighetszoner och välja olika zoner (högst tre möjliga) ska du distribuera de virtuella datorerna på olika fysiska platser och med det nya skyddet från strömförsörjnings-, kylnings-eller nätverks problem som påverkar dataceter i zonen som helhet. Men när du distribuerar fler än en virtuell dator av samma VM-serie till samma tillgänglighets zon, sker inget skydd från de virtuella datorerna som slutar på samma värd. Därför är distributionen genom Tillgänglighetszoner idealisk för SAP-ASCS och DBMS-skiktet där vi vanligt vis tittar på två virtuella datorer var och en. För SAP-program skiktet, som kan vara drastiskt mer än två virtuella datorer, kan du behöva återgå till en annan distributions modell (se senare)

Din motivation vid en distribution över Azure-tillgänglighetszoner bör vara att du, ovanpå att täcka fel i en enskild kritisk virtuell dator eller möjlighet att minska stillestånds tiden för program varu korrigeringar inom en kritisk, vill skydda från större infrastruktur problem som kan påverka tillgängligheten för ett eller flera Azure-datacenter. 

## <a name="considerations-for-deploying-across-availability-zones"></a>Att tänka på när du distribuerar i Tillgänglighetszoner


Tänk på följande när du använder Tillgänglighetszoner:

- Det finns inga garantier avseende avstånden mellan olika Tillgänglighetszoner inom en Azure-region.
- Tillgänglighetszoner är inte en idealisk DR-lösning. Natur katastrofer kan orsaka omfattande skador i världs regioner, inklusive tung skada på energi infrastrukturer. Avstånden mellan olika zoner kanske inte är tillräckligt stora för att utgöra en korrekt DR-lösning.
- Nätverks fördröjningen i Tillgänglighetszoner är inte samma i alla Azure-regioner. I vissa fall kan du distribuera och köra SAP-program skiktet över olika zoner eftersom nätverks fördröjningen från en zon till den aktiva DBMS-datorn är acceptabel. Men i vissa Azure-regioner kanske svars tiden mellan den aktiva DBMS-datorn och SAP-programinstansen, när de distribueras i olika zoner, inte är acceptabla för SAP-affärsprocesser. I dessa fall måste distributions arkitekturen vara olika, med en aktiv/aktiv arkitektur för programmet eller en aktiv/passiv arkitektur där nätverks fördröjningen för flera zoner är för hög.
- När du bestämmer var du ska använda Tillgänglighetszoner baserar du ditt beslut om nätverks fördröjningen mellan zonerna. Nätverks fördröjningen spelar en viktig roll på två områden:
    - Fördröjning mellan de två DBMS-instanser som behöver ha synkron replikering. Ju högre nätverks fördröjning, desto sannolikare kommer det att påverka arbets Belastningens skalbarhet.
    - Skillnaden i nätverks fördröjningen mellan en virtuell dator som kör en SAP-dialogruta i zonen med den aktiva DBMS-instansen och en liknande virtuell dator i en annan zon. När den här skillnaden ökar, ökar även den tid som affärs processer och batch-jobb körs, beroende på om de körs i zon med DBMS eller i en annan zon.

När du distribuerar virtuella Azure-datorer i Tillgänglighetszoner och upprättar lösningar för redundans inom samma Azure-region gäller vissa begränsningar:

- Du måste använda [Azure-Managed disks](https://azure.microsoft.com/services/managed-disks/) när du distribuerar till Azure-tillgänglighetszoner. 
- Mappningen av zon uppräkningar till fysiska zoner är fast för en Azure-prenumeration. Om du använder olika prenumerationer för att distribuera dina SAP-system måste du definiera de idealiska zonerna för varje prenumeration.
- Du kan inte distribuera Azures tillgänglighets uppsättningar i en tillgänglighets zon för Azure om du inte använder [Azure närhets placerings grupp](../../linux/co-location.md). Hur du kan distribuera SAP-DBMS-skiktet och de centrala tjänsterna över zoner och samtidigt distribuera SAP-programlagret med tillgänglighets uppsättningar och fortfarande uppnå nära de virtuella datorerna finns dokumenterade i artikelernas [närhets grupper för optimal nätverks fördröjning med SAP-program](sap-proximity-placement-scenarios.md). Om du inte använder Azure närhets placerings grupper måste du välja en eller en annan som distributions ramverk för virtuella datorer.
- Du kan inte använda ett [Azure Basic-Load Balancer](../../../load-balancer/load-balancer-overview.md) för att skapa lösningar för redundanskluster som baseras på Windows Server-redundanskluster eller Linux-pacemaker. I stället måste du använda [Azure standard load BALANCER SKU](../../../load-balancer/load-balancer-standard-availability-zones.md).



## <a name="the-ideal-availability-zones-combination"></a>Den perfekta Tillgänglighetszoner kombinationen
Om du vill distribuera ett SAP-NetWeaver eller ett S/4HANA-system mellan zoner finns det två princip arkitekturer som du kan distribuera:

- Aktiv/aktiv: ett par med virtuella datorer som kör ASCS/SCS och det par med virtuella datorer som kör DBMS-skiktet distribueras över två zoner. Antalet virtuella datorer som kör SAP-Programskiktet distribueras till ett jämnt antal i samma två zoner. Om en virtuell DBMS-eller ASCS/SCS-dator växlar över kan några av de öppna och aktiva transaktionerna återställas. Men användare är fortfarande inloggade. Det spelar ingen roll i vilken zon de aktiva DBMS-VM: en och program instanserna körs. Den här arkitekturen är den arkitektur som föredras för att distribuera mellan zoner.
- Aktiv/passiv: ett par med virtuella datorer som kör ASCS/SCS och det par med virtuella datorer som kör DBMS-skiktet distribueras över två zoner. Antalet virtuella datorer som kör SAP-Programskiktet distribueras till ett av de Tillgänglighetszoner. Du kör program skiktet i samma zon som den aktiva ASCS/SCS-och DBMS-instansen. Du använder den här distributions arkitekturen om nätverks fördröjningen i olika zoner är för hög för att köra program lagret som distribueras i zonerna. I stället måste SAP-programlagret köras i samma zon som den aktiva ASCS/SCS-och/eller DBMS-instansen. Om en virtuell ASCS/SCS-eller DBMS-dator växlar över till den sekundära zonen kan du stöta på en högre nätverks fördröjning och med en minskning av data flödet. Och du måste återställa tidigare misslyckade VM-datorer så snart som möjligt för att komma tillbaka till tidigare data flödes nivåer. Om ett zonindelade avbrott uppstår måste program lagret växlas över till den sekundära zonen. En aktivitet som användare upplever som en fullständig System avstängning. I vissa Azure-regioner är den här arkitekturen den enda livskraftiga arkitekturen när du vill använda Tillgänglighetszoner. Om du inte kan acceptera den potentiella effekten av en ASCS/SCS-eller DBMS-VM som växlar över till den sekundära zonen kan du vara bättre på att hålla koll på tillgänglighets uppsättnings distributioner


Innan du bestämmer hur du ska använda Tillgänglighetszoner måste du fastställa:

- Nätverks fördröjningen bland de tre zonerna i en Azure-region. Att känna till nätverks fördröjningen mellan zonerna i en region gör att du kan välja zoner med minsta nätverks svars tid i nätverks trafiken över flera zoner.
- Skillnaden mellan VM-till-VM-svars tid inom en av zonerna, om du väljer och nätverks fördröjningen i två zoner som du väljer.
- En bestämning av huruvida de VM-typer som du behöver distribuera är tillgängliga i de två zoner som du har valt. Med vissa virtuella datorer, särskilt virtuella datorer i M-serien, kan det uppstå situationer där vissa SKU: er endast är tillgängliga i två av de tre zonerna.

## <a name="network-latency-between-and-within-zones"></a>Nätverks fördröjning mellan och inom zoner
För att fastställa svars tiden mellan olika zoner måste du:

- Distribuera VM-SKU: n som du vill använda för din DBMS-instans i alla tre zoner. Kontrol lera att [Azure-accelererat nätverk](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) är aktiverat när du utför den här mätningen.
- När du hittar de två zonerna med minsta nätverks svars tid distribuerar du ytterligare tre virtuella datorer av VM-SKU: n som du vill använda som den virtuella program skiktet i de tre Tillgänglighetszoner. Mät nätverks fördröjningen mot de två virtuella DBMS-datorerna i de två DBMS-zonerna som du har valt. 
- Använd **`niping`** som Mät verktyg. Det här verktyget, från SAP, beskrivs i SAP support Notes [#500235](https://launchpad.support.sap.com/#/notes/500235) och [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Fokusera på de kommandon som dokumenteras för svars tids mätningar. Eftersom **ping** inte fungerar via Sök vägarna för Azure accelererade nätverks koder rekommenderar vi inte att du använder den.

Du behöver inte utföra de här testerna manuellt. Du hittar ett svar på en [fördröjning för tillgänglighets zonen](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) i PowerShell-proceduren som automatiserar svars tiderna som beskrivs. 

Utifrån dina mått och tillgängligheten för dina VM-SKU: er i Tillgänglighetszoner måste du fatta några beslut:

- Definiera de idealiska zonerna för DBMS-skiktet.
- Bestäm om du vill distribuera ditt aktiva SAP-programlager över en, två eller alla tre zonerna, baserat på skillnader i nätverks fördröjningen i zonen eller mellan zoner.
- Bestäm om du vill distribuera en aktiv/passiv konfiguration eller en aktiv/aktiv-konfiguration, från en program plats i vyn. (Dessa konfigurationer beskrivs senare i den här artikeln.)

Vid besluts fattandet bör du också ta del av rekommendationerna för nätverks fördröjning i SAP, enligt beskrivningen i SAP Obs [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> De mätningar och beslut du gör är giltiga för den Azure-prenumeration du använde när du tog måtten. Om du använder en annan Azure-prenumeration måste du upprepa måtten. Mappningen av uppräknade zoner kan vara olika för en annan Azure-prenumeration.


> [!IMPORTANT]
> Det förväntas att de mått som beskrivs ovan ger olika resultat i varje Azure-region som har stöd för [Tillgänglighetszoner](https://azure.microsoft.com/global-infrastructure/geographies/). Även om kraven på nätverks fördröjning är desamma, kan du behöva använda olika distributions strategier i olika Azure-regioner eftersom nätverks fördröjningen mellan zoner kan vara olika. I vissa Azure-regioner kan nätverks fördröjningen mellan de tre olika zonerna vara mycket annorlunda. I andra regioner kan nätverks fördröjningen mellan de tre olika zonerna vara mer enhetlig. Anspråket att det alltid finns en nätverks fördröjning mellan 1 och 2 millisekunder är inte korrekt. Det går inte att generalisera nätverks fördröjningen i Tillgänglighetszoner i Azure-regioner.

## <a name="activeactive-deployment"></a>Aktiv/aktiv-distribution
Den här distributions arkitekturen kallas aktiv/aktiv eftersom du distribuerar dina aktiva SAP-programservrar över två eller tre zoner. SAP Central Services-instansen som använder en transaktionskö kommer att distribueras mellan två zoner. Detsamma gäller för DBMS-skiktet, som kommer att distribueras över samma zoner som den centrala SAP-tjänsten. När du överväger den här konfigurationen måste du hitta de två Tillgänglighetszoner i din region som erbjuder nätverks svars tid i flera zoner som är acceptabla för arbets belastningen och din synkrona DBMS-replikering. Du vill också vara säker på att delta mellan nätverks fördröjning i de zoner som du har valt och nätverks fördröjningen för flera zoner inte är för stor. 

SAP-arkitekturens natur är att om du inte konfigurerar den på olika sätt kan användare och batch-jobb köras i de olika program instanserna. Sido effekterna av detta faktum med den aktiva/aktiva distributionen är att batch-jobb kan köras av alla SAP-programinstanser oberoende av om de körs i samma zon med det aktiva DBMS eller inte. Om skillnaden i nätverks fördröjningen mellan skillnads zonerna är liten jämfört med nätverks fördröjningen i en zon, kan skillnaden i körnings tiderna för batch-jobb vara betydande. Men ju större skillnaden för nätverks fördröjning i en zon jämfört med över zon nätverks trafik, kan körnings tiden för batch-jobb påverkas mer om jobbet körs i en zon där DBMS-instansen inte är aktiv. Det är på dig som en kund för att bestämma vilka acceptabla skillnader i körnings tiden är. Och med det vad den tillåtna nätverks fördröjningen för trafik mellan zoner är.

Azure-regioner där en sådan aktiv/aktiv-distribution är möjlig utan stora skillnader i körnings tid och data flöde i program lagret distribueras över olika Tillgänglighetszoner, till exempel:

- Västra 2; USA (alla tre zoner)
- Östra 2; USA (alla tre zonerna)
- Centrala USA (alla tre zoner)
- Nord Europa (alla tre zonerna)
- Västeuropa (två av de tre zonerna)
- USA, östra (två av de tre zonerna)
- Södra centrala USA (två av de tre zonerna)
- Storbritannien, södra (två av de tre zonerna)

Azure-regioner där denna SAP-distributions arkitektur i zoner inte rekommenderas är:

- Frankrike, centrala 
- Sydafrika, norra
- Kanada, centrala
- Japan, östra

Beroende på vad du är villig att tolerera vid körnings tiden kan andra regioner som inte anges kan kvalificeras.


Ett förenklat schema för en aktiv/aktiv-distribution över två zoner kan se ut så här:

![Distribution av aktiva/aktiva zoner](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Följande överväganden gäller för den här konfigurationen:

- Genom att inte använda [Azure närhets placerings grupp](../../linux/co-location.md)hanterar du Azure-tillgänglighetszoner som fel-och uppdaterings domäner för alla virtuella datorer eftersom det inte går att distribuera tillgänglighets uppsättningar i Azure-tillgänglighetszoner.
- Om du vill kombinera zonindelade-distributioner för DBMS-skiktet och centrala tjänster, men vill använda Azures tillgänglighets uppsättningar för program lagret, måste du använda Azure närhets grupper enligt beskrivningen i artikel [Azure närhets placerings grupper för optimal nätverks fördröjning med SAP-program](sap-proximity-placement-scenarios.md).
- För belastningsutjämnare för redundanskluster i SAP Central Services och DBMS-skiktet måste du använda [standard-SKU-Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md). Basic-Load Balancer fungerar inte mellan zoner.
- Det virtuella Azure-nätverket som du har distribuerat som värd för SAP-systemet, tillsammans med dess undernät, sträcks ut över zoner. Du behöver inte separata virtuella nätverk för varje zon.
- För alla virtuella datorer som du distribuerar måste du använda [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Ohanterade diskar stöds inte för zonindelade-distributioner.
- Azure Premium Storage, [Ultra SSD Storage](../../disks-types.md#ultra-disk)eller ANF stöder inte någon typ av lagrings replikering mellan zoner. Programmet (DBMS eller SAP Central Services) måste replikera viktiga data.
- Detsamma gäller för den delade sapmnt-katalogen, som är en delad disk (Windows), en CIFS-resurs (Windows) eller en NFS-resurs (Linux). Du måste använda en teknik som replikerar de här delade diskarna eller resurserna mellan zonerna. Dessa tekniker stöds:
  - För Windows är en kluster lösning som använder SIOS DataKeeper, som dokumenterad i [kluster en SAP ASCS/SCS-instans på ett Windows-redundanskluster med hjälp av en klusterdelad disk i Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
  - För SUSE Linux är en NFS-resurs som skapats som dokumenterad i [hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
    För närvarande stöds inte lösningen som använder Microsoft Scale-Out-filserver, som dokumenteras i [förbereda Azure-infrastruktur för SAP med hög tillgänglighet med hjälp av ett Windows-redundanskluster och fil resurs för SAP ASCS/SCS-instanser](./sap-high-availability-infrastructure-wsfc-file-share.md), i zoner.
- Den tredje zonen används som värd för SBD-enheten om du skapar ett [SUSE Linux pacemaker-kluster](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) och använder SBD-enheter i stället för Azures staket-agenten. Eller för ytterligare program instanser.
- För att uppnå körnings tids konsekvens för kritiska affärs processer kan du försöka dirigera vissa batch-jobb och användare till program instanser som finns i zoner med den aktiva DBMS-instansen med hjälp av SAP batch Server-grupper, SAP-inloggnings grupper eller RFC-grupper. Men om du vill ha en zonindelade redundans måste du manuellt flytta grupperna till instanser som körs på virtuella datorer som finns i zoner med den aktiva DB-datorn.  
- Du kanske vill distribuera inaktiva dialog instanser i varje zon. 

> [!IMPORTANT]
> I det här aktiva/aktiva scenariot annonseras ytterligare avgifter för bandbredd av Microsoft från 04/01/2020 på. Se [pris informationen för dokument bandbredden](https://azure.microsoft.com/pricing/details/bandwidth/). Data överföringen mellan SAP-program skiktet och SAP-DBMS-skiktet är väldigt intensiv. Därför kan det aktiva/aktiva scenariot bidra till kostnader som är ganska lite. Fortsätt att kontrol lera den här artikeln för att få exakta kostnader 


## <a name="activepassive-deployment"></a>Aktiv/passiv distribution
Om du inte hittar en acceptabel förändring mellan nätverks fördröjningen i en zon och fördröjningen för nätverks trafik över flera zoner kan du distribuera en arkitektur som har ett aktivt/passivt tecken från SAP-programmets lager plats. Du definierar en *aktiv* zon, som är den zon där du distribuerar det fullständiga program lagret och där du försöker köra både det aktiva DBMS-och SAP Central Services-instansen. Med en sådan konfiguration måste du se till att du inte har extrema körnings tids variationer, beroende på om ett jobb körs i zon med den aktiva DBMS-instansen eller inte, i affärs transaktioner och batch-jobb.

Azure-regioner där den här typen av distributions arkitektur i olika zoner kan föredras:

- Sydostasien
- Australien, östra
- Brasilien, södra
- Tyskland, västra centrala
- Sydafrika, norra
- Frankrike, centrala 
- Kanada, centrala
- Japan, östra


Grundläggande layout för arkitekturen ser ut så här:

![Distribution av aktiva/passiva zoner](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

Följande överväganden gäller för den här konfigurationen:

- Det går inte att distribuera tillgänglighets uppsättningar i Azure-tillgänglighetszoner. För att kompensera för detta kan du använda Azure närhets placerings grupper som dokumenteras i artikel [Azure närhets placerings grupper för optimal nätverks fördröjning med SAP-program](sap-proximity-placement-scenarios.md).
- När du använder den här arkitekturen måste du övervaka statusen nära och försöka behålla instanserna aktiva DBMS och SAP Central Services i samma zon som det distribuerade program lagret. Om det är en redundansväxling av SAP central service eller DBMS-instansen, vill du se till att du manuellt växlar tillbaka till zonen med SAP-program lagret distribuerat så snabbt som möjligt.
- För belastningsutjämnare för redundanskluster i SAP Central Services och DBMS-skiktet måste du använda [standard-SKU-Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md). Basic-Load Balancer fungerar inte mellan zoner.
- Det virtuella Azure-nätverket som du har distribuerat som värd för SAP-systemet, tillsammans med dess undernät, sträcks ut över zoner. Du behöver inte separata virtuella nätverk för varje zon.
- För alla virtuella datorer som du distribuerar måste du använda [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Ohanterade diskar stöds inte för zonindelade-distributioner.
- Azure Premium Storage, [Ultra SSD Storage](../../disks-types.md#ultra-disk)eller ANF stöder inte någon typ av lagrings replikering mellan zoner. Programmet (DBMS eller SAP Central Services) måste replikera viktiga data.
- Detsamma gäller för den delade sapmnt-katalogen, som är en delad disk (Windows), en CIFS-resurs (Windows) eller en NFS-resurs (Linux). Du måste använda en teknik som replikerar de här delade diskarna eller resurserna mellan zonerna. Dessa tekniker stöds:
    - För Windows är en kluster lösning som använder SIOS DataKeeper, som dokumenterad i [kluster en SAP ASCS/SCS-instans på ett Windows-redundanskluster med hjälp av en klusterdelad disk i Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - För SUSE Linux är en NFS-resurs som skapats som dokumenterad i [hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
  För närvarande stöds inte lösningen som använder Microsoft Scale-Out-filserver, som dokumenteras i [förbereda Azure-infrastruktur för SAP med hög tillgänglighet med hjälp av ett Windows-redundanskluster och fil resurs för SAP ASCS/SCS-instanser](./sap-high-availability-infrastructure-wsfc-file-share.md), i zoner.
- Den tredje zonen används som värd för SBD-enheten om du skapar ett [SUSE Linux pacemaker-kluster](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) och använder SBD-enheter i stället för Azures staket-agenten. Eller för ytterligare program instanser.
- Du bör distribuera vilande virtuella datorer i den passiva zonen (från en DBMS-vy) så att du kan starta program resurser om det uppstår ett zon haveri.
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) kan för närvarande inte replikera aktiva virtuella datorer till inaktiva virtuella datorer mellan zoner. 
- Du bör investera i Automation som gör att du automatiskt kan starta lagret SAP-program i den andra zonen om ett zonindelade avbrott inträffar.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Konfiguration av kombinerad hög tillgänglighet och haveri beredskap
Microsoft delar inte information om geografiska avstånd mellan de anläggningar som är värdar för olika Azure-tillgänglighetszoner i en Azure-region. Vissa kunder använder sig av zoner för en kombinerad HA-och DR-konfiguration som lovar ett återställnings punkt mål (återställnings punkt mål) på noll. Med noll menas att du inte förlorar några allokerade databas transaktioner även om haveri beredskap är klar. 

> [!NOTE]
> Vi rekommenderar att du använder en konfiguration som bara i vissa fall. Du kan till exempel använda den när data inte kan lämna Azure-regionen av säkerhets-eller efterlevnadsprinciper. 

Här är ett exempel på hur en sådan konfiguration kan se ut:

![Kombinerad hög tillgänglighet för DR i zoner](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Följande överväganden gäller för den här konfigurationen:

- Du antar att det finns ett betydande avstånd mellan de anläggningar som är värdar för en tillgänglighets zon eller om du tvingas att stanna kvar inom en viss Azure-region. Det går inte att distribuera tillgänglighets uppsättningar i Azure-tillgänglighetszoner. För att kompensera för detta kan du använda Azure närhets placerings grupper som dokumenteras i artikel [Azure närhets placerings grupper för optimal nätverks fördröjning med SAP-program](sap-proximity-placement-scenarios.md).
- När du använder den här arkitekturen måste du övervaka statusen nära och försöka behålla instanserna aktiva DBMS och SAP Central Services i samma zon som det distribuerade program lagret. Om det är en redundansväxling av SAP central service eller DBMS-instansen, vill du se till att du manuellt växlar tillbaka till zonen med SAP-program lagret distribuerat så snabbt som möjligt.
- Du bör vara förinstallerade för instanser av produktions program i de virtuella datorer som kör de aktiva program instanserna för frågor och svar.
- I ett zonindelade-fall stänger du program instanserna för frågor och svar och startar produktions instanserna i stället. Du måste använda virtuella namn för program instanserna för att det ska fungera.
- För belastningsutjämnare för redundanskluster i SAP Central Services och DBMS-skiktet måste du använda [standard-SKU-Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md). Basic-Load Balancer fungerar inte mellan zoner.
- Det virtuella Azure-nätverket som du har distribuerat som värd för SAP-systemet, tillsammans med dess undernät, sträcks ut över zoner. Du behöver inte separata virtuella nätverk för varje zon.
- För alla virtuella datorer som du distribuerar måste du använda [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Ohanterade diskar stöds inte för zonindelade-distributioner.
- Azure Premium Storage och [Ultra SSD Storage](../../disks-types.md#ultra-disk) stöder inte någon typ av lagrings replikering mellan zoner. Programmet (DBMS eller SAP Central Services) måste replikera viktiga data.
- Detsamma gäller för den delade sapmnt-katalogen, som är en delad disk (Windows), en CIFS-resurs (Windows) eller en NFS-resurs (Linux). Du måste använda en teknik som replikerar de här delade diskarna eller resurserna mellan zonerna. Dessa tekniker stöds:
    - För Windows är en kluster lösning som använder SIOS DataKeeper, som dokumenterad i [kluster en SAP ASCS/SCS-instans på ett Windows-redundanskluster med hjälp av en klusterdelad disk i Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - För SUSE Linux är en NFS-resurs som skapats som dokumenterad i [hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).

  För närvarande stöds inte lösningen som använder Microsoft Scale-Out-filserver, som dokumenteras i [förbereda Azure-infrastruktur för SAP med hög tillgänglighet med hjälp av ett Windows-redundanskluster och fil resurs för SAP ASCS/SCS-instanser](./sap-high-availability-infrastructure-wsfc-file-share.md), i zoner.
- Den tredje zonen används som värd för SBD-enheten om du skapar ett [SUSE Linux pacemaker-kluster](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) och använder SBD-enheter i stället för Azures staket-agenten. 





## <a name="next-steps"></a>Nästa steg
Här följer några exempel på hur du distribuerar i Azure-tillgänglighetszoner:

- [Klustra en SAP ASCS/SCS-instans på ett Windows-redundanskluster med hjälp av en klusterdelad disk i Azure](./sap-high-availability-guide-wsfc-shared-disk.md)
- [Förbered Azure-infrastrukturen för SAP med hög tillgänglighet genom att använda ett Windows-redundanskluster och en fil resurs för SAP ASCS/SCS-instanser](./sap-high-availability-infrastructure-wsfc-file-share.md)