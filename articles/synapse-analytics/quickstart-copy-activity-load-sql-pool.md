---
title: 'Snabb start: så här läser du in data i en dedikerad SQL-pool med kopierings aktiviteten'
description: Använd pipeline kopierings aktiviteten i Azure Synapse Analytics för att läsa in data i en dedikerad SQL-pool.
services: synapse-analytics
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: synapse-analytics
ms.topic: quickstart
ms.custom: seo-lt-2019
ms.date: 11/02/2020
ms.openlocfilehash: 568c738284a3d1b54ba907a973139fbced11f139
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222704"
---
# <a name="quickstart-load-data-into-dedicated-sql-pool-using-the-copy-activity"></a>Snabb start: Läs in data i dedikerad SQL-pool med kopierings aktiviteten

Azure Synapse Analytics erbjuder olika analys verktyg som hjälper dig att mata in, transformera, modellera och analysera dina data. En dedikerad SQL-pool ger T-SQL-baserade beräknings-och lagrings funktioner. När du har skapat en dedikerad SQL-pool på din Synapse-arbetsyta kan data läsas in, modelleras, bearbetas och levereras för snabbare analytiska insikter.

I den här snabb starten får du lära dig hur du *läser in data från Azure SQL Database i Azure Synapse Analytics*. Du kan följa liknande steg för att kopiera data från andra typer av data lager. Detta liknande flöde gäller även för data kopiering för andra källor och mottagare.

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration: om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Azure Synapse-arbetsyta: skapa en Synapse-arbetsyta med hjälp av Azure Portal följa anvisningarna i [snabb start: skapa en Synapse-arbetsyta](quickstart-create-workspace.md).
* Azure SQL Database: den här självstudien kopierar data från Adventure Works LT exempel data uppsättning i Azure SQL Database. Du kan skapa den här exempel databasen i SQL Database genom att följa anvisningarna i [skapa en exempel databas i Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md). Eller så kan du använda andra data lager genom att följa liknande steg.
* Azure Storage-konto: Azure Storage används som *mellanlagringsområdet* i kopierings åtgärden. Om du inte har något Azure-lagringskonto finns det anvisningar i [Skapa ett lagringskonto](../storage/common/storage-account-create.md).
* Azure Synapse Analytics: du använder en dedikerad SQL-pool som data lager för mottagare. Om du inte har en Azure Synapse Analytics-instans kan du läsa [skapa en dedikerad SQL-pool](quickstart-create-sql-pool-portal.md) för steg för att skapa en.

### <a name="navigate-to-the-synapse-studio"></a>Navigera till Synapse Studio

När din Synapse-arbetsyta har skapats kan du öppna Synapse Studio på två sätt:

* Öppna din Synapse-arbetsyta i [Azure Portal](https://ms.portal.azure.com/#home). Välj **Öppna** på kortet Open Synapse Studio under komma igång.
* Öppna [Azure Synapse Analytics](https://web.azuresynapse.net/) och logga in på din arbets yta.

I den här snabb starten använder vi arbets ytan med namnet "adftest2020" som exempel. Den kommer automatiskt att gå till start sidan för Synapse Studio.

![Start sida för Synapse Studio](media/doc-common-process/synapse-studio-home.png)

## <a name="create-linked-services"></a>Skapa länkade tjänster

I Azure Synapse Analytics är en länkad tjänst där du definierar din anslutnings information till andra tjänster. I det här avsnittet skapar du följande två typer av länkade tjänster: Azure SQL Database och Azure Data Lake Storage Gen2 (ADLS Gen2) länkade tjänster.

1. På Start sidan för Synapse Studio väljer du fliken **Hantera** i det vänstra navigerings fältet.
1. Under externa anslutningar väljer du länkade tjänster.
  
   ![Skapa ny länkad tjänst](media/doc-common-process/new-linked-service.png)

1. Om du vill lägga till en länkad tjänst väljer du **ny**.
1. Välj **Azure SQL Database** från galleriet och välj sedan **Fortsätt**. Du kan skriva "SQL" i sökrutan för att filtrera kopplingarna.

   ![Skapa ny Azure SQL Database länkad tjänst](media/quickstart-copy-activity-load-sql-pool/new-azure-sql-linked-service.png)

1. På sidan ny länkad tjänst väljer du Server namnet och databas namnet i list rutan och anger användar namn och lösen ord. Verifiera inställningarna genom att klicka på **Testa anslutning** och välj sedan **skapa**.

   ![Konfigurera Azure SQL Database länkade tjänsten](media/quickstart-copy-activity-load-sql-pool/azure-sql-linked-service-configuration.png)

1. Upprepa steg 3-4, men Välj **Azure Data Lake Storage Gen2** i stället från galleriet. På sidan ny länkad tjänst väljer du ditt lagrings konto namn i list rutan. Verifiera inställningarna genom att klicka på **Testa anslutning** och välj sedan **skapa**. 

   ![Konfigurera Azure Data Lake Storage Gen2](media/quickstart-copy-activity-load-sql-pool/adls-gen2-linked-service-configuration.png)
 
## <a name="create-a-pipeline"></a>Skapa en pipeline

En pipeline innehåller det logiska flödet för en körning av en uppsättning aktiviteter. I det här avsnittet ska du skapa en pipeline som innehåller en kopierings aktivitet som matar in data från Azure SQL Database i en dedikerad SQL-pool.

1. Gå till fliken **integrera** . Välj på plus ikonen bredvid pipelinens huvud och välj pipeline.

   ![Skapa en ny pipeline](media/doc-common-process/new-pipeline.png)

1. Under *flytta och transformera* i fönstret *aktiviteter* drar du **Kopiera data** till pipeline-arbetsytan.
1. Välj aktiviteten Kopiera och gå till fliken Källa. Välj **ny** för att skapa en ny käll data uppsättning.

   ![Skapa en källdatauppsättning](media/quickstart-copy-activity-load-sql-pool/new-source-dataset.png)

1. Välj **Azure SQL Database** som data lager och välj **Fortsätt**.
1. I rutan *Ange egenskaper* väljer du den Azure SQL Database länkade tjänst som du skapade i föregående steg. 
1. Under tabell namn väljer du en exempel tabell som ska användas i följande kopierings aktivitet. I den här snabb starten använder vi "SalesLT. Customer"-tabellen som ett exempel. 

   ![Konfigurera käll data uppsättnings egenskaper](media/quickstart-copy-activity-load-sql-pool/source-dataset-properties.png)
1. Välj **OK** när du är färdig.
1. Välj kopierings aktiviteten och gå till fliken mottagare. Välj **ny** för att skapa en ny data uppsättning för mottagare.
1. Välj **Azure Synapse Dedicated SQL-pool** som data lager och välj **Fortsätt**.
1. I rutan  **Ange egenskaper** väljer du den SQL Analytics-pool som du skapade i föregående steg. Om du skriver till en befintlig tabell väljer du den i list rutan under *tabell namn* . Annars kan du kontrol lera "redigera" och ange det nya tabell namnet. Välj **OK** när du är färdig.
1. För inställningar för data uppsättning för mottagare aktiverar du **Skapa tabell automatiskt** i fältet tabell alternativ.

   ![Aktivera automatisk skapande](media/quickstart-copy-activity-load-sql-pool/auto-create-table.png)

1. Markera kryss rutan för **Aktivera mellanlagring** på sidan **Inställningar** . Det här alternativet gäller om dina källdata inte är kompatibla med PolyBase. I avsnittet **Inställningar** för mellanlagring väljer du den Azure Data Lake Storage Gen2 länkade tjänst som du skapade i föregående steg som mellanlagringen. 

    Lagrings utrymmet används för att mellanlagra data innan de läses in i Azure Synapse Analytics genom att använda PolyBase. När kopieringen är klar rensas interims data i Azure Data Lake Storage Gen2 automatiskt.

   ![Aktivera mellanlagring](media/quickstart-copy-activity-load-sql-pool/staging-linked-service.png)

1. Verifiera pipelinen genom att välja **Verifiera** i verktygsfältet. Du ser resultatet av pipeline-verifieringen på höger sida av sidan. 

## <a name="debug-and-publish-the-pipeline"></a>Felsöka och publicera en pipeline

När du har konfigurerat din pipeline kan du köra en fel söknings körning innan du publicerar dina artefakter för att kontrol lera att allt är korrekt.

1. Välj **Felsöka** i verktygsfält för att felsöka pipelinen. Du ser status för pipelinekörningen på fliken **Utdata** längst ned i fönstret. 

   ![Felsöka pipeline](media/quickstart-copy-activity-load-sql-pool/debugging-result.png)

1. När pipeline-körningen lyckas väljer du **publicera alla** i det övre verktygsfältet. Den här åtgärden publicerar entiteter (data uppsättningar och pipeliner) som du skapade i Synapse Analytics-tjänsten.
1. Vänta tills du ser meddelandet om att entiteterna **har publicerats**. Om du vill se meddelanden väljer du klock knappen längst upp till höger. 


## <a name="trigger-and-monitor-the-pipeline"></a>Utlös och övervaka pipelinen

I det här avsnittet aktiverar du pipelinen som publicerades i föregående steg manuellt. 

1. Välj **Lägg till utlösare** i verktygsfältet och välj sedan **Utlös nu**. På sidan för **pipeline-körning** väljer du **OK**.  
1. Gå till fliken **övervakare** som finns i den vänstra panelen. Du ser en pipelinekörning som är utlöst av en manuell utlösare. 
1. När pipeline-körningen har slutförts väljer du länken under pipeline- **namn** -kolumnen för att visa information om aktivitets körning eller kör pipelinen igen. I det här exemplet finns det bara en aktivitet, så du ser bara en post i listan. 
1. Om du vill ha mer information om kopierings åtgärden väljer du länken **information** (glasögon ikonen) under kolumnen **aktivitets namn** . Du kan övervaka information om data volymen som kopieras från källan till mottagaren, data genom strömning, körnings steg med motsvarande varaktighet och använda konfigurationer.

   ![Aktivitetsinformation](media/quickstart-copy-activity-load-sql-pool/activity-details.png)

1. Om du vill växla tillbaka till vyn pipelines körs väljer du länken **alla pipeline-körningar** överst. Om du vill uppdatera listan väljer du **Refresh** (Uppdatera).
1. Kontrol lera att dina data är korrekt skrivna i den dedikerade SQL-poolen.


## <a name="next-steps"></a>Nästa steg

Gå vidare till följande artikel om du vill lära dig mer om Azure Synapse Analytics-support:

> [!div class="nextstepaction"]
> [Pipeline och aktiviteter](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Översikt över koppling](../data-factory/connector-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Kopierings aktivitet](../data-factory/copy-activity-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)