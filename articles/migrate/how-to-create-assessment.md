---
title: Skapa en Azure VM-utvärdering med Azure Migrate identifierings-och utvärderings verktyg | Microsoft Docs
description: Beskriver hur du skapar en Azure VM-utvärdering med verktyget Azure Migrate identifiering och bedömning
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: f901fe23d2ff04e7ad9ba920dd90ebab8a39246c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786727"
---
# <a name="create-an-azure-vm-assessment"></a>Skapa en Azure VM-utvärdering

Den här artikeln beskriver hur du skapar en Azure VM-utvärdering för lokala servrar i VMware, Hyper-V eller fysisk/annan moln miljö med Azure Migrate: identifiering och bedömning.

[Azure Migrate](migrate-services-overview.md) hjälper dig att migrera till Azure. Azure Migrate tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av lokal infrastruktur, program och data till Azure. Hubben ger Azure-verktyg för utvärdering och migrering, samt oberoende program varu leverantörer från tredje part (ISV). 

## <a name="before-you-start"></a>Innan du börjar

- Se till att du har [skapat](./create-manage-projects.md) ett Azure Migrate-projekt.
- Om du redan har skapat ett projekt ser du till att du har [lagt till](how-to-assess.md) verktyget Azure Migrate: identifiering och bedömning.
- Om du vill skapa en utvärdering måste du konfigurera en Azure Migrate-enhet för [VMware](how-to-set-up-appliance-vmware.md) eller [Hyper-V](how-to-set-up-appliance-hyper-v.md). Enheten identifierar lokala servrar och skickar metadata-och prestanda data till Azure Migrate: identifiering och bedömning. [Läs mer](migrate-appliance.md).


## <a name="azure-vm-assessment-overview"></a>Översikt över Azure VM-utvärdering
Det finns två typer av storleks kriterier som du kan använda för att skapa en Azure VM-utvärdering med Azure Migrate: identifiering och bedömning.

**Utvärdering** | **Information** | **Data**
--- | --- | ---
**Prestandabaserad** | Utvärderingar baserade på insamlade prestanda data | **Rekommenderad VM-storlek**: baserat på processor-och minnes användnings data.<br/><br/> **Rekommenderad disktyp (standard-eller Premium-hanterad disk)**: baserat på IOPS och data flödet för lokala diskar.
**Som lokalt** | Utvärderingar baserade på lokal storlek. | **Rekommenderad VM-storlek**: baserat på den lokala virtuella dator storleken<br/><br> **Rekommenderad disktyp**: baserat på den inställning för lagrings typ som du väljer för utvärderingen.

[Läs mer](concepts-assessment-calculation.md) om utvärderingar.

## <a name="run-an-assessment"></a>Köra en utvärdering

Kör en utvärdering på följande sätt:

