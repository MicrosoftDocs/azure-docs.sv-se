---
title: Skapa en AVS-utvärdering med Azure Migrate Server utvärdering | Microsoft Docs
description: Beskriver hur du skapar en AVS-utvärdering med verktyget för Azure Migrate Server bedömning
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: fb1ec55bc68ccc323f8dee90982a9169e3085219
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567652"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a>Skapa en Azure VMware-lösning (AVS)-utvärdering

Den här artikeln beskriver hur du skapar en Azure VMware-lösning (AVS)-utvärdering för lokala virtuella VMware-datorer med Azure Migrate: Server utvärdering.

[Azure Migrate](migrate-services-overview.md) hjälper dig att migrera till Azure. Azure Migrate tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av lokal infrastruktur, program och data till Azure. Hubben ger Azure-verktyg för utvärdering och migrering, samt oberoende program varu leverantörer från tredje part (ISV).

## <a name="before-you-start"></a>Innan du börjar

- Se till att du har [skapat](./create-manage-projects.md) ett Azure Migrate-projekt.
- Om du redan har skapat ett projekt kontrollerar du att du har [lagt till](how-to-assess.md) Azure Migrate: Server utvärderings verktyget.
- Om du vill skapa en utvärdering måste du konfigurera en Azure Migrate-enhet för [VMware](how-to-set-up-appliance-vmware.md), som identifierar lokala datorer och skickar metadata-och prestanda data till Azure Migrate: Server utvärdering. [Läs mer](migrate-appliance.md).
- Du kan också [Importera serverns metadata](./tutorial-discover-import.md) i CSV-format (kommaavgränsade värden).


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Översikt över Azure VMware Solution (AVS)-utvärdering

Det finns två typer av utvärderingar som du kan skapa med hjälp av Azure Migrate: Server utvärdering.

