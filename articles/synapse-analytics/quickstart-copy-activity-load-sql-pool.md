---
title: 'Snabbstart: läsa in data i en dedikerad SQL-pool med hjälp av kopieringsaktiviteten'
description: Använd pipelinekopieringsaktiviteten i Azure Synapse Analytics för att läsa in data till dedikerad SQL-pool.
services: synapse-analytics
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.custom: seo-lt-2019
ms.date: 11/02/2020
ms.openlocfilehash: 8c0ebebd98a34a82b464707b82a650a1825676be
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566263"
---
# <a name="quickstart-load-data-into-dedicated-sql-pool-using-the-copy-activity"></a>Snabbstart: Läsa in data till en dedikerad SQL-pool med kopieringsaktiviteten

Azure Synapse Analytics erbjuder olika analysmotorer som hjälper dig att mata in, transformera, modellera och analysera dina data. En dedikerad SQL-pool erbjuder T-SQL-baserade beräknings- och lagringsfunktioner. När du har skapat en dedikerad SQL-pool i Synapse-arbetsytan kan data läsas in, modelleras, bearbetas och levereras för snabbare analysinsikter.

I den här snabbstarten lär du dig att *läsa in data från Azure SQL Database till Azure Synapse Analytics*. Du kan följa liknande steg för att kopiera data från andra typer av datalager. Det här liknande flödet gäller även för datakopiering för andra källor och mottagare.

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Azure Synapse arbetsyta: Skapa en Synapse-arbetsyta med hjälp Azure Portal följa anvisningarna i [Snabbstart: Skapa en Synapse-arbetsyta](quickstart-create-workspace.md).
* Azure SQL Database: Den här självstudien kopierar data från Adventure Works LT-exempeldatauppsättningen i Azure SQL Database. Du kan skapa den här exempeldatabasen SQL Database genom att följa anvisningarna i [Skapa en exempeldatabas i Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md). Eller så kan du använda andra datalager genom att följa liknande steg.
* Azure-lagringskonto: Azure Storage används som *mellanlagringsområde* i kopieringsåtgärden. Om du inte har något Azure-lagringskonto finns det anvisningar i [Skapa ett lagringskonto](../storage/common/storage-account-create.md).
* Azure Synapse Analytics: Du använder en dedikerad SQL-pool som ett datalager för mottagare. Om du inte har en instans Azure Synapse Analytics finns anvisningar för hur du skapar [en](quickstart-create-sql-pool-portal.md) i Skapa en dedikerad SQL-pool.

### <a name="navigate-to-the-synapse-studio"></a>Gå till Synapse Studio

När synapse-arbetsytan har skapats kan du öppna din Synapse-Synapse Studio:

* Öppna Synapse-arbetsytan i [Azure Portal](https://ms.portal.azure.com/#home). Välj **Öppna** på kortet Öppna Synapse Studio under Komma igång.
* Öppna [Azure Synapse Analytics](https://web.azuresynapse.net/) och logga in på din arbetsyta.

I den här snabbstarten använder vi arbetsytan med namnet "adftest2020" som exempel. Du kommer automatiskt till Synapse Studio startsidan.

![Synapse Studio startsida](media/doc-common-process/synapse-studio-home.png)

## <a name="create-linked-services"></a>Skapa länkade tjänster

I Azure Synapse Analytics är en länkad tjänst där du definierar din anslutningsinformation till andra tjänster. I det här avsnittet skapar du följande två typer av länkade tjänster: Azure SQL Database och Azure Data Lake Storage Gen2 (ADLS Gen2) länkade tjänster.

1. På Synapse Studio väljer du fliken **Hantera i** det vänstra navigeringsfönstret.
1. Under Externa anslutningar väljer du Länkade tjänster.
  
   ![Skapa ny länkad tjänst](media/doc-common-process/new-linked-service.png)

1. Om du vill lägga till en länkad tjänst väljer du **Ny**.
1. Välj **Azure SQL Database** från galleriet och välj sedan **Fortsätt.** Du kan skriva "sql" i sökrutan för att filtrera anslutningsapparna.

   ![Skapa en ny Azure SQL Database länkad tjänst](media/quickstart-copy-activity-load-sql-pool/new-azure-sql-linked-service.png)

1. På sidan New Linked Service (Ny länkad tjänst) väljer du servernamnet och databasnamnet i listrutan och anger användarnamnet och lösenordet. Klicka **på Testa** anslutning för att verifiera inställningarna och välj sedan **Skapa.**

   ![Konfigurera Azure SQL Database länkad tjänst](media/quickstart-copy-activity-load-sql-pool/azure-sql-linked-service-configuration.png)

1. Upprepa steg 3–4, men välj **Azure Data Lake Storage Gen2** i stället från galleriet. På sidan New Linked Service (Ny länkad tjänst) väljer du namnet på ditt lagringskonto i listrutan. Klicka **på Testa** anslutning för att verifiera inställningarna och välj sedan **Skapa.** 

   ![Konfigurera Azure Data Lake Storage Gen2](media/quickstart-copy-activity-load-sql-pool/adls-gen2-linked-service-configuration.png)
 
## <a name="create-a-pipeline"></a>Skapa en pipeline

En pipeline innehåller det logiska flödet för en körning av en uppsättning aktiviteter. I det här avsnittet skapar du en pipeline som innehåller en kopieringsaktivitet som matar in data från Azure SQL Database till en dedikerad SQL-pool.

1. Gå till **fliken** Integrera. Välj plusikonen bredvid rubriken pipelines och välj Pipeline.

   ![Skapa en ny pipeline](media/doc-common-process/new-pipeline.png)

1. Under *Flytta och transformera* i *fönstret Aktiviteter* drar du Kopiera **data till** pipelinearbetsytan.
1. Välj kopieringsaktiviteten och gå till fliken Källa. Välj **Ny för** att skapa en ny källdatauppsättning.

   ![Skapa en källdatauppsättning](media/quickstart-copy-activity-load-sql-pool/new-source-dataset.png)

1. Välj **Azure SQL Database** som datalager och välj **Fortsätt.**
1. I fönstret *Ange egenskaper* väljer du den länkade Azure SQL Database som du skapade i tidigare steg. 
1. Under Tabellnamn väljer du en exempeltabell som ska användas i följande kopieringsaktivitet. I den här snabbstarten använder vi tabellen "SalesLT.Customer" som exempel. 

   ![Konfigurera egenskaper för källdatauppsättning](media/quickstart-copy-activity-load-sql-pool/source-dataset-properties.png)
1. Välj **OK när** du är klar.
1. Välj kopieringsaktiviteten och gå till fliken Mottagare. Välj **Ny för** att skapa en ny datauppsättning för mottagare.
1. Välj **Azure Synapse dedikerad SQL-pool** som datalager och välj **Fortsätt.**
1. I fönstret  **Ange egenskaper** väljer du den SQL Analytics-pool som du skapade i tidigare steg. Om du skriver till en befintlig tabell väljer du den under *Tabellnamn* i listrutan. Annars markerar du "Redigera" och anger det nya tabellnamnet. Välj **OK när** du är klar.
1. För Inställningar för datauppsättning för mottagare **aktiverar du Skapa** tabell automatiskt i fältet Tabellalternativ.

   ![Aktivera automatisk skapa](media/quickstart-copy-activity-load-sql-pool/auto-create-table.png)

1. På sidan **Inställningar** markerar du kryssrutan för Aktivera **mellanlagring.** Det här alternativet gäller om dina källdata inte är kompatibla med PolyBase. I **avsnittet Mellanlagringsinställningar** väljer du Azure Data Lake Storage Gen2 länkad tjänst som du skapade i ett tidigare steg som mellanlagring. 

    Lagringen används för mellanlagring av data innan de läses in i Azure Synapse Analytics med hjälp av PolyBase. När kopieringen är klar rensas tillfälliga data Azure Data Lake Storage Gen2 automatiskt.

   ![Aktivera mellanlagring](media/quickstart-copy-activity-load-sql-pool/staging-linked-service.png)

1. Verifiera pipelinen genom att **välja Verifiera** i verktygsfältet. Resultatet av pipelineverifieringen visas till höger på sidan. 

## <a name="debug-and-publish-the-pipeline"></a>Felsöka och publicera en pipeline

När du har konfigurerat pipelinen kan du köra en felsökningskörning innan du publicerar artefakterna för att verifiera att allt stämmer.

1. Välj **Felsöka** i verktygsfält för att felsöka pipelinen. Du ser status för pipelinekörningen på fliken **Utdata** längst ned i fönstret. 

   ![Felsöka pipeline](media/quickstart-copy-activity-load-sql-pool/debugging-result.png)

1. När pipelinekörningen lyckas väljer du Publicera alla i det **översta verktygsfältet.** Den här åtgärden publicerar entiteter (datauppsättningar och pipeliner) som du skapade Synapse Analytics tjänsten.
1. Vänta tills du ser meddelandet om att entiteterna **har publicerats**. Om du vill se meddelanden väljer du klockknappen längst upp till höger. 


## <a name="trigger-and-monitor-the-pipeline"></a>Utlösa och övervaka pipelinen

I det här avsnittet utlöser du pipelinen som publicerades i föregående steg manuellt. 

1. Välj **Lägg till** utlösare i verktygsfältet och välj sedan Trigger Now **(Utlöst nu).** På sidan **Pipeline Run (Pipelinekörning)** väljer du **OK**.  
1. Gå till **fliken Övervaka** i det vänstra sidofältet. Du ser en pipelinekörning som är utlöst av en manuell utlösare. 
1. När pipelinekörningen har slutförts väljer du länken under kolumnen **Pipelinenamn** för att visa information om aktivitetskörningar eller för att köra pipelinen igen. I det här exemplet finns det bara en aktivitet, så du ser bara en post i listan. 
1. Om du vill ha mer information om **kopieringsåtgärden väljer** du länken Information (glasögonikonen) under **kolumnen Aktivitetsnamn.** Du kan övervaka information som mängden data som kopieras från källan till mottagaren, dataflöde, körningssteg med motsvarande varaktighet och använda konfigurationer.

   ![Aktivitetsinformation](media/quickstart-copy-activity-load-sql-pool/activity-details.png)

1. Om du vill växla tillbaka till vyn med pipelinekörningar väljer du länken Alla **pipelinekörningar** högst upp. Om du vill uppdatera listan väljer du **Refresh** (Uppdatera).
1. Kontrollera att dina data är korrekt skrivna i den dedikerade SQL-poolen.


## <a name="next-steps"></a>Nästa steg

Gå vidare till följande artikel om du vill veta Azure Synapse Analytics support:

> [!div class="nextstepaction"]
> [Pipeline och aktiviteter](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Översikt över anslutningsapp](../data-factory/connector-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [aktiviteten Kopiera](../data-factory/copy-activity-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)