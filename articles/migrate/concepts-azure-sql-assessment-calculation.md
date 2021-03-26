---
title: Azure SQL-utvärderingar i Azure Migrate identifierings-och utvärderings verktyg
description: Läs mer om Azure SQL-utvärderingar i Azure Migrate identifierings-och utvärderings verktyg
author: rashi-ms
ms.author: rajosh
ms.topic: conceptual
ms.date: 02/07/2021
ms.openlocfilehash: c2e739a45c7915c957ca89e5b01b98afa945d03e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557198"
---
# <a name="assessment-overview-migrate-to-azure-sql"></a>Översikt över utvärdering (migrera till Azure SQL)

Den här artikeln innehåller en översikt över utvärderingar för migrering av lokala SQL Server instanser från en VMware-miljö till Azure SQL-databaser eller hanterade instanser med hjälp av [verktyget Azure Migrate: identifiering och bedömning](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool).

## <a name="whats-an-assessment"></a>Vad är en utvärdering?
En utvärdering med identifierings-och utvärderings verktyget är en tidpunkt för ögonblicks bilder av data och mäter den beredskap och uppskattar effekterna av att migrera lokala servrar till Azure.

## <a name="types-of-assessments"></a>Typer av utvärderingar

Det finns tre typer av utvärderingar som du kan skapa med hjälp av verktyget Azure Migrate: identifiering och bedömning.