**Utvärderingstyp** | **Information**
--- | --- 
**Azure VM** | Utvärderingar som migrerar dina lokala servrar till virtuella Azure-datorer. <br/><br/> Du kan utvärdera dina lokala [virtuella VMware-datorer](how-to-set-up-appliance-vmware.md), [virtuella Hyper-V-datorer](how-to-set-up-appliance-hyper-v.md)och [fysiska servrar](how-to-set-up-appliance-physical.md) för migrering till Azure med hjälp av den här utvärderings typen. [Läs mer](concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Utvärderingar för att migrera dina lokala servrar till [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Du kan utvärdera dina lokala [virtuella VMware-datorer](how-to-set-up-appliance-vmware.md) för migrering till Azure VMware Solution (AVS) med hjälp av den här utvärderingstypen.[Läs mer](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Azure VMware Solution (AVS)-utvärdering är för närvarande en för hands version och kan bara skapas för virtuella VMware-datorer.


Det finns två typer av storleks kriterier som du kan använda för att skapa Azure VMware-lösning (AVS)-utvärderingar:

**Utvärdering** | **Information** | **Data**
--- | --- | ---
**Prestandabaserad** | Utvärderingar baserade på insamlade prestanda data för lokala virtuella datorer. | **Rekommenderad Node-storlek**: baserat på CPU-och minnes användnings data tillsammans med nodtypen, lagrings typ och FTT-inställning som du väljer för utvärderingen.
**Som lokalt** | Utvärderingar baserade på lokal storlek. | **Rekommenderad Node-storlek**: baserat på den lokala virtuella dator storleken tillsammans med den nodtyp, lagrings typ och FTT-inställning som du väljer för utvärderingen.


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Köra en Azure VMware-lösning (AVS)-utvärdering

Kör en Azure VMware-lösning (AVS)-utvärdering enligt följande:

1. Granska [metodtipsen](best-practices-assessment.md) för att skapa utvärderingar.

2. Klicka på **utvärdera** i panelen **Azure Migrate: Server bedömning** på fliken **servrar** .

    ![Skärm bild som visar Azure Migrate servrar med utvärdering valt under utvärderings verktyg.](./media/how-to-create-assessment/assess.png)

3. I **utvärdera servrar** väljer du bedömnings typ som "Azure VMware-lösning (AVS)" och väljer identifierings källa.

    :::image type="content" source="./media/how-to-create-avs-assessment/assess-servers-avs.png" alt-text="Grundläggande om tillägg av utvärdering":::

4. Klicka på **Redigera** för att granska utvärderings egenskaperna.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="Plats för redigerings knappen för att granska utvärderings egenskaper":::

1. I **Välj datorer för att utvärdera**  >  **bedömnings namnet** > anger du ett namn för utvärderingen. 
 
1. I **Välj eller skapa en grupp** > väljer du **Skapa ny** och anger ett grupp namn. En grupp samlar en eller flera virtuella datorer för utvärdering.
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="Lägga till virtuella datorer i en grupp":::

1. I **Lägg till datorer i gruppen** väljer du de virtuella datorer som ska läggas till i gruppen.

1. Klicka på **Nästa** för att **Granska och skapa utvärdering** och granska utvärderingsinformationen.

1. Klicka på **Skapa utvärdering** för att skapa gruppen och kör utvärderingen.

1. När utvärderingen har skapats kan du se den i **Servrar** > **Azure Migrate: Serverutvärdering** > **Utvärderingar**.

1. Klicka på **Exportera utvärdering** för att ladda ned den som en Excel-fil.


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Granska en Azure VMware-lösning (AVS)-utvärdering

En Azure VMware Solution (AVS)-utvärdering beskriver:

- **Beredskap för Azure VMware-lösningen (AVS)**: om de lokala virtuella datorerna är lämpliga för migrering till Azure VMware-lösning (AVS).
- **Antal AVS-noder**: uppskattat antal AVS-noder som krävs för att köra de virtuella datorerna.
- **Användning över AVS-noder**: planerad processor, minne och lagrings belastning för alla noder.
    - Användningen omfattar upp till gångs faktor i följande omkostnader för kluster hantering, till exempel vCenter Server, NSX Manager (stor), NSX Edge, om HCX distribueras även HCX Manager och IX-apparaten som använder ~ 44vCPU (11 CPU), 75 GB av RAM-och 722GB för lagring före komprimering och deduplicering.
    - Minne, deduplicera och komprimera är för närvarande inställt på 100%-användning för minne och 1,5 deduplicerar och komprimerat, vilket är en användardefinierad indata i andra versioner som gör det möjligt för användaren att finjustera storleken på den storlek som krävs.
- **Månads kostnads uppskattning**: den uppskattade månads kostnaden för alla Azure VMware-lösningar (AVS)-noder som kör lokala virtuella datorer.


### <a name="view-an-assessment"></a>Visa en utvärdering

1. I **mål**  >   **Server** för migrering klickar du på **utvärderingar** i **Azure Migrate: Server bedömning**.

2. I **bedömningar** klickar du på en utvärdering för att öppna den.

    :::image type="content" source="./media/how-to-create-avs-assessment/avs-assessment-summary.png" alt-text="Sammanfattning av AVS-utvärdering":::

### <a name="review-azure-vmware-solution-avs-readiness"></a>Granska beredskap för Azure VMware-lösningen (AVS)

1. I **Azure-beredskap** kontrollerar du om de virtuella datorerna är klara för migrering till AVS.

2. Granska VM-statusen:
    - **Redo för AVS**: datorn kan migreras som i Azure (AVS) utan några ändringar. Den kommer att starta i AVS med fullständig AVS-support.
    - **Klar med villkor**: den virtuella datorn kan ha kompatibilitetsproblem med den aktuella vSphere-versionen samt kräva att VMware-verktyg och andra inställningar krävs innan fullständig funktionalitet från den virtuella datorn kan uppnås i AVS.
    - **Inte redo för AVS**: den virtuella datorn kommer inte att starta i AVS. Om till exempel den lokala virtuella VMware-datorn har en extern enhet ansluten, till exempel en CD-Rom, kommer VMotion-åtgärden att Miss Missing (om du använder VMware VMotion).
    - **Beredskap okänd**: Azure Migrate kunde inte fastställa datorns beredskap på grund av otillräckliga metadata som samlats in från den lokala miljön.

3. Granska det föreslagna verktyget:
    - **VMware HCX eller Enterprise**: för VMware-datorer är HCX-lösningen (VMware Hybrid Cloud Extensions) det rekommenderade Migreringsverktyg för att migrera din lokala arbets belastning till ditt Azure VMware-lösning (AVS) privat moln. [Läs mer](../azure-vmware/tutorial-deploy-vmware-hcx.md).
    - **Okänt**: Standardmigreringsverktyget är okänt för datorer som importerats via en CSV-fil. Även för VMware-datorer rekommenderar vi att du använder HCX-lösningen (VMware Hybrid Cloud Extension). 

4. Klicka på en status för **AVS-beredskap** . Du kan visa information om VM-beredskap och öka detalj nivån för att se information om virtuella datorer, inklusive beräknings-, lagrings-och nätverks inställningar.



### <a name="review-cost-details"></a>Granska kostnadsinformation

I den här vyn visas den uppskattade kostnaden för att köra virtuella datorer i Azure VMware-lösningen (AVS).

1. Granska de totala månads kostnaderna. Kostnaderna sammanställs för alla virtuella datorer i den utvärderade gruppen. 

    - Kostnads uppskattningar baseras på antalet AVS-noder som krävs med hänsyn till resurs kraven för alla virtuella datorer totalt.
    - Eftersom priserna för Azure VMware-lösningen (AVS) är per nod, har den totala kostnaden ingen beräknings kostnad och distribution av lagrings kostnader.
    - Kostnads uppskattningen är att köra lokala virtuella datorer i AVS. Azure Migrate Server-utvärderingen beaktar inte PaaS-eller SaaS-kostnader.
    
2. Du kan granska månads beräkningarna för lagrings kostnader. Den här vyn visar aggregerade lagrings kostnader för den utvärderade gruppen och delas upp över olika typer av lagrings diskar.

3. Du kan öka detalj nivån för att se information om vissa virtuella datorer.


### <a name="review-confidence-rating"></a>Granska säkerhetsomdöme

När du kör prestandabaserade utvärderingar tilldelas utvärderingen en förtroende nivå.

![Säkerhetsomdöme](./media/how-to-create-assessment/confidence-rating.png)

- En klassificering från 1 – stjärna (lägsta) till 5-stjärnor (högst) tilldelas.
- Förtroende omdömet hjälper dig att beräkna tillförlitligheten för de storleks rekommendationer som tillhandahålls av utvärderingen.
- Förtroende omdömet baseras på tillgängligheten för data punkter som behövs för att beräkna utvärderingen.
- För prestandabaserade storleks ändringar behöver AVS-utvärderingar i Server utvärderingen användnings data för CPU och VM-minne. Följande prestanda data samlas in men används inte vid storleks rekommendationer för AVS-utvärderingar:
  - Disk-IOPS och data flödes data för varje disk som är ansluten till den virtuella datorn.
  - Nätverks-I/O för att hantera prestandabaserade storleks ändringar för varje nätverkskort som är kopplat till en virtuell dator.

Tillförlitlighets klassificeringar för en utvärdering är följande.

**Tillgänglighet för data punkt** | **Säkerhetsomdöme**
--- | ---
0 %–20 % | 1 stjärna
21 %–40 % | 2 stjärnor
41 %–60 % | 3 stjärnor
61 %–80 % | 4 stjärnor
81 %–100 % | 5 stjärnor

[Läs mer](concepts-azure-vmware-solution-assessment-calculation.md) om prestanda data 


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder [beroende mappning](how-to-create-group-machine-dependencies.md) för att skapa grupper med hög exakthet.
- [Läs mer](concepts-azure-vmware-solution-assessment-calculation.md) om hur AVS-utvärderingar beräknas.