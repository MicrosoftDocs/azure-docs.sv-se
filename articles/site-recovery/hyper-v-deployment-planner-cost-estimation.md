---
title: Granska rapporten Distributionshanteraren för Azure Site Recovery kostnads uppskattning för haveri beredskap för virtuella Hyper-V-datorer till Azure | Microsoft Docs
description: Den här artikeln beskriver hur du granskar rapporten kostnads uppskattning som genererade Distributionshanteraren för Azure Site Recovery för haveri beredskap för Hyper-V till Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 29457f2f5021fed9d8785f5764c4119de4be1fa9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999220"
---
# <a name="cost-estimation-report-by-azure-site-recovery-deployment-planner"></a>Rapporten Kostnadsuppskattning från Distributionshanteraren för Azure Site Recovery 

Rapporten från Distributionshanteraren för Azure Site Recovery ger en översikt över kostnadsuppskattningen på bladet [Rekommendationer](hyper-v-deployment-planner-analyze-report.md#recommendations) och en detaljerad kostnadsanalys på bladet Kostnadsuppskattning. Där ges en detaljerad kostnadsanalys per virtuell dator. 

### <a name="cost-estimation-summary"></a>Sammanfattning av kostnadsuppskattning 
I diagrammet visas en sammanfattning av den uppskattade totala kostnaden för haveriberedskap (DR) till Azure för din valda målregion och i den valuta du har angett för rapporten.

![Sammanfattning av kostnadsuppskattning](media/hyper-v-azure-deployment-planner-cost-estimation/cost-estimation-summary-h2a.png)

Sammanfattningen hjälper dig att förstå den kostnad som du behöver betala för lagring, beräkning, nätverk och licenser när du skyddar dina kompatibla virtuella datorer med Azure Site Recovery. Kostnaden beräknas för kompatibla virtuella datorer och inte för alla profilerade virtuella datorer. 
 
Du kan visa kostnaden per månad eller per år. Läs mer om [målregioner som stöds](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) och [valutor som stöds](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies).

**Komponentkostnader**: Den totala DR-kostnaden delas upp i fyra komponenter: beräkning, lagring, nätverk och Site Recovery-licenskostnad. Kostnaden beräknas baserat på förbrukningen som uppstår vid replikering och DR-testtiden. Beräkning, lagring (premium och standard), ExpressRoute/VPN som konfigurerats mellan lokal plats och Azure samt Site Recovery-licenser används för beräkningar.

**Cost by states** (Kostnad per tillstånd) Kategorin total kostnad för haveriberedskap (DR) baseras på två olika tillstånd – replikering och DR-test. 

**Replication cost** (Replikeringskostnad): Kostnaden som tillkommer under replikering. Det här täcker kostnaden för lagring, nätverk och Site Recovery-licensen. 

**DR-Drill cost** (DR-testkostnad): Kostnaden som tillkommer under redundansväxlingstest. Site Recovery startar virtuella datorer under redundansväxlingstest. DR-testkostnaden täcker beräkning och lagring för de virtuella datorer som körs. 

**Azure Storage Cost per Month/Year** (Azure Storage-kostnad per månad/år): Den totala lagringskostnad som tillkommer för premium- och standardlagring för replikering och DR-test.

## <a name="detailed-cost-analysis"></a>Detaljerad kostnadsanalys
Azure-priser för beräkning, lagring och nätverk varierar mellan olika Azure-regioner. Du kan generera rapporten Kostnadsuppskattning med de senaste priserna baserat på din prenumeration, erbjudandet som är associerat med din prenumeration och den angivna Azure-målregionen i en angiven valuta. Som standard används Azure-regionen USA, västra 2 och valutan amerikanska dollar (USD) i verktyget. Om du använder någon annan region eller valuta används den senast använda regionen och valutan till kostnadsuppskattningen nästa gång du skapar en rapport utan prenumerations-ID, erbjudande-ID, målregionen eller valuta.

I det här avsnittet visas det prenumerations-ID och erbjudande-ID du använde till att generera rapporten. Om de inte användes är det tomt.

De grå cellerna i hela rapporten är skrivskyddade. Celler i vitt kan ändras efter dina behov.

![Detaljerad kostnadsuppskattning](media/hyper-v-azure-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-costs-by-components"></a>Övergripande kostnader för haveriberedskap per komponenter
I det första avsnittet visas den sammanlagda kostnaden för haveriberedskap per komponent och kostnaden för haveriberedskap per tillstånd. 

**Beräkna**: Kostnaden för virtuella IaaS-datorer som körs på Azure för haveriberedskap. Den inkluderar virtuella datorer som skapas av Site Recovery under DR-test (test av redundans). Den inkluderar även virtuella datorer som körs på Azure, som SQL Server med Always On-tillgänglighetsgrupper och domänkontrollanter eller DNS-servrar.

**Lagring**: Kostnaden för Azure-lagring i samband med haveriberedskap. Här ingår användningen av lagringsutrymme för replikering under DR-tester.

**Nätverk**: Kostnaden för ExpressRoute och VPN plats-till-plats i samband med haveriberedskap. 

**Azure Site Recovery licens**: Site Recovery licens kostnad för alla kompatibla virtuella datorer. Om du angett en virtuell dator manuellt i tabellen med den detaljerade kostnadsanalysen ingår även licenskostnaden för Site Recovery för den virtuella datorn.

### <a name="overall-dr-costs-by-states"></a>Övergripande kostnader för haveriberedskap per tillstånd
Den totala kostnaden för haveriberedskap kategoriseras utifrån två olika tillstånd – replikering och DR-test.

**Replikering**: Kostnader som uppstår under replikering. Det här täcker kostnaden för lagring, nätverk och Site Recovery-licensen. 

**DR-test**: Kostnader som uppstår under DR-test. Site Recovery startar virtuella datorer under DR-test. Kostnaden för DR-test täcker databearbetning och lagring för de virtuella datorer som körs.

* Total varaktighet för DR-test under ett år = antalet DR-test x varaktigheten för respektive DR-test (dagar)
* Genomsnittlig kostnad för DR-test (per månad) = total kostnad för DR-test / 12

### <a name="storage-cost-table"></a>Tabellen Lagringskostnad
I den här tabellen visas kostnaderna för premium- och standardlagring för replikering och DR-test med och utan rabatt.

### <a name="site-to-azure-network"></a>Lokalt till Azure-nätverket
Välj lämplig inställning efter behov. 

**ExpressRoute**: som standard väljer verktyget den närmaste ExpressRoute-plan som matchar nätverksbandbredden som krävs för deltareplikering. Du kan ändra planen efter behov.

**VPN Gateway type** (VPN-gatewaytyp): Välj Azure VPN-gatewayen om du har en sådan i din miljö. Som standard är värdet Saknas.

**Mål region**: angiven Azure-region för Dr. Priset som används i rapporten för bearbetning, lagring, nätverk och licenser baseras på Azure-priserna i den angivna regionen. 

### <a name="vm-running-on-azure"></a>Virtuella datorer som körs i Azure
Du kanske har en domänkontrollant eller en virtuell dator med en DNS-server eller en virtuell dator med SQL Server och Always On-tillgänglighetsgrupper som körs på Azure för haveriberedskap. Du kan ange antalet virtuella datorer och deras storlek för att ta med deras beräkningskostnad i den totala kostnaden för haveriberedskap. 

### <a name="apply-overall-discount-if-applicable"></a>Tillämpa totalrabatt i förekommande fall
Om du är en Azure-partner eller en kund med rätt till rabatt på totalpriset för Azure kan du använda det här fältet. Verktyget tillämpar rabatten (i procent) på samtliga komponenter.

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>Antal virtuella datorer av olika typer och beräkningskostnad (per år)
I den här tabellen visas antalet virtuella Windows-datorer och andra datorer samt beräkningskostnaden för dem i samband med DR-test.

### <a name="settings"></a>Inställningar 
**Using Managed disk** (Hanterade diskar används): inställningen anger om en hanterad disk används under DR-test. Standardvärdet är **Ja**. Om du ställer in **-UseManagedDisks** till **Nej** används priset för ohanterade diskar i beräkningen.

**Valuta**: valutan som rapporten ska genereras i.

**Kostnads varaktighet**: du kan visa alla kostnader antingen för månaden eller för hela året. 

## <a name="detailed-cost-analysis-table"></a>Tabell med detaljerad kostnadsanalys
![Detaljerad kostnadsanalys](media/hyper-v-azure-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png)

Den här tabellen innehåller kostnadsdata för varje kompatibel virtuell dator. Du kan också använda den här tabellen till att hämta uppskattad Azure-kostnad för haveriberedskap för icke-profilerade virtuella datorer genom att lägga till virtuella datorer manuellt. Denna information är användbar om du behöver uppskatta Azure-kostnader för en ny DR-distribution utan detaljerad profilering.

Så här lägger du till virtuella datorer manuellt:

1. Välj **Infoga rad** och infoga en ny rad mellan **start-** och **slutraderna**.

1. Fyll i följande kolumner baserat på ungefärlig storlek och antalet virtuella datorer som matchar den här konfigurationen: 

    a. **Antal virtuella datorer**

    b. **IaaS-storlek (dina val)**

    c. **Lagrings typ standard/Premium**

    d. **Totalt lagringsutrymme för virtuella datorer (GB)**

    e. **Antal DR-tester under ett år**

    f. **Varaktighet för varje DR-test (dagar)**

    ex. **Typ av operativsystem**

    h. **Dataredundans**

    i. **Azure Hybrid-förmånen**

1. Du kan använda samma värde för alla virtuella datorer i tabellen genom att välja **Använd för alla**, för kolumnerna **Antal DR-tester under ett år**, **Varaktighet för varje DR-test (dagar)**, **Dataredundans** och **Azure Hybrid-förmån**.

1. Välj **omberäkna kostnad** för att uppdatera kostnaden.

**Namn på virtuell dator**: namnet på den virtuella datorn.

**Antal virtuella datorer**: antalet virtuella datorer som matchar konfigurationen. Du kan uppdatera antalet befintliga virtuella datorer om virtuella datorer med liknande konfiguration inte är profilerade men är skyddade.

**IaaS-storlek (rekommendation)**: Storleken på de kompatibla virtuella datorer som verktyget rekommenderar för rollen. 

**IaaS-storlek (ditt val)**: Som standard är det här samma som den rekommenderade storleken. Du kan ändra rollen efter behov. Den beräknade kostnaden baseras på vald storlek för rollen.

**Lagringstyp**: Typen av lagring som används för den virtuella datorn. Det här är antingen Standard eller Premium.

**Totalt lagringsutrymme (GB) för virtuella datorer**: det totala lagringsutrymmet för den virtuella datorn.

**Antal DR-tester under ett år**: Antalet gånger som du utför DR-tester under ett år. Standardvärdet är fyra gånger per år. Antalet kan ändras för specifika virtuella datorer eller för alla virtuella datorer. Ange det nya värdet i den övre raden och välj **Använd för alla**. Den totala kostnaden för DR-tester är baserad på antalet DR-tester under ett år och varaktigheten för varje DR-test. 

**Varaktighet för varje DR-test (dagar)**: Varaktigheten för varje DR-test. Standardvärdet är sju dagar var 90:e dag enligt förmånen [Disaster Recovery Software Assurance](https://azure.microsoft.com/pricing/details/site-recovery). Perioden kan ändras för specifika virtuella datorer eller för alla virtuella datorer. Ange ett nytt värde i den övre raden och välj **Använd för alla**. Totalkostnad för DR-tester beräknas baserat på antalet DR-tester under ett år och varaktigheten för varje DR-testperiod.
 
**OS-typ**: Den virtuella datorns operativsystem. Det är antingen Windows eller Linux. Om OS-typen är Windows kan du använda Azure Hybrid-förmånen för den virtuella datorn. 

**Dataredundans**: Det kan vara lokalt redundant lagring, geo-redundant lagring eller read-access geo-redundant storage (geo-redundant lagring med läsbehörighet). Standardvärdet är lokalt redundant lagring. Du kan ändra typen baserat på lagringskontot för de olika virtuella datorerna eller så kan du använda en ny typ för alla virtuella datorer. Ändra typen i den övre raden och välj **Använd för alla**. Lagringskostnaden för replikering beräknas baserat på priset för den dataredundans du har valt. 

**Azure Hybrid-förmån**: Du kan använda Azure Hybrid-förmånen för virtuella Windows-datorer i förekommande fall. Standardvärdet är **Ja**. Du kan ändra inställningen för specifika virtuella datorer eller uppdatera alla virtuella datorer. Välj **Använd för alla**.

**Total Azure-förbrukning**: Kostnaden för beräkning, lagring och kostnaden för Site Recovery-licenser för din haveriberedskap. Baserat på ditt val visas kostnaden antingen per månad eller år.

**Replikeringskostnad för stabilt tillstånd**: Lagringskostnaden för replikering.

**Totalkostnad för DR-test (genomsnittlig)**: Kostnaden för beräkning och lagring för DR-tester.

**Azure Site Recovery licens kostnad**: Site Recovery licens kostnaden.

## <a name="supported-target-regions"></a>Målregioner som stöds
Distributionshanteraren för Site Recovery kan ge kostnadsuppskattningar för följande Azure-regioner. Om din region inte visas kan du använda någon av följande regioner vars priser ligger närmast din region:

usaöstra, usaöstra2, usavästra, usacentrala, usanorracentrala, usasödracentrala, europanorra, europavästra, asienöstra, sydostasien, japanöstra, japanvästra, australienöstra, australiensydöstra, brasiliensödra, indiensödra, indiencentrala, indienvästra, kanadacentrala, kanadaöstra, usavästra2, usavästracentrala, södrastorbritannien, västrastorbritannien, koreacentrala, koreasödra 

## <a name="supported-currencies"></a>Valutor som stöds
Distributionshanteraren för Site Recovery kan generera kostnadsrapporten i någon av följande valutor.

|Valuta|Name|Valuta|Name|Valuta|Name|
|---|---|---|---|---|---|---|---|
|ARS|Argentinska pesos ($)|AUD|Australiensiska dollar ($)|BRL|Brasilianska real (R$)|
|CAD|Kanadensiska dollar ($)|CHF|Schweiziska franc (chf)|DKK|Danska kronor (kr)|
|EUR|Euro (€)|GBP|Brittiska pund (£)|HKD|Hongkongdollar (HK$)|
|IDR|Indonesiska rupier (Rp)|INR|Indiska rupier (₹)|JPY|Japanska yen (¥)|
|KRW|Koreanska won (₩)|MXN|Mexikanska Peso (MX$)|MYR|Malaysiska Ringgit (RM$)|
|NOK|Norska kronor (kr)||NZD|Nya zeeländska dollar ($)||RUB|Rysk rubel (руб)|
|SAR|Saudiarabiska riyal (SR)|SEK|Svenska kronor (kr)|TWD|Taiwanesiska dollar (NT$)|
|TRY|Turkiska lira (TL)|USD| Amerikanska dollar ($)|ZAR|Sydafrikanska rand (R)|

## <a name="next-steps"></a>Nästa steg
Lär dig mer om hur du skyddar [virtuella Hyper-V-datorer till Azure med Site Recovery](hyper-v-azure-tutorial.md).
