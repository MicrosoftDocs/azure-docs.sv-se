---
title: Arbeta med den tidigare versionen av Azure Migrate
description: Beskriver hur du arbetar med den tidigare versionen av Azure Migrate.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.custom: mvc
ms.openlocfilehash: 719fcca74d2cd048bf170940eff7da6a25425469
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373291"
---
# <a name="work-with-the-previous-version-of-azure-migrate"></a>Arbeta med den tidigare versionen av Azure Migrate

Den här artikeln innehåller information om hur du arbetar med den tidigare versionen av Azure Migrate. 


Det finns två versioner av tjänsten Azure Migrate:

- **Aktuell version**: Använd den här versionen för att skapa Azure Migrate projekt, identifiera lokala datorer och dirigera utvärderingar och migreringar. [Läs mer](whats-new.md) om vad som är nytt i den här versionen.
- **Tidigare version**: om du använder den tidigare versionen av Azure Migrate (endast utvärdering av lokala virtuella VMware-datorer stöds) bör du nu använda den aktuella versionen. De tidigare versions projekten kallas klassiska projekt i den här artikeln. Den klassiska Azure Migrate tas ur bruk i feb 2024. Efter feb 2024 kommer den klassiska versionen av Azure Migrate inte längre att stödjas och lager-metadata i klassiska projekt kommer att tas bort. Om du fortfarande behöver använda klassiska Azure Migrate-projekt kan du inte göra något av följande:
    - Du kan inte längre skapa migreringsjobb.
    - Vi rekommenderar att du inte genomför nya identifieringar.
    - Du kan fortfarande komma åt befintliga projekt.
    - Du kan fortfarande köra utvärderingar.
    

## <a name="upgrade-between-versions"></a>Uppgradera mellan versioner

Du kan inte uppgradera projekt eller komponenter i den tidigare versionen till den nya versionen. Du måste [skapa ett nytt Azure Migrate-projekt](create-manage-projects.md)och [lägga till verktyg för utvärdering och migrering](./create-manage-projects.md) i det. Använd självstudierna för att lära dig hur du använder verktyg för bedömning och migrering som är tillgängliga. Om du har en Log Analytics arbets yta som är kopplad till ett klassiskt projekt kan du koppla den till ett projekt med aktuell version när du har tagit bort det klassiska projektet.

## <a name="find-projects-from-previous-version"></a>Hitta projekt från tidigare versioner

Hitta projekt från föregående version på följande sätt:

1. I Azure Portal > **alla tjänster** söker du efter och väljer **Azure Migrate**. 
2. På Azure Migrate-instrumentpanelen finns ett meddelande och en länk för att komma åt gamla Azure Migrate-projekt.
3. Klicka på länken för att öppna klassiska projekt.

## <a name="delete-projects-from-previous-version"></a>Ta bort projekt från tidigare version

Sök efter och ta bort projekt från den tidigare versionen enligt följande:

1. I Azure Portal > **alla tjänster** söker du efter och väljer **Azure Migrate**. 
2. På Azure Migrate-instrumentpanelen finns ett meddelande och en länk för att komma åt gamla Azure Migrate-projekt.
3. Klicka på länken för att öppna klassiska projekt.
4. Välj det projekt som du vill ta bort och ta bort det. 


## <a name="create-an-assessment"></a>Skapa en utvärdering

Efter att virtuella datorer har identifierats i portalen grupperar du dem och skapar utvärderingar.

- Du kan omedelbart skapa som lokala utvärderingar direkt efter att virtuella datorer har identifierats i portalen.
- För prestandabaserade utvärderingar rekommenderar vi att du väntar minst en dag innan du skapar en prestanda baserad utvärdering för att få tillförlitliga storleks rekommendationer.

Skapa en utvärdering på följande sätt:

1. På projektsidan **Översikt** klickar du på **+Skapa utvärdering**.
2. Klicka på **Visa alla** för att granska utvärderingsegenskaperna.
3. Skapa gruppen och ange ett gruppnamn.
4. Välj de datorer som du vill lägga till i gruppen.
5. Klicka på **Skapa utvärdering** för att skapa gruppen och utvärderingen.
6. När utvärderingen har skapats kan du se den i **Översikt** > **Instrumentpanel**.
7. Klicka på **Exportera utvärdering** för att ladda ned den som en Excel-fil.

Om du vill uppdatera en befintlig utvärdering med de senaste prestandadata, kan du använda kommandot **beräkna om** för utvärdering för att uppdatera den.

