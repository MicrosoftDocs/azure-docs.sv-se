---
title: Azure DevTest Labs användning över flera labb och prenumerationer
description: Lär dig hur du rapporterar Azure DevTest Labs användning över flera labb och prenumerationer.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1e4d1f0abb5596c7fd9d22740bf052827c2ca666
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452653"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>Rapportera Azure DevTest Labs användning över flera labb och prenumerationer

De flesta stora organisationer vill spåra resursanvändningen så att de blir mer effektiva med dessa resurser genom att visualisera trender och avviker i användningen. Baserat på resursanvändning kan labb ägare och chefer anpassa labben för att [förbättra resursanvändningen och kostnaderna](../cost-management-billing/cost-management-billing-overview.md). I Azure DevTest Labs kan du hämta resursanvändning per labb och på så sätt få djupare historik i användnings mönstren. Dessa användnings mönster kan hjälpa dig att hitta ändringar för att förbättra effektiviteten. De flesta företag vill ha både individuell labb användning och övergripande användning för [flera labb och prenumerationer](/azure/architecture/cloud-adoption/decision-guides/subscriptions/). 

Den här artikeln beskriver hur du hanterar resursanvändnings information över flera labb och prenumerationer.

![Rapportanvändning](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>Individuell labb användning

I det här avsnittet beskrivs hur du exporterar resursanvändning för ett enda labb.

Innan du kan exportera resursanvändningen för DevTest Labs måste du konfigurera ett Azure Storage-konto så att de olika filer som innehåller de användnings data som ska lagras. Det finns två vanliga sätt att köra exporten av data:

* [DevTest Labs REST API](/rest/api/dtl/labs/exportresourceusage) 
* PowerShell-AZ. Resource-modulen [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) med åtgärden `exportResourceUsage` , Lab-resurs-ID: t och de nödvändiga parametrarna. 

    Artikeln [Exportera eller ta bort personliga data](personal-data-delete-export.md) innehåller ett exempel på ett PowerShell-skript med detaljerad information om de data som exporteras. 

    > [!NOTE]
    > Datum parametern innehåller inte en tidsstämpel, så data innehåller allt från midnatt baserat på den tidszon där labbet finns.

När exporten är klar kommer det att finnas flera CSV-filer i blob-lagringen med den olika resursinformationen.
  
Det finns för närvarande två CSV-filer:

* *virtualmachines.csv* -innehåller information om de virtuella datorerna i labbet
* *disks.csv* -innehåller information om de olika diskarna i labbet 

De här filerna lagras i *labresourceusage* -BLOB-behållaren under labb namnet, Labbets unika ID, det datum som körs och antingen fullständiga eller start datum som var baserat på export förfrågan. Ett exempel på en BLOB-struktur är:

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>Exportera användning för alla labb

Om du vill exportera användnings informationen för flera labb bör du använda 

* [Azure Functions](../azure-functions/index.yml), som är tillgängligt på många språk, inklusive PowerShell, eller 
* [Azure Automation Runbook](../automation/index.yml), Använd PowerShell, python eller en anpassad grafisk designer för att skriva export koden.

Med hjälp av dessa tekniker kan du köra enskilda labb exporter i alla labb vid ett visst datum och en viss tidpunkt. 

Din Azure-funktion ska skicka data till lagrings utrymmet på längre sikt. Vid export av data för flera labb kan exporten ta lite tid. För att hjälpa till med prestanda och minska risken för dubblering av information, rekommenderar vi att du kör varje labb parallellt. Kör Azure Functions asynkront för att utföra parallellitet. Utnyttja också den timer-utlösare som Azure Functions erbjudandet.

## <a name="using-a-long-term-storage"></a>Använda långsiktig lagring

En långsiktig lagring samlar in export information från olika labb i en enda data källa. En annan fördel med att använda långsiktig lagring kan ta bort filerna från lagrings kontot för att minska duplicering och kostnad. 

Långsiktig lagring kan användas för att utföra text manipulation, till exempel: 

* lägga till egna namn
* skapa komplexa grupperingar
* aggregerar data.

Några vanliga lagrings lösningar är: [SQL Server](https://azure.microsoft.com/services/sql-database/), [Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)och [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Om du väljer vilken långsiktig lagrings lösning du väljer beror på preferens. Du kan överväga att välja verktyget beroende på vad det erbjuder när det gäller interaktions tillgänglighet vid visualisering av data.

## <a name="visualizing-data-and-gathering-insights"></a>Visualisera data och samla in insikter

Använd ett data visualiserings verktyg som du väljer för att ansluta till långsiktig lagring för att Visa användnings data och samla in insikter för att kontrol lera användnings effektiviteten. [Power BI](/power-bi/power-bi-overview) kan till exempel användas för att ordna och Visa användnings data. 

Du kan använda [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) för att skapa, länka och hantera dina resurser inom ett enda plats gränssnitt. Om du behöver mer kontroll kan du skapa en enskild resurs i en enda resurs grupp och hanterad oberoende av den Data Factory tjänsten.  

## <a name="next-steps"></a>Nästa steg

När systemet har kon figurer ATS och data flyttas till långsiktig lagring, är nästa steg att följa de frågor som data behöver för att besvara. Exempel: 

-   Vilken storlek används för virtuella datorer?

    Kan användarna välja höga prestanda (dyrare) VM-storlekar?
-   Vilka Marketplace-avbildningar används?

    Är anpassade avbildningar som är vanligast förekommande för virtuella datorer, om en gemensam avbildnings lagring skapas som [delad avbildnings Galleri](../virtual-machines/shared-image-galleries.md) eller [avbildnings fabrik](image-factory-create.md).
-   Vilka anpassade bilder används eller inte används?