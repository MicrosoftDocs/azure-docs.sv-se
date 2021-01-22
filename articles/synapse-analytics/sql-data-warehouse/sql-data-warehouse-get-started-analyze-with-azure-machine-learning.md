---
title: Analysera data med Azure Machine Learning
description: Använd Azure Machine Learning för att bygga en förutsägelse maskin inlärnings modell som baseras på data som lagras i Azure Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 76a154d3a137017f374247308a3980d598698246
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678667"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Analysera data med Azure Machine Learning

Den här självstudien använder [Azure Machine Learning designer](../../machine-learning/concept-designer.md) för att bygga en förutsägelse maskin inlärnings modell. Modellen baseras på de data som lagras i Azure dataSynapses. Scenariot för självstudien är att förutsäga om en kund sannolikt kommer att köpa en cykel eller inte så att Adventure Worker, Bike Shop, kan bygga en riktad marknadsförings kampanj.

## <a name="prerequisites"></a>Förutsättningar

För att gå igenom de här självstudierna, behöver du:

* en SQL-pool förinstallerad med AdventureWorksDW exempel data. Information om hur du etablerar den här SQL-poolen finns i [skapa en SQL-pool](create-data-warehouse-portal.md) och välja att läsa in exempel data. Om du redan har ett data lager men inte har exempel data kan du [läsa in exempel data manuellt](./load-data-from-azure-blob-storage-using-copy.md).
* en Azure Machine Learning-arbetsyta. Följ [den här självstudien](../../machine-learning/how-to-manage-workspace.md) för att skapa en ny.

## <a name="get-the-data"></a>Hämta data

De data som används finns i vyn dbo. vTargetMail i AdventureWorksDW. Om du vill använda data lager i den här självstudien exporteras data först till Azure Data Lake Storage-konto eftersom Azure Synapse inte stöder data uppsättningar för närvarande. Azure Data Factory kan användas för att exportera data från data lagret till Azure Data Lake Storage att använda [kopierings aktiviteten](../../data-factory/copy-activity-overview.md). Använd följande fråga för import:

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

När data är tillgängliga i Azure Data Lake Storage används data lager i Azure Machine Learning för att [ansluta till Azure Storage-tjänster](../../machine-learning/how-to-access-data.md). Följ stegen nedan för att skapa ett data lager och en motsvarande data uppsättning:

1. Starta Azure Machine Learning Studio antingen från Azure Portal eller logga in i [Azure Machine Learning Studio](https://ml.azure.com/).

1. Klicka på **data lager** i det vänstra fönstret i avsnittet **Hantera** och klicka sedan på **nytt data lager**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/datastores-tab.png" alt-text="Skärm bild av den vänstra rutan i Azure Machine Learning-gränssnittet":::

1. Ange ett namn för data lagret, Välj typen som "Azure Blob Storage", ange plats och autentiseringsuppgifter. Klicka sedan på **skapa**.

1. Klicka sedan på **data uppsättningar** i det vänstra fönstret i avsnittet **till gångar** . Välj **skapa data uppsättning** med alternativet **från data lager**.

1. Ange namnet på data uppsättningen och välj den typ som ska vara **tabell**. Klicka sedan på **Nästa** för att gå framåt.

1. I **avsnittet Välj eller skapa ett data lager** väljer du alternativet **tidigare skapade data lager**. Välj det data lager som skapades tidigare. Klicka på Nästa och ange sökvägen och fil inställningarna. Se till att ange kolumn rubrik om filerna innehåller en.

1. Klicka slutligen på **skapa** för att skapa data uppsättningen.

## <a name="configure-designer-experiment"></a>Konfigurera design experimentet

Följ sedan stegen nedan för design konfiguration:

1. Klicka på fliken **Designer** i det vänstra fönstret i avsnittet **författare** .

1. Välj **lättanvända inbyggda moduler** för att skapa en ny pipeline.

1. I fönstret inställningar till höger anger du namnet på pipelinen.

1. Välj också ett mål beräknings kluster för hela experimentet i Inställningar-knappen till ett tidigare allokerat kluster. Stäng fönstret inställningar.

## <a name="import-the-data"></a>Importera data

1. Välj **data uppsättningarna** underfliken i det vänstra fönstret under sökrutan.

1. Dra data uppsättningen som du skapade tidigare till arbets ytan.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/import-dataset.png" alt-text="Skärm bild av data uppsättnings modul på arbets ytan.":::

## <a name="clean-the-data"></a>Rensa data

Om du vill rensa data släpper du kolumner som inte är relevanta för modellen. Följ stegen nedan:

1. Välj **modulerna** underfliken i det vänstra fönstret.

1. Dra modulen **Välj kolumner i data uppsättning** under **dataomvandling < manipulering** till arbets ytan. Anslut den här modulen till **data uppsättnings** modulen.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-in.png" alt-text="Skärm bild av modulen för val av kolumner på arbets ytan." lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-out.png":::

1. Klicka på modulen för att öppna fönstret Egenskaper. Klicka på Redigera kolumn för att ange vilka kolumner som du vill ta bort.

1. Exkludera två kolumner: CustomerAlternateKey och GeographyKey. Klicka på **Spara**

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/drop-columns.png" alt-text="Skärm bild som visar kolumner som tas bort.":::

## <a name="build-the-model"></a>Bygga modellen

Data delas upp 80-20:80% för att träna en maskin inlärnings modell och 20% för att testa modellen. "Två klass"-algoritmer används i det här binära klassificerings problemet.

1. Dra modulen **dela data** till arbets ytan.

1. I rutan egenskaper anger du 0,8 för **bråk delar i den första utdata-datauppsättningen**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/split-data.png" alt-text="Skärm bild som visar delnings förhållandet för 0,8.":::

1. Dra modulen **tvåklassförhöjt beslutsträd** till arbetsytan.

1. Dra modulen **träna modell** till arbets ytan. Ange indata genom att ansluta den till **besluts trädet i två klasser** (ml-algoritm) och **dela data** (data för att träna algoritmen på) moduler.

1. För träna modell modell går du till alternativet **etikett kolumn** i fönstret Egenskaper och väljer Redigera kolumn. Välj kolumnen **BikeBuyer** som den kolumn som du vill förutse och välj **Spara**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/label-column.png" alt-text="Skärm bild som visar etikett kolumn, BikeBuyer, markerad.":::

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/train-model.png" alt-text="Skärm bild som visar modulen träna modell ansluten till Two-Class utökat besluts träd och delade Datamoduler.":::

## <a name="score-the-model"></a>Poängsätta modellen

Testa nu hur modellen utför test data. Två olika algoritmer jämförs för att se vilken som fungerar bättre. Följ stegen nedan:

1. Dra modulen **Poäng modell** till arbets ytan och Anslut den till **träna modell** -och **delade** Datamoduler.

1. Dra den **genomsnittliga Perceptron i Bayes** till experiment arbets ytan. Du jämför hur den här algoritmen fungerar i jämförelse med Two-Class utökat besluts träd.

1. Kopiera och klistra in modulerna **träna modell** och **Poäng modell** på arbets ytan.

1. Dra modulen **Utvärdera modell** till arbetsytan för att jämföra de två algoritmerna.

1. Klicka på **Skicka** för att konfigurera pipeline-körningen.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-in.png" alt-text="Skärm bild av alla återstående moduler på arbets ytan." lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-out.png":::

1. När körningen är klar högerklickar du på modulen **utvärdera modell** och klickar på **visualisera utvärderings resultat**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/result-visualize-zoomed-out.png" alt-text="Skärm bild av resultaten.":::

Måtten som tillhandahålls är ROC-kurvan, precisions återställnings diagrammet och lyft kurvan. Titta på de här måtten för att se att den första modellen utförts bättre än den andra. Om du vill titta på vad den första modellen förutsäger högerklickar du på modulen Poäng modell och klickar på visualisera Poäng data uppsättning för att se de förväntade resultaten.

Du ser två fler kolumner som läggs till i din test data uppsättning.

* Poängsatt sannolikhet: sannolikheten att en kund är en cykelköpare.
* Poängsatta etiketter: klassificering utförd av modellen – cykelköpare (1) eller inte (0). Det här sannolikhetströskelvärdet för etikettering anges till 50 % och kan justeras.

Jämför kolumnen BikeBuyer (faktisk) med de poäng etiketter (förutsägelse) som du vill se hur väl modellen har utförts. Sedan kan du använda den här modellen för att göra förutsägelser för nya kunder. Du kan [publicera den här modellen som en webb tjänst](../../machine-learning/tutorial-designer-automobile-price-deploy.md) eller Skriv tillbaka resultatet till Azure-Synapse.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Machine Learning finns i [Introduktion till Machine Learning på Azure](../../machine-learning/overview-what-is-azure-ml.md).

Lär dig mer om den inbyggda poängen i informations [lagret.](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)