## <a name="review-an-assessment"></a>Granska en utvärdering 

En utvärdering har tre steg:

- En utvärdering börjar med en lämplig analys för att ta reda på om datorerna är kompatibla i Azure.
- Storleks uppskattningar.
- Månads kostnads uppskattning.

En dator flyttas bara till ett senare steg om den tidigare har passerat. Om en dator till exempel inte klarar lämplig kontroll, är den markerad som olämplig för Azure, och storleks-och kostnads redovisning är inte färdigt.


### <a name="review-azure-readiness"></a>Granska Azure-beredskap

Vyn för Azure-beredskap i utvärderingen visar beredskapsstatus för varje virtuell dator.

**Beredskap** | **Stat** | **Information**
--- | --- | ---
Redo för Azure | Inga kompatibilitetsproblem. Datorn kan migreras med Azure, och den kommer att startas i Azure med fullständig support för Azure. | För förberedda virtuella datorer rekommenderar Azure Migrate en VM-storlek i Azure.
Villkorligt redo för Azure | Datorn kan starta i Azure, men kanske inte har fullständig support för Azure. Till exempel en dator med en äldre version av Windows Server som inte stöds i Azure. | Azure Migrate förklarar beredskaps problemen och ger åtgärder.
Ej redo för Azure |  Det går inte att starta den virtuella datorn i Azure. Om en virtuell dator till exempel har en disk som är större än 4 TB, kan den inte finnas i Azure. | Azure Migrate förklarar beredskaps problemen och ger åtgärder.
Beredskap okänd | Azure Migrate kan inte identifiera Azure-beredskap, vanligt vis eftersom data inte är tillgängliga.


#### <a name="azure-vm-properties"></a>Egenskaper för virtuell Azure-dator
Beredskapen tar hänsyn till ett antal VM-egenskaper, för att identifiera om den virtuella datorn kan köras i Azure.


**Egenskap** | **Information** | **Beredskap**
--- | --- | ---
**Start typ** | BIOS stöds. UEFI stöds inte. | Villkoret klart om start typen är UEFI.
**Kärnor** | Datorer Core <= det maximala antalet kärnor (128) som stöds för en virtuell Azure-dator.<br/><br/> Om prestanda historiken är tillgänglig, Azure Migrate beakta de använda kärnorna.<br/>Om en bekvämlighets faktor anges i utvärderings inställningarna multipliceras antalet använda kärnor av den praktiska faktorn.<br/><br/> Om det inte finns någon prestanda historik använder Azure Migrate tilldelade kärnor, utan att använda den praktiska faktorn. | Redo om det är mindre än eller lika med begränsningar.
**Minne** | Datorns minnes storlek <= högsta mängd minne (3892 GB i Azure M-serien Standard_M128m &nbsp; <sup>2</sup>) för en virtuell Azure-dator. [Läs mer](../virtual-machines/sizes.md).<br/><br/> Om prestanda historiken är tillgänglig Azure Migrate anses det använda minnet.<br/><br/>Om en bekvämlighets faktor anges multipliceras det använda minnet av den praktiska faktorn.<br/><br/> Om det inte finns någon historik används allokerat minne, utan att du använder den praktiska faktorn.<br/><br/> | Redo om det är inom gränserna.
**Lagrings disk** | Den allokerade storleken på en disk måste vara 4 TB (4096 GB) eller mindre.<br/><br/> Antalet diskar som är anslutna till datorn måste vara 65 eller mindre, inklusive OS-disken. | Redo om det är inom gränserna.
**Nätverk** | En dator måste ha 32 eller färre nätverkskort kopplade till sig. | Redo om det är inom gränserna.

#### <a name="guest-operating-system"></a>Gästoperativsystem

Tillsammans med VM-egenskaper tittar Azure Migrate också på gäst operativ systemet för den lokala virtuella datorn för att identifiera om den virtuella datorn kan köras i Azure.

- Azure Migrate anser att det operativ system som anges i vCenter Server.
- Eftersom identifieringen som utförs av Azure Migrate är utrustnings-baserad, har det inte något sätt att kontrol lera om operativ systemet som körs i den virtuella datorn är detsamma som det som anges i vCenter Server.

Följande logik används.

