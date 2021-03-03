---
title: Iterativ utveckling och fel sökning i Azure Data Factory
description: Lär dig att utveckla och felsöka Data Factory pipelines iterativt i ADF-UX
ms.date: 02/23/2021
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: kromerm
ms.author: makromer
ms.openlocfilehash: ef47d311f5f096db962ea27792e7871dbf0ef81a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712971"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Iterativ utveckling och felsökning med Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Med Azure Data Factory kan du iterativt utveckla och felsöka Data Factory pipelines när du utvecklar lösningar för data integrering. Med de här funktionerna kan du testa dina ändringar innan du skapar en pull-begäran eller publicerar dem till Data Factory-tjänsten. 

I en åtta minuters introduktion och demonstration av den här funktionen kan du titta på följande video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="debugging-a-pipeline"></a>Felsöka en pipeline

När du skapar med hjälp av pipeline-arbetsytan kan du testa dina aktiviteter med hjälp av **fel söknings** funktionen. När du utför test körningar behöver du inte publicera dina ändringar i data fabriken innan du väljer **Felsök**. Den här funktionen är användbar i scenarier där du vill se till att ändringarna fungerar som förväntat innan du uppdaterar data Factory-arbetsflödet.

![Fel söknings funktion på pipeline-arbetsytan](media/iterative-development-debugging/iterative-development-1.png)

När pipelinen körs kan du se resultatet av varje aktivitet på fliken **utmatning** i pipeline-arbetsytan.

Visa resultatet av test körningarna i fönstret **utdata** på arbets ytan för pipelinen.

![Fönstret utdata för pipeline-arbetsytan](media/iterative-development-debugging/iterative-development-2.png)

När en testkörning lyckas lägger du till fler aktiviteter i din pipeline och fortsätter att felsöka på ett iterativt sätt. Du kan också **avbryta** en test körning medan den pågår.

> [!IMPORTANT]
> Om du väljer **Felsök** körs pipelinen. Om pipelinen t. ex. innehåller kopierings aktivitet kopierar test körningen data från källa till mål. Därför rekommenderar vi att du använder test-mappar i dina kopierings aktiviteter och andra aktiviteter vid fel sökning. När du har felsökt pipelinen växlar du till de faktiska mapparna som du vill använda i normal drift.

### <a name="setting-breakpoints"></a>Ställa in Bryt punkter

Med Azure Data Factory kan du Felsöka en pipeline tills du når en viss aktivitet på pipeline-arbetsytan. Placera en Bryt punkt för aktiviteten tills du vill testa och välj **Felsök**. Data Factory säkerställer att testet bara körs tills Bryt punkts aktiviteten på pipeline-arbetsytan. Den här *fel sökningen tills* funktionen är användbar när du inte vill testa hela pipelinen, men endast en delmängd av aktiviteter i pipelinen.

![Bryt punkter på pipeline-arbetsytan](media/iterative-development-debugging/iterative-development-3.png)

Om du vill ange en Bryt punkt väljer du ett element på pipeline-arbetsytan. Ett *fel söknings* alternativ visas som en tom röd cirkel i det övre högra hörnet av elementet.

![Innan du anger en Bryt punkt för det valda elementet](media/iterative-development-debugging/iterative-development-4.png)

När du har valt alternativet *Felsök tills* ändras den till en fylld röd cirkel som visar att Bryt punkten är aktive rad.

![När du har angett en Bryt punkt för det valda elementet](media/iterative-development-debugging/iterative-development-5.png)

## <a name="monitoring-debug-runs"></a>Övervaka fel söknings körningar

När du kör en pipeline-felsökning visas resultatet i fönstret **utdata** på arbets ytan för pipelinen. Fliken utdata kommer bara att innehålla den senaste körningen som inträffat under den aktuella webbläsarsessionen. 

![Fönstret utdata för pipeline-arbetsytan](media/iterative-development-debugging/iterative-development-2.png)

Om du vill visa en historisk vy över fel söknings körningar eller se en lista över alla aktiva fel söknings körningar kan du gå in i **övervaknings** miljön. 

![Välj ikonen Visa aktiva fel söknings körningar](media/iterative-development-debugging/view-debug-runs.png)

> [!NOTE]
> Azure Data Factorys tjänsten bevarar endast fel söknings körnings historik i 15 dagar. 

## <a name="debugging-mapping-data-flows"></a>Fel sökning av mappnings data flöden

Genom att mappa data flöden kan du bygga kod fria data omvandlings logik som körs i stor skala. När du skapar din logik kan du aktivera en felsökningssession för att interaktivt arbeta med dina data med hjälp av ett aktivt Spark-kluster. Läs mer i [fel söknings läge för mappning av data flöde](concepts-data-flow-debug-mode.md).

Du kan övervaka aktiva data flödes fel söknings sessioner över en fabrik i **övervaknings** miljön.

![Visa fel söknings sessioner för data flöde](media/iterative-development-debugging/view-dataflow-debug-sessions.png)

Data förhands granskning i data flödes design och pipeline-felsökning av data flöden är avsedda att fungera bäst med små exempel på data. Men om du behöver testa din logik i en pipeline eller ett data flöde mot stora mängder data, ökar du storleken på de Azure Integration Runtime som används i felsökningssessionen med fler kärnor och minst generell användnings beräkning.
 
### <a name="debugging-a-pipeline-with-a-data-flow-activity"></a>Felsöka en pipeline med en data flödes aktivitet

När du kör en fel söknings pipeline som körs med ett data flöde har du två alternativ som du kan använda för att beräkna. Du kan antingen använda ett befintligt fel söknings kluster eller skapa ett nytt just-in-Time-kluster för dina data flöden.

Om du använder en befintlig felsökningssession minskar data flödets start tid avsevärt eftersom klustret redan körs, men det rekommenderas inte för komplexa eller parallella arbets belastningar eftersom det kan Miss lyckas när flera jobb körs samtidigt.

Genom att använda aktivitets körningen skapar du ett nytt kluster med de inställningar som anges i varje data flödes aktivitets integrerings körning. Detta gör att varje jobb kan isoleras och ska användas för komplexa arbets belastningar eller prestanda testning. Du kan också kontrol lera TTL i Azure IR så att de kluster resurser som används för fel sökning fortfarande är tillgängliga under den tids perioden för att betjäna ytterligare jobb förfrågningar.

> [!NOTE]
> Om du har en pipeline med data flöden som körs parallellt eller data flöden som måste testas med stora data uppsättningar, väljer du Använd aktivitets körning så att Data Factory kan använda Integration Runtime som du har valt i din data flödes aktivitet. Detta gör att data flöden kan köras på flera kluster och kan hantera dina parallella data flödes körningar.

![Köra en pipeline med ett data flöde](media/iterative-development-debugging/iterative-development-dataflow.png)

## <a name="next-steps"></a>Nästa steg

När du har testat ändringarna kan du befordra dem till högre miljöer med [kontinuerlig integrering och distribution i Azure Data Factory](continuous-integration-deployment.md).
