---
title: Planera och hantera kostnader för Azure Blob Storage
description: Lär dig att planera för och hantera kostnader för Azure Blob Storage med hjälp av kostnads analys i Azure Portal.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: 0bad4637f13bbcf02047416499e4f82fdc53eb4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98601303"
---
# <a name="plan-and-manage-costs-for-azure-blob-storage"></a>Planera och hantera kostnader för Azure Blob Storage

Den här artikeln hjälper dig att planera och hantera kostnader för Azure Blob Storage. Beräkna först kostnaderna med hjälp av pris Kalkylatorn för Azure. När du har skapat ditt lagrings konto optimerar du kontot så att du bara betalar för det du behöver. Använd funktioner för kostnads hantering för att ställa in budgetar och övervaka kostnader. Du kan också granska prognostiserade kostnader och övervaka utgifts trender för att identifiera områden där du kanske vill handla.

Tänk på att kostnader för Blob Storage bara är en del av månads kostnaderna på din Azure-faktura. Även om den här artikeln förklarar hur du uppskattar och hanterar kostnader för Blob Storage debiteras du för alla Azure-tjänster och-resurser som används för din Azure-prenumeration, inklusive tjänster från tredje part. När du är bekant med att hantera kostnader för Blob Storage kan du använda liknande metoder för att hantera kostnader för alla Azure-tjänster som används i din prenumeration.

## <a name="estimate-costs"></a>Uppskatta kostnader

Använd [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/) för att beräkna kostnader innan du skapar och börjar överföra data till ett Azure Storage-konto.