**Operativsystem** | **Information** | **Beredskap**
--- | --- | ---
Windows Server 2016 och alla SPs | Azure ger fullständig support. | Redo för Azure
Windows Server 2012 R2 och alla SPs | Azure ger fullständig support. | Redo för Azure
Windows Server 2012 och alla SPs | Azure ger fullständig support. | Redo för Azure
Windows Server 2008 R2 och alla SPs | Azure ger fullständig support.| Redo för Azure
Windows Server 2008 (32-bitars och 64-bitars) | Azure ger fullständig support. | Redo för Azure
Windows Server 2003, 2003 R2 | Out-of-support och behöver ett [anpassat support avtal (CSA)](/troubleshoot/azure/virtual-machines/server-software-support) för support i Azure. | Villkorligt redo för Azure bör du överväga att uppgradera operativ systemet innan du migrerar till Azure.
Windows 2000, 98, 95, NT, 3,1, MS-DOS | Out-of-support. Datorn kan starta i Azure, men det finns inget stöd för operativ systemet i Azure. | Villkorligt redo för Azure, vi rekommenderar att du uppgraderar operativ systemet innan du migrerar till Azure.
Windows-klient 7, 8 och 10 | Azure har endast stöd för [Visual Studio-prenumeration.](../virtual-machines/windows/client-images.md) | Villkorligt redo för Azure
Windows 10 Pro Desktop | Azure har stöd för [värd rättigheter för flera innehavare.](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md) | Villkorligt redo för Azure
Windows Vista, XP Professional | Out-of-support. Datorn kan starta i Azure, men det finns inget stöd för operativ systemet i Azure. | Villkorligt redo för Azure, vi rekommenderar att du uppgraderar operativ systemet innan du migrerar till Azure.
Linux | Azure godkänner dessa [Linux-operativsystem](../virtual-machines/linux/endorsed-distros.md). Andra Linux-operativsystem kan starta i Azure, men vi rekommenderar att du uppgraderar operativ systemet till en godkänd version innan du migrerar till Azure. | Redo för Azure om versionen är påtecknad.<br/><br/>Villkorligt klar om versionen inte har påbörjats.
Andra operativ system<br/><br/> Till exempel Oracle Solaris, Apple macOS osv., FreeBSD osv. | Azure förser inte dessa operativ system. Datorn kan starta i Azure, men det finns inget stöd för operativ systemet i Azure. | Villkorligt redo för Azure, vi rekommenderar att du installerar ett operativ system som stöds innan du migrerar till Azure.  
Operativ systemet har angetts som **annat** i vCenter Server | Azure Migrate kan inte identifiera operativ systemet i det här fallet. | Okänd beredskap. Kontrol lera att operativ systemet som körs i den virtuella datorn stöds i Azure.
32-bitars operativ system | Datorn kan starta i Azure, men Azure har kanske inte fullständig support. | Villkorligt redo för Azure bör du överväga att uppgradera datorns operativ system från 32-bitars operativ system till 64-bitars operativ system innan du migrerar till Azure.


### <a name="review-sizing"></a>Granska storlek

 Rekommendationen Azure Migrate storlek beror på storleks kriteriet som anges i utvärderings egenskaperna.

- Om storleks ändringen är prestanda beroende, tar storleks rekommendationen hänsyn till de virtuella datorernas prestanda historik (CPU och minne) och diskar (IOPS och data flöde).
- Om storleks kriteriet är "som lokalt", baseras storleks rekommendationen i Azure på storleken på den virtuella datorn lokalt. Disk storlek baseras på den lagrings typ som anges i utvärderings egenskaperna (standard är Premium diskar). Azure Migrate anser inte prestanda data för den virtuella datorn och diskarna.

### <a name="review-cost-estimates"></a>Granska kostnadsuppskattningar

Kostnads uppskattningar visar total beräknings-och lagrings kostnad för att köra de virtuella datorerna i Azure, tillsammans med information för varje dator.

- Kostnads uppskattningar beräknas med storleks rekommendationen för en virtuell dator och dess diskar samt utvärderings egenskaperna.
- Uppskattade månatliga kostnader för beräkning och lagring sammanställs för alla virtuella datorer i gruppen.
- Kostnads uppskattningen är att köra den lokala virtuella datorn som en virtuell IaaS-dator (Azure Infrastructure as a Service). Azure Migrate anser inte PaaS (Platform as a Service) eller SaaS-kostnader (Software as a Service).

### <a name="review-confidence-rating-performance-based-assessment"></a>Granska förtroende klassificering (prestanda baserad utvärdering)

Varje prestanda-baserad utvärdering är associerad med en förtroende bedömning.

