---
title: Utvärdera virtuella Hyper-V-datorer för migrering till virtuella Azure-datorer med Server utvärdering i Azure Migrate
description: Lär dig hur du bedömer virtuella Hyper-V-datorer för migrering till virtuella Azure-datorer med Server utvärdering.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 0a3975578f3424276e3655e0ca8fba903194a2dc
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567067"
---
# <a name="tutorial-assess-hyper-v-vms-for-migration-to-azure"></a>Självstudie: utvärdera virtuella Hyper-V-datorer för migrering till Azure

Som en del av migreringen till Azure bedömer du dina lokala arbets belastningar för att mäta moln beredskap, identifiera risker och beräkna kostnader och komplexitet.

Den här artikeln visar hur du bedömer identifierade virtuella Hyper-V-datorer (VM: ar) för migrering till Azure med hjälp av verktyget Azure Migrate: Server bedömning.


I de här självstudierna får du lära dig att
> [!div class="checklist"]
- Kör en utvärdering.
- Analysera en utvärdering.

> [!NOTE]
> Självstudier visar den snabbaste sökvägen för att testa ett scenario och använda standard alternativ där det är möjligt. 

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prerequisites"></a>Förutsättningar

- Innan du följer den här självstudien för att utvärdera datorerna för migrering till virtuella Azure-datorer kontrollerar du att du har identifierat de datorer som du vill utvärdera:
    - [Följ den här självstudien](tutorial-discover-hyper-v.md)för att identifiera datorer som använder Azure Migrate-installationen. 
    - [Följ den här självstudien](tutorial-discover-import.md)för att identifiera datorer som använder en importerad CSV-fil.



## <a name="decide-which-assessment-to-run"></a>Bestäm vilken utvärdering som ska köras


Bestäm om du vill köra en utvärdering med storleks kriterier baserat på dator konfigurations data/metadata som samlas in lokalt eller på dynamiska prestanda data.

**Utvärdering** | **Information** | **Rekommendation**
--- | --- | ---
**Som lokalt** | Utvärdera baserat på dator konfigurations data/metadata.  | Rekommenderad storlek på virtuell Azure-dator baseras på den lokala virtuella dator storleken.<br/><br> Den rekommenderade typen av Azure-disk baseras på vad du väljer i inställningen lagrings typ i utvärderingen.
**Prestandabaserad** | Utvärdera baserat på insamlade dynamiska prestanda data. | Rekommenderad storlek på virtuell Azure-dator baseras på processor-och minnes användnings data.<br/><br/> Den rekommenderade disk typen baseras på IOPS och data flödet för de lokala diskarna.


## <a name="run-an-assessment"></a>Köra en utvärdering

Kör en utvärdering på följande sätt:

