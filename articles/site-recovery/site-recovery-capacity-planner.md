---
title: Planera kapacitet för haveri beredskap för Hyper-V med Azure Site Recovery
description: Använd den här artikeln för att uppskatta kapaciteten när du konfigurerar haveri beredskap med Azure Site Recoverys tjänsten.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: aa96845a2bd2f4659da1155c4f75281268dce76d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737723"
---
# <a name="plan-capacity-for-hyper-v-vm-disaster-recovery"></a>Planera kapacitet för haveriberedskap för virtuella Hyper-V-datorer 

[Distributionshanteraren för Azure Site Recovery] (site-recovery-hyper-v-deployment-planner.md) för Hyper-V till Azure-distribution ger följande:

* Utvärdering av VM-berättigande, baserat på antalet diskar, disk storlek, IOPS, omsättning och några egenskaper för virtuella datorer
* Nätverkets bandbreddsbehov kontra utvärdering av återställningspunktmål
* Krav på infrastruktur för Azure
* Krav på lokal infrastruktur
* Inledande vägledning till batchreplikering
* Uppskattad total kostnad för haveri beredskap till Azure


Azure Site Recovery Capacity Planner hjälper dig att fastställa dina kapacitets krav när du replikerar virtuella Hyper-V-datorer med Azure Site Recovery.

Använd Site Recovery Capacity Planner för att analysera din käll miljö och dina arbets belastningar. Det hjälper dig att beräkna bandbredds behov, de server resurser du behöver för käll platsen och de resurser (t. ex. virtuella datorer och lagrings enheter) som du behöver på mål platsen.

Du kan köra verktyget i två lägen:

* **Snabb planering**: tillhandahåller nätverks-och Server projektioner baserat på ett genomsnittligt antal virtuella datorer, diskar, lagring och ändrings takt.
* **Detaljerad planering**: innehåller information om varje arbets belastning på VM-nivå. Analysera VM-kompatibilitet och hämta nätverks-och Server projektioner.

## <a name="before-you-start"></a>Innan du börjar

* Samla in information om din miljö, inklusive virtuella datorer, diskar per virtuell dator, lagring per disk.
* Identifiera din dagliga ändrings takt (omsättning) för replikerade data. Hämta [verktyget för kapacitets planering för Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) för att hämta ändrings takten. [Läs mer](./hyper-v-deployment-planner-overview.md) om det här verktyget. Vi rekommenderar att du kör verktyget över en vecka för att samla in genomsnitt.


## <a name="run-the-quick-planner"></a>Kör snabb planeraren
1. Ladda ned och öppna [Site Recovery Capacity Planner](/samples/browse/?redirectedfrom=TechNet-Gallery). Du måste köra makron. När du uppmanas att göra det väljer du alternativ för att aktivera redigering och innehåll.

2. I list rutan **Välj en planerings typ** väljer du **snabb planering**.

   ![Skärm bild av alternativet Välj en planerings typ, med snabb planerare valt.](./media/site-recovery-capacity-planner/getting-started.png)

3. Ange den information som krävs i kalkyl bladet **Capacity Planner** . Fyll i alla fält som är inringade i rött i följande skärm bild:

   a. I **Välj ditt scenario** väljer du **Hyper-V till Azure** eller **VMware/fysisk till Azure**.

   b. I **genomsnittlig daglig data ändrings hastighet (%)** anger du den information som du samlar in med hjälp av [verktyget för kapacitets planering för Hyper-V](./hyper-v-deployment-planner-overview.md) eller [Site Recovery distributions](./site-recovery-deployment-planner.md)hanteraren.

   c. **Komprimerings** inställningen används inte när du replikerar virtuella Hyper-V-datorer till Azure. För komprimering använder du en tredjepartsprogram, till exempel Riverbed.

   d. Under **kvarhållning i dagar** anger du i dagar hur länge repliker ska sparas.

   e. I **antal timmar där inledande replikering för batchen av virtuella datorer ska slutföras** och **antalet virtuella datorer per inledande replikering**, anger du inställningar som används för att beräkna inledande replikerings krav. När Site Recovery distribueras överförs hela den ursprungliga data uppsättningen.

   ![Skärm bild av Capacity Planner kalkyl bladet som visar nödvändig information om indata.](./media/site-recovery-capacity-planner/inputs.png)

