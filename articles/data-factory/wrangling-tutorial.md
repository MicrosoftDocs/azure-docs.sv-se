---
title: Komma igång med datatransformering Data Flow i Azure Data Factory
description: En själv studie kurs om hur du förbereder data i Azure Data Factory med datatransformering Data Flow
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: f32739b06920f6b20dc87b8e1fbd2884c323a859
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634325"
---
# <a name="prepare-data-with-data-wrangling"></a>Förbereda data med data datatransformering

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Med data datatransformering i Data Factory kan du bygga interaktiva Power Query kombinera-UPS internt i ADF och sedan köra dem i skala inuti en ADF-pipeline.

> [!NOTE]
> Power Query aktivitet i ADF är för närvarande tillgängliga i offentlig för hands version

## <a name="create-a-power-query-activity"></a>Skapa en Power Query-aktivitet

Det finns två sätt att skapa en Power Query i Azure Data Factory. Ett sätt är att klicka på plus ikonen och välja **data flöde** i fönstret fabriks resurser.

> [!NOTE]
> Tidigare fanns funktionen data datatransformering i arbets flödet för data flödet. Nu ska du skapa dina data datatransformering kombinera-in från ```New > Power query```

![Skärm bild som visar Power Query i fönstret fabriks resurser.](media/data-flow/power-query-wrangling.png)

Den andra metoden är i fönstret aktiviteter i pipeline-arbetsytan. Öppna **Power Querys** draget och dra **Power Query** aktiviteten till arbets ytan.

![Skärm bild som visar alternativet Datatransformering Data Flow.](media/data-flow/power-query-activity.png)

## <a name="author-a-wrangling-data-flow"></a>Redigera ett datatransformering-dataflöde

Lägg till en **käll data uppsättning** för din Power Query kombinera. Du kan antingen välja en befintlig data uppsättning eller skapa en ny. Du kan också välja en data uppsättning för mottagare. Du kan välja en eller flera käll data uppsättningar, men endast en mottagare tillåts för tillfället. Det är valfritt att välja en mottagar data uppsättning, men minst en käll data uppsättning krävs.

![Datatransformering](media/wrangling-data-flow/tutorial4.png)

Klicka på **skapa** för att öppna Power Query online mashup-redigeraren.

![Skärm bild som visar knappen Skapa som öppnar Power Query online mashup-redigeraren.](media/wrangling-data-flow/tutorial5.png)

Redigera ditt datatransformering-dataflöde med hjälp av kod fri förberedelse av data. En lista över tillgängliga funktioner finns i [omvandlings funktioner](wrangling-functions.md). ADF översätter ```M``` skriptet till ett data flödes skript så att du kan köra Power Query i skala med hjälp av Spark-miljön för ADF-dataflöde.

![Skärm bild som visar processen för att redigera ditt datatransformering-dataflöde.](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Köra och övervaka ett datatransformering-dataflöde

Om du vill köra en fel söknings körning av en pipeline för ett datatransformering-dataflöde klickar du på **Felsök** i pipeline-arbetsytan. När du har publicerat ditt data flöde kör **utlösaren nu** en körning på begäran av den senast publicerade pipelinen. Datatransformering data flöden kan schemaläggas med alla befintliga Azure Data Factory-utlösare.

![Skärm bild som visar hur du lägger till ett datatransformering-dataflöde.](media/wrangling-data-flow/tutorial3.png)

Gå till fliken **övervaka** för att visualisera utdata från en utlöst datatransformering data flödes aktivitet körning.

![Skärm bild som visar utdata från körningen av en utlöst datatransformering data flödes aktivitet.](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar ett data flöde för mappning](tutorial-data-flow.md).
