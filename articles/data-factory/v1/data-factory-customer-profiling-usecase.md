---
title: Användnings fall – kund profilering
description: Lär dig hur Azure Data Factory används för att skapa ett data drivet arbets flöde (pipeline) för att profilera spel kunder.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: bb7f0fb6184a813dd9fd3c193bc5e3bda5628f2d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376725"
---
# <a name="use-case---customer-profiling"></a>Användnings fall – kund profilering
Azure Data Factory är en av många tjänster som används för att implementera Cortana Intelligence Suite av lösnings acceleratorer.  Mer information om Cortana Intelligence finns [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics). I det här dokumentet beskriver vi ett enkelt användnings fall som hjälper dig att komma igång med att förstå hur Azure Data Factory kan lösa vanliga analys problem.

## <a name="scenario"></a>Scenario
Contoso är ett spel företag som skapar spel för flera plattformar: spel konsoler, handhållna enheter och persondatorer (datorer). När spelarna spelar dessa spel skapas stora mängder loggdata som spårar användnings mönster, spel stil och inställningar för användaren.  Tillsammans med demografiska, regionala och aktuella produkt data kan Contoso utföra analyser för att hjälpa dem att förbättra spelarens upplevelse och rikta dem mot uppgraderingar och köp av spel. 

Contosos mål är att identifiera affärs-och kors försäljnings möjligheter baserat på spel historiken för sina spelare och lägga till övertygande funktioner för att öka företagets tillväxt och ge en bättre upplevelse för kunderna. För det här användnings fallet använder vi ett spel företag som ett exempel på ett företag. Företaget vill optimera sina spel utifrån spelarens beteende. Dessa principer gäller för alla verksamheter som vill engagera sina kunder runt sina varor och tjänster och förbättra sina kunders erfarenhet.

I den här lösningen vill contoso utvärdera effektiviteten hos en marknadsförings kampanj som den nyligen har lanserat. Vi börjar med de obehandlade spel loggarna, bearbetar och utvärderar dem med information om geolokalisering, går med i annonserings referens data och kopierar dem slutligen till en Azure SQL Database för att analysera kampanjens påverkan.

## <a name="deploy-solution"></a>Distribuera lösning
Allt du behöver för att komma åt och testa det här enkla användnings fallet är en [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/), ett [Azure Blob Storage-konto](../../storage/common/storage-account-create.md)och en [Azure SQL Database](../../azure-sql/database/single-database-create-quickstart.md). Du distribuerar pipeline för kund profilering från panelen **exempel pipelines** på Start sidan för din data fabrik.

1. Skapa en data fabrik eller öppna en befintlig data fabrik. Information om hur du skapar en data fabrik finns i [Kopiera data från Blob Storage till SQL Database med Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .
2. På **Data Factory** -bladet för data fabriken klickar du på panelen **exempel pipelines** .

    ![Exempel panel för exempel](./media/data-factory-samples/SamplePipelinesTile.png)
3. På bladet **exempel pipelines** klickar du på den **kund profil** som du vill distribuera.

    ![Exempel på pipeline-blad](./media/data-factory-samples/SampleTile.png)
4. Ange konfigurations inställningar för exemplet. Till exempel namnet på ditt Azure Storage-konto och nyckel, logiskt SQL-servernamn, databas, användar-ID och lösen ord.

    ![Exempel blad](./media/data-factory-samples/SampleBlade.png)
5. När du är klar med att ange konfigurations inställningarna klickar du på **skapa** för att skapa/distribuera exempel pipeliner och länkade tjänster/tabeller som används av pipelinen.
6. Du ser status för distributionen i exempel panelen som du klickade på tidigare på bladet **exempel pipelines** .

    ![Status för distribution](./media/data-factory-samples/DeploymentStatus.png)
7. När du ser meddelandet **distributionen har slutförts** på panelen för exemplet stänger du bladet **exempel pipelines** .  
8. På bladet **data fabrik** ser du att länkade tjänster, data uppsättningar och pipeliner läggs till i din data fabrik.  

    ![Bladet Datafabrik](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Lösning: översikt
Det här enkla användnings fallet kan användas som ett exempel på hur du kan använda Azure Data Factory för att mata in, förbereda, transformera, analysera och publicera data.

![Arbetsflödet slutpunkt till slutpunkt](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Den här bilden illustrerar hur data pipelinen visas i Azure Portal när de har distribuerats.

1. **PartitionGameLogsPipeline** läser obehandlade spel händelser från Blob Storage och skapar partitioner baserat på år, månad och dag.
2. **EnrichGameLogsPipeline** ansluter till partitionerade spel händelser med geo Code-referens data och berikar data genom att mappa IP-adresser till motsvarande geo-platser.
3. **AnalyzeMarketingCampaignPipeline** -pipelinen använder de data som berikas och bearbetar dem med annonserings data för att skapa de slutliga utdata som innehåller marknads kampanjens effektivitet.

I det här exemplet används Data Factory för att dirigera aktiviteter som kopierar indata, transformerar och bearbetar data och skickar ut de slutliga data till en Azure SQL Database.  Du kan också visualisera nätverket för datapipeliner, hantera dem och övervaka deras status från användar gränssnittet.

## <a name="benefits"></a>Fördelar
Genom att optimera användar profils analysen och justera den med affärs mål kan spel företaget snabbt samla in användnings mönster och analysera effektiviteten hos dess marknadsförings kampanjer.