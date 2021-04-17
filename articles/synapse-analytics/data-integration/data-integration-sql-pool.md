---
title: Mata in data i en dedikerad SQL-pool
description: Lär dig hur du matar in data i en dedikerad SQL-pool i Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: pipeline
ms.date: 11/03/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 79f0b1a6942f141f0a2d2d38adc5875d82e86d50
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567629"
---
# <a name="ingest-data-into-a-dedicated-sql-pool"></a>Mata in data i en dedikerad SQL-pool

I den här artikeln lär du dig att mata in data från ett Azure Data Lake Gen 2-lagringskonto till en dedikerad SQL-pool i Azure Synapse Analytics.

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration:** Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage-konto:** Du använder Azure Data Lake Storage Gen 2 som *källdatalager.* Om du inte har ett lagringskonto kan du gå till [Skapa ett Azure Storage konto](../../storage/common/storage-account-create.md) för anvisningar om hur du skapar ett.
* **Azure Synapse Analytics:** Du använder en dedikerad SQL-pool som ett *datalager för* mottagare. Om du inte har en instans Azure Synapse Analytics finns anvisningar för hur [du skapar en](../../azure-sql/database/single-database-create-quickstart.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) i Skapa en dedikerad SQL-pool.

## <a name="create-linked-services"></a>Skapa länkade tjänster

I Azure Synapse Analytics är en länkad tjänst där du definierar din anslutningsinformation till andra tjänster. I det här avsnittet lägger du till en Azure Synapse Analytics och Azure Data Lake Storage Gen2 länkad tjänst.

1. Öppna Azure Synapse Analytics UX och gå till **fliken** Hantera.
1. Under **Externa anslutningar** väljer du Länkade **tjänster**.
1. Om du vill lägga till en länkad tjänst väljer du **Ny**.
1. Välj Azure Data Lake Storage Gen2 panelen i listan och välj **Fortsätt.**
1. Ange dina autentiseringsuppgifter. Kontonyckel, tjänstens huvudnamn och hanterad identitet stöds för närvarande av autentiseringstyper. Välj testanslutning för att verifiera att dina autentiseringsuppgifter är korrekta. Välj **Skapa** när du är klar.
1. Upprepa steg 3–5, men i stället för Azure Data Lake Storage Gen2 väljer du Azure Synapse Analytics och anger motsvarande autentiseringsuppgifter för anslutningen. För Azure Synapse Analytics stöds SQL-autentisering, hanterad identitet och tjänstens huvudnamn för närvarande.

## <a name="create-pipeline"></a>Skapa pipeline

En pipeline innehåller det logiska flödet för en körning av en uppsättning aktiviteter. I det här avsnittet skapar du en pipeline som innehåller en kopieringsaktivitet som matar in data från ADLS Gen2 till en dedikerad SQL-pool.

1. Gå till **fliken** Integrera. Välj på plusikonen bredvid rubriken pipelines och välj **Pipeline**.
1. Under **Flytta och transformera** i aktivitetsfönstret drar du **Kopiera data till** pipelinearbetsytan.
1. Välj kopieringsaktiviteten och gå till **fliken** Källa. Välj **Ny för** att skapa en ny källdatauppsättning.
1. Välj Azure Data Lake Storage gen2 som datalager och välj Fortsätt.
1. Välj DelimitedText som format och välj Fortsätt.
1. I fönstret ange egenskaper väljer du den ADLS-länkade tjänst som du skapade. Ange filsökvägen för dina källdata och ange om den första raden har en rubrik. Du kan importera schemat från filarkivet eller en exempelfil. Välj OK när du är klar.
1. Gå till **fliken Mottagare.** Välj **Ny för** att skapa en ny datauppsättning för mottagare.
1. Välj Azure Synapse Analytics som datalager och välj Fortsätt.
1. I fönstret ange egenskaper väljer du den länkade Azure Synapse Analytics som du skapade. Om du skriver till en befintlig tabell väljer du den i listrutan. Annars markerar du **Redigera** och anger det nya tabellnamnet. Välj OK när du är klar
1. Om du skapar en tabell aktiverar du **Skapa tabell automatiskt** i fältet tabellalternativ.

## <a name="debug-and-publish-pipeline"></a>Felsöka och publicera pipeline

När du har konfigurerat pipelinen kan du köra en felsökningskörning innan du publicerar artefakterna för att kontrollera att allt är korrekt.

1. Välj **Felsöka** i verktygsfält för att felsöka pipelinen. Du ser status för pipelinekörningen på fliken **Utdata** längst ned i fönstret. 
1. När pipelinen kan köras väljer du Publicera alla i det **översta verktygsfältet.** Den här åtgärden publicerar entiteter (datauppsättningar och pipeliner) som du skapade Synapse Analytics tjänsten.
1. Vänta tills du ser meddelandet om att entiteterna **har publicerats**. Om du vill se meddelanden väljer du klockknappen längst upp till höger. 


## <a name="trigger-and-monitor-the-pipeline"></a>Utlösa och övervaka pipelinen

I det här steget utlöser du pipelinen som publicerades i föregående steg manuellt. 

1. Välj **Lägg till** utlösare i verktygsfältet och välj sedan Trigger Now **(Utlöst nu).** På sidan **Pipeline Run** (Pipelinekörning) väljer du **Slutför**.  
1. Gå till **fliken Övervaka** i det vänstra sidofältet. Du ser en pipelinekörning som är utlöst av en manuell utlösare. Du kan använda länkar i kolumnen **Åtgärder** för att visa aktivitetsinformation och köra pipelinen igen.
1. Om du vill se aktivitetskörningar som är associerade med pipelinekörningen, väljer du länken **View Activity Runs** (Visa aktivitetskörningar) i kolumnen **Actions** (Åtgärder). I det här exemplet finns det bara en aktivitet, så du ser bara en post i listan. Om du vill se mer information om kopieringsåtgärden väljer du länken för **detaljer** (glasögonikonen) i kolumnen **Actions** (Åtgärder). Välj **PipelineKörningar** högst upp för att gå tillbaka till vyn Pipeline Runs (Pipelinekörningar). Välj **Uppdatera** för att uppdatera vyn.
1. Kontrollera att dina data är korrekt skrivna i den dedikerade SQL-poolen.


## <a name="next-steps"></a>Nästa steg

Mer information om dataintegrering för Azure Synapse Analytics finns i [artikeln Mata in data Azure Data Lake Storage Gen2 ](data-integration-data-lake.md) data.