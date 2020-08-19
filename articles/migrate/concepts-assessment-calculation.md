---
title: Utvärderingar av virtuella Azure-datorer i Azure Migrate Server-utvärdering
description: Lär dig mer om utvärderingar i Azure Migrate Server bedömning
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 1d9c887f42089611ce7402aa32174958cd8c0b07
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88261862"
---
# <a name="server-assessment-overview-migrate-to-azure-vms"></a>Översikt över Server utvärdering (migrera till virtuella Azure-datorer)

Den här artikeln innehåller en översikt över utvärderingar i verktyget [Azure Migrate: Server bedömning](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Verktyget kan utvärdera lokala virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska servrar för migrering till Azure.

## <a name="whats-an-assessment"></a>Vad är en utvärdering?

En utvärdering med verktyget för Server utvärdering mäter beredskap och uppskattar effekterna av att migrera lokala servrar till Azure.

> [!NOTE]
> I Azure Government granskar du platser som [stöds för mål](migrate-support-matrix.md#supported-geographies-azure-government) utvärdering. Observera att rekommendationer för VM-storlek i utvärderingar kommer att använda VM-serien specifikt för myndigheter i den offentliga sektorn. [Läs mer](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) om VM-typer.

## <a name="types-of-assessments"></a>Typer av utvärderingar

Det finns två typer av utvärderingar som du kan skapa med hjälp av Azure Migrate: Server utvärdering.

**Bedömnings typ** | **Information**
--- | --- 
**Azure VM** | Utvärderingar för att migrera dina lokala servrar till Azure Virtual Machines. <br/><br/> Du kan utvärdera dina lokala [virtuella VMware-datorer](how-to-set-up-appliance-vmware.md), [virtuella Hyper-V-datorer](how-to-set-up-appliance-hyper-v.md)och [fysiska servrar](how-to-set-up-appliance-physical.md) för migrering till Azure med hjälp av den här utvärderings typen.
**Azure VMware Solution (AVS)** | Utvärderingar för att migrera dina lokala servrar till [Azure VMware-lösningen (AVS)](../azure-vmware/introduction.md). <br/><br/> Du kan utvärdera dina lokala [virtuella VMware-datorer](how-to-set-up-appliance-vmware.md) för migrering till Azure VMware-lösningen (AVS) med den här utvärderings typen. [Läs mer](concepts-azure-vmware-solution-assessment-calculation.md)

Utvärderingar som du skapar med Server utvärdering är en tidpunkts ögonblicks bild av data. En utvärdering av virtuella Azure-datorer i Server utvärderingen innehåller två storleks villkors alternativ:

**Bedömnings typ** | **Information** | **Data**
--- | --- | ---
**Prestanda-baserade** | Utvärderingar som gör rekommendationer baserat på insamlade prestanda data | Den virtuella datorns storleks rekommendation baseras på processor-och RAM användnings data.<br/><br/> Rekommendationen för disk typen baseras på de indata/utdata-åtgärder per sekund (IOPS) och data flödet för de lokala diskarna. Disk typer är Azure Standard HDD, Azure Standard SSD och Azure Premium-diskar.
**Som lokal** | Utvärderingar som inte använder prestanda data för att göra rekommendationer | Den virtuella datorns storleks rekommendation baseras på den lokala virtuella dator storleken.<br/><br> Den rekommenderade disk typen baseras på den valda lagrings typen för utvärderingen.

## <a name="how-do-i-run-an-assessment"></a>Hur gör jag för att köra en utvärdering?

Det finns ett par olika sätt att köra en utvärdering.

- Utvärdera datorer med hjälp av Server-metadata som samlats in av en förenklad Azure Migrate-apparat. Enheten identifierar lokala datorer. Den skickar sedan datorns metadata och prestanda data till Azure Migrate.
- Utvärdera datorer med hjälp av serverns metadata som importeras i CSV-format (kommaavgränsade värden).

## <a name="how-do-i-assess-with-the-appliance"></a>Hur gör jag för att du utvärdera med produkten?

Gör så här om du distribuerar en Azure Migrate-enhet för att identifiera lokala servrar:

1. Konfigurera Azure och din lokala miljö för att arbeta med Server utvärderingen.
1. Skapa ett Azure-projekt och Lägg till Server Assessment-verktyget i den första utvärderingen.
1. Distribuera en förenklad Azure Migrate-apparat. Enheten identifierar kontinuerligt lokala datorer och skickar metadata och prestanda data till Azure Migrate. Distribuera installationen som en virtuell dator eller en fysisk dator. Du behöver inte installera något på datorer som du vill utvärdera.

När installationen har påbörjat dator identifiering kan du samla in datorer som du vill utvärdera i en grupp och köra en utvärdering av gruppen med utvärderings typ **Azure VM**.

Följ våra självstudier för [VMware](tutorial-prepare-vmware.md), [Hyper-V](tutorial-prepare-hyper-v.md)eller [fysiska servrar](tutorial-prepare-physical.md) för att prova de här stegen.

## <a name="how-do-i-assess-with-imported-data"></a>Hur gör jag för att utvärdering med importerade data?

Om du utvärderar servrar med hjälp av en CSV-fil behöver du inte en installation. Gör i stället följande steg:

1. Konfigurera Azure för att arbeta med Server utvärdering.
1. Skapa ett Azure-projekt och Lägg till Server Assessment-verktyget i den första utvärderingen.
1. Ladda ned en CSV-mall och Lägg till Server data till den.
1. Importera mallen till Server utvärderingen.
1. Identifiera servrar som har lagts till med importen, samla in dem i en grupp och kör en utvärdering för gruppen med utvärderings typ **Azure VM**.

## <a name="what-data-does-the-appliance-collect"></a>Vilka data samlas in av enheten?

Om du använder Azure Migrate-enheten för utvärdering kan du läsa om de metadata och prestanda data som samlas in för [VMware](migrate-appliance.md#collected-data---vmware) och [Hyper-V](migrate-appliance.md#collected-data---hyper-v).

## <a name="how-does-the-appliance-calculate-performance-data"></a>Hur beräknar enheten prestanda data?

Om du använder installations programmet för identifiering samlar den in prestanda data för beräknings inställningar med följande steg:

1. Enheten samlar in en exempel punkt i real tid.

    - **Virtuella VMware-datorer**: en exempel punkt samlas in var 20: e sekund.
    - **Virtuella Hyper-V-datorer**: en exempel punkt samlas in var 30: e sekund.
    - **Fysiska servrar**: en exempel punkt samlas in var femte minut.

1. Enheten kombinerar exempel punkterna för att skapa en enda data punkt var tionde minut. För att skapa data punkten väljer enheten de högsta värdena från alla exempel. Sedan skickas data punkten till Azure.
1. Server utvärderingen lagrar alla data punkter på 10 minuter för den senaste månaden.
1. När du skapar en utvärdering identifierar Server utvärderingen lämplig data punkt som ska användas för att göra rättigheter. Identifieringen baseras på percentilvärdet för *prestanda historik* och *percentils användning*.

    - Om prestanda historiken till exempel är en vecka och percentilvärdet är den 95: e percentilen, sorterar Server utvärderingen 10 minuters exempel punkter under den senaste veckan. De sorterar dem i stigande ordning och plockar 95 percentilvärdet för att få behörighet.
    - Värdet 95 percentil ser till att du ignorerar eventuella avvikande värden, som kan inkluderas om du har valt 99 percentilen.
    - Om du vill välja högsta användnings nivå för perioden och inte vill missa avvikande värden väljer du den 99 percentilen för percentils användning.

1. Det här värdet multipliceras med den praktiska faktorn för att få den effektiva prestanda användnings informationen för de mått som installeras av enheten:

    - CPU-användning
    - RAM-användning
    - Disk-IOPS (läsning och skrivning)
    - Disk data flöde (läsning och skrivning)
    - Nätverks data flöde (in och ut)

## <a name="how-are-azure-vm-assessments-calculated"></a>Hur beräknas utvärdering av virtuella Azure-datorer?

Server utvärderingen använder de lokala datorernas metadata och prestanda data för att beräkna utvärderingar. Om du distribuerar Azure Migrate-enheten använder utvärderingen de data som samlas in av enheten. Men om du kör en utvärdering som importer ATS med en CSV-fil anger du metadata för beräkningen.

Beräkningar sker i dessa tre steg:

1. **Beräkna Azure-beredskap**: Bedöm om datorerna är lämpliga för migrering till Azure.
1. **Beräkna storleks rekommendationer**: beräkna beräkning, lagring och nätverks storlek.
1. **Beräkna månads kostnader**: beräkna de uppskattade kostnaderna för beräkning och lagring för att köra datorerna i Azure efter migreringen.

Beräkningarna sker i föregående ordning. En dator server flyttas till ett senare steg endast om den tidigare har passerat. Om en server till exempel inte klarar Azure readiness-fasen, är den markerad som olämplig för Azure. Storleks-och kostnads beräkningar utförs inte för den servern.

## <a name="whats-in-an-azure-vm-assessment"></a>Vad ingår i en Azure VM-utvärdering?

Här är what's som ingår i en Azure VM-utvärdering i Server utvärderingen:

**Egenskap** | **Information**
--- | ---
**Målplats** | Den plats som du vill migrera till. Server utvärderingen stöder för närvarande följande Azure-regioner:<br/><br/> Östra Australien, sydöstra Australien, södra Brasilien, centrala Kanada, Östra Kanada, centrala Indien, centrala USA, Kina, östra, Kina, norra, Asien, östra, östra USA, östra USA 2, centrala Tyskland, Tyskland nordöstra, Östra Japan, västra Japan, centrala Korea, centrala, norra centrala USA, norra Europa, södra centrala USA, Sydostasien, södra Indien, Storbritannien, södra, Storbritannien, västra US gov, Arizona , Västra centrala USA, Västeuropa, västra Indien, västra USA och västra USA 2.
**Mål lagrings disk (i storlek)** | Den typ av disk som ska användas för lagring i Azure. <br/><br/> Ange mål lagrings disken som Premium-hanterad, Standard SSD-hanterad eller Standard HDD-hanterad.
**Mål lagrings disk (prestanda baserad storlek)** | Anger typen av mål lagrings disk som automatisk, Premium-hanterad, Standard HDD-hanterad eller Standard SSD-hanterad.<br/><br/> **Automatisk**: disk rekommendationen baseras på diskens prestanda data, vilket innebär IOPS och data flöde.<br/><br/>**Premium eller standard**: utvärderingen rekommenderar en disk-SKU i den valda lagrings typen.<br/><br/> Överväg att använda Premium-hanterade diskar om du vill ha ett service nivå avtal (SLA) för en enskild instans på en virtuell dator med 99,9%. Den här användningen säkerställer att alla diskar i utvärderingen rekommenderas som Premium-hanterade diskar.<br/><br/> Azure Migrate stöder endast hanterade diskar för migrerings bedömning.
**Azure Reserved VM Instances** | Anger [reserverade instanser](https://azure.microsoft.com/pricing/reserved-vm-instances/) så att kostnads uppskattningar i utvärderingen tar dem i beaktande.<br/><br/> När du väljer reserverade instanser, rabatten (%) och egenskaperna för den virtuella datorns drift tid är inte tillämpliga.<br/><br/> Azure Migrate stöder för närvarande endast Azure Reserved VM Instances för erbjudanden med betala per användning.
**Storleks villkor** | Används för att hitta rätt storlek den virtuella Azure-datorn.<br/><br/> Använd som-är storleks ändring eller prestanda-baserad storlek.
**Prestandahistorik** | Används med prestanda-baserad storlek. Prestanda historik anger den varaktighet som används när prestanda data utvärderas.
**Percentilutnyttjande** | Används med prestanda-baserad storlek. Percentils användning anger percentilvärdet för det prestanda exempel som används för att ha behörighet.
**VM-serie** | Den Azure VM-serie som du vill ta hänsyn till. Om du till exempel inte har en produktions miljö som behöver en-seriens virtuella datorer i Azure kan du undanta en-serien från listan över serier.
**Komfortfaktor** | Den buffert som användes under utvärderingen. Den används för processor-, RAM-, disk-och nätverks data för virtuella datorer. IT-konton för problem som säsongs användning, kort prestanda historik och sannolika ökningar i framtida användning.<br/><br/> Till exempel resulterar en virtuell dator med 10 kärnor med 20% användning vanligt vis i en virtuell dator med två kärnor. Med en bekvämlighets faktor på 2,0 är resultatet en virtuell dator med fyra kärnor i stället.
**Erbjudande** | Det [Azure-erbjudande](https://azure.microsoft.com/support/legal/offer-details/) som du har registrerat i. Server utvärderingen beräknar kostnaden för det erbjudandet.
**Valuta** | Fakturerings valutan för ditt konto.
**Rabatt (%)** | Eventuella prenumerations rabatter som du får ovanpå Azure-erbjudandet. Standardinställningen är 0%.
**VM-drifttid** | Tiden i dagar per månad och timmar per dag för virtuella Azure-datorer som inte körs kontinuerligt. Kostnads uppskattningar baseras på den varaktigheten.<br/><br/> Standardvärdena är 31 dagar per månad och 24 timmar per dag.
**Azure Hybrid-förmån** | Anger om du har Software Assurance och är berättigade till [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Om inställningen har standardvärdet "Ja", betraktas Azure-priser för andra operativ system än Windows för virtuella Windows-datorer.
**EA-prenumeration** | Anger att en Enterprise-avtal-prenumeration (EA) används för kostnads uppskattning. Tar hänsyn till den rabatt som gäller för prenumerationen. <br/><br/> Lämna inställningarna för reserverade instanser, rabatt (%) och egenskaper för VM-drifts tid med standardinställningarna.


[Gå igenom metod tipsen](best-practices-assessment.md) för att skapa en utvärdering med Server utvärdering.

## <a name="calculate-readiness"></a>Beräkna beredskap

Alla datorer är inte lämpliga att köra i Azure. En Azure VM-utvärdering bedömer alla lokala datorer och tilldelar dem en beredskaps kategori.

- **Redo för Azure**: datorn kan migreras som den är till Azure utan några ändringar. Det kommer att starta i Azure med fullständig support för Azure.
- **Villkorligt redo för Azure**: datorn kan starta i Azure men kanske inte har fullständig support för Azure. Azure stöder till exempel inte en dator som kör en tidigare version av Windows Server. Du måste vara försiktig innan du migrerar de här datorerna till Azure. Du kan åtgärda eventuella beredskaps problem genom att följa den vägledning som utvärderingen föreslår.
- **Inte redo för Azure**: datorn startar inte i Azure. Om till exempel en lokal dators disk lagrar mer än 64 TB, kan inte Azure vara värd för datorn. Följ reparations vägledningen för att åtgärda problemet innan du migrerar.
- **Beredskap okänd**: Azure Migrate kan inte fastställa datorns beredskap på grund av otillräckliga metadata.

För att beräkna beredskap granskar Server utvärderingen datorns egenskaper och de inställningar för operativ systemet som sammanfattas i följande tabeller.

### <a name="machine-properties"></a>Dator egenskaper

För en Azure VM-utvärdering granskar Server utvärderingen följande egenskaper för en lokal virtuell dator för att avgöra om den kan köras på virtuella Azure-datorer.

Egenskap | Information | Status för Azure-beredskap
--- | --- | ---
**Start typ** | Azure har stöd för virtuella datorer med en start typ av BIOS, inte UEFI. | Villkorligt redo om start typen är UEFI
**Kärnor** | Varje dator får inte ha fler än 128 kärnor, vilket är det högsta antalet som en virtuell Azure-dator stöder.<br/><br/> Om prestanda historiken är tillgänglig, Azure Migrate beakta de använda kärnorna för jämförelse. Om utvärderings inställningarna anger en bekvämlighets faktor multipliceras antalet använda kärnor av den praktiska faktorn.<br/><br/> Om det inte finns någon prestanda historik använder Azure Migrate de allokerade kärnorna för att tillämpa komfort faktorn. | Redo om antalet kärnor ligger inom gränsen
**Mycket** | Varje dator får inte ha mer än 3 892 GB RAM-minne, vilket är den maximala storleken som en Azure M-serie Standard_M128m &nbsp; <sup>2</sup> VM stöder. [Läs mer](../virtual-machines/sizes.md).<br/><br/> Om prestanda historiken är tillgänglig kan Azure Migrate anses använda RAM-minne för jämförelse. Om en bekvämlighets faktor anges multipliceras det utnyttjade RAM-minnet av den praktiska faktorn.<br/><br/> Om det inte finns någon historik används det allokerade RAM-minnet för att tillämpa en bekvämlighets faktor.<br/><br/> | Klar om mängden RAM-minne ligger inom gränsen
**Lagrings disk** | Den allokerade storleken på en disk får inte överstiga 32 TB. Även om Azure har stöd för 64 TB-diskar med Azure Ultra SSD-diskar, Azure Migrate: Server utvärderingen söker efter 32 TB som disk storleks gräns eftersom den inte har stöd för Ultra SSD än. <br/><br/> Antalet diskar som är anslutna till datorn, inklusive OS-disken, måste vara 65 eller färre. | Redo om diskens storlek och antalet ligger inom gränserna
**Nätverk** | En dator får inte ha fler än 32 nätverks gränssnitt (NIC) anslutna till sig. | Redo om antalet nätverkskort ligger inom gränsen

### <a name="guest-operating-system"></a>Gästoperativsystem

För en Azure VM-utvärdering, tillsammans med att granska VM-egenskaper, tittar Server bedömning på gäst operativ systemet på en dator för att avgöra om den kan köras på Azure.

> [!NOTE]
> För att hantera gäst analys för virtuella VMware-datorer använder Server utvärderingen det operativ system som har angetts för den virtuella datorn i vCenter Server. VCenter Server tillhandahåller dock inte kernel-versionen för operativ system för virtuella Linux-datorer. För att identifiera versionen måste du konfigurera [program identifiering](./how-to-discover-applications.md). Sedan identifierar installations programmet versions information med de autentiseringsuppgifter som du anger när du konfigurerar app-Discovery.


Server utvärderingen använder följande logik för att identifiera Azure-beredskap baserat på operativ systemet:

**Operativsystem** | **Information** | **Status för Azure-beredskap**
--- | --- | ---
Windows Server 2016 och alla SPs | Azure ger fullständig support. | Redo för Azure.
Windows Server 2012 R2 och alla SPs | Azure ger fullständig support. | Redo för Azure.
Windows Server 2012 och alla SPs | Azure ger fullständig support. | Redo för Azure.
Windows Server 2008 R2 med alla SPs | Azure ger fullständig support.| Redo för Azure.
Windows Server 2008 (32-bitars och 64-bitars) | Azure ger fullständig support. | Redo för Azure.
Windows Server 2003 och Windows Server 2003 R2 | Dessa operativ system har passerat sina slut för ande support datum och behöver ett [anpassat support avtal (CSA)](https://aka.ms/WSosstatement) för support i Azure. | Villkorligt redo för Azure. Överväg att uppgradera operativ systemet innan du migrerar till Azure.
Windows 2000, Windows 98, Windows 95, Windows NT, Windows 3,1 och MS-DOS | Dessa operativ system har passerat sina slut för ande support datum. Datorn kan starta i Azure, men Azure ger inget operativ system stöd. | Villkorligt redo för Azure. Vi rekommenderar att du uppgraderar operativ systemet innan du migrerar till Azure.
Windows 7, Windows 8 och Windows 10 | Azure har endast stöd för en [Visual Studio-prenumeration.](../virtual-machines/windows/client-images.md) | Villkorligt redo för Azure.
Windows 10 Pro | Azure har stöd för [värd rättigheter för flera innehavare.](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md) | Villkorligt redo för Azure.
Windows Vista och Windows XP Professional | Dessa operativ system har passerat sina slut för ande support datum. Datorn kan starta i Azure, men Azure ger inget operativ system stöd. | Villkorligt redo för Azure. Vi rekommenderar att du uppgraderar operativ systemet innan du migrerar till Azure.
Linux | Se [Linux-operativsystemen](../virtual-machines/linux/endorsed-distros.md) som Azure har godkänt. Andra Linux-operativsystem kan starta i Azure. Men vi rekommenderar att du uppgraderar operativ systemet till en avsignerad version innan du migrerar till Azure. | Redo för Azure om versionen är påtecknad.<br/><br/>Villkorligt klar om versionen inte har påbörjats.
Andra operativ system som Oracle Solaris, Apple macOS och FreeBSD | Azure förser inte dessa operativ system. Datorn kan starta i Azure, men Azure ger inget operativ system stöd. | Villkorligt redo för Azure. Vi rekommenderar att du installerar ett operativ system som stöds innan du migrerar till Azure.  
Operativ systemet har angetts som **annat** i vCenter Server | Azure Migrate kan inte identifiera operativ systemet i det här fallet. | Okänd beredskap. Se till att Azure har stöd för operativ systemet som körs i den virtuella datorn.
32-bitars operativ system | Datorn kan starta i Azure, men Azure kanske inte ger fullständig support. | Villkorligt redo för Azure. Överväg att uppgradera till ett 64-bitars operativ system innan du migrerar till Azure.

## <a name="calculating-sizing"></a>Beräknar storlek

När datorn har marker ATS som klar för Azure, gör Server utvärderingen storleks rekommendationer i Azure VM-utvärderingen. Dessa rekommendationer identifierar den virtuella Azure-datorn och disk-SKU: n. Storleks beräkningar beror på om du använder lokal storlek eller prestandabaserade storleks ändringar.

### <a name="calculate-sizing-as-is-on-premises"></a>Beräkna storlek (lokalt)

 Om du använder en lokal storleks förändring, beaktar inte Server utvärderingen prestanda historiken för de virtuella datorerna och diskarna i Azure VM-utvärderingen.

- **Beräknings storlek**: Server utvärderingen allokerar en Azure VM-SKU baserat på den storlek som tilldelas lokalt.
- **Storlek för lagring och disk storlek**: Server utvärderingen söker efter den lagrings typ som anges i bedömnings egenskaperna och rekommenderar lämplig disktyp. Möjliga lagrings typer är Standard HDD, Standard SSD och Premium. Standard lagrings typen är Premium.
- **Nätverks storlek**: Server utvärderingen tar hänsyn till nätverkskortet på den lokala datorn.

### <a name="calculate-sizing-performance-based"></a>Beräkna storlek (prestanda-baserad)

Om du använder prestandabaserade storleks ändringar i en Azure VM-utvärdering, gör Server utvärderingen storleks rekommendationer enligt följande:

- Server utvärderingen beaktar datorns prestanda historik för att identifiera VM-storlek och disk typ i Azure.
- Om du importerar servrar med hjälp av en CSV-fil används de värden du anger. Den här metoden är särskilt användbar om du har överbelagt den lokala datorn, om användningen är låg och du vill hitta rätt storlek den virtuella Azure-datorn för att spara kostnader.
- Om du inte vill använda prestanda data återställer du storleks kriterierna till som-är lokalt, enligt beskrivningen i föregående avsnitt.

#### <a name="calculate-storage-sizing"></a>Beräkna lagrings storlek

För lagrings storlek i en Azure VM-utvärdering försöker Azure Migrate mappa varje disk som är ansluten till datorn till en Azure-disk. Storleks ändringar fungerar på följande sätt:

1. Server utvärderingen lägger till Läs-och skriv-IOPS för en disk för att få total IOPS som krävs. På samma sätt lägger den till Skriv-och Skriv data flödes värden för att hämta det totala data flödet för varje disk. Om det gäller importbaserade utvärderingar kan du välja att tillhandahålla total IOPS, totalt genomflöde och totalt antal. av diskar i den importerade filen utan att ange enskilda disk inställningar. Om du gör detta hoppas den enskilda disk storleken över och de angivna data används direkt för att beräkna storlek och välja en lämplig VM-SKU.

1. Om du har angett lagrings typen som automatisk, baseras den valda typen på effektiva IOPS-och data flödes värden. Server utvärderingen avgör om disken ska mappas till en Standard HDD-, Standard SSD-eller Premium-disk i Azure. Om lagrings typen har angetts till någon av dessa disk typer försöker server utvärderingen hitta en disk-SKU i den valda lagrings typen.
1. Diskarna är markerade enligt följande:
    - Om Server utvärderingen inte kan hitta en disk med nödvändiga IOPS och data flöde markerar den datorn som olämplig för Azure.
    - Om Server utvärderingen hittar en uppsättning lämpliga diskar väljer den de diskar som har stöd för den plats som anges i utvärderings inställningarna.
    - Om det finns flera tillgängliga diskar väljer Server utvärderingen disken med den lägsta kostnaden.
    - Om prestanda data för en disk inte är tillgänglig används konfigurations diskens storlek för att hitta en Standard SSD disk i Azure.

#### <a name="calculate-network-sizing"></a>Beräkna nätverks storlek

För en Azure VM-utvärdering försöker server utvärderingen hitta en virtuell Azure-dator som stöder antalet och nödvändiga prestanda för nätverkskort som är anslutna till den lokala datorn.

- För att få effektiv nätverks prestanda för den lokala virtuella datorn sammanställer Server utvärderingen data överförings hastigheten från datorn (nätverket) över alla nätverkskort. Den använder sedan den praktiska faktorn. Den använder det resulterande värdet för att hitta en virtuell Azure-dator som har stöd för den nödvändiga nätverks prestandan.
- Tillsammans med nätverks prestanda anser Server utvärderingen även om den virtuella Azure-datorn har stöd för det antal nätverkskort som krävs.
- Om prestanda data för nätverket inte är tillgängliga, tar Server utvärderingen bara antalet nätverkskort för storleks ändring av virtuella datorer.

#### <a name="calculate-compute-sizing"></a>Beräkna beräknings storlek

När lagrings-och nätverks kraven har beräknats, tar Server utvärderingen hänsyn till processor-och RAM-krav för att hitta en lämplig VM-storlek i Azure.

- Azure Migrate tittar på effektiva använda kärnor och RAM-minne för att hitta en lämplig storlek på virtuella Azure-datorer.
- Om ingen lämplig storlek hittas markeras datorn som olämplig för Azure.
- Om en lämplig storlek hittas använder Azure Migrate beräkningarna för lagring och nätverk. Sedan tillämpas inställningarna för plats-och pris nivå för den slutliga rekommendationen för VM-storlek.
- Om det finns flera tillgängliga Azure VM-storlekar rekommenderas den billigaste.

## <a name="confidence-ratings-performance-based"></a>Tillförlitlighets klassificering (prestanda-baserad)

Varje prestanda-baserad Azure VM-utvärdering i Azure Migrate associeras med en förtroende klassificering. Omdömet sträcker sig från en (lägsta) till fem (högsta) stjärnor. Förtroende omdömet hjälper dig att beräkna tillförlitligheten för storleks rekommendationer Azure Migrate tillhandahåller.

- Förtroende klassificeringen tilldelas en utvärdering. Omdömet baseras på tillgängligheten för data punkter som behövs för att beräkna utvärderingen.
- För prestanda-baserad storleks kontroll behöver Server utvärderingen:
    - Användnings data för CPU och RAM för virtuella datorer.
    - Disk-IOPS och data flödes data för varje disk som är ansluten till den virtuella datorn.
    - Nätverks-I/O för att hantera prestandabaserade storleks ändringar för varje nätverkskort som är kopplat till en virtuell dator.

Om något av dessa användnings nummer inte är tillgängligt kan storleks rekommendationerna vara otillförlitliga.

> [!NOTE]
> Förtroende klassificeringar är inte tilldelade för servrar som utvärderas med hjälp av en importerad CSV-fil. Klassificeringarna gäller inte heller för den lokala utvärderingen.

### <a name="ratings"></a>Klassificeringar

I den här tabellen visas klassificeringen av bedömning av säkerhet, vilket beror på procent andelen tillgängliga data punkter:

   **Tillgänglighet för datapunkter** | **Säkerhetsomdöme**
   --- | ---
   0-20% | 1 stjärna
   21-40% | 2 stjärnor
   41-60% | 3 stjärnor
   61-80% | 4 stjärnor
   81-100% | 5 stjärnor

### <a name="low-confidence-ratings"></a>Värderingar med låg exakthet

Här följer några skäl till varför en utvärdering kan få en låg exakthet:

- Du har inte profilerat din miljö under den tid som du skapar utvärderingen. Om du till exempel skapar utvärderingen med varaktigheten inställd på en dag måste du vänta minst en dag efter att du har startat identifieringen för alla data punkter som ska samlas in.
- Vissa virtuella datorer stängdes av under den tid då utvärderingen beräknades. Om några virtuella datorer är inaktiverade under en viss tid kan Server utvärderingen inte samla in prestanda data för den perioden.
- Vissa virtuella datorer skapades under den tid då utvärderingen beräknades. Anta till exempel att du har skapat en utvärdering för prestanda historiken för den senaste månaden, men vissa virtuella datorer skapades bara för en vecka sedan. Prestanda historiken för de nya virtuella datorerna finns inte för den fullständiga varaktigheten.

> [!NOTE]
> Om en utvärderings grad är mindre än fem stjärnor rekommenderar vi att du väntar minst en dag på att installationen ska profilera miljön och sedan beräkna utvärderingen på annat sätt. Annars kan prestandabaserade storleks ändringar vara otillförlitliga. I så fall rekommenderar vi att du växlar utvärderingen till lokal storlek.

## <a name="calculate-monthly-costs"></a>Beräkna månads kostnader

När utvärderings rekommendationerna har slutförts beräknar en Azure VM-utvärdering i Azure Migrate beräknings-och lagrings kostnader för efter migreringen.

- **Beräknings kostnad**: Azure Migrate använder den rekommenderade storleken på virtuella Azure-datorer och Azure-fakturerings-API: n för att beräkna månads kostnaden för den virtuella datorn.

    Beräkningen tar hänsyn till:
    - Operativsystem
    - Software Assurance
    - Reserverade instanser
    - VM-drifttid
    - Plats
    - Valuta inställningar

    Server utvärderingen sammanställer kostnaden på alla datorer för att beräkna den totala månads beräknings kostnaden.

- **Lagrings kostnad**: kostnaden för den månatliga lagringen för en dator beräknas genom att aggregera den månatliga kostnaden för alla diskar som är anslutna till datorn.

    Server utvärderingen beräknar den totala lagrings kostnaden per månad genom att aggregera lagrings kostnaderna för alla datorer. För närvarande anser beräkningen inte några erbjudanden som anges i utvärderings inställningarna.

Kostnaderna visas i den valuta som anges i utvärderings inställningarna.

## <a name="next-steps"></a>Nästa steg

[Granska](best-practices-assessment.md) Metod tips för att skapa utvärderingar. 

- Lär dig mer om att köra utvärderingar för [virtuella VMware-datorer](tutorial-prepare-vmware.md), [virtuella Hyper-V-datorer](tutorial-prepare-hyper-v.md)och [fysiska servrar](tutorial-prepare-physical.md).
- Lär dig mer om att utvärdera servrar [som importer ATS med en CSV-fil](tutorial-assess-import.md).
- Lär dig mer om att konfigurera [beroende visualisering](concepts-dependency-visualization.md).