- Ett konfidens intervall från en stjärna till fem stjärnor (en stjärna är det lägsta och fem stjärnor).
- Förtroendet har tilldelats en utvärdering, baserat på tillgängligheten för data punkter som behövs för att beräkna utvärderingen.
- Med säkerhetsomdömet kan du beräkna tillförlitligheten i de storleksrekommendationer som anges av Azure Migrate.
- Konfidens omdömet är inte tillgängligt för lokala utvärderingar i "i befintligt skick".

För prestandabaserade storleks ändringar behöver Azure Migrate följande:
- Användnings data för CPU.
- Minnes data för virtuella datorer.
- För varje disk som är ansluten till den virtuella datorn krävs disk-IOPS och data flödes data.
- För varje nätverkskort som är kopplat till en virtuell dator behöver Azure Migrate nätverks indata/utdata.
- Om något av ovanstående inte är tillgängligt kanske storleks rekommendationer (och därmed förtroende klassificeringen) inte är tillförlitliga.


Beroende på hur många data punkter som är tillgängliga sammanfattas de möjliga förtroende klassificeringarna i tabellen.

**Tillgänglighet för datapunkter** | **Säkerhetsomdöme**
--- | ---
0 %–20 % | 1 stjärna
21 %–40 % | 2 stjärnor
41 %–60 % | 3 stjärnor
61 %–80 % | 4 stjärnor
81 %–100 % | 5 stjärnor


#### <a name="assessment-issues-affecting-confidence-ratings"></a>Utvärderings problem som påverkar förtroende värderingar

En utvärdering kanske inte har alla data punkter tillgängliga på grund av ett antal orsaker:

- Du har inte profilerat din miljö under utvärderings perioden. Om du till exempel skapar utvärderingen med varaktigheten inställd på en dag måste du vänta i minst en dag efter att du har startat identifieringen eller så har alla data punkter som ska samlas in.
- Vissa virtuella datorer stängdes ned under den period då utvärderingen beräknades. Om några virtuella datorer har stängts av under en viss tid kan Azure Migrate inte samla in prestanda data för den perioden.
- Vissa virtuella datorer skapades mellan under utvärderings beräknings perioden. Om du till exempel skapar en utvärdering med hjälp av den senaste månadens prestanda historik, men skapar ett antal virtuella datorer i miljön för en vecka sedan, blir prestanda historiken för de nya virtuella datorerna inte under hela varaktigheten.

> [!NOTE]
> Om förtroende omdömet för en utvärdering är under fem stjärnor, väntar du i minst en dag på att enheten ska profilera miljön och sedan beräknar du om utvärderingen. Om du inte har en prestanda-baserad storlek kanske du inte är tillförlitlig. Om du inte vill omberäkna, rekommenderar vi att du växlar till som lokal storleks ändring genom att ändra utvärderings egenskaperna.



## <a name="create-groups-using-dependency-visualization"></a>Skapa grupper med hjälp av beroende visualisering

Förutom att skapa grupper manuellt kan du skapa grupper med hjälp av beroende visualisering.
- Du använder vanligt vis den här metoden när du vill utvärdera grupper med högre förtroende nivå genom att kontrol lera maskin beroenden innan du kör en utvärdering.
- Beroende visualisering kan hjälpa dig att effektivt planera din migrering till Azure. Det hjälper dig att se till att inget är kvar bakom och att oväntade avbrott inte inträffar när du migrerar till Azure.
- Du kan identifiera alla beroende system som behöver migreras tillsammans och identifiera om ett system som körs fortfarande betjänar användare eller är en kandidat för inaktive ring i stället för migrering.
- Azure Migrate använder Tjänstkarta-lösningen i Azure Monitor för att aktivera beroende visualisering.

> [!NOTE]
> Beroende visualisering är inte tillgänglig i Azure Government.

Om du vill konfigurera beroende visualisering associerar du en Log Analytics-arbetsyta med ett Azure Migrate-projekt, installerar agenter på datorer som du vill visualisera beroenden för och skapar sedan grupper med hjälp av beroende information. 



### <a name="associate-a-log-analytics-workspace"></a>Koppla en Log Analytics-arbetsyta

Om du vill använda beroende visualisering associerar du en Log Analytics arbets yta med ett migreringsjobb. Du kan bara skapa eller koppla en arbets yta i samma prenumeration där migreringsjobbet skapas.

