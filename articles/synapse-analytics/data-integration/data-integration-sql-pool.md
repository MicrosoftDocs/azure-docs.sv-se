---
title: Mata in data i en dedikerad SQL-pool
description: Lär dig hur du matar in data i en dedikerad SQL-pool i Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/03/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: a02abff712fc26f653307108fcc3bb284444d0c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676653"
---
# <a name="ingest-data-into-a-dedicated-sql-pool"></a>Mata in data i en dedikerad SQL-pool

I den här artikeln får du lära dig hur du matar in data från ett Azure Data Lake gen 2-lagrings konto till en dedikerad SQL-pool i Azure Synapse Analytics.

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration**: om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage-konto**: du använder Azure Data Lake Storage gen 2 som *käll* data lager. Om du inte har ett lagrings konto kan du läsa [skapa ett Azure Storage-konto](../../storage/common/storage-account-create.md) för att skapa ett.
* **Azure Synapse Analytics**: du använder en dedikerad SQL-pool som data lager för *mottagare* . Om du inte har en Azure Synapse Analytics-instans kan du läsa [skapa en dedikerad SQL-pool](../../azure-sql/database/single-database-create-quickstart.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) för steg för att skapa en.

## <a name="create-linked-services"></a>Skapa länkade tjänster

I Azure Synapse Analytics är en länkad tjänst där du definierar din anslutnings information till andra tjänster. I det här avsnittet ska du lägga till en Azure Synapse Analytics och Azure Data Lake Storage Gen2 länkad tjänst.

1. Öppna Azure Synapse Analytics-UX och gå till fliken **Hantera** .
1. Under **externa anslutningar** väljer du **länkade tjänster**.
1. Om du vill lägga till en länkad tjänst väljer du **ny**.
1. Välj panelen Azure Data Lake Storage Gen2 i listan och välj **Fortsätt**.
1. Ange autentiseringsuppgifterna för autentisering. Konto nyckel, tjänstens huvud namn och hanterad identitet stöds för närvarande autentiseringstyper som stöds. Kontrol lera att dina autentiseringsuppgifter är korrekta genom att välja testa anslutning. Välj **Skapa** när du är klar.
1. Upprepa steg 3-5, men i stället för Azure Data Lake Storage Gen2 väljer du panelen Azure Synapse Analytics och anger motsvarande autentiseringsuppgifter för anslutning. För Azure Synapse Analytics stöds SQL-autentisering, hanterad identitet och tjänstens huvud namn för närvarande.

## <a name="create-pipeline"></a>Skapa pipeline

En pipeline innehåller det logiska flödet för en körning av en uppsättning aktiviteter. I det här avsnittet ska du skapa en pipeline som innehåller en kopierings aktivitet som matar in data från ADLS Gen2 i en dedikerad SQL-pool.

1. Gå till fliken **integrera** . Välj på plus ikonen bredvid pipelinens huvud och välj **pipeline**.
1. Under **flytta och transformera** i fönstret aktiviteter drar du **Kopiera data** till pipeline-arbetsytan.
1. Välj aktiviteten Kopiera och gå till fliken **källa** . Välj **ny** för att skapa en ny käll data uppsättning.
1. Välj Azure Data Lake Storage Gen2 som data lager och välj Fortsätt.
1. Välj DelimitedText som format och välj Fortsätt.
1. I rutan Ange egenskaper väljer du den länkade tjänsten ADLS som du skapade. Ange fil Sök vägen till dina källdata och ange om den första raden har ett sidhuvud. Du kan importera schemat från fil arkivet eller en exempel fil. Välj OK när du är färdig.
1. Gå till fliken **mottagare** . Välj **ny** för att skapa en ny data uppsättning för mottagare.
1. Välj Azure Synapse Analytics som data lager och välj Fortsätt.
1. I rutan Ange egenskaper väljer du den länkade Azure Synapse Analytics-tjänsten som du skapade. Om du skriver till en befintlig tabell väljer du den i list rutan. Annars markerar du **Redigera** och anger i det nya tabell namnet. Välj OK när du är färdig
1. Om du skapar en tabell aktiverar du **Skapa tabell automatiskt** i fältet tabell alternativ.

## <a name="debug-and-publish-pipeline"></a>Felsöka och publicera pipeline

När du har konfigurerat din pipeline kan du köra en fel söknings körning innan du publicerar dina artefakter för att kontrol lera att allt är korrekt.

1. Välj **Felsöka** i verktygsfält för att felsöka pipelinen. Du ser status för pipelinekörningen på fliken **Utdata** längst ned i fönstret. 
1. När pipelinen har körts kan du gå till det övre verktygsfältet och välja **publicera alla**. Den här åtgärden publicerar entiteter (data uppsättningar och pipeliner) som du skapade i Synapse Analytics-tjänsten.
1. Vänta tills du ser meddelandet om att entiteterna **har publicerats**. Om du vill se meddelanden väljer du klock knappen längst upp till höger. 


## <a name="trigger-and-monitor-the-pipeline"></a>Utlös och övervaka pipelinen

I det här steget aktiverar du pipelinen som publicerades i föregående steg manuellt. 

1. Välj **Lägg till utlösare** i verktygsfältet och välj sedan **Utlös nu**. På sidan **Pipeline Run** (Pipelinekörning) väljer du **Slutför**.  
1. Gå till fliken **övervakare** som finns i den vänstra panelen. Du ser en pipelinekörning som är utlöst av en manuell utlösare. Du kan använda länkar i kolumnen **åtgärder** för att Visa aktivitets information och köra pipelinen igen.
1. Om du vill se aktivitetskörningar som är associerade med pipelinekörningen, väljer du länken **View Activity Runs** (Visa aktivitetskörningar) i kolumnen **Actions** (Åtgärder). I det här exemplet finns det bara en aktivitet, så du ser bara en post i listan. Om du vill se mer information om kopieringsåtgärden väljer du länken för **detaljer** (glasögonikonen) i kolumnen **Actions** (Åtgärder). Välj **pipeline-körningar** överst för att gå tillbaka till vyn pipelines-körningar. Välj **Uppdatera** för att uppdatera vyn.
1. Kontrol lera att dina data är korrekt skrivna i den dedikerade SQL-poolen.


## <a name="next-steps"></a>Nästa steg

Mer information om data integrering för Azure Synapse Analytics finns i mata in [data i Azure Data Lake Storage Gen2 ](data-integration-data-lake.md) artikel.