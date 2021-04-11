---
title: Metod tips för utvärdering i Azure Migrate identifierings-och utvärderings verktyg
description: Tips för att skapa utvärderingar med Azure Migrate identifierings-och utvärderings verktyg.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 1bf844dafe450e90213db2e447bb5392064eb245
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786778"
---
# <a name="best-practices-for-creating-assessments"></a>Metod tips för att skapa utvärderingar

[Azure Migrate](./migrate-services-overview.md) innehåller en hubb med verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbets belastningar till Microsoft Azure. Hubben omfattar Azure Migrate-verktyg och oberoende program varu leverantörer från tredje part (ISV).

Den här artikeln sammanfattar bästa praxis när du skapar utvärderingar med hjälp av verktyget Azure Migrate identifiering och bedömning.

Utvärderingar som du skapar med Azure Migrate: identifierings-och utvärderings verktyget är en tidpunkts ögonblicks bild av data. Det finns tre typer av utvärderingar som du kan skapa med hjälp av Azure Migrate: identifiering och utvärdering:

**Utvärderingstyp** | **Information**
--- | --- 
**Azure VM** | Utvärderingar som migrerar dina lokala servrar till virtuella Azure-datorer. <br/><br/> Du kan utvärdera dina lokala servrar i [VMware](how-to-set-up-appliance-vmware.md) [-och Hyper-V-](how-to-set-up-appliance-hyper-v.md) miljön och [fysiska servrar](how-to-set-up-appliance-physical.md) för migrering till Azure med hjälp av den här utvärderings typen. [Läs mer](concepts-assessment-calculation.md)
**Azure SQL** | Utvärderingar för att migrera dina lokala SQL-servrar från din VMware-miljö till Azure SQL Database eller Azure SQL-hanterad instans. [Läs mer](concepts-azure-sql-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Utvärderingar för att migrera dina lokala servrar till [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Du kan utvärdera dina lokala [virtuella VMware-datorer](how-to-set-up-appliance-vmware.md) för migrering till Azure VMware-lösningen (AVS) med den här utvärderings typen. [Läs mer](concepts-azure-vmware-solution-assessment-calculation.md)


### <a name="sizing-criteria"></a>Storlekskriterier
Storleks villkors alternativ i Azure Migrate utvärderingar:

**Storlekskriterier** | **Information** | **Data**
--- | --- | ---
**Prestandabaserad** | Utvärderingar som gör rekommendationer baserat på insamlade prestandadata | **Azure VM-utvärdering**: Rekommendationen för VM-storlek baseras på processor- och minnesanvändningsdata.<br/><br/> Rekommendation för disktyp (Standard HDD/SSD eller premiumhanterade diskar) baseras på IOPS och dataflödet för de lokala diskarna.<br/><br/>**Azure SQL-utvärdering**: Azure SQL-konfigurationen baseras på prestanda data för SQL-instanser och databaser, vilket innefattar: processor användning, minnes användning, IOPS (data-och loggfiler), data flöde och svars tid för IO-åtgärder<br/><br/>**Utvärdering med Azure VMware Solution (AVS)** : Rekommendationen för AVS-noder baseras på processor- och minnesanvändningsdata.
**Som lokalt** | Utvärderingar som inte använder prestandadata till att göra rekommendationer. | **Azure VM-utvärdering**: Rekommendationen för VM-storlek baseras på den lokala virtuella datorstorleken<br/><br> Rekommenderad disktyp baseras på vad du väljer i lagringstypens inställning vid utvärderingen.<br/><br/> **Utvärdering med Azure VMware Solution (AVS)** : Rekommendationen för AVS-noder baseras på den lokala virtuella datorstorleken.

#### <a name="example"></a>Exempel
Om du t. ex. har en lokal virtuell dator med fyra kärnor med 20% belastning och minne på 8 GB med 10% användning, så är Azure VM-utvärderingen följande:

- **Prestanda baserad utvärdering**:
    - Identifierar effektiva kärnor och minne baserat på kärnan (4 x 0,20 = 0,8) och minne (8 GB x 0,10 = 0,8) användning.
    - Använder den bekvämlighets faktor som anges i bedömnings egenskaper (vi antar 1,3 x) för att hämta de värden som ska användas för storleks ändring. 
    - Rekommenderar den närmaste virtuella dator storleken i Azure som har stöd för ~ 1,04 kärnor (0,8 x 1,3) och ~ 1,04 GB (0,8 x 1,3) minne.

- **Som är (som lokal) utvärdering**:
    -  Rekommenderar en virtuell dator med fyra kärnor; 8 GB minne.


## <a name="best-practices-for-creating-assessments"></a>Metod tips för att skapa utvärderingar

Azure Migrates apparaten profiler använder kontinuerligt din lokala miljö och skickar metadata-och prestanda data till Azure. Följ dessa rekommenderade metoder för utvärdering av servrar som identifierats med en installation:

- **Skapa som-är-utvärderingar**: du kan skapa som-är-utvärderingar direkt när dina servrar visas i Azure Migrate-portalen. Du kan inte skapa en Azure SQL-utvärdering med storleks kriterier som lokalt.
- **Skapa prestanda baserad utvärdering**: när du har konfigurerat identifiering rekommenderar vi att du väntar minst en dag innan du kör en prestanda-baserad utvärdering:
    - Insamling av prestanda data tar tid. Om du väntar minst en dag säkerställer det att det finns tillräckligt med prestanda data punkter innan du kör utvärderingen.
    - När du kör prestandabaserade utvärderingar kontrollerar du att du profilerar din miljö för utvärderings perioden. Om du till exempel skapar en utvärdering med en varaktighet på en vecka måste du vänta i minst en vecka efter att du har startat identifieringen för att alla data punkter ska samlas in. Om du inte gör det får utvärderingen inget omdöme på fem stjärnor.
- **Beräkna om utvärderingar**: eftersom utvärderingarna är tidpunkter för ögonblicks bilder uppdateras de inte automatiskt med den senaste informationen. Om du vill uppdatera en utvärdering med senaste data måste du beräkna om den.

Följ dessa metod tips för utvärdering av servrar som importer ATS till Azure Migrate via. CSV-fil:

- **Skapa som-är-utvärderingar**: du kan skapa som-är-utvärderingar direkt när dina servrar visas i Azure Migrate-portalen.
- **Skapa prestandabaserade utvärderingar**: Detta hjälper till att få en bättre kostnads uppskattning, särskilt om du har överetablerat Server kapacitet lokalt. Noggrannheten hos den prestandabaserade utvärderingen beror dock på de prestanda data som du har angett för servrarna. 
- **Beräkna om utvärderingar**: eftersom utvärderingarna är tidpunkter för ögonblicks bilder uppdateras de inte automatiskt med den senaste informationen. Om du vill uppdatera en utvärdering med de senaste importerade data måste du beräkna om den.
 
### <a name="ftt-sizing-parameters-for-avs-assessments"></a>FTT storleks parametrar för AVS-bedömningar

Den lagrings motor som används i AVS är virtuellt San. vSAN-lagringsprinciperna definierar lagringskraven för dina virtuella datorer. Med dessa principer garanteras den tjänstnivå som krävs för dina virtuella datorer eftersom de fastställer hur lagringen allokeras till den virtuella datorn. Tillgängliga FTT-RAID-kombinationer: 

**Fel som ska kunna hanteras (FTT)** | **RAID-konfiguration** | **Lägsta antal värdar som krävs** | **Storleksövervägande**
--- | --- | --- | --- 
1 | RAID-1 (spegling) | 3 | En virtuell dator på 100 GB skulle förbruka 200 GB.
1 | RAID-5 (raderingsskyddad kod) | 4 | En virtuell dator på 100 GB skulle förbruka 133,33 GB
2 | RAID-1 (spegling) | 5 | En virtuell dator på 100 GB skulle förbruka 300 GB.
2 | RAID-6 (raderingsskyddad kod) | 6 | En virtuell dator på 100 GB skulle förbruka 150 GB.
3 | RAID-1 (spegling) | 7 | En virtuell dator på 100 GB skulle förbruka 400 GB.


## <a name="best-practices-for-confidence-ratings"></a>Metod tips för förtroende värderingar

När du kör prestandabaserade utvärderingar tilldelas en förtroende nivå från 1 – stjärna (lägsta) till 5 stjärnor (högsta) till utvärderingen. Så här använder du Tillförlitlighets klassificeringen effektivt:

- Azure VM och AVS-bedömningar behöver:
    - PROCESSOR-och minnes användnings data för var och en av servrarna
    - Läsa/skriva IOPS/data flödes data för varje disk som är ansluten till den lokala servern
    - Nätverket in/ut-data för varje nätverkskort som är kopplat till-servern.
     
- Azure SQL-utvärderingar behöver prestanda data för de SQL-instanser och-databaser som utvärderas, bland annat:
    - PROCESSOR-och minnes användnings data
    - Data-och loggfilerna läsa/skriva IOPS/data flöde
    - Svars tiden för IO-åtgärder

Beroende på hur många data punkter i procent som är tillgängliga för den valda varaktigheten, anges förtroendet för en utvärdering som sammanfattas i följande tabell.

   **Tillgänglighet för data punkt** | **Säkerhetsomdöme**
   --- | ---
   0 %–20 % | 1 stjärna
   21 %–40 % | 2 stjärnor
   41 %–60 % | 3 stjärnor
   61 %–80 % | 4 stjärnor
   81 %–100 % | 5 stjärnor


## <a name="common-assessment-issues"></a>Vanliga utvärderings problem

Så här löser du några vanliga miljö problem som påverkar utvärderingen.

###  <a name="out-of-sync-assessments"></a>Utvärderingar utanför synkroniseringen

Om du lägger till eller tar bort servrar från en grupp efter att du har skapat en utvärdering, markeras den utvärdering som du har skapat **utanför synkroniseringen**. Kör utvärderingen igen (**Beräkna om**) för att återspegla grupp ändringarna.

### <a name="outdated-assessments"></a>Inaktuella bedömningar

#### <a name="azure-vm-assessment-and-avs-assessment"></a>Utvärdering av Azure VM och AVS-utvärdering
Om det finns ändringar på de lokala servrar som finns i en grupp som har bedömts, markeras utvärderingen som **inaktuell**. En utvärdering kan markeras som "inaktuell" på grund av en eller flera ändringar i nedanstående egenskaper:
- Antal processor kärnor
- Allokerat minne
- Start typ eller inbyggd program vara
- Operativ systemets namn, version och arkitektur
- Antal diskar
- Antal nätverkskort
- Ändring av disk storlek (GB allokerat)
- Uppdatera NIC-egenskaper. Exempel: Mac-adress ändringar, IP-adress tillägg osv.
    
Kör utvärderingen igen (**Beräkna om**) för att återspegla ändringarna.
    
#### <a name="azure-sql-assessment"></a>Azure SQL-utvärdering
Om det finns ändringar i lokala SQL-instanser och databaser som finns i en grupp som har utvärderats, markeras utvärderingen som **föråldrad**. En utvärdering kan markeras som "inaktuell" på grund av ett eller flera av följande orsaker:
- En SQL-instans lades till eller togs bort från en server
- En SQL-databas lades till eller togs bort från en SQL-instans
- Den totala databasstorleken i en SQL-instans har ändrats med mer än 20 %
- Ändring i antal processor kärnor
- Ändra i allokerat minne        
  
    Kör utvärderingen igen (**Beräkna om**) för att återspegla ändringarna.

### <a name="low-confidence-rating"></a>Bedömning av låg exakthet

En utvärdering kanske inte har alla data punkter av olika anledningar:

- Du profilerade inte din miljö för hela den varaktighet för vilken du skapar utvärderingen. Om du till exempel skapar en utvärdering med en varaktighet på en vecka måste du vänta minst en vecka efter att identifieringen startade, tills alla datapunkter har samlats in. Om du inte kan vänta hela varaktigheten ändrar du varaktigheten för prestanda till en kortare period och ”räknar om” utvärderingen.
 
- Utvärderingen kan inte samla in prestandadata för vissa eller alla servrar under utvärderingsperioden. För en hög exakthet bör du se till att: 
    - Servrarna är påslagna under utvärderings perioden
    - Utgående anslutningar på portarna 443 tillåts
    - För Hyper-V-servrar är dynamiskt minne aktiverat 
    - Anslutnings statusen för agenter i Azure Migrate är ansluten och kontrollerar senaste pulsslag
    - För Azure SQL-utvärderingar är Azure Migrate anslutnings status för alla SQL-instanser "ansluten" på bladet identifierad SQL-instans

    Beräkna om utvärderingen så att de senaste ändringarna återspeglas i säkerhetsomdömet.

- För virtuella Azure-datorer och AVS-bedömningar skapades några servrar efter att identifieringen hade startats. Om du till exempel skapar en utvärdering för prestanda historiken för den senaste månaden, men bara några servrar har skapats i miljön för en vecka sedan. I det här fallet är prestanda data för de nya servrarna inte tillgängliga under hela varaktigheten och förtroendet är lågt.

- Vid Azure SQL-utvärderingar skapades några SQL-instanser eller databaser efter att identifieringen hade startats. Om du till exempel skapar en utvärdering för prestanda historiken för den senaste månaden, men bara några SQL-instanser eller databaser har skapats i miljön för en vecka sedan. I det här fallet är prestanda data för de nya servrarna inte tillgängliga under hela varaktigheten och förtroendet är lågt.

### <a name="migration-tool-guidance-for-avs-assessments"></a>Vägledning för Migration Tool för AVS-utvärderingar

I Azure-beredskapsrapporten för Azure VMware Solution (AVS)-utvärdering föreslås följande verktyg: 
- **VMware HCX eller Enterprise**: för VMware-servrar är VMware Hybrid Cloud Extensions (HCX)-lösningen det rekommenderade migreringsjobbet för att migrera din lokala arbets belastning till ditt Azure VMware-lösning (AVS) privat moln. [Läs mer](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Okänd**: för servrar som importeras via en CSV-fil är standard verktyget för migrering okänt. För servrar i VMware-miljön rekommenderar vi dock att du använder VMware Hybrid Cloud Extension (HCX)-lösningen.


## <a name="next-steps"></a>Nästa steg

- [Lär dig](concepts-assessment-calculation.md) hur utvärderingar beräknas.
- [Lär dig](how-to-modify-assessment.md) hur du anpassar en utvärdering.
