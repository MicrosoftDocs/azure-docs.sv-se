---
title: Utvärderingar av virtuella Azure-datorer i Azure Migrate
description: Lär dig mer om utvärderingar i Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 7d756b53247206ab4dd4f955c954e6bd105afa1d
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778499"
---
# <a name="assessment-overview-migrate-to-azure-vms"></a>Översikt över utvärdering (migrera till virtuella Azure-datorer)

Den här artikeln innehåller en översikt över utvärderingar i verktyget [Azure Migrate: Server identifiering och bedömning](migrate-services-overview.md) . Verktyget kan utvärdera lokala servrar i VMware Virtual och Hyper-V-miljön och fysiska servrar för migrering till Azure.

## <a name="whats-an-assessment"></a>Vad är en utvärdering?

En utvärdering med verktyget för identifiering och bedömning mäter beredskap och uppskattar effekterna av att migrera lokala servrar till Azure.

> [!NOTE]
> I Azure Government granskar du platser som [stöds för mål](migrate-support-matrix.md#supported-geographies-azure-government) utvärdering. Observera att rekommendationer för VM-storlek i utvärderingar kommer att använda VM-serien specifikt för myndigheter i den offentliga sektorn. [Läs mer](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) om VM-typer.

## <a name="types-of-assessments"></a>Typer av utvärderingar

Det finns tre typer av utvärderingar som du kan skapa med hjälp av Azure Migrate: identifiering och bedömning.

***Bedömnings typ** | **Information**
--- | --- 
**Azure VM** | Utvärderingar som migrerar dina lokala servrar till virtuella Azure-datorer. Du kan utvärdera dina lokala servrar i [VMware](how-to-set-up-appliance-vmware.md) [-och Hyper-V-](how-to-set-up-appliance-hyper-v.md) miljön och [fysiska servrar](how-to-set-up-appliance-physical.md) för migrering till virtuella Azure-datorer med den här utvärderings typen.
**Azure SQL** | Utvärderingar för att migrera dina lokala SQL-servrar från din VMware-miljö till Azure SQL Database eller Azure SQL-hanterad instans.
**Azure VMware Solution (AVS)** | Utvärderingar för att migrera dina lokala servrar till [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). Du kan utvärdera dina lokala [virtuella VMware-datorer](how-to-set-up-appliance-vmware.md) för migrering till Azure VMware-lösningen (AVS) med den här utvärderings typen. [Läs mer](concepts-azure-vmware-solution-assessment-calculation.md)

Utvärderingar som du skapar med Azure Migrate är en tidpunkts ögonblicks bild av data. En utvärdering av virtuella Azure-datorer innehåller två alternativ för storleks ändring:

**Bedömnings typ** | **Information** | **Data**
--- | --- | ---
**Prestandabaserad** | Utvärderingar som gör rekommendationer baserat på insamlade prestandadata | Den virtuella datorns storleks rekommendation baseras på processor-och RAM användnings data.<br/><br/> Rekommendationen för disk typen baseras på de indata/utdata-åtgärder per sekund (IOPS) och data flödet för de lokala diskarna. Disk typer är Azure Standard HDD, Azure Standard SSD och Azure Premium-diskar.
**Som lokalt** | Utvärderingar som inte använder prestanda data för att göra rekommendationer | Den virtuella datorns storleks rekommendation baseras på den lokala server storleken.<br/><br> Den rekommenderade disk typen baseras på den valda lagrings typen för utvärderingen.

## <a name="how-do-i-run-an-assessment"></a>Hur gör jag för att köra en utvärdering?

Det finns ett par olika sätt att köra en utvärdering.

- Utvärdera servrar med hjälp av Server-metadata som samlats in av en förenklad Azure Migrate-installation. Enheten identifierar lokala servrar. Den skickar sedan serverns metadata och prestanda data till Azure Migrate.
- Utvärdera servrar med hjälp av serverns metadata som importeras i CSV-format (kommaavgränsade värden).

## <a name="how-do-i-assess-with-the-appliance"></a>Hur gör jag för att du utvärdera med produkten?

Gör så här om du distribuerar en Azure Migrate-enhet för att identifiera lokala servrar:

1. Konfigurera Azure och din lokala miljö för att arbeta med Azure Migrate.
1. Skapa ett Azure-projekt för din första utvärdering och Lägg till identifierings-och utvärderings verktyget i det.
1. Distribuera en förenklad Azure Migrate-apparat. Enheten identifierar kontinuerligt lokala servrar och skickar metadata och prestanda data till Azure Migrate. Distribuera installationen som en virtuell dator eller fysisk server. Du behöver inte installera något på de servrar som du vill utvärdera.

När installationen påbörjat Server identifiering kan du samla in servrar som du vill utvärdera i en grupp och köra en utvärdering av gruppen med utvärderings typ **Azure VM**.

Följ våra självstudier för [VMware](./tutorial-discover-vmware.md), [Hyper-V](./tutorial-discover-hyper-v.md)eller [fysiska servrar](./tutorial-discover-physical.md) för att prova de här stegen.

## <a name="how-do-i-assess-with-imported-data"></a>Hur gör jag för att utvärdering med importerade data?

Om du utvärderar servrar med hjälp av en CSV-fil behöver du inte en installation. Gör i stället följande steg:

1. Konfigurera Azure för att arbeta med Azure Migrate
1. Skapa ett Azure-projekt för din första utvärdering och Lägg till identifierings-och utvärderings verktyget i det.
1. Ladda ned en CSV-mall och Lägg till Server data till den.
1. Importera mallen till Azure Migrate
1. Identifiera servrar som har lagts till med importen, samla in dem i en grupp och kör en utvärdering för gruppen med utvärderings typ **Azure VM**.

## <a name="what-data-does-the-appliance-collect"></a>Vilka data samlas in av enheten?

Om du använder Azure Migrate-enheten för utvärdering kan du läsa om de metadata och prestanda data som samlas in för [VMware](migrate-appliance.md#collected-data---vmware) och [Hyper-V](migrate-appliance.md#collected-data---hyper-v).

## <a name="how-does-the-appliance-calculate-performance-data"></a>Hur beräknar enheten prestanda data?

Om du använder installations programmet för identifiering samlar den in prestanda data för beräknings inställningar med följande steg:

1. Enheten samlar in en exempel punkt i real tid.

    - **Virtuella VMware-datorer**: en exempel punkt samlas in var 20: e sekund.
    - **Virtuella Hyper-V-datorer**: en exempel punkt samlas in var 30: e sekund.
    - **Fysiska servrar**: en exempel punkt samlas in var femte minut.

1. Enheten kombinerar exempel punkterna för att skapa en enda data punkt var tionde minut för VMware-och Hyper-V-servrar och var 5: e minut för fysiska servrar. För att skapa data punkten väljer enheten de högsta värdena från alla exempel. Sedan skickas data punkten till Azure.
1. Utvärderingen lagrar alla data punkter på 10 minuter för den senaste månaden.
1. När du skapar en utvärdering identifierar utvärderingen lämplig data punkt som ska användas för att göra rättigheter. Identifieringen baseras på percentilvärdet för *prestanda historik* och *percentils användning*.

    - Om prestanda historiken till exempel är en vecka och percentilvärdet använder 95 percentilen, sorterar utvärderingen 10-minuters exempel punkter för den senaste veckan. De sorterar dem i stigande ordning och plockar 95 percentilvärdet för att få behörighet.
    - Värdet 95 percentil ser till att du ignorerar eventuella avvikande värden, som kan inkluderas om du har valt 99 percentilen.
    - Om du vill välja högsta användnings nivå för perioden och inte vill missa avvikande värden väljer du den 99 percentilen för percentils användning.

1. Det här värdet multipliceras med den praktiska faktorn för att få den effektiva prestanda användnings informationen för de mått som installeras av enheten:

    - CPU-användning
    - RAM-användning
    - Disk-IOPS (läsning och skrivning)
    - Disk data flöde (läsning och skrivning)
    - Nätverks data flöde (in och ut)

## <a name="how-are-azure-vm-assessments-calculated"></a>Hur beräknas utvärdering av virtuella Azure-datorer?

Utvärderingen använder de lokala servrarnas metadata och prestanda data för att beräkna utvärderingar. Om du distribuerar Azure Migrate-enheten använder utvärderingen de data som samlas in av enheten. Men om du kör en utvärdering som importer ATS med en CSV-fil anger du metadata för beräkningen.

Beräkningar sker i dessa tre steg:

1. **Beräkna Azure-beredskap**: utvärdera om servrarna är lämpliga för migrering till Azure.
1. **Beräkna storleks rekommendationer**: beräkna beräkning, lagring och nätverks storlek.
1. **Beräkna månads kostnader**: beräkna de uppskattade kostnaderna för beräkning och lagring för att köra servrarna i Azure efter migreringen.

Beräkningarna sker i föregående ordning. En server Server flyttas till ett senare steg endast om den tidigare har passerat. Om en server till exempel inte klarar Azure readiness-fasen, är den markerad som olämplig för Azure. Storleks-och kostnads beräkningar utförs inte för den servern.

## <a name="whats-in-an-azure-vm-assessment"></a>Vad ingår i en Azure VM-utvärdering?

Här är what's som ingår i en Azure VM-utvärdering:

**Egenskap** | **Information**
--- | ---
**Målplats** | Den plats som du vill migrera till. Utvärderingen stöder för närvarande följande Azure-regioner:<br/><br/> Östra Australien, sydöstra Australien, södra Brasilien, centrala Kanada, Östra Kanada, centrala Indien, centrala USA, Kina, östra, Kina, norra, Asien, östra, östra USA, östra USA 2, centrala Tyskland, Tyskland nordöstra, Östra Japan, västra Japan, centrala Korea, centrala, norra centrala USA, norra Europa, södra centrala USA, Sydostasien, södra Indien, Storbritannien, södra, Storbritannien, västra US gov, Arizona , Västra centrala USA, Västeuropa, västra Indien, västra USA och västra USA 2.
**Mål lagrings disk (i storlek)** | Den typ av disk som ska användas för lagring i Azure. <br/><br/> Ange mål lagrings disken som Premium-hanterad, Standard SSD-hanterad eller Standard HDD-hanterad.
**Mål lagrings disk (prestanda baserad storlek)** | Anger typen av mål lagrings disk som automatisk, Premium-hanterad, Standard HDD-hanterad eller Standard SSD-hanterad.<br/><br/> **Automatisk**: disk rekommendationen baseras på diskens prestanda data, vilket innebär IOPS och data flöde.<br/><br/>**Premium eller standard**: utvärderingen rekommenderar en disk-SKU i den valda lagrings typen.<br/><br/> Överväg att använda Premium-hanterade diskar om du vill ha ett service nivå avtal (SLA) för en enskild instans på en virtuell dator med 99,9%. Den här användningen säkerställer att alla diskar i utvärderingen rekommenderas som Premium-hanterade diskar.<br/><br/> Azure Migrate stöder endast hanterade diskar för migrerings bedömning.
**Azure Reserved VM Instances** | Anger [reserverade instanser](https://azure.microsoft.com/pricing/reserved-vm-instances/) så att kostnads uppskattningar i utvärderingen tar dem i beaktande.<br/><br/> När du väljer reserverade instanser, rabatten (%) och egenskaperna för den virtuella datorns drift tid är inte tillämpliga.<br/><br/> Azure Migrate stöder för närvarande endast Azure Reserved VM Instances för erbjudanden med betala per användning.
**Storlekskriterier** | Används för att hitta rätt storlek den virtuella Azure-datorn.<br/><br/> Använd som-är storleks ändring eller prestanda-baserad storlek.
**Prestandahistorik** | Används med prestanda-baserad storlek. Prestanda historik anger den varaktighet som används när prestanda data utvärderas.
**Percentilutnyttjande** | Används med prestanda-baserad storlek. Percentils användning anger percentilvärdet för det prestanda exempel som används för att ha behörighet.
**VM-serie** | Den Azure VM-serie som du vill ta hänsyn till. Om du till exempel inte har en produktions miljö som behöver en-seriens virtuella datorer i Azure kan du undanta en-serien från listan över serier.
**Komfortfaktor** | Den buffert som användes under utvärderingen. Den används för processor-, RAM-, disk-och nätverks data för virtuella datorer. IT-konton för problem som säsongs användning, kort prestanda historik och sannolika ökningar i framtida användning.<br/><br/> Till exempel resulterar en virtuell dator med 10 kärnor med 20% användning vanligt vis i en virtuell dator med två kärnor. Med en bekvämlighets faktor på 2,0 är resultatet en virtuell dator med fyra kärnor i stället.
**Erbjudande** | Det [Azure-erbjudande](https://azure.microsoft.com/support/legal/offer-details/) som du har registrerat i. Utvärderingen beräknar kostnaden för det erbjudandet.
**Valuta** | Fakturerings valutan för ditt konto.
**Rabatt (%)** | Eventuella prenumerations rabatter som du får ovanpå Azure-erbjudandet. Standardinställningen är 0%.
**VM-drifttid** | Tiden i dagar per månad och timmar per dag för virtuella Azure-datorer som inte körs kontinuerligt. Kostnads uppskattningar baseras på den varaktigheten.<br/><br/> Standardvärdena är 31 dagar per månad och 24 timmar per dag.
**Azure Hybrid-förmån** | Anger om du har Software Assurance och är berättigade till [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Om inställningen har standardvärdet "Ja", betraktas Azure-priser för andra operativ system än Windows för virtuella Windows-datorer.
**EA-prenumeration** | Anger att en Enterprise-avtal-prenumeration (EA) används för kostnads uppskattning. Tar hänsyn till den rabatt som gäller för prenumerationen. <br/><br/> Lämna inställningarna för reserverade instanser, rabatt (%) och egenskaper för VM-drifts tid med standardinställningarna.


[Granska de bästa metoderna](best-practices-assessment.md) för att skapa en utvärdering med Azure Migrate.

## <a name="calculate-readiness"></a>Beräkna beredskap

Alla servrar är inte lämpliga att köra i Azure. En Azure VM-utvärdering bedömer alla lokala servrar och tilldelar dem en beredskaps kategori.

- **Redo för Azure**: servern kan migreras som den är till Azure utan några ändringar. Det kommer att starta i Azure med fullständig support för Azure.
- **Villkorligt redo för Azure**: servern kan starta i Azure men kanske inte har fullständig support för Azure. Azure stöder till exempel inte en server som kör en tidigare version av Windows Server. Du måste vara försiktig innan du migrerar de här servrarna till Azure. Du kan åtgärda eventuella beredskaps problem genom att följa den vägledning som utvärderingen föreslår.
- **Inte redo för Azure**: servern startar inte i Azure. Om till exempel en lokal servers disk lagrar mer än 64 TB, kan inte Azure vara värd för servern. Följ reparations vägledningen för att åtgärda problemet innan du migrerar.
- **Beredskap okänd**: Azure Migrate kan inte fastställa serverns beredskap på grund av otillräckliga metadata.

För att beräkna beredskap granskar utvärderingen de Server egenskaper och operativ system inställningar som sammanfattas i följande tabeller.

### <a name="server-properties"></a>Serveregenskaper

För en Azure VM-utvärdering granskar utvärderingen följande egenskaper för en lokal virtuell dator för att avgöra om den kan köras på virtuella Azure-datorer.

Egenskap | Information | Status för Azure-beredskap
--- | --- | ---
**Start typ** | Azure har stöd för virtuella datorer med en start typ av BIOS, inte UEFI. | Villkorligt redo om start typen är UEFI
**Kärnor** | Varje server får inte ha fler än 128 kärnor, vilket är det högsta antalet som en virtuell Azure-dator stöder.<br/><br/> Om prestanda historiken är tillgänglig, Azure Migrate beakta de använda kärnorna för jämförelse. Om utvärderings inställningarna anger en bekvämlighets faktor multipliceras antalet använda kärnor av den praktiska faktorn.<br/><br/> Om det inte finns någon prestanda historik använder Azure Migrate de allokerade kärnorna för att tillämpa komfort faktorn. | Redo om antalet kärnor ligger inom gränsen
**Mycket** | Varje server får inte ha mer än 3 892 GB RAM-minne, vilket är den maximala storleken som en Azure M-serie Standard_M128m &nbsp; <sup>2</sup> VM stöder. [Läs mer](../virtual-machines/sizes.md).<br/><br/> Om prestanda historiken är tillgänglig kan Azure Migrate anses använda RAM-minne för jämförelse. Om en bekvämlighets faktor anges multipliceras det utnyttjade RAM-minnet av den praktiska faktorn.<br/><br/> Om det inte finns någon historik används det allokerade RAM-minnet för att tillämpa en bekvämlighets faktor.<br/><br/> | Klar om mängden RAM-minne ligger inom gränsen
**Lagrings disk** | Den allokerade storleken på en disk får inte överstiga 32 TB. Även om Azure har stöd för 64 TB-diskar med Azure Ultra SSD-diskar, kontrollerar utvärderingen för närvarande att det finns 32 TB som disk storleks begränsning eftersom den inte har stöd för Ultra SSD än. <br/><br/> Antalet diskar som är anslutna till servern, inklusive OS-disken, måste vara 65 eller färre. | Redo om diskens storlek och antalet ligger inom gränserna
**Nätverk** | En server får inte ha fler än 32 nätverks gränssnitt (NIC) anslutna till sig. | Redo om antalet nätverkskort ligger inom gränsen

### <a name="guest-operating-system"></a>Gästoperativsystem

För en Azure VM-utvärdering, tillsammans med att granska VM-egenskaper, tittar utvärderingen på gäst operativ systemet på en server för att avgöra om den kan köras på Azure.

> [!NOTE]
> För att hantera gäst analys för virtuella VMware-datorer använder utvärderingen det operativ system som anges för den virtuella datorn i vCenter Server. VCenter Server tillhandahåller dock inte kernel-versionen för operativ system för virtuella Linux-datorer. För att identifiera versionen måste du konfigurera [program identifiering](./how-to-discover-applications.md). Sedan identifierar installations programmet versions information med de autentiseringsuppgifter som du anger när du konfigurerar app-Discovery.


I utvärderingen används följande logik för att identifiera Azure-beredskap baserat på operativ systemet:

**Operativsystem** | **Information** | **Status för Azure-beredskap**
--- | --- | ---
Windows Server 2016 och alla SPs | Azure ger fullständig support. | Redo för Azure.
Windows Server 2012 R2 och alla SPs | Azure ger fullständig support. | Redo för Azure.
Windows Server 2012 och alla SPs | Azure ger fullständig support. | Redo för Azure.
Windows Server 2008 R2 med alla SPs | Azure ger fullständig support.| Redo för Azure.
Windows Server 2008 (32-bitars och 64-bitars) | Azure ger fullständig support. | Redo för Azure.
Windows Server 2003 och Windows Server 2003 R2 | Dessa operativ system har passerat sina slut för ande support datum och behöver ett [anpassat support avtal (CSA)](/troubleshoot/azure/virtual-machines/server-software-support) för support i Azure. | Villkorligt redo för Azure. Överväg att uppgradera operativ systemet innan du migrerar till Azure.
Windows 2000, Windows 98, Windows 95, Windows NT, Windows 3,1 och MS-DOS | Dessa operativ system har passerat sina slut för ande support datum. Servern kan starta i Azure, men Azure ger inget operativ system stöd. | Villkorligt redo för Azure. Vi rekommenderar att du uppgraderar operativ systemet innan du migrerar till Azure.
Windows 7, Windows 8 och Windows 10 | Azure har endast stöd för en [Visual Studio-prenumeration.](../virtual-machines/windows/client-images.md) | Villkorligt redo för Azure.
Windows 10 Pro | Azure har stöd för [värd rättigheter för flera innehavare.](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md) | Villkorligt redo för Azure.
Windows Vista och Windows XP Professional | Dessa operativ system har passerat sina slut för ande support datum. Servern kan starta i Azure, men Azure ger inget operativ system stöd. | Villkorligt redo för Azure. Vi rekommenderar att du uppgraderar operativ systemet innan du migrerar till Azure.
Linux | Se [Linux-operativsystemen](../virtual-machines/linux/endorsed-distros.md) som Azure har godkänt. Andra Linux-operativsystem kan starta i Azure. Men vi rekommenderar att du uppgraderar operativ systemet till en avsignerad version innan du migrerar till Azure. | Redo för Azure om versionen är påtecknad.<br/><br/>Villkorligt klar om versionen inte har påbörjats.
Andra operativ system som Oracle Solaris, Apple macOS och FreeBSD | Azure förser inte dessa operativ system. Servern kan starta i Azure, men Azure ger inget operativ system stöd. | Villkorligt redo för Azure. Vi rekommenderar att du installerar ett operativ system som stöds innan du migrerar till Azure.  
Operativ systemet har angetts som **annat** i vCenter Server | Azure Migrate kan inte identifiera operativ systemet i det här fallet. | Okänd beredskap. Se till att Azure har stöd för operativ systemet som körs i den virtuella datorn.
32-bitars operativ system | Servern kan starta i Azure, men Azure kanske inte ger fullständig support. | Villkorligt redo för Azure. Överväg att uppgradera till ett 64-bitars operativ system innan du migrerar till Azure.

## <a name="calculating-sizing"></a>Beräknar storlek

När servern har marker ATS som klar för Azure, gör utvärderingen storleks rekommendationer i Azure VM-utvärderingen. Dessa rekommendationer identifierar den virtuella Azure-datorn och disk-SKU: n. Storleks beräkningar beror på om du använder lokal storlek eller prestandabaserade storleks ändringar.

### <a name="calculate-sizing-as-is-on-premises"></a>Beräkna storlek (lokalt)

 Om du använder en lokal storleks förändring, beaktar inte utvärderingen prestanda historiken för de virtuella datorerna och diskarna i Azure VM-utvärderingen.

- **Beräknings storlek**: utvärderingen tilldelar en Azure VM-SKU baserat på den storlek som tilldelas lokalt.
- **Storlek på lagring och disk storlek**: utvärderingen tittar på den lagrings typ som anges i bedömnings egenskaperna och rekommenderar lämplig disk typ. Möjliga lagrings typer är Standard HDD, Standard SSD och Premium. Standard lagrings typen är Premium.
- **Nätverks storlek**: utvärderingen tar hänsyn till nätverkskortet på den lokala servern.

### <a name="calculate-sizing-performance-based"></a>Beräkna storlek (prestanda-baserad)

Om du använder prestandabaserade storleks ändringar i en Azure VM-utvärdering, gör utvärderingen storleks rekommendationer enligt följande:

- Utvärderingen tar hänsyn till serverns prestanda historik för att identifiera VM-storlek och disk typ i Azure.
- Om du importerar servrar med hjälp av en CSV-fil används de värden du anger. Den här metoden är särskilt användbar om du har överbelagt den lokala servern, om användningen är låg och du vill hitta rätt storlek den virtuella Azure-datorn för att spara kostnader.
- Om du inte vill använda prestanda data återställer du storleks kriterierna till som-är lokalt, enligt beskrivningen i föregående avsnitt.

#### <a name="calculate-storage-sizing"></a>Beräkna lagrings storlek

För lagrings storlek i en Azure VM-utvärdering försöker Azure Migrate mappa varje disk som är kopplad till servern till en Azure-disk. Storleks ändringar fungerar på följande sätt:

1. Utvärderingen lägger till Läs-och skriv-IOPS för en disk för att hämta total IOPS som krävs. På samma sätt lägger den till Skriv-och Skriv data flödes värden för att hämta det totala data flödet för varje disk. Om det gäller importbaserade utvärderingar kan du välja att tillhandahålla total IOPS, totalt genomflöde och totalt antal. av diskar i den importerade filen utan att ange enskilda disk inställningar. Om du gör detta hoppas den enskilda disk storleken över och de angivna data används direkt för att beräkna storlek och välja en lämplig VM-SKU.

1. Om du har angett lagrings typen som automatisk, baseras den valda typen på effektiva IOPS-och data flödes värden. Utvärderingen avgör om disken ska mappas till en Standard HDD-, Standard SSD-eller Premium-disk i Azure. Om lagrings typen är inställd på någon av dessa disk typer försöker utvärderingen hitta en disk-SKU i den valda lagrings typen.
1. Diskarna är markerade enligt följande:
    - Om utvärderingen inte kan hitta en disk med nödvändiga IOPS och data flöde, markerar den servern som olämplig för Azure.
    - Om utvärderingen hittar en uppsättning lämpliga diskar väljer den de diskar som har stöd för den plats som anges i utvärderings inställningarna.
    - Om det finns flera tillgängliga diskar väljer utvärderingen disken med den lägsta kostnaden.
    - Om prestanda data för en disk inte är tillgänglig används konfigurations diskens storlek för att hitta en Standard SSD disk i Azure.

#### <a name="calculate-network-sizing"></a>Beräkna nätverks storlek

Vid en Azure VM-utvärdering försöker utvärderingen hitta en virtuell Azure-dator som stöder antalet och nödvändiga prestanda för nätverkskort som är anslutna till den lokala servern.

- För att få effektiv nätverks prestanda för den lokala servern, sammanställer utvärderingen data överförings hastigheten från servern (nätverket) över alla nätverkskort. Den använder sedan den praktiska faktorn. Den använder det resulterande värdet för att hitta en virtuell Azure-dator som har stöd för den nödvändiga nätverks prestandan.
- Tillsammans med nätverks prestanda, anser utvärderingen även om den virtuella Azure-datorn kan stödja antalet nätverkskort som krävs.
- Om nätverks prestanda data inte är tillgängliga, tar utvärderingen bara hänsyn till antalet nätverkskort för storleks ändring av virtuella datorer.

#### <a name="calculate-compute-sizing"></a>Beräkna beräknings storlek

När lagrings-och nätverks kraven har beräknats, tar utvärderingen upp processor-och RAM-krav för att hitta en lämplig VM-storlek i Azure.

- Azure Migrate tittar på effektiva använda kärnor och RAM-minne för att hitta en lämplig storlek på virtuella Azure-datorer.
- Om ingen lämplig storlek hittas markeras servern som olämplig för Azure.
- Om en lämplig storlek hittas använder Azure Migrate beräkningarna för lagring och nätverk. Sedan tillämpas inställningarna för plats-och pris nivå för den slutliga rekommendationen för VM-storlek.
- Om det finns flera tillgängliga Azure VM-storlekar rekommenderas den billigaste.

## <a name="confidence-ratings-performance-based"></a>Tillförlitlighets klassificering (prestanda-baserad)

Varje prestanda-baserad Azure VM-utvärdering i Azure Migrate associeras med en förtroende klassificering. Omdömet sträcker sig från en (lägsta) till fem (högsta) stjärnor. Förtroende omdömet hjälper dig att beräkna tillförlitligheten för storleks rekommendationer Azure Migrate tillhandahåller.

- Förtroende klassificeringen tilldelas en utvärdering. Omdömet baseras på tillgängligheten för data punkter som behövs för att beräkna utvärderingen.
- För prestandabaserade storleks ändringar måste utvärderingen utföras:
    - Användnings data för CPU och RAM.
    - Disk-IOPS och data flödes data för varje disk som är ansluten till-servern.
    - Nätverks-I/O för att hantera prestandabaserade storleks ändringar för varje nätverkskort som är kopplat till en server.

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

- Du profilerade inte din miljö för hela den varaktighet för vilken du skapar utvärderingen. Om du till exempel skapar utvärderingen med varaktigheten inställd på en dag måste du vänta minst en dag efter att du har startat identifieringen för alla data punkter som ska samlas in.
- Utvärderingen kan inte samla in prestandadata för vissa eller alla servrar under utvärderingsperioden. För en hög exakthet bör du se till att: 
    - Servrarna är påslagna under utvärderings perioden
    - Utgående anslutningar på portarna 443 tillåts
    - För Hyper-V-servrar är dynamiskt minne aktiverat 
    
    Beräkna om utvärderingen så att de senaste ändringarna återspeglas i säkerhetsomdömet.

- Vissa servrar skapades under den tid då utvärderingen beräknades. Anta till exempel att du har skapat en utvärdering för prestanda historiken för den senaste månaden, men vissa servrar har endast skapats för en vecka sedan. I det här fallet är prestanda data för de nya servrarna inte tillgängliga under hela varaktigheten och förtroendet är lågt.

> [!NOTE]
> Om en utvärderings grad är mindre än fem stjärnor rekommenderar vi att du väntar minst en dag på att installationen ska profilera miljön och sedan beräkna utvärderingen på annat sätt. Annars kan prestandabaserade storleks ändringar vara otillförlitliga. I så fall rekommenderar vi att du växlar utvärderingen till lokal storlek.

## <a name="calculate-monthly-costs"></a>Beräkna månads kostnader

När utvärderings rekommendationerna har slutförts beräknar en Azure VM-utvärdering i Azure Migrate beräknings-och lagrings kostnader för efter migreringen.

- **Beräknings kostnad**: Azure Migrate använder den rekommenderade storleken på virtuella Azure-datorer och Azures fakturerings-API för att beräkna månads kostnaden för servern.

    Beräkningen tar hänsyn till:
    - Operativsystem
    - Software Assurance
    - Reserverade instanser
    - VM-drifttid
    - Location
    - Valuta inställningar

    Utvärderingen sammanställer kostnaden för alla servrar för att beräkna den totala månads beräknings kostnaden.

- **Lagrings kostnad**: den månatliga lagrings kostnaden för en server beräknas genom att aggregera månads kostnaden för alla diskar som är kopplade till servern.

    Utvärderingen beräknar den totala lagrings kostnaden per månad genom att aggregera lagrings kostnaderna för alla servrar. För närvarande anser beräkningen inte några erbjudanden som anges i utvärderings inställningarna.

Kostnaderna visas i den valuta som anges i utvärderings inställningarna.

## <a name="next-steps"></a>Nästa steg

[Granska](best-practices-assessment.md) Metod tips för att skapa utvärderingar. 

- Lär dig mer om att köra utvärderingar för servrar som körs i [VMware](./tutorial-discover-vmware.md) [-och Hyper-V- ](./tutorial-discover-hyper-v.md) miljön och [fysiska servrar](./tutorial-discover-physical.md).
- Lär dig mer om att utvärdera servrar [som importer ATS med en CSV-fil](./tutorial-discover-import.md).
- Lär dig mer om att konfigurera [beroende visualisering](concepts-dependency-visualization.md).