1. Om du vill bifoga en Log Analytics arbets yta till ett projekt i **översikt**> **Essentials** klickar du på **kräver konfiguration**.
2. Du kan skapa en ny arbets yta eller bifoga en befintlig:
  - Ange ett namn för att skapa en ny arbets yta. Arbets ytan skapas i en region i samma Azure- [geografi](https://azure.microsoft.com/global-infrastructure/geographies/) som migreringsjobbet.
  - När du ansluter en befintlig arbets yta kan du välja mellan alla tillgängliga arbets ytor i samma prenumeration som migreringsjobbet. Endast de arbets ytor som har skapats i en [tjänstkarta region som stöds](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions). Se till att du har "läsare"-åtkomst till arbets ytan för att koppla en arbets yta.

> [!NOTE]
> Du kan inte ändra arbets ytan som är associerad med ett migreringsjobb.

### <a name="download-and-install-vm-agents"></a>Hämta och installera VM-agenter

När du har konfigurerat en arbets yta laddar du ned och installerar agenter på varje lokal dator som du vill utvärdera. Om du dessutom har datorer som saknar Internet anslutning måste du ladda ned och installera [Log Analytics Gateway](../azure-monitor/platform/gateway.md) på dem.

1. I **Översikt** klickar du på **Hantera**  >  **datorer** och väljer den dator som krävs.
2. I kolumnen **beroenden** klickar du på **installera agenter**.
3. På sidan **beroenden** laddar du ned och installerar Microsoft Monitoring Agent (MMA) och beroende agenten på varje virtuell dator som du vill utvärdera.
4. Kopiera arbetsytans ID och nyckel. Du behöver dessa när du installerar MMA på den lokala datorn.

> [!NOTE]
> Om du vill automatisera installationen av agenter kan du använda ett distributions verktyg som Configuration Manager eller ett partner verktyg, till exempel en [Intigua](https://www.intigua.com/intigua-for-azure-migration), som tillhandahåller en agent distributions lösning för Azure Migrate.


#### <a name="install-the-mma-agent-on-a-windows-machine"></a>Installera MMA-agenten på en Windows-dator

Så här installerar du agenten på en Windows-dator:

1. Dubbelklicka på den hämtade agenten.
2. På sidan **Välkommen** klickar du på **Nästa**. På sidan **licens villkor** klickar du på **Jag accepterar** att godkänna licensen.
3. I **målmappen**, Behåll eller ändra standardmappen för installationen > **Nästa**.
4. I **installations alternativ för agent** väljer du **Azure Log Analytics**  >  **Nästa**.
5. Klicka på **Lägg** till för att lägga till en ny Log Analytics-arbetsyta. Klistra in det arbetsyte-ID och den nyckel som du kopierade från portalen. Klicka på **Nästa**.

Du kan installera agenten från kommando raden eller med en automatiserad metod som Configuration Manager. [Lär dig mer](../azure-monitor/platform/log-analytics-agent.md#installation-options) om att använda dessa metoder för att installera MMA-agenten.

#### <a name="install-the-mma-agent-on-a-linux-machine"></a>Installera MMA-agenten på en Linux-dator

Så här installerar du agenten på en Linux-dator:

1. Överför lämpligt paket (x86 eller x64) till Linux-datorn med hjälp av SCP/SFTP.
2. Installera paketet med hjälp av argumentet--install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Läs mer](../azure-monitor/platform/agents-overview.md#supported-operating-systems) om listan med stöd för Linux-operativsystem i MMA.

### <a name="install-the-mma-agent-on-a-machine-monitored-by-operations-manager"></a>Installera MMA-agenten på en dator som övervakas av Operations Manager

För datorer som övervakas med System Center Operations Manager 2012 R2 eller senare behöver du inte installera MMA-agenten. Tjänstkarta integreras med Operations Manager-MMA för att samla in nödvändiga beroende data. [Läs mer](../azure-monitor/insights/service-map-scom.md#prerequisites). Beroende agenten måste vara installerad.

### <a name="install-the-dependency-agent"></a>Installera beroendeagenten

1. Installera beroende agenten på en Windows-dator genom att dubbelklicka på installations filen och följa guiden.
2. Installera beroende agenten på en Linux-dator genom att installera som rot med följande kommando:

    ```sh InstallDependencyAgent-Linux64.bin```

- Läs mer om [stöd för beroende agent](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) för operativ systemen Windows och Linux.
- [Läs mer](../azure-monitor/insights/vminsights-enable-hybrid.md#dependency-agent) om hur du kan använda skript för att installera beroende agenten.

>[!NOTE]
> Azure Monitor for VMs artikel som refereras till för att ge en översikt över system kraven och metoderna för att distribuera beroende agenten gäller även för Tjänstkarta-lösningen.

### <a name="create-a-group-with-dependency-mapping"></a>Skapa en grupp med beroende mappning

1. När du har installerat agenterna går du till portalen och klickar på **Hantera**  >  **datorer**.
2. Sök efter den dator där du installerade agenterna.
3. Kolumnen **beroenden** för datorn ska nu visas som **Visa beroenden**. Klicka på kolumnen om du vill visa datorernas beroenden.
4. Beroende kartan för datorn visar följande information:
    - Inkommande (klienter) och utgående (servrar) TCP-anslutningar till/från datorn
        - De beroende datorer som inte har MMA-och beroende agenten installerade grupperas efter port nummer.
        - De beroende datorer som har MMA och beroende agenten installerad visas som separata rutor.
    - Processer som körs inuti datorn kan du expandera varje dator om du vill visa processerna
    - Datorns egenskaper, inklusive FQDN, operativ system, MAC-adress visas. Du kan klicka på varje dator box om du vill visa information.

4. Du kan Visa beroenden för olika tids perioder genom att klicka på tids längden i tidsintervallet. Som standard är intervallet en timme. Du kan ändra tidsintervallet, eller ange start-och slutdatum samt varaktighet.

   > [!NOTE]
   >    Ett tidsintervall på upp till en timme stöds. Använd Azure Monitor loggar för att [fråga beroende data](./how-to-create-group-machine-dependencies.md) över en längre varaktighet.

5. När du har identifierat beroende datorer som du vill gruppera tillsammans använder du Ctrl + klicka för att markera flera datorer på kartan och klicka på **grupp datorer**.
6. Ange ett grupp namn. Kontrol lera att de beroende datorerna upptäcks av Azure Migrate.

    > [!NOTE]
    > Om en beroende dator inte upptäcks av Azure Migrate kan du inte lägga till den i gruppen. Om du vill lägga till sådana datorer i gruppen måste du köra identifierings processen igen med rätt omfång i vCenter Server och kontrol lera att datorn identifieras av Azure Migrate.  

7. Om du vill skapa en utvärdering för den här gruppen, markerar du kryss rutan för att skapa en ny utvärdering för gruppen.
8. Spara gruppen genom att klicka på **OK** .

När gruppen har skapats bör du installera agenter på alla datorer i gruppen och förfina gruppen genom att visualisera beroendet av hela gruppen.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Fråga beroende data från Azure Monitor loggar

Beroende data som samlas in av Tjänstkarta är tillgängliga för frågor i arbets ytan Log Analytics som är associerad med ditt Azure Migrate-projekt. [Läs mer](../azure-monitor/insights/service-map.md#log-analytics-records) om tjänstkarta data tabeller att fråga i Azure Monitor loggar. 

Så här kör du Kusto-frågorna:

1. När du har installerat agenterna går du till portalen och klickar på **Översikt**.
2. I **Översikt** går du till avsnittet **Essentials** i projektet och klickar på arbets ytans namn bredvid **OMS-arbetsytan**.
3. Klicka på **allmänna** loggar på sidan Log Analytics arbets yta  >  .
4. Skriv din fråga för att samla in beroende data med Azure Monitor loggar. Sök efter exempel frågor i nästa avsnitt.
5. Kör frågan genom att klicka på Kör. 

[Läs mer](../azure-monitor/log-query/log-analytics-tutorial.md) om hur du skriver Kusto-frågor. 

### <a name="sample-azure-monitor-logs-queries"></a>Exempel på Azure Monitor loggar frågor

Följande är exempel frågor som du kan använda för att extrahera beroende data. Du kan ändra frågorna för att extrahera önskade data punkter. En fullständig lista över fälten i beroende data poster finns [här](../azure-monitor/insights/service-map.md#log-analytics-records). Sök efter fler exempel frågor [här](../azure-monitor/insights/service-map.md#sample-log-searches).

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Sammanfatta inkommande anslutningar på en uppsättning datorer

Posterna i tabellen för anslutnings mått, VMConnection, representerar inte enskilda fysiska nätverks anslutningar. Flera fysiska nätverks anslutningar är grupperade i en logisk anslutning. [Läs mer](../azure-monitor/insights/service-map.md#connections) om hur data för fysiska nätverks anslutningar sammanställs i en enda logisk post i VMConnection. 

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Sammanfatta mängden data som skickas och tas emot på inkommande anslutningar mellan en uppsättning datorer

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```


## <a name="next-steps"></a>Nästa steg
[Läs mer om](migrate-services-overview.md) den senaste versionen av Azure Migrate.