---
title: Skapa en AVS-utvärdering med Azure Migrate | Microsoft Docs
description: Beskriver hur du skapar en AVS-utvärdering med Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 72372e6365a2535e449681549a515c3f8594f2f1
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786608"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a>Skapa en Azure VMware-lösning (AVS)-utvärdering

Den här artikeln beskriver hur du skapar en Azure VMware-lösning (AVS)-utvärdering för lokala servrar i VMware-miljön med Azure Migrate: identifiering och bedömning.

[Azure Migrate](migrate-services-overview.md) hjälper dig att migrera till Azure. Azure Migrate tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av lokal infrastruktur, program och data till Azure. Hubben ger Azure-verktyg för utvärdering och migrering, samt oberoende program varu leverantörer från tredje part (ISV).

## <a name="before-you-start"></a>Innan du börjar

- Se till att du har [skapat](./create-manage-projects.md) ett Azure Migrate-projekt.
- Om du redan har skapat ett projekt ser du till att du har [lagt till](how-to-assess.md) verktyget Azure Migrate: identifiering och bedömning.
- Om du vill skapa en utvärdering måste du konfigurera en Azure Migrate-enhet för [VMware](how-to-set-up-appliance-vmware.md), som identifierar lokala servrar och skickar metadata-och prestanda data till Azure Migrate: identifiering och bedömning. [Läs mer](migrate-appliance.md).
- Du kan också [Importera serverns metadata](./tutorial-discover-import.md) i CSV-format (kommaavgränsade värden).


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Översikt över Azure VMware Solution (AVS)-utvärdering

Det finns tre typer av utvärderingar som du kan skapa med hjälp av Azure Migrate: identifiering och bedömning.