4. När du har angett värden för käll miljön innehåller utdata som visas:

   * **Bandbredd som krävs för delta-replikering (i megabit per sekund)**: nätverks bandbredden för delta-replikering beräknas enligt den genomsnittliga dagliga data ändrings hastigheten.
   * **Bandbredd som krävs för inledande replikering (i megabit per sekund)**: nätverks bandbredden för inledande replikering beräknas för de inledande replikeringsinställningar som du anger.
   * **Lagring krävs (i GB)**: den totala Azure-lagring som krävs.
   * **Totalt antal IOPS på standard lagring**: antalet beräknas baserat på 8K IOPS-enhetens storlek på de totala standard lagrings kontona. För snabb planeraren beräknas antalet baserat på alla virtuella käll diskar och den dagliga data ändrings hastigheten. För den detaljerade planeraren beräknas antalet baserat på det totala antalet virtuella datorer som har mappats till virtuella Azure-datorer och data ändrings takten för dessa virtuella datorer.
   * **Antal standard lagrings konton som krävs**: det totala antalet standard lagrings konton som behövs för att skydda de virtuella datorerna. Ett standard lagrings konto kan innehålla upp till 20 000 IOPS över alla virtuella datorer i standard lagring. Högst 500 IOPS stöds per disk.
   * **Antal BLOB-diskar som krävs**: antalet diskar som skapas i Azure Storage.
   * **Antal nödvändiga Premium konton**: det totala antalet Premium Storage-konton som behövs för att skydda de virtuella datorerna. En virtuell käll dator med hög IOPS (större än 20 000) behöver ett Premium Storage-konto. Ett Premium Storage-konto kan innehålla upp till 80 000 IOPS.
   * **Totalt antal IOPS på Premium Storage**: antalet beräknas baserat på den extra 256 IOPS-enhetens storlek på de totala Premium Storage-kontona. För snabb planeraren beräknas antalet baserat på alla virtuella käll diskar och den dagliga data ändrings hastigheten. För den detaljerade planeraren beräknas antalet baserat på det totala antalet virtuella datorer som är mappade till Premium virtuella Azure-datorer (DS och GS-serien) och data ändrings takten för dessa virtuella datorer.
   * **Antal konfigurations servrar som krävs**: visar hur många konfigurations servrar som krävs för distributionen.
   * **Antal ytterligare processerver som krävs**: visar om det krävs ytterligare process servrar, utöver den processerver som körs på konfigurations servern som standard.
   * **100% ytterligare lagrings utrymme på källan**: visar om ytterligare lagrings utrymme krävs på käll platsen.

      ![Skärm bild av de utdata som visas baserat på angivna indata.](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Kör den detaljerade planeraren

1. Ladda ned och öppna [Site Recovery Capacity Planner](/samples/browse/?redirectedfrom=TechNet-Gallery). Du måste köra makron. När du uppmanas att göra det väljer du alternativ för att aktivera redigering och innehåll.

2. I **Välj en planerings typ** väljer du **detaljerad Planner** i list rutan.

   ![Skärm bild av alternativet Välj en planerings typ, där detaljerad Planner har valts.](./media/site-recovery-capacity-planner/getting-started-2.png)

3. Ange den information som krävs i kalkyl bladet för **arbets belastnings kvalificering** . Du måste fylla i alla markerade fält.

   a. I **processor kärnor** anger du det totala antalet kärnor på en käll Server.

   b. I **minnes tilldelning (i MB)** anger du ram-storleken för en käll Server.

   c. I **antal nätverkskort** anger du antalet nätverkskort på en käll Server.

   d. I **totalt lagrings utrymme (i GB)** anger du den totala storleken för VM-lagringen. Om käll servern till exempel har tre diskar med 500 GB vardera, är den totala lagrings storleken 1 500 GB.

   e. I **Antal diskar anslutna** anger du det totala antalet diskar på en käll Server.

   f. I **disk kapacitets användning (%)** anger du den genomsnittliga användningen.

   ex. I **daglig data ändrings hastighet (%)** anger du den dagliga data ändrings hastigheten för en käll Server.

   h. I **avbilda storlek på virtuell Azure-dator** anger du storleken på den virtuella Azure-datorn som du vill mappa. Om du inte vill göra detta manuellt väljer du **Compute IaaS VM**. Om du anger en manuell inställning och sedan väljer **Compute IaaS VM**: ar, kan den manuella inställningen skrivas över. Beräknings processen identifierar automatiskt den bästa matchningen på storleken på den virtuella Azure-datorn.

   ![Skärm bild av kalkyl bladet arbets belastnings kvalificering som visar nödvändig information.](./media/site-recovery-capacity-planner/workload-qualification.png)

4. Gör så här om du väljer **Compute IaaS VM**: ar:

   * Validerar de obligatoriska indata.
   * Beräknar IOPS och föreslår den bästa storleken för virtuella Azure-datorer för varje virtuell dator som är tillgänglig för replikering till Azure. Om en lämplig storlek på en virtuell Azure-dator inte kan identifieras visas ett fel meddelande. Om till exempel antalet anslutna diskar är 65 visas ett fel eftersom den högsta storleken för en virtuell Azure-dator är 64.
   * Föreslår ett lagrings konto som kan användas för en virtuell Azure-dator.
   * Beräknar det totala antalet standard lagrings konton och Premium Storage-konton som krävs för arbets belastningen. Rulla nedåt för att Visa typen av Azure-lagring och det lagrings konto som kan användas för en käll Server.
   * Slutför och sorterar resten av tabellen baserat på den nödvändiga lagrings typen (standard eller Premium) som är tilldelad en virtuell dator och antalet diskar som är anslutna. För alla virtuella datorer som uppfyller kraven för Azure **är kolumnen VM-kvalificerad?** visar **Ja**. Om en virtuell dator inte kan säkerhets kopie ras till Azure visas ett fel meddelande.

Kolumner AA to AE är utdata och ger information för varje virtuell dator.

![Skärm bild som visar utdatakolumner AA till AE.](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Exempel
Som exempel, för sex virtuella datorer med värdena som visas i tabellen, beräknar verktyget och tilldelar den bästa matchningen av virtuella Azure-datorer och Azure Storage-kraven.

![Skärm bild som visar tilldelningar av arbets belastning.](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* Tänk på följande i exemplet på utdata:

  * Den första kolumnen är en validerings kolumn för de virtuella datorerna, diskarna och omsättningen.
  * Det krävs två standard lagrings konton och ett Premium Storage-konto för fem virtuella datorer.
  * VM3 är inte kvalificerat för skydd eftersom en eller flera diskar är större än 1 TB.
  * VM1 och VM2 kan använda det första standard lagrings kontot
  * VM4 kan använda det andra standard lagrings kontot.
  * VM5 och VM6 behöver ett Premium Storage-konto och båda kan använda ett enda konto.

    > [!NOTE]
    > IOPS på standard-och Premium-lagring beräknas på VM-nivå och inte på disk nivå. En virtuell standard dator kan hantera upp till 500 IOPS per disk. Om IOPS för en disk är större än 500 behöver du Premium Storage. Om IOPS för en disk är mer än 500 men IOPS för de totala virtuella dator diskarna finns inom support standarderna för virtuella Azure-datorer, väljer Planner en standard-VM och inte DS-eller GS-serien. (De virtuella Azure-gränserna är VM-storlek, antal diskar, antal kort, CPU och minne.) Du måste uppdatera mappningen av Azure size-cellen manuellt med lämplig VM för DS eller GS-serien.


När all information har angetts väljer du **skicka data till Planner-verktyget** för att öppna Capacity Planner. Arbets belastningar markeras för att visa om de är berättigade till skydd.

### <a name="submit-data-in-capacity-planner"></a>Skicka data i Capacity Planner
1. När du öppnar **Capacity Planner** kalkyl bladet fylls det i baserat på de inställningar du har angett. Ordet "arbets belastning" visas i käll cellen för **infraröda indata** för att visa att indata är kalkyl bladet för **arbets belastnings kvalificering** .

2. Om du vill göra ändringar måste du ändra kalkyl bladet **arbets belastnings kvalificering** . Välj sedan **skicka data till Planner-verktyget** igen.

   ![Skärm bild som visar ändrade indata och resulterande utdata i kalkyl bladet Capacity Planner.](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Nästa steg
[Lär dig hur du kör](./hyper-v-deployment-planner-overview.md) kapacitets planerings verktyget.