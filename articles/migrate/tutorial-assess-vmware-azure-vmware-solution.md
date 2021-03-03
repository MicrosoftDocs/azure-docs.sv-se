---
title: Utvärdera virtuella VMware-datorer för migrering till Azure VMware-lösning (AVS) med Azure Migrate
description: Lär dig hur du bedömer virtuella VMware-datorer för migrering till AVS med Azure Migrate Server bedömning.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: c1c56edacbc777b5e8b53da588bc763201379964
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101718836"
---
# <a name="tutorial-assess-vmware-vms-for-migration-to-avs"></a>Självstudie: utvärdera virtuella VMware-datorer för migrering till AVS

Som en del av migreringen till Azure bedömer du dina lokala arbets belastningar för att mäta moln beredskap, identifiera risker och beräkna kostnader och komplexitet.

Den här artikeln visar hur du bedömer identifierade virtuella VMware-datorer (VM) för migrering till Azure VMware-lösning (AVS) med hjälp av verktyget Azure Migrate: Server bedömning. AVS är en hanterad tjänst som gör att du kan köra VMware-plattformen i Azure.

I den här guiden får du lära dig att:
> [!div class="checklist"]
- Kör en utvärdering baserat på datorns metadata och konfigurations information.
- Kör en utvärdering baserat på prestanda data.

> [!NOTE]
> Självstudier visar den snabbaste sökvägen för att testa ett scenario och använda standard alternativ där det är möjligt. 

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.



## <a name="prerequisites"></a>Förutsättningar

Innan du följer den här självstudien för att utvärdera datorerna för migrering till AVS, kontrollerar du att du har identifierat de datorer som du vill utvärdera:

- [Följ den här självstudien](tutorial-discover-vmware.md)för att identifiera datorer som använder Azure Migrate-installationen. 
- [Följ den här självstudien](tutorial-discover-import.md)för att identifiera datorer som använder en importerad CSV-fil.


## <a name="decide-which-assessment-to-run"></a>Bestäm vilken utvärdering som ska köras


Bestäm om du vill köra en utvärdering med storleks kriterier baserat på dator konfigurations data/metadata som samlas in lokalt eller på dynamiska prestanda data.

**Utvärdering** | **Information** | **Rekommendation**
--- | --- | ---
**Som lokalt** | Utvärdera baserat på dator konfigurations data/metadata.  | Den rekommenderade nodtypen i AVS baseras på den lokala virtuella dator storleken, tillsammans med de inställningar som du anger i utvärderingen för inställningen nodtyp, lagrings typ och problem-till-tolerera.
**Prestandabaserad** | Utvärdera baserat på insamlade dynamiska prestanda data. | Rekommenderad nodadress i AVS baseras på processor-och minnes användnings data, tillsammans med de inställningar som du anger i utvärderingen för inställningen nodtyp, lagrings typ och typ av problem.

> [!NOTE]
> Azure VMware Solution (AVS)-utvärdering kan bara skapas för virtuella VMware-datorer.

## <a name="run-an-assessment"></a>Köra en utvärdering

Kör en utvärdering på följande sätt:

1. På sidan **servrar** > **Windows-och Linux-servrar** klickar du på **utvärdera och migrera servrar**.

   ![Knappen utvärdera och migrera servrar](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. Klicka på **utvärdera** i **Azure Migrate: Server bedömning**.

1. I   >  **utvärderings typ av utvärderings** servrar väljer du **Azure VMware-lösning (AVS)**.

1. I **identifierings källa**:

    - Om du har identifierat datorer som använder-enheten väljer du **datorer som identifierats från Azure Migrate**-installationen.
    - Om du har identifierat datorer som använder en importerad CSV-fil väljer du **importerade datorer**. 
    
1. Klicka på **Redigera** för att granska utvärderings egenskaperna.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="Sida där du väljer bedömnings inställningar":::
 

1. I **Egenskaper för kontroll**  >  **mål**:

    - Ange den Azure-region som du vill migrera till på **mål platsen**.
       - Storleks-och kostnads rekommendationer baseras på den plats som du anger.
   - **Lagrings typen** är standard för **virtuellt San**. Detta är standard lagrings typen för ett privat AVS-moln.
   - **Reserverade instanser** stöds för närvarande inte för AVS-noder.
1. I **VM-storlek**:
    - **Nodtypen** är standard för **AV36**. Azure Migrate rekommenderar noden med noder som behövs för att migrera de virtuella datorerna till AVS.
    - I **FTT-inställningen, RAID-nivå**, väljer du det går inte att TOLERERA och RAID-kombinationen.  Det valda alternativet FTT, kombinerat med kravet på lokal virtuell dator disk, bestämmer det totala virtuellt San-lagrings utrymmet som krävs i AVS.
    - I **CPU-överprenumeration** anger du förhållandet mellan virtuella kärnor som är associerade med en fysisk kärna i AVS-noden. Överprenumeration på över 4:1 kan orsaka prestanda försämring, men kan användas för arbets belastningar för webb server typ.
    - I **minnes överinchecknings faktor** anger du förhållandet mellan minne och incheckning i klustret. Värdet 1 representerar 100% minnes användning, 0,5 till exempel 50% och 2 använder 200% av tillgängligt minne. Du kan bara lägga till värden från 0,5 till 10 till en decimal.
    - I **deduplicera och komprimerings faktor** anger du den förväntade deduplicerade och komprimerings faktorn för dina arbets belastningar. Det faktiska värdet kan hämtas från lokala virtuellt SAN eller Storage config och detta kan variera beroende på arbets belastning. Värdet 3 innebär tre gånger så att endast 100 GB lagrings utrymme används för 300 GB disk. Värdet 1 innebär ingen deduplicerad eller komprimering. Du kan bara lägga till värden från 1 till 10 upp till en decimal.
1. I **Node-storlek**: 
    - I **storleks kriterium** väljer du om du vill basera utvärderingen på statiska metadata eller på prestandabaserade data. Om du använder prestanda data:
        - I **prestanda historik** anger du den data varaktighet som du vill basera utvärderingen på.
        - I **percentils användning** anger du det percentilvärdet som du vill använda för prestanda exemplet. 
    - I **komfort faktor** anger du den buffert som du vill använda under utvärderingen. Dessa konton för problem som säsongs användning, kort prestanda historik och sannolika ökningar i framtida användning. Om du till exempel använder en bekvämlighets faktor på två:
    
        **Komponent** | **Effektiv användning** | **Lägg till bekvämlighets faktor (2,0)**
        --- | --- | ---
        Kärnor | 2  | 4
        Minne | 8 GB | 16 GB  

1. I **prissättning**:
    - I **erbjudandet** är [Azure-erbjudandet](https://azure.microsoft.com/support/legal/offer-details/) som du har registrerat i visat att Server utvärderingen beräknar kostnaden för det erbjudandet.
    - I **valuta** väljer du fakturerings valutan för ditt konto.
    - I **rabatt (%)**, Lägg till eventuella prenumerations rabatter som du får ovanpå Azure-erbjudandet. Standardinställningen är 0%.

1. Klicka på **Spara** om du gör ändringar.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="Utvärderingsegenskaper":::

1. I **utvärdera servrar** klickar du på **Nästa**.

1. I **Välj datorer för att utvärdera**  >  **bedömnings namnet** > anger du ett namn för utvärderingen. 
 
1. I **Välj eller skapa en grupp** > väljer du **Skapa ny** och anger ett grupp namn. 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="Lägga till virtuella datorer i en grupp":::
 
1. Välj enheten och välj de virtuella datorer som du vill lägga till i gruppen. Klicka på **Nästa**.

1. Granska utvärderings informationen i **Granska och skapa utvärdering** och klicka på **Skapa utvärdering** för att skapa gruppen och köra utvärderingen.

    > [!NOTE]
    > För prestandabaserade utvärderingar rekommenderar vi att du väntar minst en dag efter att du har startat identifieringen innan du skapar en utvärdering. Detta ger dig tid att samla in prestanda data med högre tillförlitlighet. Vi rekommenderar att du när du har startat identifieringen och väntar på varaktigheten för prestanda som du anger (dag/vecka/månad) för en bedömning med hög exakthet.

## <a name="review-an-assessment"></a>Granska en utvärdering

En AVS-utvärdering beskriver:

- AVS-beredskap: om de lokala virtuella datorerna är lämpliga för migrering till Azure VMware-lösningen (AVS).
- Antal AVS-noder: uppskattat antal AVS-noder som krävs för att köra de virtuella datorerna.
- Användning över AVS-noder: planerad processor, minne och lagrings belastning för alla noder.
    - Användningen inkluderar den främre faktorn i följande omkostnader för kluster hantering, till exempel vCenter Server, NSX Manager (stor), NSX Edge, om HCX har distribuerats även HCX Manager och IX-apparaten som använder ~ 44vCPU (11 CPU), 75 GB av RAM-och 722GB för lagring före komprimering och deduplicering. 
- Månads kostnads uppskattning: den uppskattade månads kostnaden för alla Azure VMware-lösningar (AVS)-noder som kör lokala virtuella datorer.

## <a name="view-an-assessment"></a>Visa en utvärdering

Så här visar du en utvärdering:

1. I **servrar**  >  **Azure Migrate: Server bedömning** klickar du på siffran bredvid **utvärderingar**.

1. I **Utvärderingar** väljer du en utvärdering för att öppna den. Som exempel (uppskattningar och kostnader endast för exempel): 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="Sammanfattning av AVS-utvärdering":::

1. Granska utvärderings sammanfattningen. Du kan också redigera bedömnings egenskaperna eller beräkna om utvärderingen.
 

### <a name="review-readiness"></a>Granska beredskap

1. Klicka på **Azure-beredskap**.
2. I **Azure-beredskap** granskar du VM-statusen.

    - **Redo för AVS**: datorn kan migreras i form av Azure-AVS, utan några ändringar. Datorn kommer att starta i AVS, med fullständigt stöd för AVS.
    - **Klar med villkor**: datorn kan ha kompatibilitetsproblem med den aktuella vSphere-versionen. Det kan krävas att VMware-verktyg är installerade, eller andra inställningar, innan de har fullständig funktionalitet i AVS.
    - **Inte redo för AVS**: den virtuella datorn startar inte i AVS. Om till exempel en lokal virtuell VMware-dator har en extern enhet (t. ex. en CD-ROM) som är ansluten till den och du använder VMware VMotion, Miss lyckas åtgärden VMotion.
 - **Beredskap okänd**: Azure Migrate kunde inte fastställa maskin beredskap, på grund av otillräckliga metadata som samlats in från den lokala miljön.

3. Granska det föreslagna verktyget.

    - VMware HCX eller Enterprise: för VMware-datorer är HCX-lösningen (VMware Hybrid Cloud Extensions) det rekommenderade Migreringsverktyg för att migrera din lokala arbets belastning till ditt Azure VMware-lösning (AVS) privat moln. Lära sig mer.
    - Okänt: Standardmigreringsverktyget är okänt för datorer som importerats via en CSV-fil. Även för VMware-datorer rekommenderar vi att du använder HCX-lösningen (VMware Hybrid Cloud Extension).
4. Klicka på en status för AVS-beredskap. Du kan visa information om VM-beredskap och öka detalj nivån för att se information om virtuella datorer, inklusive beräknings-, lagrings-och nätverks inställningar.

### <a name="review-cost-estimates"></a>Granska kostnadsuppskattningar

Utvärderings sammanfattningen visar den beräknade beräknings-och lagrings kostnaden för virtuella datorer som körs i Azure. 

1. Granska de totala månads kostnaderna. Kostnaderna sammanställs för alla virtuella datorer i den utvärderade gruppen.

    - Kostnads uppskattningar baseras på antalet AVS-noder som krävs med hänsyn till resurs kraven för alla virtuella datorer totalt.
    - Eftersom prissättningen för AVS är per nod, har den totala kostnaden ingen beräknings kostnad och distribution av lagrings kostnader.
    - Kostnads uppskattningen är att köra lokala virtuella datorer i AVS. I AVS-utvärderingen beaktas inte PaaS-eller SaaS-kostnader.

2. Granska uppskattningar för månatlig lagring. Vyn visar de aggregerade lagrings kostnaderna för den utvärderade gruppen och delas över olika typer av lagrings diskar. 
3. Du kan öka detalj nivån för att se kostnads information för vissa virtuella datorer.

### <a name="review-confidence-rating"></a>Granska säkerhetsomdöme

Server utvärderingen tilldelar en förtroende bedömning till prestandabaserade utvärderingar. Omdömet är från en stjärna (lägst) till fem stjärnor (högst).

![Säkerhetsomdöme](./media/tutorial-assess-vmware-azure-vmware-solution/confidence-rating.png)

Förtroende omdömet hjälper dig att beräkna tillförlitligheten för storleks rekommendationer i utvärderingen. Omdömet baseras på tillgängligheten för data punkter som behövs för att beräkna utvärderingen.

> [!NOTE]
> Förtroende klassificeringar tilldelas inte om du skapar en utvärdering som baseras på en CSV-fil.

Tillförlitlighets klassificeringen är som följer.

**Tillgänglighet för data punkt** | **Säkerhetsomdöme**
--- | ---
0 %–20 % | 1 stjärna
21 %–40 % | 2 stjärnor
41 %–60 % | 3 stjärnor
61 %–80 % | 4 stjärnor
81 %–100 % | 5 stjärnor

[Läs mer](concepts-assessment-calculation.md#confidence-ratings-performance-based) om Tillförlitlighets klassificeringar.

## <a name="next-steps"></a>Nästa steg

- Hitta dator beroenden med hjälp av [beroende mappning](concepts-dependency-visualization.md).
- Konfigurera en [agent utan agent](how-to-create-group-machine-dependencies-agentless.md) eller [agent](how-to-create-group-machine-dependencies.md) beroende mappning.
