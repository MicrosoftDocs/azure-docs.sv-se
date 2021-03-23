---
title: Beräkningar av AVS-bedömning i Azure Migrate | Microsoft Docs
description: Innehåller en översikt över beräkningar av AVS-bedömning i Azure Migrates tjänsten.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: 1d9918786b22faddaeb07a12f0840b36a11ffe4d
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778389"
---
# <a name="assessment-overview-migrate-to-azure-vmware-solution"></a>Översikt över utvärdering (migrera till Azure VMware-lösning)

[Azure Migrate](migrate-services-overview.md) tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av dina lokala appar och arbets belastningar. Den spårar också dina privata och offentliga moln instanser till Azure. Hubben erbjuder Azure Migrate verktyg för utvärdering och migrering, samt oberoende program varu leverantörer från tredje part (ISV).

Verktyget för identifiering och bedömning i Azure Migrate utvärderar lokala servrar för migrering till virtuella Azure-datorer och Azure VMware-lösning (AVS). Den här artikeln innehåller information om hur Azure-utvärderingar av VMware-lösning (AVS) beräknas.

> [!NOTE]
> Azure VMware Solution (AVS)-utvärdering kan bara skapas för virtuella VMware-datorer.

## <a name="types-of-assessments"></a>Typer av utvärderingar

Utvärderingar som du skapar med Azure Migrate är en tidpunkts ögonblicks bild av data. Det finns två typer av utvärderingar som du kan skapa med hjälp av Azure Migrate:

**Utvärderingstyp** | **Information**
--- | --- 
**Azure VM** | Utvärderingar som migrerar dina lokala servrar till virtuella Azure-datorer. Du kan utvärdera dina lokala servrar i [VMware](how-to-set-up-appliance-vmware.md) [-och Hyper-V-](how-to-set-up-appliance-hyper-v.md) miljön och [fysiska servrar](how-to-set-up-appliance-physical.md) för migrering till virtuella Azure-datorer med den här utvärderings typen.
**Azure SQL** | Utvärderingar för att migrera dina lokala SQL-servrar från din VMware-miljö till Azure SQL Database eller Azure SQL-hanterad instans.
**Azure VMware Solution (AVS)** | Utvärderingar för att migrera dina lokala servrar till [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). Du kan utvärdera dina lokala [virtuella VMware-datorer](how-to-set-up-appliance-vmware.md) för migrering till Azure VMware-lösningen (AVS) med den här utvärderings typen. [Läs mer](concepts-azure-vmware-solution-assessment-calculation.md)

Azure VMware Solution (AVS)-utvärdering ger två storlekar för storleks villkor:

| **Utvärdering** | **Information** | **Data** |
| - | - | - |
| **Prestandabaserad** | Utvärderingar baserade på insamlade prestanda data för lokala virtuella datorer. | **Rekommenderad Node-storlek**: baserat på CPU-och minnes användnings data tillsammans med nodtypen, lagrings typ och FTT-inställning som du väljer för utvärderingen. |
| **Som lokalt** | Utvärderingar baserade på lokal storlek. | **Rekommenderad Node-storlek**: baserat på den lokala virtuella dator storleken tillsammans med den nodtyp, lagrings typ och FTT-inställning som du väljer för utvärderingen. |

## <a name="how-do-i-run-an-assessment"></a>Hur gör jag för att köra en utvärdering?

Det finns ett par olika sätt att köra en utvärdering.

- Utvärdera servrar med hjälp av Server-metadata som samlats in av en förenklad Azure Migrate-installation. Enheten identifierar lokala servrar. Den skickar sedan serverns metadata och prestanda data till Azure Migrate. Detta ger mer precision.
- Utvärdera servrar med hjälp av serverns metadata som importeras i CSV-format (kommaavgränsade värden).

## <a name="how-do-i-assess-with-the-appliance"></a>Hur gör jag för att du utvärdera med produkten?

Gör så här om du distribuerar en Azure Migrate-enhet för att identifiera lokala servrar:

1. Konfigurera Azure och din lokala miljö för att arbeta med Azure Migrate.
2. Skapa ett Azure-projekt för din första utvärdering och Lägg till identifierings-och utvärderings verktyget i det.
3. Distribuera en förenklad Azure Migrate-apparat. Enheten identifierar kontinuerligt lokala servrar och skickar metadata och prestanda data till Azure Migrate. Distribuera apparaten som en virtuell dator. Du behöver inte installera något på de servrar som du vill utvärdera.

När installationen påbörjat Server identifiering kan du samla in servrar som du vill utvärdera i en grupp och köra en utvärdering av gruppen med utvärderings typ **Azure VMware-lösning (AVS)**.

Skapa din första Azure VMware Solution (AVS)-utvärdering genom att följa stegen [här](how-to-create-azure-vmware-solution-assessment.md).

## <a name="how-do-i-assess-with-imported-data"></a>Hur gör jag för att utvärdering med importerade data?

Om du utvärderar servrar med hjälp av en CSV-fil behöver du inte en installation. Gör i stället följande steg:

1. Konfigurera Azure för att arbeta med Azure Migrate.
2. Skapa ett Azure-projekt för din första utvärdering och Lägg till identifierings-och utvärderings verktyget i det.
3. Ladda ned en CSV-mall och Lägg till Server data till den.
4. Importera mallen till Azure Migrate.
5. Identifiera servrar som har lagts till med importen, samla in dem i en grupp och kör en utvärdering för gruppen med utvärderings typ **Azure VMware-lösning (AVS)**.

## <a name="what-data-does-the-appliance-collect"></a>Vilka data samlas in av enheten?

