---
title: Planera och hantera kostnader för Azure Data Factory
description: I den här artikeln beskrivs hur du kan planera och hantera kostnader för Azure Data Factory
author: shirleywangmsft
ms.author: shwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: d89275acdb8e13a792ea3166d874fabf911bb686
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100372900"
---
# <a name="plan-and-manage-costs-for-azure-data-factory"></a>Planera och hantera kostnader för Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory är en server lös och elastisk data integrerings tjänst som skapats för moln skalning.  Det innebär att det inte finns en beräkning med fast storlek som du måste planera för topp belastningen. i stället anger du hur mycket resurs som ska allokeras på begäran per åtgärd, vilket gör att du kan utforma ETL-processerna på ett mycket mer skalbart sätt. Dessutom debiteras ADF i en förbruknings plan, vilket innebär att du bara betalar för det du använder.

I den här artikeln beskrivs hur du kan planera och hantera kostnader för Azure Data Factory.

*   Först, i början av ETL-projektet, genomför du POC-konceptet och använder en kombination av förbrukning och pris kalkylator per pipeline för att beräkna kostnaderna.
*   När du har distribuerat dina pipelines till produktion använder du funktionerna för kostnads hantering för att ställa in budgetar och övervaka kostnader. Du kan också granska de prognostiserade kostnaderna och identifiera utgifts trender.
*   Dessutom kan du Visa förbrukningen per pipeline och förbrukning per aktivitet för att förstå vilka pipelines och vilka aktiviteter som är Costliest och identifiera kandidater för kostnads reducering.

## <a name="estimate-costs-using-pipeline-and-activity-run-consumption-and-pricing-calculator"></a>Beräkna kostnader med hjälp av pipeline och aktivitets körning och pris kalkylator

