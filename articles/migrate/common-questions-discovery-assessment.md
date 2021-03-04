---
title: Frågor om identifiering, utvärdering och beroende analys i Azure Migrate
description: Få svar på vanliga frågor om identifiering, utvärdering och beroende analys i Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: 6c4dfed27a105fad951ae12ca053b6d86772717a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032576"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Identifiering, utvärdering och beroende analys – vanliga frågor

I den här artikeln besvaras vanliga frågor om identifiering, utvärdering och beroende analys i Azure Migrate. Om du har andra frågor kontrollerar du följande resurser:

- [Allmänna frågor](resources-faq.md) om Azure Migrate
- Frågor om [Azure Migrate-enheten](common-questions-appliance.md)
- Frågor om [Server migrering](common-questions-server-migration.md)
- Få svar på frågor i [Azure Migrate-forumet](https://aka.ms/AzureMigrateForum)


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Vilka geografiska områden stöds för identifiering och utvärdering med Azure Migrate?

Granska de geografiska områden som stöds för [offentliga moln](migrate-support-matrix.md#supported-geographies-public-cloud) och [myndighetsmoln](migrate-support-matrix.md#supported-geographies-azure-government).


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Hur många virtuella datorer kan jag identifiera med en apparat?

Du kan identifiera upp till 10 000 virtuella VMware-datorer, upp till 5 000 virtuella Hyper-V-datorer och upp till 1000 fysiska servrar med hjälp av en enda apparat. Om du har fler datorer läser du om [skalning av en Hyper-V-utvärdering](scale-hyper-v-assessment.md), [skalning av en VMware-utvärdering](scale-vmware-assessment.md)eller [skalning av en fysisk server-utvärdering](scale-physical-assessment.md).

## <a name="how-do-i-choose-the-assessment-type"></a>Hur väljer jag utvärderingstyp?

- Använd **Azure VM-utvärderingar** när du vill utvärdera dina lokala [virtuella VMware-datorer](how-to-set-up-appliance-vmware.md), [virtuella Hyper-V-datorer](how-to-set-up-appliance-hyper-v.md)och [fysiska servrar](how-to-set-up-appliance-physical.md) för migrering till virtuella Azure-datorer. [Läs mer](concepts-assessment-calculation.md)

- Använd utvärderings typ **Azure SQL** när du vill utvärdera din lokala SQL Server från VMware-miljön för migrering till Azure SQL Database eller Azure SQL-hanterad instans. [Läs mer](concepts-assessment-calculation.md)

    > [!Note]
    > Identifiering och utvärdering av SQL Server instanser och databaser som körs i din VMware-miljö är nu i för hands version. Om du vill testa den här funktionen använder du [**den här länken**](https://aka.ms/AzureMigrate/SQL) för att skapa ett projekt i regionen **östra Australien** . Om du redan har ett projekt i östra Australien och vill testa den här funktionen, måste du se till att du har slutfört dessa [**krav**](how-to-discover-sql-existing-project.md) på portalen.

- Använd **Azure VMware Solution (AVS)-** utvärderingar när du vill utvärdera dina lokala [virtuella VMware-datorer](how-to-set-up-appliance-vmware.md) för migrering till [Azure VMware-lösningen (AVS)](../azure-vmware/introduction.md) med den här utvärderings typen. [Läs mer](concepts-azure-vmware-solution-assessment-calculation.md)

- Du kan använda en gemensam grupp med endast VMware-datorer om du vill köra båda utvärderingstyperna. Om du kör AVS-utvärderingar i Azure Migrate för första gången rekommenderar vi att du skapar en ny grupp med VMware-datorer.
 

## <a name="why-is-performance-data-missing-for-someall-servers-in-my-azure-vm-andor-avs-assessment-report"></a>Varför saknas prestanda data för vissa/alla servrar i min Azure VM-och/eller AVS-bedömnings rapport?

Det står PercentageOfCoresUtilizedMissing eller PercentageOfMemoryUtilizedMissing för prestandabaserad utvärdering i utvärderingsrapporten när Azure Migrate-installationen inte kan samla in prestandadata för lokala virtuella datorer. Kontrollera följande:

- Om de virtuella datorerna är påslagna under hela den varaktighet för vilken du skapar utvärderingen
- Om endast minnes räknare saknas och du försöker utvärdera virtuella Hyper-V-datorer. I det här scenariot aktiverar du dynamiskt minne på de virtuella datorerna och omberäknar utvärderingen för att avspegla de senaste ändringarna. Enheten kan samla in minnes användnings värden för virtuella Hyper-V-datorer endast när den virtuella datorn har dynamiskt minne aktiverat.

- Om alla prestanda räknare saknas kontrollerar du att utgående anslutningar på portarna 443 (HTTPS) är tillåtna.

    > [!Note]
    > Om någon av prestanda räknarna saknas, Azure Migrate: Server utvärdering återgår till allokerade kärnor/minne lokalt och rekommenderar en VM-storlek enligt detta.


## <a name="why-is-performance-data-missing-for-someall-sql-instancesdatabases-in-my-azure-sql-assessment"></a>Varför saknas prestanda data för vissa/alla SQL-instanser/-databaser i min Azure SQL-utvärdering?

För att säkerställa att prestanda data samlas in kontrollerar du följande:

- Om SQL-servrarna är påslagna under den tid som du skapar utvärderingen
- Om anslutnings statusen för SQL-agenten i Azure Migrate är ansluten och kontrol lera senaste pulsslag 
- Om Azure Migrate anslutnings status för alla SQL-instanser är "ansluten" på bladet identifierad SQL-instans
- Om alla prestanda räknare saknas kontrollerar du att utgående anslutningar på portarna 443 (HTTPS) är tillåtna

Om någon av prestanda räknarna saknas rekommenderar Azure SQL-utvärderingen den minsta Azure SQL-konfigurationen för den instansen/databasen.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Varför har min utvärdering lågt säkerhetsomdöme?

Säkerhetsomdömet beräknas för ”prestandabaserade” utvärderingar baserat på den procentandel av [tillgängliga datapunkter](./concepts-assessment-calculation.md#ratings) som behövdes för att beräkna utvärderingen. Ett lågt säkerhetsomdöme för en utvärdering kan bero på något av följande:

- Du profilerade inte din miljö för hela den varaktighet för vilken du skapar utvärderingen. Om du till exempel skapar en utvärdering med en varaktighet på en vecka måste du vänta minst en vecka efter att identifieringen startade, tills alla datapunkter har samlats in. Om du inte kan vänta i varaktigheten ändrar du varaktigheten för prestanda till en kortare period och **beräknar om** utvärderingen.
 
- Utvärderingen kan inte samla in prestanda data för vissa eller alla servrar i utvärderings perioden. För en hög exakthet bör du se till att: 
    - Servrarna är påslagna under utvärderings perioden
    - Utgående anslutningar på portarna 443 tillåts
    - För Hyper-V-servrar är dynamiskt minne aktiverat 
    - Anslutnings statusen för agenter i Azure Migrate är ansluten och kontrollerar senaste pulsslag
    - För Azure SQL-utvärderingar är Azure Migrate anslutnings status för alla SQL-instanser "ansluten" på bladet identifierad SQL-instans

    **Beräkna** om utvärderingen så att den återspeglar de senaste ändringarna i förtroende klassificeringen.

- För virtuella Azure-datorer och AVS-bedömningar skapades några servrar efter att identifieringen hade startats. Om du till exempel skapar en utvärdering för prestanda historiken för den senaste månaden, men bara några servrar har skapats i miljön för en vecka sedan. I det här fallet är prestanda data för de nya servrarna inte tillgängliga under hela varaktigheten och förtroendet är lågt. [Läs mer](./concepts-assessment-calculation.md#confidence-ratings-performance-based)

- För Azure SQL-utvärderingar skapades några SQL-instanser eller databaser efter att identifieringen startades. Om du till exempel skapar en utvärdering för prestanda historiken för den senaste månaden, men bara några SQL-instanser eller databaser har skapats i miljön för en vecka sedan. I det här fallet är prestanda data för de nya servrarna inte tillgängliga under hela varaktigheten och förtroendet är lågt. [Läs mer](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)

## <a name="i-want-to-try-out-the-new-azure-sql-assessment-feature-in-azure-migrate"></a>Jag vill prova den nya funktionen för Azure SQL-utvärdering i Azure Migrate
Om du vill testa den här funktionen använder du [den här länken](https://go.microsoft.com/fwlink/?linkid=2155668L) för att skapa ett projekt i regionen **östra Australien** .
- Gå till vägledningen för [identifiering](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware) och [utvärdering](https://docs.microsoft.com/azure/migrate/tutorial-assess-sql) för att komma igång.
- Observera att identifiering och utvärdering av SQL Server instanser och databaser som körs i din VMware-miljö är för närvarande en för hands version.

## <a name="i-cant-see-some-servers-when-i-am-creating-an-azure-sql-assessment"></a>Jag kan inte se vissa servrar när jag skapar en Azure SQL-utvärdering

- Azure SQL-utvärderingen kan bara utföras på servrar som kör där SQL-instanser identifierades. Om du inte ser de servrar och SQL-instanser som du vill utvärdera väntar du en stund tills identifieringen har slutförts och skapar sedan utvärderingen. 
- Om du inte kan se en tidigare skapad grupp när du har skapat utvärderingen tar du bort alla icke-VMware-servrar eller servrar utan SQL-instans från gruppen.
- Om du kör Azure SQL-utvärderingar i Azure Migrate för första gången är det lämpligt att skapa en ny grupp med servrar.

## <a name="i-want-to-understand-how-was-the-readiness-for-my-instance-computed"></a>Jag vill veta hur är beredskapen för min instans Beräknad?
Beredskap för dina SQL-instanser har beräknats efter en funktions kompatibilitetskontroll med den mål distributions typ för Azure SQL (Azure SQL Database eller Azure SQL-hanterad instans). [Läs mer](./concepts-azure-sql-assessment-calculation.md#calculate-readiness)

## <a name="why-is-the-readiness-for-all-my-sql-instances-marked-as-unknown"></a>Varför är beredskapen för alla mina SQL-instanser markerade som okända?
Om din identifiering har startats nyligen och fortfarande pågår, kan det hända att du ser beredskap för vissa eller alla SQL-instanser som okända. Vi rekommenderar att du väntar ett tag på att installationen ska profilera miljön och sedan beräknar om utvärderingen.
SQL-identifieringen utförs en gång var 24: e timme och du kan behöva vänta upp till en dag för att de senaste konfigurations ändringarna ska avspeglas. 

## <a name="why-is-the-readiness-for-some-of-my-sql-instances-marked-as-unknown"></a>Varför är beredskap för några av mina SQL-instanser markerade som okända?
Detta kan inträffa om: 
- Identifieringen pågår fortfarande. Vi rekommenderar att du väntar ett tag på att installationen ska profilera miljön och sedan beräknar om utvärderingen.
- Det finns vissa identifierings problem som du behöver åtgärda på bladet fel och meddelanden.

SQL-identifieringen utförs en gång var 24: e timme och du kan behöva vänta upp till en dag för att de senaste konfigurations ändringarna ska avspeglas.

## <a name="my-assessment-is-in-outdated-state"></a>Min utvärdering är i inaktuellt tillstånd

### <a name="azure-vmavs-assessment"></a>Azure VM/AVS-utvärdering
Om det finns lokala ändringar av virtuella datorer som finns i en grupp som har bedömts, markeras utvärderingen som föråldrad. En utvärdering kan markeras som "inaktuell" på grund av en eller flera ändringar i nedanstående egenskaper:
- Antal processor kärnor
- Allokerat minne
- Start typ eller inbyggd program vara
- Operativ systemets namn, version och arkitektur
- Antal diskar
- Antal nätverkskort
- Ändring av disk storlek (GB allokerat)
- Uppdatera NIC-egenskaper. Exempel: Mac-adress ändringar, IP-adress tillägg osv.

**Beräkna** om utvärderingen så att den återspeglar de senaste ändringarna i utvärderingen.

### <a name="azure-sql-assessment"></a>Azure SQL-utvärdering
Om det finns ändringar i lokala SQL-instanser och databaser som finns i en grupp som har utvärderats, markeras utvärderingen som **föråldrad**:
- SQL-instans lades till eller togs bort från en server
- SQL Database lades till eller togs bort från en SQL-instans
- Den totala databas storleken i en SQL-instans har ändrats med mer än 20%
- Ändring i antal processor kärnor och/eller allokerat minne

**Beräkna** om utvärderingen så att den återspeglar de senaste ändringarna i utvärderingen.

## <a name="why-was-i-recommended-a-particular-target-deployment-type"></a>Varför rekommenderar jag en viss mål distributions typ?
Azure Migrate rekommenderar en unik distributions typ för Azure SQL som är kompatibel med SQL-instansen. Om du migrerar till ett Microsoft-rekommenderat mål minskar du din totala migrering. Den här Azure SQL-konfigurationen (SKU) rekommenderas efter att ha beaktat prestanda egenskaperna för din SQL-instans och de databaser som den hanterar. Om flera Azure SQL-konfigurationer är berättigade rekommenderar vi den som är mest kostnads effektiv. [Läs mer](./concepts-azure-sql-assessment-calculation.md#calculate-sizing)

## <a name="what-deployment-target-should-i-choose-if-my-sql-instance-is-ready-for-azure-sql-db-and-azure-sql-mi"></a>Vilket distributions mål ska jag välja om min SQL-instans är redo för Azure SQL DB och Azure SQL MI? 
Om instansen är klar för både Azure SQL DB och Azure SQL MI rekommenderar vi mål distributions typen för vilken den uppskattade kostnaden för Azure SQL-konfigurationen är lägre.

## <a name="why-is-my-instance-marked-as-potentially-ready-for-azure-vm-in-my-azure-sql-assessment"></a>Varför har min instans marker ATS som potentiellt redo för virtuell Azure-dator i min Azure SQL-utvärdering?
Detta kan inträffa när den mål distributions typ som valts i utvärderings egenskaperna **rekommenderas** och SQL-instansen inte är klar för Azure SQL Database och Azure SQL-hanterad instans. Användaren rekommenderas att skapa en utvärdering i Azure Migrate med utvärderings typ som **virtuell Azure-dator** för att avgöra om den server där instansen körs är redo att migrera till en virtuell Azure-dator.
Användaren rekommenderas att skapa en utvärdering i Azure Migrate med utvärderings typ som **virtuell Azure-dator** för att avgöra om den server där instansen körs är redo att migrera till en virtuell Azure-dator i stället:
- Azure VM-utvärderingar i Azure Migrate lyfts för närvarande – en-Shift-fokuserad och kommer inte att beakta de exakta prestanda måtten för att köra SQL-instanser och databaser på den virtuella Azure-datorn. 
- När du kör en Azure VM-utvärdering på en server, kommer de rekommenderade storlekarna och kostnads uppskattningarna att vara för alla instanser som körs på servern och kan migreras till en virtuell Azure-dator med hjälp av verktyget Migreringsverktyg. Innan du migrerar bör du [gå igenom rikt linjerna för prestanda](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/performance-guidelines-best-practices) för SQL Server på virtuella Azure-datorer.

## <a name="i-cant-see-some-databases-in-my-assessment-even-though-the-instance-is-part-of-the-assessment"></a>Jag kan inte se vissa databaser i min utvärdering trots att instansen ingår i utvärderingen

Azure SQL-utvärderingen inkluderar bara databaser som har statusen online. Om databasen är i någon annan status ignorerar utvärderingen beredskapen, storleks ändringen och kostnads beräkningen för dessa databaser. Om du vill utvärdera sådana databaser, ändra databasens status och beräkna om utvärderingen i en viss tid.

## <a name="i-want-to-compare-costs-for-running-my-sql-instances-on-azure-vm-vs-azure-sql-databaseazure-sql-managed-instance"></a>Jag vill jämföra kostnader för att köra mina SQL-instanser på Azure VM vs Azure SQL Database/Azure SQL Managed instance

Du kan skapa en utvärdering med typen **virtuell Azure-dator** i samma grupp som användes i din **Azure SQL** -utvärdering. Du kan sedan jämföra de två rapporterna sida vid sida. Azure VM-utvärderingar i Azure Migrate lyfts för närvarande och flyttas och kommer inte att beakta de exakta prestanda måtten för att köra SQL-instanser och databaser på den virtuella Azure-datorn. När du kör en Azure VM-utvärdering på en server, kommer de rekommenderade storlekarna och kostnads uppskattningarna att vara för alla instanser som körs på servern och kan migreras till en virtuell Azure-dator med hjälp av verktyget Migreringsverktyg. Innan du migrerar bör du [gå igenom rikt linjerna för prestanda](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/performance-guidelines-best-practices) för SQL Server på virtuella Azure-datorer.

## <a name="the-storage-cost-in-my-azure-sql-assessment-is-zero"></a>Lagrings kostnaden i min Azure SQL-utvärdering är noll
För Azure SQL-hanterad instans finns ingen lagrings kostnad tillagd för den första 32 GB/instans/månad-lagringen och ytterligare lagrings kostnader läggs till för lagring i 32 GB steg. [Läs mer](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)

## <a name="i-cant-see-some-groups-when-i-am-creating-an-azure-vmware-solution-avs-assessment"></a>Jag kan inte se vissa grupper när jag skapar en Azure VMware-lösning (AVS)-utvärdering

- En AVS-utvärdering kan göras för grupper som endast innehåller VMware-datorer. Ta bort alla datorer som inte är VMware-datorer från gruppen om du tänker köra en AVS-utvärdering.
- Om du kör AVS-utvärderingar i Azure Migrate för första gången rekommenderar vi att du skapar en ny grupp med VMware-datorer.

## <a name="i-cant-see-some-vm-types-in-azure-government"></a>Jag kan inte se vissa typer av virtuella datorer i Azure Government

VM-typer som stöds för utvärdering och migrering är beroende av tillgängligheten på Azure Government plats. Du kan [Granska och jämföra](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) VM-typer i Azure Government.

## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>Storleken på den virtuella datorn ändrades. Kan jag köra en utvärdering igen?

Azure Migrates enheten samlar kontinuerligt in information om den lokala miljön.  En utvärdering är en tidpunkts ögonblicks bild av lokala virtuella datorer. Om du ändrar inställningarna för en virtuell dator som du vill utvärdera använder du alternativet beräkna om du vill uppdatera utvärderingen med de senaste ändringarna.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Hur gör jag för att identifiera virtuella datorer i en miljö med flera organisationer?

- **VMware**: om en miljö delas mellan klienter och du inte vill identifiera en innehavares virtuella datorer i en annan klient prenumeration, skapar du VMware vCenter Server autentiseringsuppgifter som bara kan komma åt de virtuella datorer som du vill identifiera. Använd sedan autentiseringsuppgifterna när du startar identifiering i Azure Migrate-installationen.
- **Hyper-v**: identifiering använder autentiseringsuppgifter för Hyper-v-värden. Om virtuella datorer delar samma Hyper-V-värd finns det för närvarande inget sätt att separera identifieringen.  

## <a name="do-i-need-vcenter-server"></a>Behöver jag vCenter Server?

Ja, Azure Migrate kräver vCenter Server i en VMware-miljö för att utföra identifiering. Azure Migrate stöder inte identifiering av ESXi-värdar som inte hanteras av vCenter Server.

## <a name="what-are-the-sizing-options-in-an-azure-vm-assessment"></a>Vilka storleks alternativ finns i en Azure VM-utvärdering?

Vid samma storlek som lokal storlek kan Azure Migrate inte beakta prestanda data för virtuella datorer för utvärdering. Azure Migrate bedömer VM-storlekar baserat på den lokala konfigurationen. Med prestanda-baserad storleks ändring baseras storleken på användnings data.

Till exempel om en lokal virtuell dator har fyra kärnor och 8 GB minne med 50% processor användning och 50% minnes användning:
- Som lokal storlek rekommenderar vi en Azure VM-SKU som har fyra kärnor och 8 GB minne.
- Prestandabaserade storleks ändringar kommer att rekommendera en VM-SKU som har två kärnor och 4 GB minne, eftersom användnings procenten beaktas.

På samma sätt beror disk storleken på storleks kriterierna och lagrings typen:
- Om storleks kriteriet är prestanda beroende och lagrings typen är automatisk, använder Azure Migrate IOPS-och data flödes värden för disken i kontot när den identifierar mål disk typ (standard eller Premium).
- Om storleks kriteriet är prestanda beroende och lagrings typen är Premium rekommenderar Azure Migrate en Premium-disk-SKU som baseras på den lokala diskens storlek. Samma logik används för disk storlek när storleken är lokalt och lagrings typen är standard eller Premium.

## <a name="does-performance-history-and-utilization-affect-sizing-in-an-azure-vm-assessment"></a>Påverkar prestanda historiken och användningen storleken på en Azure VM-utvärdering?

Ja, prestanda historiken och användningen påverkar storleks ändringen i en Azure VM-utvärdering.

### <a name="performance-history"></a>Prestandahistorik

För prestandabaserade storleks ändringar samlar Azure Migrate prestanda historiken för lokala datorer och använder den för att rekommendera den virtuella datorns storlek och disk typ i Azure:

1. Enheten registrerar kontinuerligt den lokala miljön för att samla in användnings data i real tid var 20: e sekund.
2. Enheten samlar in de insamlade 20-sekunderna exemplen och använder dem för att skapa en enda data punkt var 15: e minut.
3. För att skapa data punkten väljer installationen det högsta värdet från alla 20-sekunders exempel.
4. Enheten skickar data punkten till Azure.

### <a name="utilization"></a>Användning

När du skapar en utvärdering i Azure, beroende på prestanda varaktighet och percentilvärdet för prestanda historik som anges, beräknar Azure Migrate det effektiva användning svärdet och använder det sedan för storleks ändring.

Om du till exempel ställer in varaktigheten på en dag och percentilvärdet till 95 percentil, sorterar Azure Migrate de 15 minuters exempel punkter som skickats av insamlaren för den senaste dagen i stigande ordning. Den plockar 95 percentilvärdet som effektiv användning.

Genom att använda 95 percentil-värdet ser du till att avvikande värden ignoreras. Mät värden kan inkluderas om din Azure Migrate använder 99 percentilen. Om du vill välja högsta användnings nivå för perioden utan saknade avvikande värden, ställer du in Azure Migrate att använda 99-percentilen.


## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>Hur skiljer sig de importbaserade utvärderingarna från utvärderingar med identifierings källa som apparat?

Import-baserade Azure VM-utvärderingar är skapade med datorer som importeras till Azure Migrate med hjälp av en CSV-fil. Endast fyra fält är obligatoriska för import: Server namn, kärnor, minne och operativ system. Här är några saker att tänka på: 
 - Beredskaps kriterierna är mindre strikta i import-baserade utvärderingar i Start typs parametern. Om start typen inte anges förutsätts att datorn har BIOS-starttyp och att datorn inte är markerad som **villkorligt klar**. I utvärderingar med identifierings källa som apparat markeras beredskapen som **villkorligt redo** om start typen saknas. Den här skillnaden i beredskaps beräkningen är att användare kanske inte har all information på datorerna i det tidiga skedet vid planering av migrering när importbaserade utvärderingar görs. 
 - De prestandabaserade import utvärderingarna använder det användnings värde som användaren har angett för beräkningar med rätt storlek. Eftersom användning svärdet tillhandahålls av användaren inaktive ras alternativen **prestanda historik** och **percentil** i utvärderings egenskaperna. I utvärderingar med identifierings källa som apparat plockas det valda percentilvärdet från de prestanda data som samlas in av produkten.

## <a name="why-is-the-suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>Varför är det föreslagna Migreringsverktyg i importerad AVS-utvärdering markerad som okänd?

För datorer som importeras via en CSV-fil är standard verktyget för migrering i en AVS-utvärdering okänd. För VMware-datorer rekommenderar vi dock att du använder HCX-lösningen (VMware Hybrid Cloud Extension). [Läs mer](../azure-vmware/tutorial-deploy-vmware-hcx.md).


## <a name="what-is-dependency-visualization"></a>Vad är beroende visualisering?

Beroende visualisering kan hjälpa dig att utvärdera grupper av virtuella datorer för att migrera med större tillförlitlighet. Beroende visualisering mellan kontroller av dator beroenden innan du kör en utvärdering. Det hjälper till att se till att inget är kvar bakom och hjälper till att undvika oväntade avbrott när du migrerar till Azure. Azure Migrate använder Tjänstkarta-lösningen i Azure Monitor för att aktivera beroende visualisering. [Läs mer](concepts-dependency-visualization.md).

> [!NOTE]
> Agent-baserad beroende analys är inte tillgänglig i Azure Government. Du kan använda agentens beroende analys

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Vad är skillnaden mellan agent-och agenten?

Skillnaderna mellan agent utan visualisering och agentbaserade visualiseringar sammanfattas i tabellen.

**Krav** | **Utan agent** | **Agent-baserad**
--- | --- | ---
Support | Det här alternativet är för närvarande en för hands version och är bara tillgängligt för virtuella VMware-datorer. [Granska](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) operativ system som stöds. | Allmän tillgänglighet (GA).
Agent | Du behöver inte installera agenter på datorer som du vill kryssa för. | Agenter som ska installeras på varje lokal dator som du vill analysera: [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md)och [beroende agenten](../azure-monitor/agents/agents-overview.md#dependency-agent). 
Förutsättningar | [Granska](concepts-dependency-visualization.md#agentless-analysis) kraven och distributions kraven. | [Granska](concepts-dependency-visualization.md#agent-based-analysis) kraven och distributions kraven.
Log Analytics | Krävs inte. | Azure Migrate använder [tjänstkarta](../azure-monitor/vm/service-map.md) -lösningen i [Azure Monitor loggar](../azure-monitor/logs/log-query-overview.md) för beroende visualisering. [Läs mer](concepts-dependency-visualization.md#agent-based-analysis).
Så här fungerar det | Fångar upp TCP-anslutningsfel på datorer aktiverade för beroende visualisering. Efter identifieringen samlar den in data i intervall om fem minuter. | Tjänstkarta agenter som installerats på en dator samla in data om TCP-processer och inkommande/utgående anslutningar för varje process.
Data | Käll datorns Server namn, process, program namn.<br/><br/> Mål datorns Server namn, process, program namn och port. | Käll datorns Server namn, process, program namn.<br/><br/> Mål datorns Server namn, process, program namn och port.<br/><br/> Antalet anslutningar, svars tid och data överförings information samlas in och är tillgängliga för Log Analytics frågor. 
Visualisering | Beroende karta för enskild server kan visas över en varaktighet på en timme till 30 dagar. | Beroende karta för en enskild server.<br/><br/> Kartan kan endast visas över en timme.<br/><br/> Beroende karta för en grupp med servrar.<br/><br/> Lägga till och ta bort servrar i en grupp från MAP-vyn.
Dataexport | De senaste 30 dagarna kan hämtas i CSV-format. | Data kan frågas med Log Analytics.


## <a name="do-i-need-to-deploy-the-appliance-for-agentless-dependency-analysis"></a>Behöver jag distribuera enheten för en agent lös beroende analys?

Ja, [Azure Migrate](migrate-appliance.md) -installationen måste distribueras.

## <a name="do-i-pay-for-dependency-visualization"></a>Betalar jag för beroende visualisering?

Nej. Läs mer om [Azure Migrate prissättning](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>Vad installerar jag för en agent-baserad beroende visualisering?

Om du vill använda agentbaserade beroende visualisering, laddar du ned och installerar agenter på varje lokal dator som du vill utvärdera:

- [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md)
- [Beroendeagent](../azure-monitor/agents/agents-overview.md#dependency-agent)
- Om du har datorer som inte har Internet anslutning kan du hämta och installera Log Analytics gateway på dem.

Du behöver bara dessa agenter om du använder en agent-baserad beroende visualisering.

## <a name="can-i-use-an-existing-workspace"></a>Kan jag använda en befintlig arbets yta?

Ja, för en agent-baserad beroende visualisering kan du koppla en befintlig arbets yta till migreringsjobbet och använda den för beroende visualisering. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Kan jag exportera beroendevisualiseringsrapporten?

Nej, det går inte att exportera den beroende visualiserings rapporten i agent-baserad visualisering. Azure Migrate använder dock Tjänstkarta och du kan använda [tjänstkarta-REST API](/rest/api/servicemap/machines/listconnections) för att hämta beroenden i JSON-format.

## <a name="can-i-automate-agent-installation"></a>Kan jag automatisera Agent installationen?

För agent-baserad beroende visualisering:

- Använd ett [skript för att installera beroende agenten](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent).
- För MMA [använder du kommando raden eller Automation](../azure-monitor/agents/log-analytics-agent.md#installation-options)eller använder ett [skript](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Förutom skript kan du använda distributions verktyg som Microsoft Endpoint Configuration Manager och [Intigua](https://www.intigua.com/intigua-for-azure-migration) för att distribuera agenterna.

## <a name="what-operating-systems-does-mma-support"></a>Vilka operativ system stöder MMA?

- Visa listan över [Windows-operativsystem som stöds av MMA](../azure-monitor/agents/log-analytics-agent.md#installation-options).
- Visa listan över [Linux-operativsystem som stöds av MMA](../azure-monitor/agents/log-analytics-agent.md#installation-options).

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>Kan jag visualisera beroenden i mer än en timme?

För en agent-baserad visualisering kan du visualisera beroenden i upp till en timme. Du kan gå tillbaka så långt som en månad till ett visst datum i historiken, men den maximala varaktigheten för visualiseringen är en timme. Du kan till exempel använda tids längden i beroende kartan för att Visa beroenden för igår, men du kan bara Visa beroenden för ett entimmes fönster. Du kan dock använda Azure Monitor loggar för att [fråga beroende data](./how-to-create-group-machine-dependencies.md) under en längre tid.

För övervakning utan agent kan du Visa beroende kartan för en enskild server från en varaktighet på mellan en timme och 30 dagar.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Kan jag visualisera beroenden för grupper med fler än 10 virtuella datorer?

Du kan [visualisera beroenden](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) för grupper som har upp till 10 virtuella datorer. Om du har en grupp som har fler än 10 virtuella datorer rekommenderar vi att du delar upp gruppen i mindre grupper och sedan visualiserar beroendena.

## <a name="next-steps"></a>Nästa steg

Läs [Azure Migrate översikt](migrate-services-overview.md).