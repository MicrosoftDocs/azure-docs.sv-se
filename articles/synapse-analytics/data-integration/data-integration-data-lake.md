---
title: Mata in i Azure Data Lake Storage Gen2
description: Lär dig hur du matar in data i Azure Data Lake Storage Gen2 i Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: e3024fe1a8fe524a1deddef23a67d86a600b9394
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567612"
---
# <a name="ingest-data-into-azure-data-lake-storage-gen2"></a>Mata in data i Azure Data Lake Storage Gen2 

I den här artikeln lär du dig att mata in data från en plats till en annan i ett Azure Data Lake Gen 2-lagringskonto (Azure Data Lake Gen 2) med hjälp av Azure Synapse Analytics.

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration:** Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage konto:** Du använder Azure Data Lake Gen2 som *källdatalager.* Om du inte har ett lagringskonto kan du gå till [Skapa ett Azure Storage konto](../../storage/common/storage-account-create.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) för anvisningar om hur du skapar ett.

## <a name="create-linked-services"></a>Skapa länkade tjänster

I Azure Synapse Analytics är en länkad tjänst där du definierar din anslutningsinformation till andra tjänster. I det här avsnittet lägger du till Azure Synapse Analytics Azure Data Lake Gen 2 som länkade tjänster.

1. Öppna Azure Synapse Analytics UX och gå till **fliken** Hantera.
1. Under **Externa anslutningar** väljer du Länkade **tjänster**.
1. Om du vill lägga till en länkad tjänst väljer du **Ny**.
1. Välj Azure Data Lake Storage Gen2 panelen i listan och välj **Fortsätt.**
1. Ange dina autentiseringsuppgifter. Kontonyckel, tjänstens huvudnamn och hanterad identitet stöds för närvarande av autentiseringstyper. Välj testanslutning för att verifiera att dina autentiseringsuppgifter är korrekta. 
1. Välj **Skapa** när du är klar.

## <a name="create-pipeline"></a>Skapa pipeline

En pipeline innehåller det logiska flödet för en körning av en uppsättning aktiviteter. I det här avsnittet skapar du en pipeline som innehåller en kopieringsaktivitet som matar in data från Azure Data Lake Gen 2 till en dedikerad SQL-pool.

1. Gå till **fliken Orkestrera.** Välj på plusikonen bredvid pipelinerubriken och välj **Pipeline**.
1. Dra **Kopiera data till pipelinearbetsytan** under Flytta och **transformera** i aktivitetsfönstret.
1. Välj kopieringsaktiviteten och gå till **fliken** Källa. Välj **Ny för** att skapa en ny källdatauppsättning.
1. Välj Azure Data Lake Storage Gen2 som datalager och välj Fortsätt.
1. Välj DelimitedText som format och välj Fortsätt.
1. I fönstret ange egenskaper väljer du den ADLS-länkade tjänst som du skapade. Ange filsökvägen för dina källdata och ange om den första raden har en rubrik. Du kan importera schemat från filarkivet eller en exempelfil. Välj OK när du är klar.
1. Gå till **fliken Mottagare.** Välj **Ny för** att skapa en ny datauppsättning för mottagare.
1. Välj Azure Data Lake Storage gen2 som datalager och välj Fortsätt.
1. Välj DelimitedText som format och välj Fortsätt.
1. I fönstret Ange egenskaper väljer du den ADLS-länkade tjänst som du skapade. Ange sökvägen till den mapp där du vill skriva data. Välj OK när du är klar.

## <a name="debug-and-publish-pipeline"></a>Felsöka och publicera pipeline

När du har konfigurerat pipelinen kan du köra en felsökningskörning innan du publicerar artefakterna för att kontrollera att allt är korrekt.

1. Välj **Felsöka** i verktygsfält för att felsöka pipelinen. Du ser status för pipelinekörningen på fliken **Utdata** längst ned i fönstret. 
1. När pipelinen kan köras väljer du Publicera alla i det **översta verktygsfältet.** Den här åtgärden publicerar entiteter (datauppsättningar och pipeliner) som du har skapat Synapse Analytics tjänsten.
1. Vänta tills du ser meddelandet om att entiteterna **har publicerats**. Om du vill se meddelanden väljer du klockknappen längst upp till höger. 


## <a name="trigger-and-monitor-the-pipeline"></a>Utlösa och övervaka pipelinen

I det här steget utlöser du manuellt pipelinen som publicerades i föregående steg. 

1. Välj **Lägg till** utlösare i verktygsfältet och välj sedan Trigger Now **(Utlöst nu).** På sidan **Pipeline Run** (Pipelinekörning) väljer du **Slutför**.  
1. Gå till **fliken Övervaka** i det vänstra sidofältet. Du ser en pipelinekörning som är utlöst av en manuell utlösare. Du kan använda länkar i kolumnen **Åtgärder** för att visa aktivitetsinformation och köra pipelinen igen.
1. Om du vill se aktivitetskörningar som är associerade med pipelinekörningen, väljer du länken **View Activity Runs** (Visa aktivitetskörningar) i kolumnen **Actions** (Åtgärder). I det här exemplet finns det bara en aktivitet, så du ser bara en post i listan. Om du vill se mer information om kopieringsåtgärden väljer du länken för **detaljer** (glasögonikonen) i kolumnen **Actions** (Åtgärder). Välj **Pipelinekörningar** högst upp för att gå tillbaka till vyn Pipeline Runs (Pipelinekörningar). Välj **Uppdatera** för att uppdatera vyn.
1. Kontrollera att dina data är korrekt skrivna i den dedikerade SQL-poolen.


## <a name="next-steps"></a>Nästa steg

Mer information om dataintegrering för Azure Synapse Analytics finns i artikeln [Mata in data i en dedikerad SQL-pool.](data-integration-sql-pool.md)