Du kan använda [pris Kalkylatorn för automatisk](https://azure.microsoft.com/pricing/calculator/?service=data-factory) distribution för att få en uppskattning av kostnaden för att köra ETL-arbetsbelastningen i Azure Data Factory.  Om du vill använda kalkylatorn måste du ange information som antal aktivitets körningar, antal data integrerings timmar, typ av beräkning som används för data flöde, antal kärnor, instans antal, körnings tid och så vidare.

En av de vanliga frågorna om pris kalkylatorn är vilka värden som ska användas som indata.  Under POC-fasen kan du utföra utvärderings körningar med hjälp av exempel data uppsättningar för att förstå förbrukningen för olika ADF-mätare.  Baserat på förbrukningen för exempel data uppsättningen kan du projicera ut förbrukningen för det fullständiga data uppsättningen och driftsättning-schemat.

> [!NOTE]
> Priserna som används i de här exemplen nedan är hypotetiska och är inte avsedda att innebära den faktiska prissättningen.

Anta till exempel att du behöver flytta 1 TB data dagligen från AWS S3 till Azure Data Lake Gen2.  Du kan utföra POCen för att flytta 100 GB data för att mäta data inmatnings flödet och förstå motsvarande fakturerings förbrukning.

Här är ett exempel på kopierings aktivitetens körnings information (den faktiska mil kostnaden varierar beroende på formen på din specifika data uppsättning, nätverks hastigheter, utgående gränser på S3-konto, inträngande gränser på ADLS Gen2 och andra faktorer).

![S3-kopierings körning](media/plan-manage-costs/s3-copy-run-details.png)

Genom att dra nytta av [förbruknings övervakningen i pipeline-körnings nivå](#monitor-consumption-at-pipeline-run-level)kan du se motsvarande kvantiteter data förflyttnings mätnings förbrukning:

![Förbrukning av pipeline för S3-kopiering](media/plan-manage-costs/s3-copy-pipeline-consumption.png)

Det totala antalet DIU timmar det tar att flytta 1 TB per dag under hela månaden är därför:

1,2667 (DIU-timmar) * (1 TB/100 GB) * 30 (dagar i månaden) = 380 DIU-timmar

Nu kan du ansluta 30 aktivitets körningar och 380 DIU till ADF-pris Kalkylatorn för att få en uppskattning av din månads faktura:

![Pris kalkylator för S3-kopiering](media/plan-manage-costs/s3-copy-pricing-calculator.png)

## <a name="use-budgets-and-cost-alerts"></a>Använda budgetar och kostnadsaviseringar

Du kan skapa [budgetar](../cost-management-billing/costs/tutorial-acm-create-budgets.md) för att hantera kostnader och skapa aviseringar som automatiskt meddelar mottagarna om kostnadsavvikelser och risker för överförbrukning.  Aviseringar baseras på utgifter jämfört med budget- och kostnadströsklar.  När du skapar en budget kan du antingen göra det på prenumerations nivå eller med en lägre kornig het genom att lägga till ytterligare filter, till exempel resurs-ID och mätar namn.  Men du kan inte skapa budgetar för enskilda pipelines i en fabrik.

## <a name="monitor-costs-at-factory-level"></a>Övervaka kostnader på fabriks nivå

När du börjar använda Azure Data Factory kan du se kostnaderna som uppkommer i fönstret [kostnads analys](../cost-management-billing/costs/quick-acm-cost-analysis.md) i Azure Portal.

1. Om du vill visa [kostnads analys](../cost-management-billing/costs/quick-acm-cost-analysis.md)öppnar du fönstret **Cost Management + fakturering** , väljer **kostnads hantering** på menyn och väljer sedan **Öppna kostnads analys**.
2. I standardvyn visas ackumulerade kostnader för den aktuella månaden.  Du kan växla till ett annat tidsintervall och en annan kornig het, som varje dag eller månad.
3. Om du vill begränsa kostnaderna för en enskild tjänst, till exempel Azure Data Factory, väljer du **Lägg till filter** och väljer sedan **tjänst namn**.  Välj sedan **Azure Data Factory v2** från listan.
4. Du kan lägga till ytterligare filter för att analysera kostnader för en speciell fabriks instans och en bestämd precisions nivå för ADF-mätare.

   ![Kostnadsanalys](media/plan-manage-costs/cost-analysis.png)

## <a name="monitor-consumption-at-pipeline-run-level"></a>Övervaka förbrukning i pipeline – körnings nivå

Beroende på vilka typer av aktiviteter som du har i din pipeline, hur mycket data du flyttar och transformerar och hur komplex transformeringen är, kommer körningen av en pipeline att snurra olika fakturerings mätare i Azure Data Factory.

Du kan visa förbruknings mängden för olika mätare för enskilda pipelines i Azure Data Factory användar upplevelse. Öppna övervaknings upplevelsen genom att välja panelen **övervaka & hantera** på Data Factory-bladet i [Azure Portal](https://portal.azure.com/). Om du redan befinner dig i ADF-UX klickar du på ikonen **övervaka** på den vänstra panelen. Vyn standard övervakning är en lista över pipeliner som körs.

Om du klickar på knappen **förbrukning** bredvid pipeline-namnet visas ett popup-fönster som visar hur du använder pipeline-körningen som samlats in i alla aktiviteter i pipelinen.

![Körnings förbrukning för pipeline](media/plan-manage-costs/pipeline-run-consumption.png)

![Information om pipeline-förbrukning](media/plan-manage-costs/pipeline-consumption-details.png)

I vyn pipeline-körnings användning visas hur mycket som förbrukas för varje ADF-mätare för den specifika pipeline-körningen, men det visar inte det faktiska priset, eftersom det belopp som debiteras du beror på vilken typ av Azure-konto du har och vilken typ av valuta som används.  Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](../cost-management-billing/costs/understand-cost-mgt-data.md).

## <a name="monitor-consumption-at-activity-run-level"></a>Övervaka förbrukning vid aktivitet – körnings nivå
När du förstår den aggregerade förbrukningen på pipeline-körning finns det scenarier där du behöver öka detalj nivån och identifiera vilken som är den mest kostsamma aktiviteten i pipelinen.

Om du vill se förbrukningen vid aktivitet – körnings nivå går du till Data Factory- **redigeraren & övervaka** användar gränssnitt. På fliken **övervaka** där du ser en lista över pipeliner, klickar du på länken **pipeline-namn** för att få åtkomst till listan över aktivitets körningar i pipeline-körningen.  Klicka på knappen **utdata** bredvid aktivitets namnet och leta efter egenskapen **billableDuration** i JSON-utdata:

Här är ett exempel på en kopierings aktivitets körning:

![Kopiera utdata](media/plan-manage-costs/copy-output.png)

Här är ett exempel på en mappning av data flödes aktivitet som körs:

![Data flödes utdata](media/plan-manage-costs/dataflow-output.png)

## <a name="next-steps"></a>Nästa steg

I följande artiklar finns mer information om hur prissättning fungerar i Azure Data Factory:

- [Sidan Azure Data Factory prissättning](https://azure.microsoft.com/pricing/details/data-factory/ssis/)
- [Förstå Azure Data Factory via exempel](./pricing-concepts.md)
- [Azure Data Factory pris kalkylator](https://azure.microsoft.com/pricing/calculator/?service=data-factory)