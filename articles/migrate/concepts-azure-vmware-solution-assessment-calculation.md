---
title: Beräkningar av AVS-bedömning i Azure Migrate | Microsoft Docs
description: Innehåller en översikt över beräkningar av AVS-bedömning i Azure Migrates tjänsten.
author: rashi-ms
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/25/2020
ms.author: mahain
ms.openlocfilehash: 3469b612081f9cb96beec98a065e0827d1c04b4c
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88261845"
---
# <a name="server-assessment-overview-migrate-to-azure-vmware-solution"></a>Översikt över Server utvärdering (migrera till Azure VMware-lösning)

[Azure Migrate](migrate-services-overview.md) tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av dina lokala appar och arbets belastningar. Den spårar också dina privata och offentliga moln instanser till Azure. Hubben erbjuder Azure Migrate verktyg för utvärdering och migrering, samt oberoende program varu leverantörer från tredje part (ISV).

Server utvärdering är ett verktyg i Azure Migrate som bedömer lokala servrar för migrering till virtuella Azure IaaS-datorer och Azure VMware-lösning (AVS). Den här artikeln innehåller information om hur Azure-utvärderingar av VMware-lösning (AVS) beräknas.

> [!NOTE]
> Azure VMware Solution (AVS)-utvärdering är för närvarande en för hands version och kan bara skapas för virtuella VMware-datorer.

## <a name="types-of-assessments"></a>Typer av utvärderingar

Utvärderingar som du skapar med Server utvärdering är en tidpunkts ögonblicks bild av data. Det finns två typer av utvärderingar som du kan skapa med hjälp av Azure Migrate: Server utvärdering.

