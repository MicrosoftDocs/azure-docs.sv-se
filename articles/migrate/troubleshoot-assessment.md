---
title: Felsök utvärderings-och beroende visualisering i Azure Migrate
description: Få hjälp med fel sökning av utvärdering och beroende visualisering i Azure Migrate.
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: 24e7a1660da4dd021ef7ceb2594b4db2340cf104
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263035"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Felsöka utvärdering/beroendevisualisering

Den här artikeln hjälper dig att felsöka problem med utvärderings-och beroende visualisering med [Azure Migrate: Server utvärdering](migrate-services-overview.md#azure-migrate-server-assessment-tool).


## <a name="assessment-readiness-issues"></a>Problem med utvärderings beredskap

Åtgärda problem med utvärderings beredskap enligt följande:

**Problem** | **Åtgärda**
--- | ---
Start typen stöds inte | Azure har inte stöd för virtuella datorer med en EFI-starttyp. Vi rekommenderar att du konverterar start typen till BIOS innan du kör en migrering. <br/><br/>Du kan använda migrering av Azure Migrate Server för att hantera migrering av sådana virtuella datorer. Den kommer att konvertera start typen för den virtuella datorn till BIOS under migreringen.
Villkorligt Windows-operativsystem som stöds | Operativ systemet har passerat sitt slutdatum och måste ha ett anpassat support avtal (CSA) för [support i Azure](https://aka.ms/WSosstatement). Överväg att uppgradera innan du migrerar till Azure.
Windows-operativsystem som inte stöds | Azure stöder endast [valda Windows OS-versioner](https://aka.ms/WSosstatement). Överväg att uppgradera datorn innan du migrerar till Azure.
Villkorligt godkänt Linux OS | Azure har endast godkänt [valda Linux OS-versioner](../virtual-machines/linux/endorsed-distros.md). Överväg att uppgradera datorn innan du migrerar till Azure. Se även [här](https://docs.microsoft.com/azure/migrate/troubleshoot-assessment#linux-vms-are-conditionally-ready-in-an-azure-vm-assessment) för mer information.
Avsignerat Linux OS | Datorn kan starta i Azure, men Azure tillhandahåller inget stöd för operativ system. Överväg att uppgradera till en [godkänd Linux-version](../virtual-machines/linux/endorsed-distros.md) innan du migrerar till Azure.
Okänt operativ system | Operativ systemet för den virtuella datorn angavs som "Övrigt" i vCenter Server. Det här beteendet blockerar Azure Migrate från att verifiera den virtuella datorns Azure-beredskap. Kontrol lera att operativ systemet [stöds](https://aka.ms/azureoslist) av Azure innan du migrerar datorn.
Bit versionen stöds inte | Virtuella datorer med ett 32-bitars operativ system kan starta i Azure, men vi rekommenderar att du uppgraderar till 64-bit innan du migrerar till Azure.
Kräver en Microsoft Visual Studio-prenumeration | Datorn kör ett Windows-klient operativ system som bara stöds via en Visual Studio-prenumeration.
Den virtuella datorn hittades inte för lagrings prestanda som krävs | Lagrings prestandan (indata/utdata per sekund [IOPS] och data flöde) som krävs för datorn överskrider stöd för virtuella Azure-datorer. Minska lagrings kraven för datorn innan du migrerar.
Det gick inte att hitta den virtuella datorn för den nödvändiga nätverks prestandan | Nätverks prestandan (in/ut) som krävs för datorn överskrider stöd för virtuella Azure-datorer. Minska nätverks kraven för datorn.
Den virtuella datorn hittades inte på den angivna platsen | Använd en annan målplats innan migreringen.
En eller flera olämpliga diskar | En eller flera diskar som är anslutna till den virtuella datorn uppfyller inte kraven för Azure. En<br/><br/> Azure Migrate: Server utvärderingen stöder för närvarande inte Ultra SSD diskar och utvärderar diskarna baserat på disk gränserna för Premium Managed disks (32 TB).<br/><br/> För varje disk som är ansluten till den virtuella datorn ser du till att storleken på disken är < 64 TB (stöds av Ultra SSD diskar).<br/><br/> Om den inte är det kan du minska disk storleken innan du migrerar till Azure, eller använda flera diskar i Azure och [Stripa dem](../virtual-machines/windows/premium-storage-performance.md#disk-striping) för att få högre lagrings gränser. Kontrol lera att prestandan (IOPS och data flödet) som krävs för varje disk stöds av Azure- [hanterade virtuella dator diskar](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits).
Ett eller flera olämpliga nätverkskort. | Ta bort oanvända nätverkskort från datorn innan migreringen.
Antalet diskar överskrider gränsen | Ta bort oanvända diskar från datorn innan migreringen.
Disk storleken överskrider gränsen | Azure Migrate: Server utvärderingen stöder för närvarande inte Ultra SSD diskar och utvärderar diskarna baserat på Premium disk gränser (32 TB).<br/><br/> Azure stöder dock diskar med upp till 64 TB-storlek (stöds av Ultra SSD diskar). Minska diskar till mindre än 64 TB innan migrering, eller Använd flera diskar i Azure och dela upp [dem](../virtual-machines/windows/premium-storage-performance.md#disk-striping) för att få högre lagrings gränser.
Disken är inte tillgänglig på den angivna platsen | Kontrol lera att disken finns på mål platsen innan du migrerar.
Disken är inte tillgänglig för den angivna redundansen | Disken bör använda den redundans lagrings typ som definierats i utvärderings inställningarna (LRS som standard).
Det gick inte att fastställa diskens lämplighet på grund av ett internt fel | Försök att skapa en ny utvärdering för gruppen.
Det gick inte att hitta den virtuella datorn med nödvändiga kärnor och minne | Azure kunde inte hitta en lämplig VM-typ. Minska minnet och antalet kärnor för den lokala datorn innan du migrerar.
Det gick inte att fastställa VM-lämplighet på grund av ett internt fel | Försök att skapa en ny utvärdering för gruppen.
Det gick inte att fastställa lämplighet för en eller flera diskar på grund av ett internt fel | Försök att skapa en ny utvärdering för gruppen.
Det gick inte att fastställa lämplighet för ett eller flera nätverkskort på grund av ett internt fel | Försök att skapa en ny utvärdering för gruppen.
Ingen storlek för virtuell dator hittades för den reserverade instansen för erbjudande valutan | Datorn har marker ATS som ej lämplig eftersom det inte gick att hitta VM-storleken för den valda kombinationen av RI, erbjudande och valuta. Redigera bedömnings egenskaperna för att välja giltiga kombinationer och beräkna om utvärderingen. 
Villkorligt klar Internet Protocol | Gäller endast för Azure VMware Solution (AVS)-utvärderingar. AVS stöder inte IPv6 Internet adresss faktor.Kontakta AVS-teamet för att få vägledning om din dator identifieras med IPv6.

## <a name="suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>Föreslaget Migreringsverktyg i importerad AVS-utvärdering markerad som okänd

För datorer som importeras via en CSV-fil är standard verktyget för migrering i och AVS-utvärderingen okänt. För VMware-datorer rekommenderar vi dock att du använder HCX-lösningen (VMware Hybrid Cloud Extension). [Läs mer](../azure-vmware/hybrid-cloud-extension-installation.md).

## <a name="linux-vms-are-conditionally-ready-in-an-azure-vm-assessment"></a>Virtuella Linux-datorer är "villkorligt redo" i en Azure VM-utvärdering

När det gäller VMware-och Hyper-V-datorer markerar Server utvärderingen virtuella Linux-datorer som "villkorligt redo" på grund av ett känt mellanrum i Server utvärderingen. 

- Luckan förhindrar att den lägre versionen av Linux OS som är installerad på lokala virtuella datorer identifieras.
- För RHEL 6,10 identifieras till exempel bara RHEL 6 som operativ system version för för närvarande. Detta beror på att vCenter Server ar Hyper-V-värden inte tillhandahåller kernel-versionen för virtuella Linux-operativsystem.
-  Eftersom Azure bara godkänner vissa versioner av Linux är de virtuella Linux-datorerna för närvarande markerade som villkorligt klara i Server utvärderingen.
- Du kan avgöra om Linux-operativsystemet som körs på den lokala virtuella datorn har godkänts i Azure genom att granska [Azure Linux-supporten](https://aka.ms/migrate/selfhost/azureendorseddistros).
-  När du har verifierat den godkända distributionen kan du ignorera den här varningen.

Denna lucka kan åtgärdas genom att aktivera [program identifiering](./how-to-discover-applications.md) på de virtuella VMware-datorerna. Server Assessment använder operativsystemet som identifierats från den virtuella datorn med hjälp av de gästautentiseringsuppgifter som angetts. Detta operativ system data identifierar rätt operativ Systems information när det gäller virtuella Windows-och Linux-datorer.

## <a name="operating-system-version-not-available"></a>Operativ system versionen är inte tillgänglig

För fysiska servrar bör den lägre versions informationen för operativ systemet vara tillgänglig. Kontakta Microsoft Support om den inte är tillgänglig. För VMware-datorer använder Server utvärderingen den operativ Systems information som anges för den virtuella datorn i vCenter Server. VCenter Server tillhandahåller dock inte den lägre versionen av operativ system. För att identifiera den lägre versionen måste du konfigurera [program identifiering](./how-to-discover-applications.md). För virtuella Hyper-V-datorer stöds inte identifiering av lägre operativ system versioner. 

## <a name="azure-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Azure-SKU: er större än lokalt i en Azure VM-utvärdering

Azure Migrate Server utvärderingen kan rekommendera Azure VM SKU: er med fler kärnor och minne än den aktuella lokala allokeringen baserat på typen av utvärdering:

- Den virtuella datorns SKU-rekommendation beror på utvärderings egenskaperna.
- Detta påverkas av den typ av utvärdering som du utför i Server utvärderingen: *prestanda-baserad*eller *lokalt*.
- För prestandabaserade utvärderingar beaktar Server utvärderingen användnings data för de lokala virtuella datorerna (CPU, minne, disk och nätverks användning) för att fastställa rätt mål-SKU för virtuella datorer för dina lokala virtuella datorer. Även en komfortfaktor läggs till när du fastställer effektiv användning.
- För lokal storleks sortering beaktas inte prestanda data och SKU: n rekommenderas för lokal allokering.

För att visa hur detta kan påverka rekommendationerna, tar vi ett exempel:

Vi har en lokal virtuell dator med fyra kärnor och åtta GB minne, med 50% processor användning och 50% minnes användning och en angiven bekvämlighets faktor på 1,3.

-  Om utvärderingen är **lokalt**, rekommenderas en Azure VM-SKU med fyra kärnor och 8 GB minne.
- Om utvärderingen är prestanda beroende av, baserat på effektiv processor-och minnes användning (50% av 4 kärnor * 1,3 = 2,6 kärnor och 50% av 8 GB minne * 1,3 = 5,3-GB minne), rekommenderas billigaste VM-SKU: n för fyra kärnor (närmaste antal kärnor som stöds) och åtta GB minne (närmaste minnes storlek som stöds) rekommenderas.
- [Läs mer](concepts-assessment-calculation.md#types-of-assessments) om utvärderings storlek.

## <a name="why-is-the-recommended-azure-disk-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Varför är de rekommenderade Azure disk-SKU: erna större än lokala i en Azure VM-utvärdering?

Azure Migrate Server-utvärderingen kan rekommendera en större disk baserat på typen av utvärdering.
- Disk storlek i Server utvärderingen är beroende av två bedömnings egenskaper: storleks kriterier och lagrings typ.
- Om storleks kriteriet är **prestanda baserat**och lagrings typen är inställd på **Automatisk**, beaktas IOPS-och data flödes värden för disken när mål disk typen identifieras (standard HDD, standard SSD eller Premium). En disk-SKU från disk typen rekommenderas och rekommendationen tar hänsyn till storleks kraven för den lokala disken.
- Om storleks kriteriet är **prestanda baserat**och lagrings typen är **Premium**rekommenderas en SKU för Premium-diskar i Azure baserat på IOPS, data flöde och storleks krav för den lokala disken. Samma logik används för att utföra disk storlek när storleks kriteriet är **lokalt** och lagrings typen är **standard HDD**, **standard SSD**eller **Premium**.

Om du till exempel har en lokal disk med 32 GB minne, men den aggregerade läsnings-och skriv-IOPS för disken är 800 IOPS, rekommenderar Server utvärderingen en Premium disk (på grund av de högre IOPS-kraven) och rekommenderar sedan en disk-SKU som stöder den nödvändiga IOPS och storleken. Den bästa matchningen i det här exemplet är P15 (256 GB, 1100 IOPS). Även om den storlek som krävs av den lokala disken var 32 GB, rekommenderar Server utvärderingen en större disk på grund av det höga IOPS-kravet för den lokala disken.

## <a name="why-is-performance-data-missing-for-someall-vms-in-my-assessment-report"></a>Varför saknas prestandadata för vissa/alla virtuella datorer i min utvärderingsrapport?

Det står PercentageOfCoresUtilizedMissing eller PercentageOfMemoryUtilizedMissing för prestandabaserad utvärdering i utvärderingsrapporten när Azure Migrate-installationen inte kan samla in prestandadata för lokala virtuella datorer. Kontrollera följande:

- Om de virtuella datorerna är påslagna under hela den varaktighet för vilken du skapar utvärderingen
- Om endast minnesräknare saknas och du försöker utvärdera virtuella Hyper-V-datorer kontrollerar du om dynamiskt minne är aktiverat på de virtuella datorerna. Det finns för närvarande ett känt problem som gör att Azure Migrate-installationen inte kan samla in minnesanvändning för sådana virtuella datorer.
- Om alla prestanda räknare saknas kontrollerar du att port åtkomst kraven för utvärdering är uppfyllda. Läs mer om Port åtkomst krav för [VMware](./migrate-support-matrix-vmware.md#port-access-requirements), [Hyper-V](./migrate-support-matrix-hyper-v.md#port-access) och utvärderingen av [fysiska](./migrate-support-matrix-physical.md#port-access) servrar.
Obs! Om någon av prestandaräknarna saknas återgår Azure Migrate: Server Assessment till de allokerade kärnorna/minnet lokalt och rekommenderar lämplig VM-storlek.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Varför har min utvärdering lågt säkerhetsomdöme?

Säkerhetsomdömet beräknas för ”prestandabaserade” utvärderingar baserat på den procentandel av [tillgängliga datapunkter](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#ratings) som behövdes för att beräkna utvärderingen. Ett lågt säkerhetsomdöme för en utvärdering kan bero på något av följande:

- Du profilerade inte din miljö för hela den varaktighet för vilken du skapar utvärderingen. Om du till exempel skapar en utvärdering med en varaktighet på en vecka måste du vänta minst en vecka efter att identifieringen startade, tills alla datapunkter har samlats in. Om du inte kan vänta hela varaktigheten ändrar du varaktigheten för prestanda till en kortare period och ”räknar om” utvärderingen.
 
- Server Assessment kan inte samla in prestandadata för vissa eller alla virtuella datorer under utvärderingsperioden. Kontrollera att de virtuella datorerna var påslagna under utvärderingen och att utgående anslutningar tillåts på port 443. Om dynamiskt minne är aktiverat för virtuella Hyper-V-datorer saknas minnesräknare, vilket ger ett lågt säkerhetsomdöme. Beräkna om utvärderingen så att de senaste ändringarna återspeglas i säkerhetsomdömet. 

- Få virtuella datorer skapades efter att identifieringen startades i Server Assessment. Om du till exempel skapar en utvärdering för prestandahistoriken för den senaste månaden, men några virtuella datorer skapades i miljön för en vecka sedan. I detta fall kommer prestandadata för de nya virtuella datorerna inte att vara tillgängliga för hela tidsperioden och säkerhetsomdömet blir lågt.

[Läs mer](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#confidence-ratings-performance-based) om säkerhetsomdömen.

## <a name="is-the-operating-system-license-included-in-an-azure-vm-assessment"></a>Ingår operativ Systems licensen i en Azure VM-utvärdering?

Azure Migrate Server utvärderingen betraktar för närvarande endast operativ Systems licens kostnaden för Windows-datorer. Licens kostnader för Linux-datorer anses inte för närvarande.

## <a name="how-does-performance-based-sizing-work-in-an-azure-vm-assessment"></a>Hur fungerar prestandabaserade storlekar i en Azure VM-utvärdering?

Server Assessment samlar kontinuerligt in prestandadata för lokala datorer och använder dem för att rekommendera VM-SKU:n och disk-SKU:n i Azure. [Lär dig hur](concepts-assessment-calculation.md#calculate-sizing-performance-based) prestandabaserade data samlas in.

## <a name="why-is-my-assessment-showing-a-warning-that-it-was-created-with-an-invalid-combination-of-reserved-instances-vm-uptime-and-discount-"></a>Varför visar min utvärdering en varning om att den har skapats med en ogiltig kombination av reserverade instanser, VM-drift tid och rabatt (%)?
När du väljer reserverade instanser, rabatten (%) och egenskaperna för den virtuella datorns drift tid är inte tillämpliga. När utvärderingen skapades med en ogiltig kombination av dessa egenskaper inaktive ras knapparna redigera och beräkna om. Skapa en ny utvärdering. [Läs mer](https://go.microsoft.com/fwlink/?linkid=2131554).

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Jag ser inte prestanda data för vissa nätverkskort på mina fysiska servrar

Detta kan inträffa om Hyper-V-virtualisering är aktiverat på den fysiska servern. På dessa servrar, på grund av en produkt lucka, identifierar Azure Migrate för närvarande både fysiska och virtuella nätverkskort. Nätverks data flödet samlas bara in på de virtuella nätverkskort som identifierats.

## <a name="recommended-azure-vm-sku-for-my-physical-server-is-oversized"></a>Rekommenderad Azure VM-SKU för min fysiska server är överändrad

Detta kan inträffa om Hyper-V-virtualisering är aktiverat på den fysiska servern. På dessa servrar identifierar Azure Migrate för närvarande både fysiska och virtuella nätverkskort. Därför är nej. de identifierade nätverkskorten är högre än vad som är faktiskt. När Server utvärderingen väljer en virtuell Azure-dator som har stöd för det antal nätverkskort som krävs kan detta resultera i en överändrad virtuell dator. [Läs mer](./concepts-assessment-calculation.md#calculating-sizing) om effekten av nej. av nätverkskort för storleks ändring. Detta är en produkt lucka som kommer att åtgärdas framåt.

## <a name="readiness-category-not-ready-for-my-physical-server"></a>Beredskaps kategorin är inte klar för min fysiska server

Kategorin beredskap kan felaktigt markeras som "inte redo" om en fysisk server som har Hyper-V-virtualisering är aktive rad. På dessa servrar, på grund av en produkt lucka, identifierar Azure Migrate för närvarande både fysiska och virtuella nätverkskort. Därför är nej. de identifierade nätverkskorten är högre än vad som är faktiskt. I både lokala och prestandabaserade utvärderingar väljer Server utvärdering en virtuell Azure-dator som har stöd för det antal nätverkskort som krävs. Om antalet nätverkskort upptäcks vara högre än 32 är det maximala värdet Nej. för nätverkskort som stöds på virtuella Azure-datorer markeras datorn som "inte redo".  [Läs mer](./concepts-assessment-calculation.md#calculating-sizing) om effekten av nej. Nätverkskort vid storleks ändring.


## <a name="number-of-discovered-nics-higher-than-actual-for-physical-servers"></a>Antal identifierade nätverkskort som är högre än vad som är faktiskt för fysiska servrar

Detta kan inträffa om Hyper-V-virtualisering är aktiverat på den fysiska servern. På dessa servrar identifierar Azure Migrate för närvarande både fysiska och virtuella nätverkskort. Därför är nej. av nätverkskort som har identifierats är högre än faktiskt.

## <a name="dependency-visualization-in-azure-government"></a>Beroende visualisering i Azure Government

Azure Migrate är beroende av Tjänstkarta för funktionerna för beroende visualisering. Eftersom Tjänstkarta för närvarande inte är tillgängligt i Azure Government är den här funktionen inte tillgänglig i Azure Government.

## <a name="dependencies-dont-show-after-agent-install"></a>Beroenden visas inte efter agent installation

När du har installerat beroende visualiserings agenter på lokala virtuella datorer tar Azure Migrate vanligt vis 15-30 minuter att Visa beroenden i portalen. Om du har väntat i mer än 30 minuter ser du till att Microsoft Monitoring Agent (MMA) kan ansluta till Log Analytics-arbetsytan.

För virtuella Windows-datorer:
1. Starta MMA på kontroll panelen.
2. I **egenskaperna för Microsoft Monitoring Agent**  >  **Azure Log Analytics (OMS)** kontrollerar du att arbets ytans **status** är grön.
3. Om statusen inte är grön kan du försöka ta bort arbets ytan och lägga till den igen till MMA.

    ![Status för MMA](./media/troubleshoot-assessment/mma-properties.png)

För virtuella Linux-datorer måste du kontrol lera att installations kommandona för MMA och beroende agenten lyckades.

## <a name="supported-operating-systems"></a>Operativsystem som stöds

- **MMS-agent**: granska de [Windows](../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)-och [Linux](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) -operativsystem som stöds.
- **Beroende agent**: de Windows- [och Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) -operativsystem som stöds.

## <a name="visualize-dependencies-for--hour"></a>Visualisera beroenden för > timme

Med agent lös beroende analys kan du visualisera beroenden eller exportera dem i en karta under en varaktighet på upp till 30 dagar.

Med agent-baserad beroende analys, även om Azure Migrate låter dig gå tillbaka till ett visst datum under den senaste månaden, så är den maximala varaktigheten för vilken du kan visualisera beroendena en timme. Du kan t. ex. använda funktionen tids varaktighet i beroende kartan för att Visa beroenden för igår, men du kan bara visa dem under en timmes period. Du kan dock använda Azure Monitor loggar för att [fråga beroende data](./how-to-create-group-machine-dependencies.md) över en längre varaktighet.

## <a name="visualized-dependencies-for--10-machines"></a>Visualiserings beroenden för > 10-datorer

I Azure Migrate Server utvärdering, med agent beroende analys, kan du [visualisera beroenden för grupper](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) med upp till 10 virtuella datorer. För större grupper rekommenderar vi att du delar upp de virtuella datorerna i mindre grupper för att visualisera beroenden.


## <a name="machines-show-install-agent"></a>Datorer visar "installera agent"

När du har migrerat datorer med beroende visualisering aktive rad till Azure kan datorerna Visa åtgärden "installera agent" i stället för "Visa beroenden" på följande sätt:


- När migreringen till Azure är inaktive rad stängs lokala datorer av och motsvarande virtuella datorer är i Azure. De här datorerna hämtar en annan MAC-adress.
- Datorerna kan också ha en annan IP-adress, baserat på om du har bevarat den lokala IP-adressen eller inte.
- Om både MAC-och IP-adresser skiljer sig från lokala datorer associerar Azure Migrate inte lokala datorer med Tjänstkarta beroende data. I det här fallet visas alternativet för att installera agenten i stället för att Visa beroenden.
- Efter en testmigrering till Azure förblir lokala datorer aktiverade som förväntat. Likvärdiga datorer som är förändrade i Azure får en annan MAC-adress och kan förvärva olika IP-adresser. Om du inte blockerar utgående Azure Monitor logg trafik från dessa datorer, kommer Azure Migrate inte att associera de lokala datorerna med några Tjänstkarta beroende data, och därför visar alternativet att installera agenter i stället för att Visa beroenden.

## <a name="dependencies-export-csv-shows-unknown-process"></a>Beroenden exportera CSV visar "okänd process"
I en agent utan beroende analys, fångas process namnen på bästa möjliga villkor. I vissa fall, även om käll-och mål server namnen och mål porten har fångats, är det inte möjligt att fastställa process namnen i båda ändar av beroendet. I sådana fall markeras processen som "okänd process".

## <a name="my-log-analytics-workspace-is-not-listed-when-trying-to-configure-the-workspace-in-server-assessment"></a>Min Log Analytics-arbetsyta visas inte när du försöker konfigurera arbets ytan i Server utvärderingen
Azure Migrate har för närvarande stöd för att skapa OMS-arbetsytor i regionerna USA, östra, Asien, sydöstra och Europa, västra. Om arbets ytan har skapats utanför Azure Migrate i någon annan region, kan den för närvarande inte associeras med ett Azure Migrate-projekt.


## <a name="capture-network-traffic"></a>Avbilda nätverks trafik

Samla in nätverks trafik loggar enligt följande:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Tryck på F12 för att starta Utvecklarverktyg. Om det behövs avmarkerar du  **navigerings inställningen rensa poster** .
3. Välj fliken **nätverk** och börja samla in nätverks trafik:
   - I Chrome väljer du **bevara logg**. Inspelningen bör starta automatiskt. En röd cirkel visar att trafiken fångas. Om den röda cirkeln inte visas väljer du den svarta cirkeln för att starta.
   - I Microsoft Edge och Internet Explorer ska inspelningen starta automatiskt. Om den inte är det väljer du den gröna uppspelnings knappen.
4. Försök att återskapa felet.
5. När du har påträffat felet under inspelningen, stoppa inspelningen och spara en kopia av den inspelade aktiviteten:
   - I Chrome högerklickar du på och väljer **Spara som har innehåll**. Den här åtgärden komprimerar och exporterar loggarna som en. har-fil.
   - I Microsoft Edge eller Internet Explorer väljer du alternativet **Exportera insamlad trafik** . Den här åtgärden komprimerar och exporterar loggen.
6. Välj fliken **konsol** för att kontrol lera om det finns varningar eller fel. Så här sparar du konsol loggen:
   - I Chrome högerklickar du på valfri plats i konsol loggen. Välj **Spara som**, för att exportera och zippa loggen.
   - I Microsoft Edge eller Internet Explorer högerklickar du på felen och väljer **Kopiera alla**.
7. Stäng Utvecklarverktyg.


## <a name="where-is-the-operating-system-data-in-my-assessment-discovered-from"></a>Var har operativ Systems data i min utvärdering upptäckts från?

- För virtuella VMware-datorer är det som standard operativ Systems data som tillhandahålls av vCenter. 
   - Om program identifiering har Aktiver ATS för virtuella VMware Linux-datorer hämtas informationen om operativ systemet från den virtuella gäst datorn. Om du vill kontrol lera vilken information om operativ systemet som visas i utvärderingen går du till vyn identifierade servrar och pekar på värdet i kolumnen "operativ system". I texten som öppnas kan du se om de OS-data som du ser samlas in från vCenter-servern eller från den virtuella gäst datorn med hjälp av autentiseringsuppgifter för den virtuella datorn. 
   - För virtuella Windows-datorer hämtas informationen om operativ systemet alltid från vCenter Server.
- För virtuella Hyper-V-datorer samlas operativ Systems data in från Hyper-V-värden
- För fysiska servrar hämtas den från-servern.

## <a name="next-steps"></a>Nästa steg

[Skapa](how-to-create-assessment.md) eller [Anpassa](how-to-modify-assessment.md) en utvärdering.