1. På sidan **översikt** > **Windows, Linux och SQL Server** klickar du på **utvärdera och migrera servrar**.

   ![Knappen utvärdera och migrera servrar](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. I **Azure Migrate: identifiering och bedömning**, klicka på **utvärdera** och välj **virtuell Azure-dator**

    ![Utvärderings knappens placering](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. I   >  **utvärderings typ av utvärderings** servrar
4. I **identifierings källa**:

    - Om du har identifierat servrar som använder-enheten väljer du **servrar som identifierats från Azure Migrate**-installationen.
    - Om du har identifierat servrar som använder en importerad CSV-fil väljer du **importerade servrar**. 
    
1. Klicka på **Redigera** för att granska utvärderings egenskaperna.

    ![Placering av knappen Visa alla för att granska bedömnings egenskaper](./media/tutorial-assess-vmware-azure-vm/assessment-name.png)

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
     - I **storleks kriterium** väljer du om du vill basera utvärderingen på Server konfigurations data/metadata eller på prestandabaserade data. Om du använder prestanda data:
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
    - I **erbjudandet** anger du [Azure-erbjudandet](https://azure.microsoft.com/support/legal/offer-details/) om du är registrerad. Utvärderingen beräknar kostnaden för det erbjudandet.
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

1. I **Välj servrar för att utvärdera**  >  **bedömnings namnet** > anger du ett namn för utvärderingen. 

1. I **Välj eller skapa en grupp** > väljer du **Skapa ny** och anger ett grupp namn. 
    
     ![Lägga till virtuella datorer i en grupp](./media/tutorial-assess-vmware-azure-vm/assess-group.png)


1. Välj enheten och välj de virtuella datorer som du vill lägga till i gruppen. Klicka på **Nästa**.


1. Granska utvärderings informationen i **Granska och skapa utvärdering** och klicka på **Skapa utvärdering** för att skapa gruppen och köra utvärderingen.

1. När utvärderingen har skapats kan du Visa den på **servrar**  >  **Azure Migrate:**  >  **utvärderingar** av identifiering och bedömning.

1. Klicka på **Exportera utvärdering** för att ladda ned den som en Excel-fil.
    > [!NOTE]
    > För prestandabaserade utvärderingar rekommenderar vi att du väntar minst en dag efter att du har startat identifieringen innan du skapar en utvärdering. Detta ger dig tid att samla in prestanda data med högre tillförlitlighet. Vi rekommenderar att du när du har startat identifieringen och väntar på varaktigheten för prestanda som du anger (dag/vecka/månad) för en bedömning med hög exakthet.

## <a name="review-an-azure-vm-assessment"></a>Granska en Azure VM-utvärdering

En Azure VM-utvärdering beskriver:

- **Azure-beredskap**: Om servrarna är lämpliga för migrering till Azure.
- **Månads kostnads uppskattning**: beräknad kostnad för beräkning och lagring för de virtuella datorerna i Azure.
- **Uppskattad månatlig lagringskostnad**: Uppskattade kostnader för disklagring efter migreringen.

### <a name="view-an-azure-vm-assessment"></a>Visa en utvärdering av Azure VM

1. I **Windows, Linux och SQL Server**  >  **Azure Migrate: identifiering och utvärdering** klickar du på siffran bredvid **utvärdering av Azure-VM**.
2. I **Utvärderingar** väljer du en utvärdering för att öppna den. Som exempel (uppskattningar och kostnader endast för exempel): 

    ![Sammanfattning av utvärdering](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Granska Azure-beredskap

1. I **Azure-beredskap** kontrollerar du om servrarna är klara för migrering till Azure.
2. Granska Server statusen:
    - **Redo för Azure**: Azure Migrate rekommenderar en VM-storlek och kostnadsuppskattning för virtuella datorer i utvärderingen.
    - **Klar med villkor**: visar problem och Rekommenderad reparation.
    - **Inte redo för Azure**: visar problem och förslag på åtgärder.
    - **Beredskap okänd**: används när Azure Migrate inte kan utvärdera beredskap på grund av problem med data tillgänglighet.

3. Klicka på en status för **Azure-beredskap** . Du kan visa information om Server beredskap och öka detalj nivån för att se Server information, inklusive beräknings-, lagrings-och nätverks inställningar.



### <a name="review-cost-details"></a>Granska kostnadsinformation

Vyn visar uppskattad beräknings- och lagringskostnad för att köra de virtuella datorerna i Azure.

1. Granska kostnader för beräkning och lagring per månad. Kostnaderna aggregeras för alla servrar i den utvärderade gruppen.

    - Kostnads uppskattningar baseras på storleks rekommendationerna för en server och dess diskar och egenskaper.
    - Uppskattade månatliga kostnader för beräkning och lagring visas.
    - Kostnads uppskattningen är att köra lokala servrar som virtuella IaaS-datorer. Azure VM-utvärderingen beaktar inte PaaS-eller SaaS-kostnader.

2. Du kan granska månads beräkningarna för lagrings kostnader. Den här vyn visar aggregerade lagrings kostnader för den utvärderade gruppen och delas upp över olika typer av lagrings diskar.
3. Du kan öka detalj nivån för att se information om vissa servrar.


### <a name="review-confidence-rating"></a>Granska säkerhetsomdöme

När du kör prestandabaserade utvärderingar tilldelas utvärderingen en förtroende nivå.

![Säkerhetsomdöme](./media/how-to-create-assessment/confidence-rating.png)

- En klassificering från 1 – stjärna (lägsta) till 5-stjärnor (högst) tilldelas.
- Förtroende omdömet hjälper dig att beräkna tillförlitligheten för de storleks rekommendationer som tillhandahålls av utvärderingen.
- Förtroende omdömet baseras på tillgängligheten för data punkter som behövs för att beräkna utvärderingen.

Tillförlitlighets klassificeringar för en utvärdering är följande.

**Tillgänglighet för data punkt** | **Säkerhetsomdöme**
--- | ---
0 %–20 % | 1 stjärna
21 %–40 % | 2 stjärnor
41 %–60 % | 3 stjärnor
61 %–80 % | 4 stjärnor
81 %–100 % | 5 stjärnor




## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder [beroende mappning](how-to-create-group-machine-dependencies.md) för att skapa grupper med hög exakthet.
- [Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.