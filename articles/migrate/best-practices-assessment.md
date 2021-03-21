---
title: Rekommenderade metoder för utvärdering i Azure Migrate Server-utvärdering
description: Tips för att skapa utvärderingar med Azure Migrate Server-utvärdering.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: c1fff5b5b7f6450ad8d1977e55a1f6b255f3d668
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96754325"
---
# <a name="best-practices-for-creating-assessments"></a>Metod tips för att skapa utvärderingar

[Azure Migrate](./migrate-services-overview.md) innehåller en hubb med verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbets belastningar till Microsoft Azure. Hubben omfattar Azure Migrate-verktyg och oberoende program varu leverantörer från tredje part (ISV).

I den här artikeln sammanfattas metod tips när du skapar utvärderingar med verktyget Azure Migrate Server Assessment.

## <a name="about-assessments"></a>Om utvärderingar

Utvärderingar som du skapar med Azure Migrate Server utvärdering är en tidpunkts ögonblicks bild av data. Det finns två typer av utvärderingar som du kan skapa med hjälp av Azure Migrate: Server utvärdering:

**Utvärderingstyp** | **Information**
--- | --- 
**Azure VM** | Utvärderingar som migrerar dina lokala servrar till virtuella Azure-datorer. <br/><br/> Du kan utvärdera dina lokala [virtuella VMware-datorer](how-to-set-up-appliance-vmware.md), [virtuella Hyper-V-datorer](how-to-set-up-appliance-hyper-v.md) och [fysiska servrar](how-to-set-up-appliance-physical.md) för migrering till Azure med denna utvärderingstyp. [Läs mer](concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Utvärderingar för att migrera dina lokala servrar till [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Du kan utvärdera dina lokala [virtuella VMware-datorer](how-to-set-up-appliance-vmware.md) för migrering till Azure VMware-lösningen (AVS) med den här utvärderings typen. [Läs mer](concepts-azure-vmware-solution-assessment-calculation.md)


### <a name="sizing-criteria"></a>Storlekskriterier
Serverutvärderingen innehåller två alternativ för storleksändring:

**Storlekskriterier** | **Information** | **Data**
--- | --- | ---
**Prestandabaserad** | Utvärderingar som gör rekommendationer baserat på insamlade prestandadata | **Azure VM-utvärdering**: Rekommendationen för VM-storlek baseras på processor- och minnesanvändningsdata.<br/><br/> Rekommendation för disktyp (Standard HDD/SSD eller premiumhanterade diskar) baseras på IOPS och dataflödet för de lokala diskarna.<br/><br/> **Utvärdering med Azure VMware Solution (AVS)** : Rekommendationen för AVS-noder baseras på processor- och minnesanvändningsdata.
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

- **Skapa som-är-utvärderingar**: du kan skapa som-är-utvärderingar direkt när dina datorer visas i Azure Migrate-portalen.
- **Skapa prestanda baserad utvärdering**: när du har konfigurerat identifiering rekommenderar vi att du väntar minst en dag innan du kör en prestanda-baserad utvärdering:
    - Insamling av prestanda data tar tid. Om du väntar minst en dag säkerställer det att det finns tillräckligt med prestanda data punkter innan du kör utvärderingen.
    - När du kör prestandabaserade utvärderingar kontrollerar du att du profilerar din miljö för utvärderings perioden. Om du till exempel skapar en utvärdering med en varaktighet på en vecka måste du vänta i minst en vecka efter att du har startat identifieringen för att alla data punkter ska samlas in. Om du inte gör det får utvärderingen inget omdöme på fem stjärnor.
- **Beräkna om utvärderingar**: eftersom utvärderingarna är tidpunkter för ögonblicks bilder uppdateras de inte automatiskt med den senaste informationen. Om du vill uppdatera en utvärdering med senaste data måste du beräkna om den.

Följ dessa metod tips för utvärdering av servrar som importer ATS till Azure Migrate via. CSV-fil:

- **Skapa som-är-utvärderingar**: du kan skapa som-är-utvärderingar direkt när dina datorer visas i Azure Migrate-portalen.
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
- Azure Migrate Server utvärderingen behöver användnings data för CPU/minne för virtuell dator.
- För varje disk som är ansluten till den lokala virtuella datorn behöver den läsa/skriva IOPS/data flödes data.
- För varje nätverkskort som är kopplat till den virtuella datorn behöver det nätverket i/ut-data.

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

Om du lägger till eller tar bort datorer från en grupp efter att du har skapat en utvärdering, markeras den utvärdering som du har skapat **utanför synkroniseringen**. Kör utvärderingen igen (**Beräkna om**) för att återspegla grupp ändringarna.

### <a name="outdated-assessments"></a>Inaktuella bedömningar

Om det finns lokala ändringar av virtuella datorer som finns i en grupp som har bedömts, markeras utvärderingen som **föråldrad**. En utvärdering kan markeras som "inaktuell" på grund av en eller flera ändringar i nedanstående egenskaper:

- Antal processor kärnor
- Allokerat minne
- Start typ eller inbyggd program vara
- Operativ systemets namn, version och arkitektur
- Antal diskar
- Antal nätverkskort
- Ändring av disk storlek (GB allokerat)
- Uppdatera NIC-egenskaper. Exempel: Mac-adress ändringar, IP-adress tillägg osv.

Kör utvärderingen igen (**Beräkna om**) för att återspegla ändringarna.

### <a name="low-confidence-rating"></a>Bedömning av låg exakthet

En utvärdering kanske inte har alla data punkter av olika anledningar:

- Du profilerade inte din miljö för hela den varaktighet för vilken du skapar utvärderingen. Om du till exempel skapar en *prestanda baserad utvärdering* med varaktigheten en vecka, måste du vänta i minst en vecka efter att du har startat identifieringen av alla data punkter som ska samlas in. Du kan alltid klicka på **Beräkna** om för att se den senaste giltiga förtroende klassificeringen. Förtroende klassificeringen gäller endast när du skapar en *prestanda baserad* utvärdering.

- Några virtuella datorer stängdes av under perioden som utvärderingen utfördes. Om några virtuella datorer var avstängda under en viss period, kan Server Assessment inte samla in prestandadata för den perioden.

- Få virtuella datorer skapades efter att identifieringen startades i Server Assessment. Om du till exempel skapar en utvärdering för prestandahistoriken för den senaste månaden, men några virtuella datorer skapades i miljön för en vecka sedan. I detta fall kommer prestandadata för de nya virtuella datorerna inte att vara tillgängliga för hela tidsperioden och säkerhetsomdömet blir lågt.

### <a name="migration-tool-guidance-for-avs-assessments"></a>Vägledning för Migration Tool för AVS-utvärderingar

I Azure-beredskapsrapporten för Azure VMware Solution (AVS)-utvärdering föreslås följande verktyg: 
- **VMware HCX eller Enterprise**: för VMware-datorer är HCX-lösningen (VMware Hybrid Cloud Extensions) det rekommenderade Migreringsverktyg för att migrera din lokala arbets belastning till ditt Azure VMware-lösning (AVS) privat moln. [Läs mer](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Okänt**: Standardmigreringsverktyget är okänt för datorer som importerats via en CSV-fil. För VMware-datorer rekommenderar vi dock att du använder HCX-lösningen (VMware Hybrid Cloud Extension).


## <a name="next-steps"></a>Nästa steg

- [Lär dig](concepts-assessment-calculation.md) hur utvärderingar beräknas.
- [Lär dig](how-to-modify-assessment.md) hur du anpassar en utvärdering.