**Bedömnings typ** | **Information**
--- | --- 
**Azure VM** | Utvärderingar för att migrera dina lokala servrar till Azure Virtual Machines. <br/><br/> Du kan utvärdera dina lokala [virtuella VMware-datorer](how-to-set-up-appliance-vmware.md), [virtuella Hyper-V-datorer](how-to-set-up-appliance-hyper-v.md)och [fysiska servrar](how-to-set-up-appliance-physical.md) för migrering till Azure med hjälp av den här utvärderings typen. [Läs mer](concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Utvärderingar för att migrera dina lokala servrar till [Azure VMware-lösningen (AVS)](../azure-vmware/introduction.md). <br/><br/> Du kan utvärdera dina lokala [virtuella VMware-datorer](how-to-set-up-appliance-vmware.md) för migrering till Azure VMware-lösningen (AVS) med den här utvärderings typen. [Läs mer](concepts-azure-vmware-solution-assessment-calculation.md)

Azure VMware Solution (AVS)-utvärdering i Server utvärdering ger två storlekar för storleks villkor:

**Utvärdering** | **Information** | **Data**
--- | --- | ---
**Prestanda-baserade** | Utvärderingar baserade på insamlade prestanda data för lokala virtuella datorer. | **Rekommenderad Node-storlek**: baserat på CPU-och minnes användnings data tillsammans med nodtypen, lagrings typ och FTT-inställning som du väljer för utvärderingen.
**Som lokalt** | Utvärderingar baserade på lokal storlek. | **Rekommenderad Node-storlek**: baserat på den lokala virtuella dator storleken tillsammans med den nodtyp, lagrings typ och FTT-inställning som du väljer för utvärderingen.

## <a name="how-do-i-run-an-assessment"></a>Hur gör jag för att köra en utvärdering?

Det finns ett par olika sätt att köra en utvärdering.

- Utvärdera datorer med hjälp av Server-metadata som samlats in av en förenklad Azure Migrate-apparat. Enheten identifierar lokala datorer. Den skickar sedan datorns metadata och prestanda data till Azure Migrate.
- Utvärdera datorer med hjälp av serverns metadata som importeras i CSV-format (kommaavgränsade värden).

## <a name="how-do-i-assess-with-the-appliance"></a>Hur gör jag för att du utvärdera med produkten?

Gör så här om du distribuerar en Azure Migrate-enhet för att identifiera lokala servrar:

1. Konfigurera Azure och din lokala miljö för att arbeta med Server utvärderingen.
2. Skapa ett Azure-projekt och Lägg till Server Assessment-verktyget i den första utvärderingen.
3. Distribuera en förenklad Azure Migrate-apparat. Enheten identifierar kontinuerligt lokala datorer och skickar metadata och prestanda data till Azure Migrate. Distribuera apparaten som en virtuell dator. Du behöver inte installera något på datorer som du vill utvärdera.

När installationen har påbörjat dator identifiering kan du samla in datorer som du vill utvärdera i en grupp och köra en utvärdering av gruppen med utvärderings typ **Azure VMware-lösning (AVS)**.

Skapa din första Azure VMware Solution (AVS)-utvärdering genom att följa stegen [här](how-to-create-azure-vmware-solution-assessment.md).

## <a name="how-do-i-assess-with-imported-data"></a>Hur gör jag för att utvärdering med importerade data?

Om du utvärderar servrar med hjälp av en CSV-fil behöver du inte en installation. Gör i stället följande steg:

1. Konfigurera Azure för att arbeta med Server utvärdering.
2. Skapa ett Azure-projekt och Lägg till Server Assessment-verktyget i den första utvärderingen.
3. Ladda ned en CSV-mall och Lägg till Server data till den.
4. Importera mallen till Server utvärderingen.
5. Identifiera servrar som har lagts till med importen, samla in dem i en grupp och kör en utvärdering för gruppen med utvärderings typ **Azure VMware-lösning (AVS)**.

## <a name="what-data-does-the-appliance-collect"></a>Vilka data samlas in av enheten?

Om du använder Azure Migrate-enheten för utvärdering kan du läsa om de metadata och prestanda data som samlas in för [VMware](migrate-appliance.md#collected-data---vmware).

## <a name="how-does-the-appliance-calculate-performance-data"></a>Hur beräknar enheten prestanda data?

Om du använder installations programmet för identifiering samlar den in prestanda data för beräknings inställningar med följande steg:

1. Enheten samlar in en exempel punkt i real tid.

    - **Virtuella VMware-datorer**: en exempel punkt samlas in var 20: e sekund.

2. Enheten kombinerar exempel punkterna för att skapa en enda data punkt var tionde minut. För att skapa data punkten väljer enheten de högsta värdena från alla exempel. Sedan skickas data punkten till Azure.
3. Server utvärderingen lagrar alla data punkter på 10 minuter för den senaste månaden.
4. När du skapar en utvärdering identifierar Server utvärderingen lämplig data punkt som ska användas för att göra rättigheter. Identifieringen baseras på percentilvärdet för *prestanda historik* och *percentils användning*.

    - Om prestanda historiken till exempel är en vecka och percentilvärdet är den 95: e percentilen, sorterar Server utvärderingen 10 minuters exempel punkter under den senaste veckan. De sorterar dem i stigande ordning och plockar 95 percentilvärdet för att få behörighet.
    - Värdet 95 percentil ser till att du ignorerar eventuella avvikande värden, som kan inkluderas om du har valt 99 percentilen.
    - Om du vill välja högsta användnings nivå för perioden och inte vill missa avvikande värden väljer du den 99 percentilen för percentils användning.

5. Det här värdet multipliceras med den praktiska faktorn för att få den effektiva prestanda användnings informationen för de mått som installeras av enheten:

    - CPU-användning
    - RAM-användning
    - Disk-IOPS (läsning och skrivning)
    - Disk data flöde (läsning och skrivning)
    - Nätverks data flöde (in och ut)

Följande prestanda data samlas in men används inte vid storleks rekommendationer för AVS-utvärderingar:
  - Disk-IOPS och data flödes data för varje disk som är ansluten till den virtuella datorn.
  - Nätverks-I/O för att hantera prestandabaserade storleks ändringar för varje nätverkskort som är kopplat till en virtuell dator.

## <a name="how-are-avs-assessments-calculated"></a>Hur beräknas AVS-bedömningar?

Server utvärderingen använder de lokala datorernas metadata och prestanda data för att beräkna utvärderingar. Om du distribuerar Azure Migrate-enheten använder utvärderingen de data som samlas in av enheten. Men om du kör en utvärdering som importer ATS med en CSV-fil anger du metadata för beräkningen.

Beräkningar sker i dessa tre steg:

1. **Beräkna beredskap för Azure VMware-lösningen (AVS)**: om de lokala virtuella datorerna är lämpliga för migrering till Azure VMware-lösningen (AVS).
2. **Beräkna antal AVS-noder och användning över noder**: uppskattat antal AVS-noder som krävs för att köra de virtuella datorerna och planerad processor, minne och lagrings belastning på alla noder.
3. **Månads kostnads uppskattning**: den uppskattade månads kostnaden för alla Azure VMware-lösningar (AVS)-noder som kör lokala virtuella datorer.

Beräkningarna sker i föregående ordning. En dator server flyttas till ett senare steg endast om den tidigare har passerat. Om en server till exempel inte klarar AVS-beredskaps fasen, är den markerad som olämplig för Azure. Storleks-och kostnads beräkningar utförs inte för den servern

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Vad finns i en Azure VMware-lösning (AVS)-utvärdering?

Här är what's som ingår i en AVS-utvärdering i Server utvärderingen:


| **Egenskap** | **Information** 
| - | - 
| **Målplats** | Anger den plats för molnets privata moln som du vill migrera till.<br/><br/> AVS-utvärderingen i Server utvärderingen stöder för närvarande dessa mål regioner: östra USA, västra Europa, västra USA. 
| **Lagringstyp** | Anger den lagrings motor som ska användas i AVS.<br/><br/> AVS-utvärderingar stöder endast virtuellt San som standard lagrings typ. 
**Reserverade instanser (RIs)** | Med den här egenskapen kan du ange reserverade instanser i AVS. Fjärrinstallationstjänster stöds för närvarande inte för AVS-noder. 
**Nodtyp** | Anger den [typ av AVS-nodtyp](../azure-vmware/concepts-private-clouds-clusters.md) som används för att mappa lokala virtuella datorer. Standard-nodtypen är AV36. <br/><br/> Azure Migrate rekommenderar att antalet noder som krävs för de virtuella datorerna migreras till AVS. 
**FTT-inställning, RAID-nivå** | Anger tillämpligt problem för att tolerera och RAID-kombinationer. Det valda FTT-alternativet kombinerat med kravet på lokal virtuell dator disk avgör den totala virtuellt SAN lagring som krävs i AVS. 
**Ändra storlek på kriterium** | Anger de kriterier som ska användas för att ändra storlek på virtuella datorer i AVS *-format* . Du kan välja *prestandabaserade* storleks ändringar eller *lokalt* utan att behöva beakta prestanda historiken. 
**Prestandahistorik** | Ställer in varaktigheten för att utvärdera prestanda data för datorer. Den här egenskapen gäller bara när storleks kriteriet är *prestanda-baserat*. 
**Percentilutnyttjande** | Anger percentilvärdet för den prestanda exempel uppsättning som ska beaktas för höger storlek. Den här egenskapen gäller bara när storleken är prestanda-baserad.
**Komfortfaktor** | Azure Migrate Server-utvärderingen anses vara en buffert (bekvämlighets faktor) under utvärderingen. Bufferten tillämpas utöver datorns användningsdata för virtuella datorer (CPU, minne, disk och nätverk). Komfortfaktorn väger in problem som säsongsbaserad användning, kort prestandahistorik och troliga ökningar i kommande användning.<br/><br/> Till exempel resulterar en virtuell dator med 10 kärnor med 20 % användning vanligen i en virtuell dator med 2 kärnor. Med en komfortfaktor på 2.0x blir resultatet istället en virtuell dator med 4 kärnor. 
**Erbjudande** | Visar [Azure-erbjudandet](https://azure.microsoft.com/support/legal/offer-details/) som du har registrerat i. Azure Migrate beräknar kostnaden enligt detta.
**Valuta** | Visar fakturerings valutan för ditt konto. 
**Rabatt (%)** | Visar en lista över den prenumerations information som du får ovanpå Azure-erbjudandet. Standardinställningen är 0%. 
**Azure Hybrid-förmån** | Anger om du har Software Assurance och är berättigade till [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Även om den inte har någon inverkan på priserna för Azure VMware-lösningar på grund av det nodbaserade priset kan kunderna fortfarande använda lokala OS-licenser (Microsoft-baserade) i AVS med Azure Hybrid-förmåner. Andra program varu leverantörer måste ange sina egna licens villkor, till exempel RHEL. 
**vCPU överprenumeration** | Anger förhållandet mellan antalet virtuella kärnor som är knutna till en fysisk kärna i AVS-noden. Standardvärdet i beräkningarna är 4 vCPU: 1 fysisk kärna i AVS. <br/><br/> API-användare kan ange det här värdet som ett heltal. Observera att vCPU överprenumeration > 4:1 kan påverka arbets belastningar beroende på CPU-användning. 


## <a name="azure-vmware-solution-avs-suitability-analysis"></a>Analys av lämplighet för Azure VMware Solution (AVS)

AVS-utvärderingen i Server utvärderingen bedömer varje lokal virtuell dator för att vara lämplig för AVS genom att granska datorns egenskaper. Den tilldelar också varje utvärderad dator till någon av följande kategorier för lämplighet:

- **Redo för AVS**: datorn kan migreras som i Azure (AVS) utan några ändringar. Den kommer att starta i AVS med fullständig AVS-support.
- **Klar med villkor**: den virtuella datorn kan ha kompatibilitetsproblem med den aktuella vSphere-versionen samt kräva att VMware-verktyg och andra inställningar krävs innan fullständig funktionalitet från den virtuella datorn kan uppnås i AVS.
- **Inte redo för AVS**: den virtuella datorn kommer inte att starta i AVS. Om till exempel den lokala virtuella VMware-datorn har en extern enhet ansluten, till exempel en CD-Rom, kommer VMware VMotion åtgärden att Miss Missing (om du använder VMware VMotion).
- **Beredskap okänd**: Azure Migrate kunde inte fastställa datorns beredskap på grund av otillräckliga metadata som samlats in från den lokala miljön.

Server utvärderingen granskar datorns egenskaper för att fastställa Azure-beredskap för den lokala datorn.

### <a name="machine-properties"></a>Dator egenskaper

Server utvärderingen granskar följande egenskap för den lokala virtuella datorn för att avgöra om den kan köras på Azure VMware-lösning (AVS).


| **Egenskap** | **Information** | **Status för AVS-beredskap** 
| - | - | - 
| **Internet Protocol** | AVS stöder för närvarande inte IPv6 för Internetadressering.<br/><br/> Kontakta ditt lokala MSFT-GBB-team för vägledning om din dator identifieras med IPv6.| Villkorligt klar Internet Protocol


### <a name="guest-operating-system"></a>Gästoperativsystem

För närvarande granskar inte AVS-bedömningar operativ systemet som en del av lämplig analys. Alla operativ system som körs på lokala virtuella datorer körs förmodligen på Azure VMware-lösning (AVS).

Tillsammans med VM-egenskaper tittar Server utvärderingen på datorns gäst operativ system för att avgöra om den kan köras på Azure.


## <a name="sizing"></a>Storlekar

När en dator har marker ATS som klar för AVS, gör AVS-utvärderingen i Server utvärderingen rekommendationer för Node-storlek, vilket innefattar att identifiera lämpliga lokala VM-krav och hitta det totala antalet AVS-noder som krävs. De här rekommendationerna varierar beroende på vilka bedömnings egenskaper som anges.

- Om utvärderingen använder *prestandabaserade storleks*ändringar, Azure Migrate beakta datorns prestanda historik för att göra lämplig storleks rekommendation för AVS. Den här metoden är särskilt användbar om du har överallokerat den lokala virtuella datorn, men användningen är låg och du vill ha rätt storlek på den virtuella datorn i AVS för att spara kostnader. Den här metoden hjälper dig att optimera storlekarna under migreringen.
- Om du inte vill tänka på prestanda data för storleks ändring av virtuella datorer och vill ta med de lokala datorerna som de är i AVS-läge kan du ange storleks villkor som *lokalt*. Sedan kommer Server utvärderingen att ändra de virtuella datorerna baserat på den lokala konfigurationen utan att ta hänsyn till användnings data. 


### <a name="ftt-sizing-parameters"></a>Parametrar för FTT storlek

Den lagrings motor som används i AVS är virtuellt San. Virtuellt San lagrings principer definierar lagrings krav för dina virtuella datorer. Med dessa principer garanteras den tjänstnivå som krävs för dina virtuella datorer eftersom de fastställer hur lagringen allokeras till den virtuella datorn. De tillgängliga FTT-RAID-kombinationerna är: 

**Fel som ska kunna hanteras (FTT)** | **RAID-konfiguration** | **Lägsta antal värdar som krävs** | **Storleksövervägande**
--- | --- | --- | --- 
1 | RAID-1 (spegling) | 3 | En virtuell dator på 100 GB skulle förbruka 200 GB.
1 | RAID-5 (raderingsskyddad kod) | 4 | En virtuell dator på 100 GB skulle förbruka 133,33 GB
2 | RAID-1 (spegling) | 5 | En virtuell dator på 100 GB skulle förbruka 300 GB.
2 | RAID-6 (raderingsskyddad kod) | 6 | En virtuell dator på 100 GB skulle förbruka 150 GB.
3 | RAID-1 (spegling) | 7 | En virtuell dator på 100 GB skulle förbruka 400 GB.

### <a name="performance-based-sizing"></a>Prestanda-baserad storlek

För prestandabaserade storleks ändringar börjar Server utvärderingen med de diskar som är anslutna till den virtuella datorn följt av nätverkskort. Den mappar sedan de virtuella dator kraven till ett lämpligt antal noder för AVS. Azure Migrates enheten profiler den lokala miljön för att samla in prestanda data för CPU, minne, diskar och nätverkskort.

**Steg för insamling av prestanda data:**

1. För virtuella VMware-datorer samlar Azure Migrate-installationen in en exempel punkt i real tid med varje 20-sekunders intervall. 
2. Enheten samlar in de exempel punkter som samlats in var tionde minut och skickar det högsta värdet för de senaste 10 minuterna till Server utvärderingen.
3. Server utvärderingen lagrar alla exempel punkter på 10 minuter under den senaste månaden. Beroende på vilka bedömnings egenskaper som anges för *prestanda historik* och *percentiler*, identifierar den lämplig data punkt som ska användas för höger storlek. Om prestanda historiken till exempel är inställd på 1 dag och percentilvärdet är den 95: e percentilen, använder Server utvärderingen 10 minuters exempel punkter under den senaste dagen, sorterar dem i stigande ordning och väljer värdet för 95 percentilvärdet för höger storlek.
4. Det här värdet multipliceras med den praktiska faktorn för att få ut effektiva prestanda användnings data för varje mått (processor belastning, minnes användning, disk-IOPS (läsning och skrivning), disk data flöde (läsning och skrivning) och nätverks data flöde (in och ut) som installationen samlar in.

När det effektiva användning svärdet har fastställts hanteras lagring, nätverk och beräknings storlek enligt följande.

**Lagrings storlek**: Azure Migrate använder det totala disk utrymmet för den lokala virtuella datorn som en beräknings parameter för att fastställa lagrings krav för AVS-virtuellt San utöver den KUNDvalda FTT-inställningen. FTT – fel som kan tolereras och kräver minst antal noder per FTT avgör den totala virtuellt San-lagringen som krävs kombinerat med kravet på VM-diskar.

**Nätverksstorlek**: I serverutvärderingen tas för närvarande inga nätverksinställningar i beaktande för AVS-utvärderingar.

**Beräknings storlek**: när den beräknar lagrings kraven tar Server utvärderingen hänsyn till processor-och minnes krav för att fastställa antalet noder som krävs för AVS-baserat på nodtypen.

- Utifrån storleks kriterierna tittar Server utvärderingen antingen på de prestandabaserade VM-data eller den lokala VM-konfigurationen. Inställningen bekvämlighets faktor gör det möjligt att ange en tillväxt faktor för klustret. För närvarande är hypertrådning aktiverat som standard och noder med 36 kärnor har därmed 72 virtuella kärnor. 4 virtuella kärnor per fysisk kärna används för att fastställa CPU-tröskelvärden per kluster med VMware-standarden med högst 80 procents användning så att underhåll eller fel kan hanteras utan att klustrets tillgänglighet äventyras. Det finns för närvarande ingen åsidosättning tillgänglig för att ändra överprenumerations värden och vi kan ha detta i framtida versioner.

### <a name="as-on-premises-sizing"></a>Som lokal storlek

Om du använder *som lokal storlek*, anser Server utvärderingen inte prestanda historiken för de virtuella datorerna och diskarna. I stället allokeras AVS-noder baserat på den storlek som tilldelas lokalt. Standard lagrings typen är virtuellt San i AVS.

## <a name="confidence-ratings"></a>Tillförlitlighets klassificering

Varje prestandabaserade utvärdering i Azure Migrate associeras med en förtroende grad som sträcker sig från ett (lägst) till fem stjärnor (högst).

- Säkerhetsomdömet tilldelas en utvärdering baserat på tillgängligheten av datapunkter som behövs för att beräkna utvärderingen.
- Med säkerhetsomdömet kan du beräkna tillförlitligheten i de storleksrekommendationer som anges av Azure Migrate.
- Tillförlitlighets klassificeringar är inte tillämpliga för *den lokala* utvärderingen.
- För prestandabaserade storleks ändringar behöver AVS-utvärderingar i Server utvärderingen användnings data för CPU och VM-minne. Följande data samlas in men används inte vid storleks rekommendationer för AVS:
  - Disk-IOPS och data flödes data för varje disk som är ansluten till den virtuella datorn.
  - Nätverks-I/O för att hantera prestandabaserade storleks ändringar för varje nätverkskort som är kopplat till en virtuell dator.

  Om något av dessa användnings nummer är otillgängligt i vCenter Server kanske storleks rekommendationen inte är tillförlitlig.

Beroende på hur stor procent andel data punkter som är tillgängliga, så går förtroendet för utvärderingen enligt följande.


| **Tillgänglighet för datapunkter** | **Säkerhetsomdöme** |
| - | - |
| 0-20% | 1 stjärna |
| 21-40% | 2 stjärnor |
| 41-60% | 3 stjärnor |
| 61-80% | 4 stjärnor |
| 81-100% | 5 stjärnor |

### <a name="low-confidence-ratings"></a>Värderingar med låg exakthet

Här följer några skäl till varför en utvärdering kan få en låg exakthet:

- Du har inte profilerat din miljö under den tid som du skapar utvärderingen. Om du till exempel skapar utvärderingen med varaktigheten inställd på en dag måste du vänta i minst en dag efter att du har startat identifieringen för alla data punkter som ska samlas in.
- Vissa virtuella datorer stängdes ned under den period då utvärderingen beräknades. Om några virtuella datorer är inaktiverade under en viss tid kan Server utvärderingen inte samla in prestanda data för den perioden.
- Vissa virtuella datorer skapades under den period då utvärderingen beräknades. Om du till exempel har skapat en utvärdering för prestanda historiken för den senaste månaden, men vissa virtuella datorer endast har skapats i miljön för en vecka sedan, så finns det inga prestanda historik för de nya virtuella datorerna för den fullständiga varaktigheten.

> [!NOTE]
> Om en utvärderings grad är mindre än fem stjärnor rekommenderar vi att du väntar minst en dag för att enheten ska profilera miljön och sedan beräkna utvärderingen på annat sätt. Om du inte gör det kanske prestandabaserade storleks ändringar inte är pålitliga. I så fall rekommenderar vi att du växlar utvärderingen till lokal storlek.

## <a name="monthly-cost-estimation"></a>Månatlig kostnads uppskattning

När storleks rekommendationerna har slutförts beräknar Azure Migrate den totala kostnaden för att köra de lokala arbets belastningarna i AVS genom att multiplicera antalet AVS-noder som krävs av Node-priset. Kostnaden per VM-kostnad beräknas genom att den totala kostnaden divideras med antalet virtuella datorer i utvärderingen. 
- Beräkningen tar antalet noder som krävs, nodtypen och platsen i kontot.
- Den sammanställer kostnaden för alla noder för att beräkna den totala månads kostnaden.
- Kostnaderna visas i den valuta som anges i utvärderings inställningarna.

Eftersom priserna för Azure VMware-lösningen (AVS) är per nod, har den totala kostnaden ingen beräknings kostnad och distribution av lagrings kostnader. [Läs mer](../azure-vmware/introduction.md)

Observera att när Azure VMware-lösningen (AVS) är i för hands version, är nodadressen i utvärderingen förhands gransknings priser. Kontakta ditt lokala team för MSFT-GBB om du behöver hjälp.

## <a name="migration-tool-guidance"></a>Vägledning för Migreringsverktyg

I Azure-beredskapsrapporten för Azure VMware Solution (AVS)-utvärdering föreslås följande verktyg: 
- **VMware HCX eller Enterprise**: för VMware-datorer är HCX-lösningen (VMware Hybrid Cloud Extensions) det rekommenderade Migreringsverktyg för att migrera din lokala arbets belastning till ditt Azure VMware-lösning (AVS) privat moln. [Läs mer](../azure-vmware/hybrid-cloud-extension-installation.md).
- **Okänt**: Standardmigreringsverktyget är okänt för datorer som importerats via en CSV-fil. Även för VMware-datorer rekommenderar vi att du använder HCX-lösningen (VMware Hybrid Cloud Extension).

## <a name="next-steps"></a>Nästa steg

Skapa en utvärdering för [virtuella datorer i AVS](how-to-create-azure-vmware-solution-assessment.md)-miljö.