1. På sidan **servrar** > **Windows-och Linux-servrar** klickar du på **utvärdera och migrera servrar**.

   ![Knappen utvärdera och migrera servrar](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. Klicka på **utvärdera** i **Azure Migrate: Server bedömning**.

    ![Utvärderings knappens placering](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. I   >  **utvärderings typ av utvärderings** servrar väljer du **virtuell Azure-dator**.
4. I **identifierings källa**:

    - Om du har identifierat datorer som använder-enheten väljer du **datorer som identifierats från Azure Migrate**-installationen.
    - Om du har identifierat datorer som använder en importerad CSV-fil väljer du **importerade datorer**. 
    
1. Klicka på **Redigera** för att granska utvärderings egenskaperna.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vm/assessment-name.png" alt-text="Plats för redigerings knappen för att granska utvärderings egenskaper":::

1. I **Egenskaper för kontroll**  >  **mål**:
    - Ange den Azure-region som du vill migrera till på **mål platsen**.
        - Storleks-och kostnads rekommendationer baseras på den plats som du anger. När du har ändrat mål platsen från standard uppmanas du att ange **reserverade instanser** och VM- **serien**.
        - I Azure Government kan du rikta in dig på utvärderingar i [dessa regioner](migrate-support-matrix.md#supported-geographies-azure-government)
    - I **lagrings typ**,
        - Om du vill använda prestandabaserade data i utvärderingen väljer du **Automatisk** för Azure Migrate för att rekommendera en lagrings typ, baserat på disk-IOPS och data flöde.
        - Alternativt väljer du den lagrings typ som du vill använda för den virtuella datorn när du migrerar den.
    - I **reserverade instanser** anger du om du vill använda reserverade instanser för den virtuella datorn när du migrerar den.
        - Om du väljer att använda en reserverad instans kan du inte ange **rabatt (%)** eller **VM-drift tid**. 
        - [Läs mer](https://aka.ms/azurereservedinstances).
 1. I **VM-storlek**:
     - I **storleks kriterium** väljer du om du vill basera utvärderingen på dator konfigurations data/metadata eller på prestandabaserade data. Om du använder prestanda data:
        - I **prestanda historik** anger du den data varaktighet som du vill basera utvärderingen på.
        - I **percentils användning** anger du det percentilvärdet som du vill använda för prestanda exemplet. 
    - I **VM-serien** anger du den Azure VM-serien som du vill ta hänsyn till.
        - Om du använder Performance-baserad utvärdering föreslår Azure Migrate ett värde för dig.
        - Ändra inställningarna efter behov. Om du till exempel inte har en produktions miljö som behöver en-seriens virtuella datorer i Azure kan du undanta en-serien från listan över serier.
    - I **komfort faktor** anger du den buffert som du vill använda under utvärderingen. Dessa konton för problem som säsongs användning, kort prestanda historik och sannolika ökningar i framtida användning. Om du till exempel använder en bekvämlighets faktor på två:
        **Komponent** | **Effektiv användning** | **Lägg till bekvämlighets faktor (2,0)**
        --- | --- | ---
        Kärnor | 2  | 4
        Minne | 8 GB | 16 GB
   
1. I **prissättning**:
    - I **erbjudandet** anger du [Azure-erbjudandet](https://azure.microsoft.com/support/legal/offer-details/) om du är registrerad. Server utvärderingen beräknar kostnaden för det erbjudandet.
    - I **valuta** väljer du fakturerings valutan för ditt konto.
    - I **rabatt (%)**, Lägg till eventuella prenumerations rabatter som du får ovanpå Azure-erbjudandet. Standardinställningen är 0%.
    - I **VM-drift tid** anger du den varaktighet (dagar per månad/timme per dag) som de virtuella datorerna ska köras.
        - Detta är användbart för virtuella Azure-datorer som inte körs kontinuerligt.
        - Kostnads uppskattningar baseras på den angivna varaktigheten.
        - Standardvärdet är 31 dagar per månad/24 timmar per dag.
    - I **EA-prenumeration** anger du om du vill att prenumerations rabatten för Enterprise-avtal (EA) ska tas med i kontot för kostnads uppskattning. 
    - I **Azure Hybrid-förmån** anger du om du redan har en Windows Server-licens. Om du gör det, och de omfattas med aktiva Software Assurance för Windows Server-prenumerationer, kan du ansöka om [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-use-benefit/) när du använder licenser i Azure.

1. Klicka på **Spara** om du gör ändringar.

    ![Utvärderingsegenskaper](./media/tutorial-assess-vmware-azure-vm/assessment-properties.png)

1. I **utvärdera servrar** > klickar du på **Nästa**.

1. I **Välj datorer för att utvärdera**  >  **bedömnings namnet** > anger du ett namn för utvärderingen. 

1. I **Välj eller skapa en grupp** > väljer du **Skapa ny** och anger ett grupp namn. 
   
    :::image type="content" source="./media/tutorial-assess-hyper-v/assess-machines.png" alt-text="Skapa en ny grupp och Lägg till datorer":::

1. Välj enheten och välj de virtuella datorer som du vill lägga till i gruppen. Klicka på **Nästa**.


1. Granska utvärderings informationen i **Granska och skapa utvärdering** och klicka på **Skapa utvärdering** för att skapa gruppen och köra utvärderingen.

1. När utvärderingen har skapats kan du se den i **Servrar** > **Azure Migrate: Serverutvärdering** > **Utvärderingar**.

1. Klicka på **Exportera utvärdering** för att ladda ned den som en Excel-fil.
    > [!NOTE]
    > För prestandabaserade utvärderingar rekommenderar vi att du väntar minst en dag efter att du har startat identifieringen innan du skapar en utvärdering. Detta ger dig tid att samla in prestanda data med högre tillförlitlighet. Vi rekommenderar att du när du har startat identifieringen och väntar på varaktigheten för prestanda som du anger (dag/vecka/månad) för en bedömning med hög exakthet.

## <a name="review-an-assessment"></a>Granska en utvärdering

En utvärdering beskriver:

- **Azure-beredskap**: om virtuella datorer är lämpliga för migrering till Azure.
- **Månads kostnads uppskattning**: beräknad kostnad för beräkning och lagring för de virtuella datorerna i Azure.
- **Uppskattad månatlig lagringskostnad**: Uppskattade kostnader för disklagring efter migreringen.

Så här visar du en utvärdering:

1. I **servrar**  >  **Azure Migrate: Server bedömning** klickar du på siffran bredvid **utvärderingar**.
2. I **Utvärderingar** väljer du en utvärdering för att öppna den. Som exempel (uppskattningar och kostnader endast för exempel): 

    ![Sammanfattning av utvärdering](./media/tutorial-assess-vmware-azure-vm/assessment-summary.png)

3. Granska utvärderings sammanfattningen. Du kan också redigera bedömnings egenskaperna eller beräkna om utvärderingen.
 
 
### <a name="review-readiness"></a>Granska beredskap

1. Klicka på **Azure-beredskap**.
2. I **Azure-beredskap** granskar du VM-statusen:
    - **Redo för Azure**: används när Azure Migrate rekommenderar en VM-storlek och kostnads uppskattningar för virtuella datorer i utvärderingen.
    - **Klar med villkor**: visar problem och Rekommenderad reparation.
    - **Inte redo för Azure**: visar problem och förslag på åtgärder.
    - **Beredskap okänd**: används när Azure Migrate inte kan utvärdera beredskap på grund av data tillgänglighets problem.

3. Välj en status för **Azure-beredskap**. Du kan visa information om VM-beredskap. Du kan också öka detalj nivån för att se information om virtuella datorer, inklusive beräknings-, lagrings-och nätverks inställningar.

### <a name="review-cost-estimates"></a>Granska kostnadsuppskattningar

Utvärderings sammanfattningen visar den beräknade beräknings-och lagrings kostnaden för virtuella datorer som körs i Azure. 

1. Granska de totala månads kostnaderna. Kostnaderna sammanställs för alla virtuella datorer i den utvärderade gruppen.

    - Kostnads uppskattningar baseras på storleks rekommendationerna för en dator, dess diskar och dess egenskaper.
    - Uppskattade månatliga kostnader för beräkning och lagring visas.
    - Kostnads uppskattningen är att köra lokala virtuella datorer på virtuella Azure-datorer. Uppskattningen tar inte hänsyn till PaaS-eller SaaS-kostnader.

2. Granska lagrings kostnaderna per månad. Vyn visar de aggregerade lagrings kostnaderna för den utvärderade gruppen och delas över olika typer av lagrings diskar. 
3. Du kan öka detalj nivån för att se kostnads information för vissa virtuella datorer.

### <a name="review-confidence-rating"></a>Granska säkerhetsomdöme

Server utvärderingen tilldelar en förtroende bedömning till prestandabaserade utvärderingar. Omdömet är från en stjärna (lägst) till fem stjärnor (högst).

![Säkerhetsomdöme](./media/tutorial-assess-vmware-azure-vm/confidence-rating.png)

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
- Konfigurera en [agent-baserad](how-to-create-group-machine-dependencies.md) beroende mappning.