1. På sidan [pris kalkylator för Azure](https://azure.microsoft.com/pricing/calculator/) väljer du panelen **lagrings konton** .

2. Rulla ned sidan och leta upp avsnittet **lagrings konton** i beräkningen.

3. Välj alternativ i list rutorna. 

   När du ändrar värdet för dessa List rutor ändras kostnads beräkningen. Denna uppskattning visas i det övre hörnet och längst ned i beräkningen.

   ![Skärm bild som visar din uppskattning](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   När du ändrar värdet i list rutan **typ** ändras även andra alternativ som visas i det här kalkyl bladet. Använd länkarna i avsnittet **Mer information** för att lära dig mer om vad varje alternativ innebär och hur dessa alternativ påverkar priset för lagrings-relaterade åtgärder. 

4. Ändra de återstående alternativen för att se hur de påverkar beräkningen.

   > [!NOTE]
   > Du kan betala för Azure Blob Storage-avgifter med din Azure-förskotts betalning (tidigare kallat betalnings åtagande) kredit. Du kan dock inte använda Azures förskotts kredit för att betala för avgifter för produkter och tjänster från tredje part, inklusive de som finns på Azure Marketplace.

## <a name="optimize-costs"></a>Optimera kostnader

Överväg att använda de här alternativen för att minska kostnaderna. 

- Reservera lagrings kapacitet

- Ordna data i åtkomst nivåer

- Flytta automatiskt data mellan åtkomst nivåer

I det här avsnittet beskrivs varje alternativ i detalj. 

#### <a name="reserve-storage-capacity"></a>Reservera lagrings kapacitet

Du kan spara pengar på lagrings kostnader för BLOB-data med Azure Storage reserverad kapacitet. Azure Storage reserverad kapacitet ger dig rabatt på kapacitet för block-blobbar och för Azure Data Lake Storage Gen2 data i standard lagrings konton när du genomför en reservation för ett år eller tre år. En reservation tillhandahåller en fast mängd lagrings kapacitet för reservationens period. Azure Storage reserverad kapacitet kan avsevärt minska kapacitets kostnaderna för block-blobbar och Azure Data Lake Storage Gen2 data. 

Läs mer i [optimera kostnader för Blob Storage med reserverad kapacitet](../blobs/storage-blob-reserved-capacity.md).

#### <a name="organize-data-into-access-tiers"></a>Ordna data i åtkomst nivåer

Du kan minska kostnaderna genom att placera BLOB-data i de mest kostnads effektiva åtkomst nivåerna. Välj mellan tre nivåer som är utformade för att optimera dina kostnader kring data användning. *Nivån frekvent har till exempel* en högre lagrings kostnad men lägre åtkomst kostnad. Om du planerar att komma åt data ofta kan den frekventa nivån därför vara det mest kostnads effektiva valet. Om du planerar att få åtkomst till data mindre ofta, kan *kall* eller *Arkiv* lag rings nivå vara det bästa sättet eftersom det höjer kostnaden för att komma åt data samtidigt som kostnaden för att lagra data minskar.    

Mer information finns i [Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv](../blobs/storage-blob-storage-tiers.md?tabs=azure-portal)lag rings nivåer.

#### <a name="automatically-move-data-between-access-tiers"></a>Flytta automatiskt data mellan åtkomst nivåer

Använd principer för livs cykel hantering för att regelbundet flytta data mellan nivåer för att spara flest pengar. Dessa principer kan flytta data till genom att använda regler som du anger. Du kan till exempel skapa en regel som flyttar blobbar till Arkiv nivån om denna BLOB inte har ändrats på 90 dagar. Genom att skapa principer som justerar åtkomst nivån för dina data kan du utforma de billigaste lagrings alternativen för dina behov.

Läs mer i [Hantera Azure Blob Storage-livscykeln](../blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal)

## <a name="create-budgets"></a>Skapa budgetar

Du kan skapa [budgetar](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) för att hantera kostnader och skapa aviseringar som automatiskt meddelar mottagarna om kostnadsavvikelser och risker för överförbrukning. Aviseringar baseras på utgifter jämfört med budget- och kostnadströsklar. Budgetar och aviseringar skapas för Azure-prenumerationer och resurs grupper, så de är användbara som en del av en övergripande kostnads övervaknings strategi. De kan dock ha begränsade funktioner för att hantera enskilda kostnader för Azure-tjänster, till exempel kostnaden för Azure Storage eftersom de är utformade för att spåra kostnader på en högre nivå.

## <a name="monitor-costs"></a>Övervaka kostnader

När du använder Azure-resurser med Azure Storage debiteras du kostnader. Kostnaderna för resursanvändningen varierar med tidsintervall (sekunder, minuter, timmar och dagar) eller per enhets användning (byte, megabyte osv.) Kostnaderna påförs så snart användningen av Azure Storage startar. Du kan se kostnaderna i fönstret [kostnads analys](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) i Azure Portal.

När du använder kostnads analys kan du Visa Azure Storage kostnader i grafer och tabeller i olika tidsintervall. Några exempel är per dag, innevarande och föregående månad och år. Du kan också Visa kostnader för budgetar och prognostiserade kostnader. Om du växlar till längre vyer över tid kan du identifiera utgifts trender och se var överförbrukningen kan ha inträffat. Om du har skapat budgetar kan du också enkelt se var de överskreds.

>[!NOTE]
> Kostnadsanalys stöder en mängd olika typer av Azure-konton. Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Om du vill visa kostnadsdata behöver du minst läsbehörighet för ditt Azure-konto. Mer information om hur du får åtkomst till Azure Cost Management finns i [Tilldela åtkomst till data](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Så här visar du Azure Storage kostnader i kostnads analys:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Öppna fönstret **Cost Management + fakturering** , Välj **kostnads hantering** på menyn och välj sedan **kostnads analys**. Du kan sedan ändra omfånget för en speciell prenumeration från List rutan **omfattning** .

   ![Skärm bild som visar omfång](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Om du bara vill visa kostnader för Azure Storage väljer du **Lägg till filter** och väljer sedan **tjänst namn**. Välj sedan **Storage (lagring** ) i listan. 

   Här är ett exempel som visar kostnader för just Azure Storage:

   ![Skärm bild som visar filter efter lagring](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

I föregående exempel visas den aktuella kostnaden för tjänsten. Kostnader per Azure-regioner (platser) och per resurs grupp visas också. Du kan även lägga till andra filter (till exempel: ett filter om du vill visa kostnader för vissa lagrings konton).

## <a name="export-cost-data"></a>Exportera kostnadsdata

Du kan också [Exportera dina kostnads data](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) till ett lagrings konto. Detta är användbart när du behöver eller andra för att utföra ytterligare data analyser för kostnader. Ett ekonomi team kan till exempel analysera data med hjälp av Excel eller Power BI. Du kan exportera dina kostnader enligt ett dags-, vecko-eller månads schema och ange ett anpassat datum intervall. Att exportera kostnads data är det rekommenderade sättet att hämta kostnads data uppsättningar.

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur prissättningen fungerar med Azure Storage. Se [Azure Storage översikts priser](https://azure.microsoft.com/pricing/details/storage/).
- [Optimera kostnader för Blob Storage med reserverad kapacitet](../blobs/storage-blob-reserved-capacity.md).
- Lär dig [hur du optimerar din moln investering med Azure Cost Management](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Lär dig mer om att hantera kostnader med [kostnads analys](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Lär dig mer om att [förhindra oväntade kostnader](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Ta den [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) guidade utbildningen.