***Bedömnings typ** | **Information**
--- | --- 
**Azure VM** | Utvärderingar som migrerar dina lokala servrar till virtuella Azure-datorer. Du kan utvärdera dina lokala servrar i [VMware](how-to-set-up-appliance-vmware.md) [-och Hyper-V-](how-to-set-up-appliance-hyper-v.md) miljön och [fysiska servrar](how-to-set-up-appliance-physical.md) för migrering till virtuella Azure-datorer med den här utvärderings typen.
**Azure SQL** | Utvärderingar för att migrera dina lokala SQL-servrar från din VMware-miljö till Azure SQL Database eller Azure SQL-hanterad instans.
**Azure VMware Solution (AVS)** | Utvärderingar för att migrera dina lokala servrar till [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). Du kan utvärdera dina lokala servrar i VMware- [miljön](how-to-set-up-appliance-vmware.md) för migrering till Azure VMware-lösningen (AVS) med den här utvärderings typen. [Läs mer](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Azure VMware Solution (AVS)-utvärdering kan bara skapas för servrar i VMware-miljön.


Det finns två typer av storleks kriterier som du kan använda för att skapa Azure VMware-lösning (AVS)-utvärderingar:

**Utvärdering** | **Information** | **Data**
--- | --- | ---
**Prestandabaserad** | Utvärderingar baserade på insamlade prestanda data för lokala servrar. | **Rekommenderad Node-storlek**: baserat på CPU-och minnes användnings data tillsammans med nodtypen, lagrings typ och FTT-inställning som du väljer för utvärderingen.
**Som lokalt** | Utvärderingar baserade på lokal storlek. | **Rekommenderad Node-storlek**: baserat på den lokala server storleken tillsammans med den nodtyp, lagrings typ och FTT-inställning som du väljer för utvärderingen.


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Köra en Azure VMware-lösning (AVS)-utvärdering

1.  På sidan **översikt** > **Windows, Linux och SQL Server** klickar du på **utvärdera och migrera servrar**.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Översikts sida för Azure Migrate":::

1. I **Azure Migrate: identifiering och bedömning** klickar du på **utvärdera**.

   ![Utvärderings knappens placering](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. I   >  **utvärderings typ av utvärderings** servrar väljer du **Azure VMware-lösning (AVS)**.

1. I **identifierings källa**:

    - Om du har identifierat servrar som använder-enheten väljer du **servrar som identifierats från Azure Migrate**-installationen.
    - Om du har identifierat servrar som använder en importerad CSV-fil väljer du **importerade servrar**. 
    
1. Klicka på **Redigera** för att granska utvärderings egenskaperna.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="Sida där du väljer bedömnings inställningar":::
 

1. I **Egenskaper för kontroll**  >  **mål**:

    - Ange den Azure-region som du vill migrera till på **mål platsen**.
       - Storleks-och kostnads rekommendationer baseras på den plats som du anger.
   - **Lagrings typen** är standard för **virtuellt San**. Detta är standard lagrings typen för ett privat AVS-moln.
   - **Reserverade instanser** stöds för närvarande inte för AVS-noder.
1. I **VM-storlek**:
    - **Nodtypen** är standard för **AV36**. Azure Migrate rekommenderar noden med noder som behövs för att migrera servrarna till AVS.
    - I **FTT-inställningen, RAID-nivå**, väljer du det går inte att TOLERERA och RAID-kombinationen.  Det valda alternativet FTT, kombinerat med disk kravet för lokal server, fastställer det totala virtuellt San-lagrings utrymmet som krävs i AVS.
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
    - I **erbjudandet** visas [Azure-erbjudandet](https://azure.microsoft.com/support/legal/offer-details/) som du har registrerat i. Utvärderingen beräknar kostnaden för det erbjudandet.
    - I **valuta** väljer du fakturerings valutan för ditt konto.
    - I **rabatt (%)**, Lägg till eventuella prenumerations rabatter som du får ovanpå Azure-erbjudandet. Standardinställningen är 0%.

1. Klicka på **Spara** om du gör ändringar.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="Utvärderingsegenskaper":::

1. I **utvärdera servrar** klickar du på **Nästa**.

1. I **Välj servrar för att utvärdera**  >  **bedömnings namnet** > anger du ett namn för utvärderingen. 
 
1. I **Välj eller skapa en grupp** > väljer du **Skapa ny** och anger ett grupp namn. 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="Lägga till servrar i en grupp":::
 
1. Välj enheten och välj de servrar som du vill lägga till i gruppen. Klicka på **Nästa**.

1. Granska utvärderings informationen i **Granska och skapa utvärdering** och klicka på **Skapa utvärdering** för att skapa gruppen och köra utvärderingen.

    > [!NOTE]
    > För prestandabaserade utvärderingar rekommenderar vi att du väntar minst en dag efter att du har startat identifieringen innan du skapar en utvärdering. Detta ger dig tid att samla in prestanda data med högre tillförlitlighet. Vi rekommenderar att du när du har startat identifieringen och väntar på varaktigheten för prestanda som du anger (dag/vecka/månad) för en bedömning med hög exakthet.


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Granska en Azure VMware-lösning (AVS)-utvärdering

En Azure VMware Solution (AVS)-utvärdering beskriver:

- **Beredskap för Azure VMware-lösningen (AVS)**: om de lokala servrarna är lämpliga för migrering till Azure VMware-lösningen (AVS).
- **Antal AVS-noder**: uppskattat antal AVS-noder som krävs för att köra servrarna.
- **Användning över AVS-noder**: planerad processor, minne och lagrings belastning för alla noder.
    - Användningen omfattar upp till gångs faktor i följande omkostnader för kluster hantering, till exempel vCenter Server, NSX Manager (stor), NSX Edge, om HCX distribueras även HCX Manager och IX-apparaten som använder ~ 44vCPU (11 CPU), 75 GB av RAM-och 722GB för lagring före komprimering och deduplicering.
- **Månads kostnads uppskattning**: den uppskattade månads kostnaden för alla Azure VMware-lösningar (AVS)-noder som kör lokala virtuella datorer.


### <a name="view-an-assessment"></a>Visa en utvärdering

1. I **Windows, Linux och SQL Server**  >  **Azure Migrate: identifiering och utvärdering** klickar du på siffran bredvid * * Azure VMware-lösning * *.

1. I **Utvärderingar** väljer du en utvärdering för att öppna den. Som exempel (uppskattningar och kostnader endast för exempel): 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="Sammanfattning av AVS-utvärdering":::

1. Granska utvärderings sammanfattningen. Du kan också redigera bedömnings egenskaperna eller beräkna om utvärderingen.

### <a name="review-azure-vmware-solution-avs-readiness"></a>Granska beredskap för Azure VMware-lösningen (AVS)

1. I **Azure-beredskap** kontrollerar du om servrarna är klara för migrering till AVS.

2. Granska Server statusen:
    - **Redo för AVS**: servern kan migreras som-är till Azure (AVS) utan några ändringar. Den kommer att starta i AVS med fullständig AVS-support.
    - **Klar med villkor**: servern kan ha kompatibilitetsproblem med den aktuella vSphere-versionen samt kräva VMware-verktyg och andra inställningar innan fullständig funktionalitet från servern kan uppnås i AVS.
    - **Inte klar för AVS**: servern kommer inte att starta i AVS. Om den lokala servern t. ex. har en extern enhet ansluten, till exempel en CD-Rom, kommer VMotion-åtgärden att Miss Missing (om du använder VMware VMotion).
    - **Beredskap okänd**: Azure Migrate kunde inte fastställa serverns beredskap på grund av otillräckliga metadata som samlats in från den lokala miljön.

3. Granska det föreslagna verktyget:
    - **VMware HCX eller Enterprise**: för VMware-servrar är VMware Hybrid Cloud Extensions (HCX)-lösningen det rekommenderade migreringsjobbet för att migrera din lokala arbets belastning till ditt Azure VMware-lösning (AVS) privat moln. [Läs mer](../azure-vmware/tutorial-deploy-vmware-hcx.md).
    - **Okänd**: för servrar som importeras via en CSV-fil är standard verktyget för migrering okänt. Även för VMware-servrar rekommenderar vi att du använder HCX-lösningen (VMware Hybrid Cloud Extension). 

4. Klicka på en status för **AVS-beredskap** . Du kan visa information om VM-beredskap och öka detalj nivån för att se information om virtuella datorer, inklusive beräknings-, lagrings-och nätverks inställningar.

### <a name="review-cost-details"></a>Granska kostnadsinformation

I den här vyn visas den uppskattade kostnaden för att köra servrar i Azure VMware-lösningen (AVS).

1. Granska de totala månads kostnaderna. Kostnaderna aggregeras för alla servrar i den utvärderade gruppen. 

    - Kostnads uppskattningar baseras på antalet AVS-noder som krävs med hänsyn till resurs kraven för alla servrar som är totalt.
    - Eftersom priserna för Azure VMware-lösningen (AVS) är per nod, har den totala kostnaden ingen beräknings kostnad och distribution av lagrings kostnader.
    - Kostnads uppskattningen är att köra lokala servrar i AVS. I AVS-utvärderingen beaktas inte PaaS-eller SaaS-kostnader.
    
2. Du kan granska månads beräkningarna för lagrings kostnader. Den här vyn visar aggregerade lagrings kostnader för den utvärderade gruppen och delas upp över olika typer av lagrings diskar.

3. Du kan öka detalj nivån för att se information om vissa servrar.


### <a name="review-confidence-rating"></a>Granska säkerhetsomdöme

När du kör prestandabaserade utvärderingar tilldelas utvärderingen en förtroende nivå.

![Säkerhetsomdöme](./media/how-to-create-assessment/confidence-rating.png)

- En klassificering från 1 – stjärna (lägsta) till 5-stjärnor (högst) tilldelas.
- Förtroende omdömet hjälper dig att beräkna tillförlitligheten för de storleks rekommendationer som tillhandahålls av utvärderingen.
- Förtroende omdömet baseras på tillgängligheten för data punkter som behövs för att beräkna utvärderingen.
- För prestandabaserade storleks ändringar måste du använda användnings data för processor-och server minne i AVS-utvärderingen. Följande prestanda data samlas in men används inte vid storleks rekommendationer för AVS-utvärderingar:
  - Disk-IOPS och data flödes data för varje disk som är ansluten till-servern.
  - Nätverks-I/O för att hantera prestandabaserade storleks ändringar för varje nätverkskort som är kopplat till en server.

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