**Utvärderingstyp** | **Information**
--- | --- 
**Azure VM** | Utvärderingar som migrerar dina lokala servrar till virtuella Azure-datorer. <br/><br/> Du kan utvärdera dina lokala servrar i [VMware](how-to-set-up-appliance-vmware.md) [-och Hyper-V-](how-to-set-up-appliance-hyper-v.md) miljön och [fysiska servrar](how-to-set-up-appliance-physical.md) för migrering till virtuella Azure-datorer med den här utvärderings typen.
**Azure SQL** | Utvärderingar för att migrera dina lokala SQL-servrar från din VMware-miljö till Azure SQL Database eller Azure SQL-hanterad instans.
**Azure VMware Solution (AVS)** | Utvärderingar för att migrera dina lokala servrar till [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Du kan utvärdera dina lokala [virtuella VMware-datorer](how-to-set-up-appliance-vmware.md) för migrering till Azure VMware-lösningen (AVS) med den här utvärderings typen. [Läs mer](concepts-azure-vmware-solution-assessment-calculation.md)

En Azure SQL-utvärdering ger ett storleks villkor:

**Storlekskriterier** | **Information** | **Data**
--- | --- | ---
**Prestandabaserad** | Utvärderingar som gör rekommendationer baserat på insamlade prestandadata | Azure SQL-konfigurationen baseras på prestanda data för SQL-instanser och databaser, vilket omfattar: processor användning, minnes användning, IOPS (data-och loggfiler), data flöde och svars tid för IO-åtgärder.

## <a name="how-do-i-assess-my-on-premises-sql-servers"></a>Hur gör jag för att du utvärdera mina lokala SQL-servrar?

Du kan utvärdera dina lokala SQL Server-instanser genom att använda konfigurations-och användnings data som samlas in av en Lightweight Azure Migrate-enhet. Enheten identifierar lokala SQL Server-instanser och databaser och skickar konfigurations-och prestanda data till Azure Migrate. [Läs mer](how-to-set-up-appliance-vmware.md)

## <a name="how-do-i-assess-with-the-appliance"></a>Hur gör jag för att du utvärdera med produkten?
Gör så här om du distribuerar en Azure Migrate-enhet för att identifiera lokala servrar:
1.  Konfigurera Azure och din lokala miljö för att arbeta med Azure Migrate.
2.  För din första utvärdering skapar du ett Azure Migrate-projekt och lägger till Azure Migrate: identifierings-och utvärderings verktyget.
3.  Distribuera en förenklad Azure Migrate-apparat. Enheten identifierar kontinuerligt lokala servrar och skickar konfigurations-och prestanda data till Azure Migrate. Distribuera installationen som en virtuell dator eller fysisk server. Du behöver inte installera något på de servrar som du vill utvärdera.

När installationen har påbörjat identifieringen kan du samla in servrar som du vill utvärdera i en grupp och köra en utvärdering av gruppen med utvärderings typ **Azure SQL**.

Följ våra självstudier för att utvärdera [SQL Server instanser](tutorial-assess-sql.md) för att prova de här stegen.

## <a name="how-does-the-appliance-calculate-performance-data-for-sql-instances-and-databases"></a>Hur beräknar enheten prestanda data för SQL-instanser och databaser?

Installations programmet samlar in prestanda data för beräknings inställningar med följande steg:
1. Enheten samlar in en exempel punkt i real tid. För SQL-servrar samlas en exempel punkt in var 30: e sekund.
2. Enheten sammanställer exempel data punkterna som samlats in var 30: e sekund över 10 minuter. För att skapa data punkten väljer enheten de högsta värdena från alla exempel. Den skickar Max, medelvärde och varians för varje räknare till Azure.
3. Azure Migrate lagrar alla data punkter på 10 minuter för den senaste månaden.
4. När du skapar en utvärdering identifierar Azure Migrate lämplig data punkt som ska användas för att göra rättigheter. Identifieringen baseras på percentilvärdet för prestanda historik och percentils användning.
    - Om prestanda historiken till exempel är en vecka och percentilvärdet använder 95 percentilen, sorterar utvärderingen 10-minuters exempel punkter för den senaste veckan. De sorterar dem i stigande ordning och plockar 95 percentilvärdet för att få behörighet.
    - Värdet 95 percentil ser till att du ignorerar eventuella avvikande värden, som kan inkluderas om du har valt 99 percentilen.
    - Om du vill välja högsta användnings nivå för perioden och inte vill missa avvikande värden väljer du den 99 percentilen för percentils användning.
5. Det här värdet multipliceras med den praktiska faktorn för att få den effektiva prestanda användnings informationen för de mått som installeras av enheten:
    - PROCESSOR användning (%)
    - Minnes användning (%)
    - Läs-i/o/s och skriv-i/o/s (data-och loggfiler)
    - Läsa MB/s och Write MB/s (data flöde)
    - Svars tid för IO-åtgärder

## <a name="what-properties-are-used-to-create-and-customize-an-azure-sql-assessment"></a>Vilka egenskaper används för att skapa och anpassa en Azure SQL-utvärdering?

Här är what's som ingår i utvärderings egenskaperna för Azure SQL:

**Egenskap** | **Information**
--- | ---
**Målplats** | Den Azure-region som du vill migrera till. Azure SQL-konfigurationen och kostnads rekommendationerna baseras på den plats som du anger.
**Mål distributions typ** | Den mål distributions typ som du vill köra utvärderingen på: <br/><br/> Välj **rekommenderas** om du vill Azure Migrate utvärdera beredskap för dina SQL-servrar för att migrera till Azure SQL mi och Azure SQL DB, och rekommendera det bästa anpassade distributions alternativet, mål nivån, Azure SQL-konfigurationen och månads uppskattningar.<br/><br/>Välj **Azure SQL DB** om du vill utvärdera dina SQL-servrar för att migrera till enbart Azure SQL-databaser och granska mål nivån, Azure SQL DB-konfigurationen och månads uppskattningar.<br/><br/>Välj **Azure SQL mi**, om du vill utvärdera dina SQL-servrar för att migrera till enbart Azure SQL-databaser och granska mål nivån, Azure SQL mi-konfiguration och månads uppskattningar.
**Reserverad kapacitet** | Anger reserverad kapacitet så att kostnads uppskattningar i utvärderingen tar dem i beaktande.<br/><br/> Om du väljer alternativet reserverad kapacitet kan du inte ange "rabatt (%)".
**Storlekskriterier** | Den här egenskapen används för att anpassa Azure SQL-konfigurationen till rätt storlek. <br/><br/> Standardvärdet är " **prestanda** ", vilket innebär att utvärderingen samlar in SQL Server instanser och databas prestanda mått för att rekommendera en optimal storlek för Azure SQL-hanterad instans och/eller Azure SQL Database nivå/konfigurations rekommendation.
**Prestandahistorik** | Prestanda historik anger den varaktighet som används när prestanda data utvärderas.
**Percentilutnyttjande** | Percentils användning anger percentilvärdet för det prestanda exempel som används för att ha behörighet.
**Komfortfaktor** | Den buffert som användes under utvärderingen. IT-konton för problem som säsongs användning, kort prestanda historik och sannolika ökningar i framtida användning.<br/><br/> Till exempel resulterar en 10-Core-instans med 20% belastning vanligt vis i en instans med två kärnor. Med en bekvämlighets faktor på 2,0 är resultatet en fyra kärnor-instans i stället.
**Erbjudande/licensierings program** | Det [Azure-erbjudande](https://azure.microsoft.com/support/legal/offer-details/) som du har registrerat i. För närvarande kan du bara välja från "betala per användning" och "betala per användning"-utveckling/-test. Observera att du kan utnyttja ytterligare rabatt genom att använda reserverad kapacitet och Azure Hybrid-förmån ovanpå erbjudandet betala per användning.
**Tjänstenivå** | Det lämpligaste tjänst nivå alternativet för att tillgodose ditt företags behov av migrering till Azure SQL Database och/eller Azure SQL-hanterad instans:<br/><br/>**Rekommenderas** om du vill Azure Migrate rekommendera den bästa lämpliga tjänst nivån för dina servrar. Detta kan vara generell användning eller affärs kritisk. <br/><br/> **Generell användning** Om du vill ha en Azure SQL-konfiguration som är utformad för budgetorienterade arbets belastningar. [Läs mer](../azure-sql/database/service-tier-general-purpose.md) <br/><br/> **Affärskritisk** Om du vill ha en Azure SQL-konfiguration som är utformad för arbets belastningar med låg latens med hög återhämtnings förmåga till fel och snabba redundanser. [Läs mer](../azure-sql/database/service-tier-business-critical.md)
**Valuta** | Fakturerings valutan för ditt konto.
**Rabatt (%)** | Eventuella prenumerations rabatter som du får ovanpå Azure-erbjudandet. Standardinställningen är 0%.
**Azure Hybrid-förmån** | Anger om du redan har en SQL Server-licens. <br/><br/> Om du gör det, och de omfattas med aktiv Software Assurance för SQL Server prenumerationer, kan du ansöka om Azure Hybrid-förmån när du gör licenser till Azure.

[Granska de bästa metoderna](best-practices-assessment.md) för att skapa en utvärdering med Azure Migrate.

## <a name="calculate-readiness"></a>Beräkna beredskap

> [!NOTE]
Utvärderingen inkluderar bara databaser som har statusen online. Om det finns databaser med någon annan status, ignorerar utvärderingen beredskapen, storleken och kostnadsberäkningen för dessa databaser. Om du vill utvärdera dessa databaser ändrar du databasens status och beräknar om utvärderingen efter ett tag.

### <a name="azure-sql-readiness"></a>Azure SQL-beredskap

Azure SQL-beredskap för SQL-instanser och databaser baseras på en funktions kompatibilitetskontroll med Azure SQL Database och Azure SQL-hanterad instans:
1. Azure SQL-utvärderingen betraktar de SQL Server instans funktioner som för närvarande används av käll SQL Server arbets belastningar (SQL Agent-jobb, länkade servrar osv.) och användar databas scheman (tabeller, vyer, utlösare, lagrade procedurer osv.) för att identifiera kompatibilitetsproblem.
1. Om det inte finns några kompatibilitetsproblem markeras beredskapen som **klar** för mål distributions typen (Azure SQL Database eller Azure SQL-hanterad instans)
1. Om det finns icke-kritiska kompatibilitetsproblem, till exempel degraderade eller icke-stödda funktioner som inte blockerar migreringen till en speciell mål distributions typ, markeras beredskapen som **klar** (ikon ikonen hyperlänkad och blå) med **varnings** information och rekommendationer om rekommenderade reparationer.
1. Om det finns kompatibilitetsproblem som kan blockera migreringen till en speciell mål distributions typ, markeras beredskapen som **ej klar** med **problem** information och Rekommenderad reparations vägledning.
    - Om det finns en databas i en SQL-instans som inte är redo för en viss mål distributions typ markeras instansen som **ej klar** för den distributions typen.
1. Om identifieringen fortfarande pågår eller det förekommer några identifierings problem för en SQL-instans eller databas, markeras beredskapen som **okänd** eftersom utvärderingen inte kunde beräkna beredskap för den SQL-instansen.

### <a name="recommended-deployment-type"></a>Rekommenderad distributions typ

Om du väljer mål distributions typen som **rekommenderas** i Azure SQL Assessment-egenskaperna rekommenderar Azure Migrate en distributions typ för Azure SQL som är kompatibel med SQL-instansen. Om du migrerar till ett Microsoft-rekommenderat mål minskar du den totala migreringen. 

#### <a name="recommended-deployment-type-based-on-azure-sql-readiness"></a>Rekommenderad distributions typ baserat på Azure SQL-beredskap

 **Beredskap för Azure SQL DB** | **Azure SQL MI-beredskap** | **Rekommenderad distributions typ** | **Beräknas Azure SQL-konfiguration och kostnads uppskattningar?**
 --- | --- | --- | --- |
 Redo | Redo | Azure SQL DB eller <br/>Azure SQL MI | Yes
 Redo | Inte klar eller<br/> Okänt | Azure SQL-databas | Yes
 Inte klar eller<br/>Okänt | Redo | Azure SQL MI | Yes
 Inte klar | Inte klar | Potentiellt redo för virtuell Azure-dator | No
 Inte klar eller<br/>Okänt | Inte klar eller<br/>Okänt | Okänt | No

> [!NOTE]
> Om den rekommenderade distributions typen väljs som **rekommenderat** i bedömnings egenskaper, och om käll SQL Server passar bra för både Azure SQL DB-databasen och den hanterade Azure SQL-instansen, rekommenderar utvärderingen ett särskilt alternativ som optimerar kostnaden och passar i storlek och prestanda gränser.

#### <a name="potentially-ready-for-azure-vm"></a>Potentiellt redo för virtuell Azure-dator

Om SQL-instansen inte är klar för Azure SQL Database och Azure SQL-hanterad instans, markeras den rekommenderade distributions typen som *potentiellt redo för virtuella Azure-datorer*.
- Användaren rekommenderas att skapa en utvärdering i Azure Migrate med utvärderings typ som "Azure VM" för att avgöra om den server där instansen körs är redo att migrera till en virtuell Azure-dator i stället. Tänk på följande:
    - Azure VM-utvärderingar i Azure Migrate lyfts för närvarande och flyttas fokuserat och tar inte hänsyn till de exakta prestanda måtten för att köra SQL-instanser och databaser på den virtuella Azure-datorn. 
    - När du kör en Azure VM-utvärdering på en server, kommer de rekommenderade storlekarna och kostnadsuppskattningarna att gälla för alla instanser som körs på servern och som kan migreras till en virtuell Azure-dator med hjälp av verktyget för servermigrering. Innan du migrerar bör du [gå igenom prestandariktlinjerna](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) för SQL Server på virtuella Azure-datorer.


## <a name="calculate-sizing"></a>Beräkna storlek

### <a name="azure-sql-configuration"></a>Azure SQL-konfiguration

När utvärderingen bestämmer beredskap och den rekommenderade distributions typen för Azure SQL beräknar den en bestämd tjänst nivå och Azure SQL-konfiguration (SKU-storlek) som kan uppfylla eller överskrida den lokala SQL-instansens prestanda:
1. Under identifierings processen samlar Azure Migrate in konfiguration och prestanda för SQL-instanser som innehåller:
    - Virtuella kärnor (tilldelad) och processor användning (%)
        - CPU-användning för en SQL-instans är procent andelen allokerad processor som används av instansen på SQL-servern
        - CPU-användning för en databas är procent andelen allokerad processor som används av databasen på SQL-instansen
    - Minne (allokerat) och minnes användning (%)
    - Läs-i/o/s och skriv-i/o/s (data-och loggfiler)
        - Läs-i/o/s och skriv-i/o/s på en SQL-instansnivå beräknas genom att du lägger till Läs-i/o/s och skriv-i/o/s för alla databaser som identifierats
    - Läsa MB/s och Write MB/s (data flöde)
    - Svars tid för IO-åtgärder
    - Total databas storlek och databas fil organisationer
        - Databas storleken beräknas genom att lägga till alla data-och loggfiler.
1. Utvärderingen sammanställer alla konfigurations-och prestanda data och försöker hitta den bästa matchningen för olika Azure SQL Service-nivåer och-konfigurationer, och plockar en konfiguration som kan matcha eller överskrida prestanda kraven för SQL-instanser, och optimera kostnaden.

### <a name="confidence-ratings"></a>Tillförlitlighets klassificering 
Varje Azure SQL-utvärdering är associerad med en förtroende bedömning. Omdömet sträcker sig från en (lägsta) till fem (högsta) stjärnor. Förtroende omdömet hjälper dig att beräkna tillförlitligheten för storleks rekommendationer Azure Migrate tillhandahåller.
- Förtroende klassificeringen tilldelas en utvärdering. Omdömet baseras på tillgängligheten för data punkter som behövs för att beräkna utvärderingen.
- För prestandabaserade storleks ändringar samlar utvärderingen in prestanda data för alla SQL-instanser och databaser, bland annat:
    - PROCESSOR användning (%)
    - Minnes användning (%)
    - Läs-i/o/s och skriv-i/o/s (data-och loggfiler)
    - Läsa MB/s och Write MB/s (data flöde)
    - Svars tid för IO-åtgärder
    
Om något av dessa användnings nummer inte är tillgängligt kan storleks rekommendationerna vara otillförlitliga.
I den här tabellen visas klassificeringen av bedömning av säkerhet, vilket beror på procent andelen tillgängliga data punkter:

**Tillgänglighet för data punkt** | **Säkerhetsomdöme**
--- | ---
0 %–20 % | 1 stjärna
21 %–40 % | 2 stjärnor
41 %–60 % | 3 stjärnor
61 %–80 % | 4 stjärnor
81 %–100 % | 5 stjärnor

#### <a name="low-confidence-ratings"></a>Värderingar med låg exakthet
Här följer några skäl till varför en utvärdering kan få en låg exakthet:
- Du profilerade inte din miljö för hela den varaktighet för vilken du skapar utvärderingen. Om du till exempel skapar utvärderingen med varaktigheten inställd på en dag måste du vänta minst en dag efter att du har startat identifieringen för alla data punkter som ska samlas in.
- Utvärderingen kan inte samla in prestandadata för vissa eller alla servrar under utvärderingsperioden. För en hög exakthet bör du se till att:
    - Servrarna är påslagna under utvärderings perioden
    - Utgående anslutningar på portarna 443 tillåts
    - Om Azure Migrate anslutnings status för SQL-agenten i Azure Migrate är ansluten och kontrol lera senaste pulsslag 
    - Att anslutningsstatusen i Azure Migrate för alla SQL-instanser är ”Ansluten” på bladet med den identifierade SQL-instansen

    Beräkna om utvärderingen så att de senaste ändringarna återspeglas i säkerhetsomdömet.
- Vissa databaser eller instanser skapades under den tid då utvärderingen beräknades. Anta till exempel att du har skapat en utvärdering för prestanda historiken för den senaste månaden, men vissa databaser eller instanser skapades bara för en vecka sedan. I det här fallet är prestanda data för de nya servrarna inte tillgängliga under hela varaktigheten och förtroendet är lågt.

> [!NOTE]
> I takt med att Azure SQL-utvärderingar är prestandabaserade utvärderingar, rekommenderar vi att du väntar minst en dag för installationen av miljön och sedan beräknar om utvärderingen, om förtroende omdömet för en utvärdering är mindre än fem stjärnor. Annars kan prestandabaserade storleks ändringar vara otillförlitliga.

## <a name="calculate-monthly-costs"></a>Beräkna månads kostnader
När du har ändrat rekommendationerna beräknar Azure SQL-utvärderingen beräknings-och lagrings kostnaderna för de rekommenderade Azure SQL-konfigurationerna med hjälp av ett internt pris-API. Den sammanställer beräknings-och lagrings kostnaden i alla instanser för att beräkna den totala månads beräknings kostnaden. 
### <a name="compute-cost"></a>Beräknings kostnad
- För att beräkna beräknings kostnaden för en Azure SQL-konfiguration beaktas följande egenskaper av utvärderingen:
    - Azure Hybrid-förmån för SQL-licenser
    - Reserverad kapacitet
    - Mål plats för Azure
    - Valuta
    - Erbjudande/licensierings program
    - Rabatt (%)

### <a name="storage-cost"></a>Lagringskostnad
- Beräkningarna av lagrings kostnaden omfattar bara datafiler och inte loggfiler. 
- Vid beräkning av lagrings kostnader för en Azure SQL-konfiguration beaktas följande egenskaper av utvärderingen:
    - Mål plats för Azure
    - Valuta
    - Erbjudande/licensierings program
    - Rabatt (%)
- Lagrings kostnaden för säkerhets kopiering ingår inte i utvärderingen.
- **Azure SQL Database**
    - Minst 5 GB lagrings kostnader läggs till i kostnads beräkningen och ytterligare lagrings kostnader läggs till för lagring i 1 GB steg. [Läs mer](https://azure.microsoft.com/pricing/details/sql-database/single/)
- **Hanterad Azure SQL-instans**
    - Ingen lagrings kostnad har lagts till för den första 32 GB/instans/månad-lagringen och ytterligare lagrings kostnader har lagts till för lagring i 32 GB steg. [Läs mer](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)
        
## <a name="next-steps"></a>Nästa steg
- [Granska](best-practices-assessment.md) Metod tips för att skapa utvärderingar. 
- Lär dig hur du kör en [Azure SQL-utvärdering](how-to-create-azure-sql-assessment.md).