Om du använder Azure Migrate-enheten för utvärdering kan du läsa om de metadata och prestanda data som samlas in för [VMware](migrate-appliance.md#collected-data---vmware).

## <a name="how-does-the-appliance-calculate-performance-data"></a>Hur beräknar enheten prestanda data?

Om du använder installations programmet för identifiering samlar den in prestanda data för beräknings inställningar med följande steg:

1. Enheten samlar in en exempel punkt i real tid.

   - **Virtuella VMware-datorer**: en exempel punkt samlas in var 20: e sekund.
2. Enheten kombinerar exempel punkterna för att skapa en enda data punkt var tionde minut. För att skapa data punkten väljer enheten de högsta värdena från alla exempel. Sedan skickas data punkten till Azure.
3. Azure Migrate lagrar alla data punkter på 10 minuter för den senaste månaden.
4. När du skapar en utvärdering identifierar utvärderingen lämplig data punkt som ska användas för att göra rättigheter. Identifieringen baseras på percentilvärdet för *prestanda historik* och *percentils användning*.

   - Om prestanda historiken till exempel är en vecka och percentilvärdet använder 95 percentilen, sorterar utvärderingen 10-minuters exempel punkter för den senaste veckan. De sorterar dem i stigande ordning och plockar 95 percentilvärdet för att få behörighet.
   - Värdet 95 percentil ser till att du ignorerar eventuella avvikande värden, som kan inkluderas om du har valt 99 percentilen.
   - Om du vill välja högsta användnings nivå för perioden och inte vill missa avvikande värden väljer du den 99 percentilen för percentils användning.
5. Det här värdet multipliceras med den praktiska faktorn för att få den effektiva prestanda användnings informationen för de mått som installeras av enheten:

   - CPU-användning
   - RAM-användning

Följande prestanda data samlas in men används inte vid storleks rekommendationer för AVS-utvärderingar:

- Disk-IOPS och data flödes data för varje disk som är ansluten till den virtuella datorn.
- Nätverks-I/O för att hantera prestandabaserade storleks ändringar för varje nätverkskort som är kopplat till en virtuell dator.

## <a name="how-are-avs-assessments-calculated"></a>Hur beräknas AVS-bedömningar?

AVS-utvärderingen använder de lokala servrarnas metadata och prestanda data för att beräkna utvärderingar. Om du distribuerar Azure Migrate-enheten använder utvärderingen de data som samlas in av enheten. Men om du kör en utvärdering som importer ATS med en CSV-fil anger du metadata för beräkningen.

Beräkningar sker i dessa tre steg:

1. **Beräkna beredskap för Azure VMware-lösningen (AVS)**: om de lokala virtuella datorerna är lämpliga för migrering till Azure VMware-lösningen (AVS).
2. **Beräkna antal AVS-noder och användning över noder**: uppskattat antal AVS-noder som krävs för att köra virtuella VMware-datorer och projekterad processor, minne och lagrings belastning för alla noder.
3. **Månads kostnads uppskattning**: den uppskattade månads kostnaden för alla Azure VMware-lösningar (AVS)-noder som kör lokala virtuella datorer.

Beräkningarna sker i föregående ordning. En server flyttas till ett senare steg endast om den tidigare har passerat. Om en server till exempel inte klarar AVS-beredskaps fasen, är den markerad som olämplig för Azure. Storleks-och kostnads beräkningar utförs inte för den servern

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Vad finns i en Azure VMware-lösning (AVS)-utvärdering?

Här är what's som ingår i en AVS-utvärdering:

| **Egenskap** | **Information** |
| - | - |
| **Målplats** | Anger den plats för molnets privata moln som du vill migrera till. |
| **Lagringstyp** | Anger den lagrings motor som ska användas i AVS. AVS har för närvarande endast stöd för virtuellt San som standard lagrings typ, men fler lagrings alternativ kommer att göras enligt översikt. |
| **Reserverade instanser (RIs)** | Med den här egenskapen kan du ange reserverade instanser i AVS om det har köpts och den reserverade instansens period. Används för att beräkna kostnader. |
| **Nodtyp** | Anger den [typ av AVS-nodtyp](../azure-vmware/concepts-private-clouds-clusters.md) som används i Azure. Standard-nodtypen är AV36. Fler nodtyper kan vara tillgängliga i framtiden.  Azure Migrate rekommenderar att antalet noder som krävs för de virtuella datorerna migreras till AVS. |
| **FTT-inställning, RAID-nivå** | Anger en giltig kombination av felen för att tolerera och RAID-kombinationer. Det valda FTT-alternativet kombinerat med RAID-nivån och kravet på lokal virtuell dator disk avgör den totala virtuellt SAN lagring som krävs i AVS. Den totala mängden tillgängligt lagrings utrymme efter beräkningar innehåller även ett) utrymme som är reserverat för hanterings objekt som vCenter och b) 25% minnes slack krävs för virtuellt San-åtgärder. |
| **Ändra storlek på kriterium** | Anger de kriterier som ska användas för att fastställa minnes-, processor-och lagrings krav för AVS-noder. Du kan välja *prestandabaserade* storleks ändringar eller *lokalt* utan att behöva beakta prestanda historiken. Om du bara vill lyfta och byta Skift väljer du lokalt. För att få användning baserad storlek väljer du prestanda baserat. |
| **Prestandahistorik** | Ställer in varaktigheten för att utvärdera prestanda data för servrar. Den här egenskapen gäller bara när storleks kriteriet är *prestanda-baserat*. |
| **Percentilutnyttjande** | Anger percentilvärdet för den prestanda exempel uppsättning som ska beaktas för höger storlek. Den här egenskapen gäller bara när storleken är prestanda-baserad. |
| **Komfortfaktor** | Azure Migrate överväger en buffert (komfortfaktor) under utvärderingen. Den här bufferten används ovanpå Server användnings data för virtuella datorer (CPU, minne och disk). Komfortfaktorn väger in problem som säsongsbaserad användning, kort prestandahistorik och troliga ökningar i kommande användning. Till exempel resulterar en virtuell dator med 10 kärnor med 20 % användning vanligen i en virtuell dator med 2 kärnor. Med en komfortfaktor på 2.0x blir resultatet istället en virtuell dator med 4 kärnor. |
| **Erbjudande** | Visar [Azure-erbjudandet](https://azure.microsoft.com/support/legal/offer-details/) som du har registrerat i. Azure Migrate beräknar kostnaden enligt detta. |
| **Valuta** | Visar fakturerings valutan för ditt konto. |
| **Rabatt (%)** | Visar en lista över den prenumerations information som du får ovanpå Azure-erbjudandet. Standardinställningen är 0%. |
| **Azure Hybrid-förmån** | Anger om du har Software Assurance och är berättigade till [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Även om den inte har någon inverkan på priserna för Azure VMware-lösningar på grund av det Node-baserade priset kan kunderna fortfarande använda lokala operativ system eller SQL-licenser (Microsoft-baserade) i AVS med Azure Hybrid-förmåner. Andra program varu leverantörer måste ange sina egna licens villkor, till exempel RHEL. |
| **vCPU överprenumeration** | Anger förhållandet mellan antalet virtuella kärnor som är knutna till en fysisk kärna i AVS-noden. Standardvärdet i beräkningarna är 4 vCPU: 1 fysisk kärna i AVS. API-användare kan ange det här värdet som ett heltal. Observera att vCPU överprenumeration > 4:1 kan påverka arbets belastningar beroende på CPU-användning. När du ändrar storlek, antar vi alltid 100% användning av de kärnor som valts. |
| **Minnes överinchecknings faktor** | Anger förhållandet för minne som ska allokeras i klustret. Värdet 1 representerar 100% minnes användning, 0,5 till exempel 50% och 2 använder 200% av tillgängligt minne. Du kan bara lägga till värden från 0,5 till 10 till en decimal. |
| **Deduplicera och komprimerings faktor** | Anger förväntad deduplicerad och komprimerings faktor för dina arbets belastningar. Det faktiska värdet kan hämtas från lokala virtuellt SAN eller Storage config. Dessa varierar beroende på arbets belastning. Värdet 3 innebär tre gånger så att endast 100 GB lagrings utrymme används för 300 GB disk. Värdet 1 innebär ingen deduplicerad eller komprimering. Du kan bara lägga till värden från 1 till 10 upp till en decimal. |

## <a name="azure-vmware-solution-avs-suitability-analysis"></a>Analys av lämplighet för Azure VMware Solution (AVS)

AVS-utvärderingen bedömer varje lokal virtuell dator för att vara lämplig för AVS genom att granska Server egenskaperna. Den tilldelar också varje utvärderad server till någon av följande kategorier för lämplighet:

- **Redo för AVS**: servern kan migreras som-är till Azure (AVS) utan några ändringar. Den kommer att starta i AVS med fullständig AVS-support.
- **Klar med villkor**: den virtuella datorn kan ha kompatibilitetsproblem med den aktuella vSphere-versionen samt kräva att VMware-verktyg och andra inställningar krävs innan fullständig funktionalitet från den virtuella datorn kan uppnås i AVS.
- **Inte redo för AVS**: den virtuella datorn kommer inte att starta i AVS. Om till exempel den lokala virtuella VMware-datorn har en extern enhet ansluten, till exempel en CD-Rom, kommer VMware VMotion åtgärden att Miss Missing (om du använder VMware VMotion).
- **Beredskap okänd**: Azure Migrate kunde inte fastställa serverns beredskap på grund av otillräckliga metadata som samlats in från den lokala miljön.

Utvärderingen granskar Server egenskaperna för att fastställa Azure-beredskap för den lokala servern.

### <a name="server-properties"></a>Serveregenskaper

Utvärderingen granskar följande egenskap för den lokala virtuella datorn för att avgöra om den kan köras på Azure VMware-lösning (AVS).

| **Egenskap** | **Information** | **Status för AVS-beredskap** |
| - | - | - |
| **Internet Protocol** | Azure stöder för närvarande inte slut punkt till slut punkt för IPv6-Internet adressering. Kontakta ditt lokala team för MSFT-GBB för vägledning om hur du får vägledning om din server identifieras med IPv6. | Villkorligt klar Internet Protocol |

### <a name="guest-operating-system"></a>Gästoperativsystem

För närvarande granskar inte AVS-bedömningar operativ systemet som en del av lämplig analys. Alla operativ system som körs på lokala virtuella datorer körs förmodligen på Azure VMware-lösning (AVS).

Tillsammans med VM-egenskaperna tittar utvärderingen på gäst operativ systemet på servrarna för att avgöra om den kan köras på Azure.

## <a name="sizing"></a>Storlekar

När en server har marker ATS som klar för AVS, gör AVS-utvärderingen en storleks rekommendation för noder, vilket innefattar att identifiera lämpliga lokala VM-krav och hitta det totala antalet AVS-noder som krävs. De här rekommendationerna varierar beroende på vilka bedömnings egenskaper som anges.

- Om utvärderingen använder *prestandabaserade storleks* ändringar, tar Azure Migrate hänsyn till serverns prestanda historik för att göra lämplig storleks rekommendation för AVS. Den här metoden är särskilt användbar om du har överallokerat den lokala virtuella datorn, men användningen är låg och du vill ha rätt storlek på den virtuella datorn i AVS för att spara kostnader. Den här metoden hjälper dig att optimera storlekarna under migreringen.
- Om du inte vill ta hänsyn till prestanda data för storleks ändring av virtuella datorer och vill ta med lokala servrar i AVS-läge kan du ange storleks villkoren till * lokalt *. Sedan ändrar utvärderingen storlek på de virtuella datorerna baserat på den lokala konfigurationen utan att ta hänsyn till användnings data.

### <a name="ftt-sizing-parameters"></a>Parametrar för FTT storlek

Den lagrings motor som används i AVS är virtuellt San. Virtuellt San lagrings principer definierar lagrings krav för dina servrar. Med dessa principer garanteras den tjänstnivå som krävs för dina virtuella datorer eftersom de fastställer hur lagringen allokeras till den virtuella datorn. De tillgängliga FTT-Raid kombinationerna är:

| **Fel som ska kunna hanteras (FTT)** | **RAID-konfiguration** | **Lägsta antal värdar som krävs** | **Storleksövervägande** |
| - | - | - | - |
| 1 | RAID-1 (spegling) | 3 | En virtuell dator på 100 GB skulle förbruka 200 GB. |
| 1 | RAID-5 (raderingsskyddad kod) | 4 | En virtuell dator på 100 GB skulle förbruka 133,33 GB |
| 2 | RAID-1 (spegling) | 5 | En virtuell dator på 100 GB skulle förbruka 300 GB. |
| 2 | RAID-6 (raderingsskyddad kod) | 6 | En virtuell dator på 100 GB skulle förbruka 150 GB. |
| 3 | RAID-1 (spegling) | 7 | En virtuell dator på 100 GB skulle förbruka 400 GB. |

### <a name="performance-based-sizing"></a>Prestanda-baserad storlek

För prestandabaserade storleks ändringar kan Azure Migrates utrustnings profiler i den lokala miljön för att samla in prestanda data för CPU, minne och disk. Prestandabaserade storleks ändringar för AVS tar därmed hänsyn till det allokerade disk utrymmet och använder den valda percentilen av minne och CPU. Till exempel om en virtuell dator har 4vCores tilldelad men bara använder 25%, kommer AVS att ändra storlek för 1vCore för den virtuella datorn.

**Steg för insamling av prestanda data:**

1. För virtuella VMware-datorer samlar Azure Migrate-installationen in en exempel punkt i real tid med varje 20-sekunders intervall.
2. Enheten samlar in de exempel punkter som samlats in var tionde minut och skickar det högsta värdet under de senaste 10 minuterna att Azure Migrate.
3. Azure Migrate lagrar alla exempel punkter på 10 minuter under den senaste månaden. Beroende på vilka bedömnings egenskaper som anges för *prestanda historik* och *percentiler*, identifierar den lämplig data punkt som ska användas för höger storlek. Om prestanda historiken till exempel är inställd på 1 dag och percentilvärdet är 95 percentilen använder Azure Migrate 10 minuters exempel punkter för den senaste dagen, sorterar dem i stigande ordning och väljer 95 percentilvärdet för höger storlek.
4. Det här värdet multipliceras med den praktiska faktorn för att hämta den effektiva prestanda användnings informationen för varje mått (processor användning och minnes användning) som samlas in av enheten.

När det effektiva användning svärdet har fastställts hanteras lagring, nätverk och beräknings storlek enligt följande.

**Lagrings storlek**: Azure Migrate använder det totala disk utrymmet för den lokala virtuella datorn som en beräknings parameter för att fastställa lagrings krav för AVS-virtuellt San utöver den KUNDvalda FTT-inställningen. FTT – fel som kan tolereras och kräver minst antal noder per FTT avgör den totala virtuellt San-lagringen som krävs kombinerat med kravet på VM-diskar. Lagrings användningen är för närvarande inte att beaktas och logiken söker i allokerat lagrings utrymme per virtuell dator.

**Nätverks storlek**: AVS-bedömningar tar för närvarande inte hänsyn till några nätverks inställningar.

**Beräknings storlek**: när den beräknar lagrings kraven tar AVS-utvärderingen hänsyn till processor-och minnes krav för att fastställa antalet noder som krävs för AVS-baserat på nodtypen.

- Enligt kriterierna för storleks ändring ser AVS-utvärderingen ut antingen den prestandabaserade VM-informationen eller den lokala virtuella dator konfigurationen. Inställningen bekvämlighets faktor gör det möjligt att ange en tillväxt faktor för klustret. För närvarande är hypertrådning aktiverat som standard och noder med 36 kärnor har därmed 72 virtuella kärnor. 4 virtuella kärnor per fysisk kärna används för att fastställa CPU-tröskelvärden per kluster med VMware-standarden med högst 80 procents användning så att underhåll eller fel kan hanteras utan att klustrets tillgänglighet äventyras. Det finns för närvarande ingen åsidosättning tillgänglig för att ändra överprenumerations värden och vi kan ha detta i framtida versioner.

### <a name="as-on-premises-sizing"></a>Som lokal storlek

Om du använder *som lokal storleks ändring*, tar inte AVS-utvärderingen hänsyn till prestanda historiken för de virtuella datorerna och diskarna. I stället allokeras AVS-noder baserat på den storlek som tilldelas lokalt. Standard lagrings typen är virtuellt San i AVS.

[Läs mer](./tutorial-assess-vmware-azure-vmware-solution.md#review-an-assessment) om hur du granskar en Azure VMware-lösning.

### <a name="cpu-utilization-on-avs-nodes"></a>CPU-användning på AVS-noder

PROCESSOR användningen förutsätter 100% användning av de tillgängliga kärnorna. Om du vill minska antalet noder som krävs kan du öka överprenumerationen från 4:1 till att säga 6:1 baserat på arbets Belastningens egenskaper och den lokala miljön. Till skillnad från för disk, placerar inte AVS-gränserna några gränser för processor användningen, det är upp till kunder att säkerställa att deras kluster presterar optimalt, så om "rinnande het" krävs, justeras därefter. Minska överprenumerationen eller öka värdet för tillväxtfaktorn om du vill tillåta mer utrymme för tillväxt.

PROCESSOR användningen är också redan ett konto för hanterings kostnader från vCenter, NSX Manager och andra mindre resurser.

### <a name="memory-utilization-on-avs-nodes"></a>Minnes användning på AVS-noder

Minnes användning visar det totala minnet från alla noder kontra krav från Server eller arbets belastningar. Det går att prenumerera på ett minne och AVS-platser har inga gränser och det är upp till kunden att köra optimala kluster prestanda för sina arbets belastningar.

Minnes användningen är också redan ett konto för hanterings kostnader från vCenter, NSX Manager och andra mindre resurser.

### <a name="storage-utilization-on-avs-nodes"></a>Lagrings användning på AVS-noder

Lagrings användningen beräknas baserat på följande ordning:

1. Storlek som krävs för den virtuella datorn (antingen tilldelad som är eller baserat på ett prestanda baserat utrymme som används)
2. Tillämpa tillväxtfaktorn om det finns
3. Lägg till hanterings kostnader och tillämpa FTT-förhållandet
4. Använd deduplicera och komprimerings faktor
5. Använd nödvändigt 25% slack för virtuellt San
6. Resultat tillgängligt lagrings utrymme för virtuella datorer av total lagring, inklusive hanterings kostnader.

Tillgängligt lagrings utrymme på ett kluster med tre noder kommer att baseras på standard lagrings principen som är RAID-1 och använder tjocka etablering. När du beräknar radering-kodning eller RAID-5, krävs till exempel minst 4 noder. Observera att lagrings principen i AVS måste ändras av administratören för att det ska bli mer utrymme.

## <a name="confidence-ratings"></a>Tillförlitlighets klassificering

Varje prestandabaserade utvärdering i Azure Migrate associeras med en förtroende grad som sträcker sig från ett (lägst) till fem stjärnor (högst).

- Säkerhetsomdömet tilldelas en utvärdering baserat på tillgängligheten av datapunkter som behövs för att beräkna utvärderingen.
- Med säkerhetsomdömet kan du beräkna tillförlitligheten i de storleksrekommendationer som anges av Azure Migrate.
- Tillförlitlighets klassificeringar är inte tillämpliga för *den lokala* utvärderingen.
- För prestandabaserade storleks ändringar måste du använda användnings data för CPU och VM-minne. Följande data samlas in men används inte vid storleks rekommendationer för AVS:

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

- Du profilerade inte din miljö för hela den varaktighet för vilken du skapar utvärderingen. Om du till exempel skapar utvärderingen med varaktigheten inställd på en dag måste du vänta minst en dag efter att du har startat identifieringen för alla data punkter som ska samlas in.
- Utvärderingen kan inte samla in prestanda data för vissa eller alla virtuella datorer under utvärderings perioden. För en hög exakthet bör du se till att:

  - Virtuella datorer är påslagna under utvärderings perioden
  - Utgående anslutningar på portarna 443 tillåts
  - För Hyper-V virtuella datorer är dynamiskt minne aktiverat

  Beräkna om utvärderingen så att de senaste ändringarna återspeglas i säkerhetsomdömet.
- Vissa virtuella datorer skapades under den tid då utvärderingen beräknades. Anta till exempel att du har skapat en utvärdering för prestanda historiken för den senaste månaden, men vissa virtuella datorer skapades bara för en vecka sedan. I detta fall kommer prestandadata för de nya virtuella datorerna inte att vara tillgängliga för hela tidsperioden och säkerhetsomdömet blir lågt.

> [!NOTE]
> Om en utvärderings grad är mindre än fem stjärnor rekommenderar vi att du väntar minst en dag för att enheten ska profilera miljön och sedan beräkna utvärderingen på annat sätt. Om du inte gör det kanske prestandabaserade storleks ändringar inte är pålitliga. I så fall rekommenderar vi att du växlar utvärderingen till lokal storlek.

## <a name="monthly-cost-estimation"></a>Månatlig kostnads uppskattning

När storleks rekommendationerna har slutförts beräknar Azure Migrate den totala kostnaden för att köra de lokala arbets belastningarna i AVS genom att multiplicera antalet AVS-noder som krävs av Node-priset. Kostnaden per VM-kostnad beräknas genom att den totala kostnaden divideras med antalet virtuella datorer i utvärderingen.

- Beräkningen tar antalet noder som krävs, nodtypen och platsen i kontot.
- Den sammanställer kostnaden för alla noder för att beräkna den totala månads kostnaden.
- Kostnaderna visas i den valuta som anges i utvärderings inställningarna.

Eftersom priserna för Azure VMware-lösningen (AVS) är per nod, har den totala kostnaden ingen beräknings kostnad och distribution av lagrings kostnader. [Läs mer](../azure-vmware/introduction.md)

## <a name="migration-tool-guidance"></a>Vägledning för Migreringsverktyg

I Azure-beredskapsrapporten för Azure VMware Solution (AVS)-utvärdering föreslås följande verktyg:

- **VMware HCX eller Enterprise**: för VMware-servrar är VMware Hybrid Cloud Extensions (HCX)-lösningen det rekommenderade migreringsjobbet för att migrera din lokala arbets belastning till ditt Azure VMware-lösning (AVS) privat moln. [Läs mer](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Okänd**: för servrar som importeras via en CSV-fil är standard verktyget för migrering okänt. Även för VMware-servrar rekommenderar vi att du använder HCX-lösningen (VMware Hybrid Cloud Extension).

## <a name="next-steps"></a>Nästa steg

Skapa en utvärdering för [virtuella datorer i AVS](how-to-create-azure-vmware-solution-assessment.md)-miljö.
