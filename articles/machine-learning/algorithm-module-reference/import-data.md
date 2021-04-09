---
title: 'Importera data: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen importera data i Azure Machine Learning för att läsa in data i en maskin inlärnings pipeline från befintliga data tjänster i molnet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 69d27c102ca059974da87224e44f0ad7aa103fff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "94592642"
---
# <a name="import-data-module"></a>Importera datamodul

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att läsa in data i en maskin inlärnings pipeline från befintliga data tjänster i molnet. 

> [!Note]
> Alla funktioner som tillhandahålls av den här modulen kan utföras av **data lager** och **data uppsättningar** på landnings sidan för arbets yta. Vi rekommenderar att du använder data **lager** och **data uppsättning** som innehåller ytterligare funktioner som data övervakning. Mer information finns i artikeln om [att komma åt data](../how-to-access-data.md) och [registrera data uppsättningar](../how-to-create-register-datasets.md) .
> När du har registrerat en data uppsättning kan du hitta den i kategorin **data uppsättningar**  ->  **mina data uppsättningar** i designern. Den här modulen är reserverad för Studio (klassiska) användare till en välbekant upplevelse. 
>

Modulen **Importera data** har stöd för läsning av data från följande källor:

- URL via HTTP
- Azures moln lagring via [**data lager**](../how-to-access-data.md))
    - Azure Blob-behållare
    - Azure-filresurs
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Azure SQL Database
    - Azure-PostgreSQL    

Innan du använder moln lagring måste du registrera ett data lager i din Azure Machine Learning-arbetsyta först. Mer information finns i [så här kommer du åt data](../how-to-access-data.md). 

När du har definierat de data som du vill ha och ansluter till källan, härleds data typen för varje kolumn baserat på de värden som den innehåller och data läses in i din **[Designer-pipeline](./import-data.md)** . Utdata från **import data** är en data uppsättning som kan användas med alla designer-pipeliner.

Om dina källdata ändras kan du uppdatera data uppsättningen och lägga till nya data genom att köra [Importera data](./import-data.md)på nytt.

> [!WARNING]
> Om din arbets yta finns i ett virtuellt nätverk måste du konfigurera dina data lager så att de använder designerns funktioner för data visualisering. Mer information om hur du använder data lager och data uppsättningar i ett virtuellt nätverk finns i [använda Azure Machine Learning Studio i ett virtuellt Azure-nätverk](../how-to-enable-studio-virtual-network.md).


## <a name="how-to-configure-import-data"></a>Konfigurera import data

1. Lägg till modulen **Importera data** till din pipeline. Du hittar den här modulen i kategorin **data indata och utdata** i designern.

1. Välj modulen för att öppna den högra rutan.

1. Välj **data källa** och välj typ av data källa. Det kan vara HTTP eller data lager.

    Om du väljer data lager kan du välja befintliga data lager som redan har registrerats på din Azure Machine Learning-arbetsyta eller skapa ett nytt data lager. Definiera sedan sökvägen till de data som ska importeras i data lagret. Du kan enkelt bläddra i sökvägen genom att klicka på **Bläddra** bana- ![ skärm bild visas länken bläddra bana som öppnar dialog rutan Sök vägs val.](media/module/import-data-path.png)

    > [!NOTE]
    > **Importera** datamodulen är endast för **tabell** data.
    > Om du vill importera flera datafiler i en tabell i taget, kräver det följande villkor, annars inträffar fel:
    > 1. Om du vill inkludera alla datafiler i mappen måste du ange `folder_name/**` **sökväg**.
    > 2. Alla datafiler måste vara kodade i Unicode-8.
    > 3. Alla datafiler måste ha samma kolumn nummer och kolumn namn.
    > 4. Resultatet av att importera flera datafiler sammanfogar alla rader från flera filer i ordning.

1. Välj förhands gransknings schema för att filtrera de kolumner som du vill inkludera. Du kan också definiera avancerade inställningar som avgränsare i tolknings alternativ.

    ![Importera – data – för hands version](media/module/import-data.png)

1. Kryss rutan **skapa utdata** igen, bestämmer om modulen ska köras för att återskapa utdata vid körning. 

    Den är som standard omarkerad, vilket innebär att om modulen har körts med samma parametrar tidigare kommer systemet att återanvända utdata från senaste körning för att minska körnings tiden. 

    Om den är markerad kommer systemet att köra modulen igen för att återskapa utdata. Välj det här alternativet när underliggande data i lagringen uppdateras, så kan du få den senaste informationen.


1. Skicka pipelinen.

    När importera data läser in data i designern härleds data typen för varje kolumn baserat på de värden som den innehåller, antingen numeriska eller kategoriska.

    Om det finns ett sidhuvud används rubriken för att namnge kolumnerna i data uppsättningen för utdata.

    Om det inte finns några befintliga kolumn rubriker i data genereras nya kolumn namn med formatet col1, col2,... , coln*.

## <a name="results"></a>Resultat

När importen är klar högerklickar du på data uppsättningen för utdata och väljer **visualisera** för att se om data har importer ATS korrekt.

Om du vill spara data för åter användning, i stället för att importera en ny data uppsättning varje gång pipelinen körs, väljer du ikonen **registrera data uppsättning** under fliken **utdata + loggar** i den högra panelen i modulen. Välj ett namn för data uppsättningen. Den sparade data uppsättningen bevarar data vid tidpunkten för att spara. data uppsättningen uppdateras inte när pipelinen körs igen, även om data uppsättningen i pipelinen ändras. Detta kan vara användbart för att ta ögonblicks bilder av data.

När du har importerat data kan det behövas ytterligare förberedelser för modellering och analys:

- Använd [Redigera metadata](./edit-metadata.md) för att ändra kolumn namn, för att hantera en kolumn som en annan datatyp, eller för att ange att vissa kolumner är etiketter eller funktioner.

- Använd [Välj kolumner i data uppsättning](./select-columns-in-dataset.md) för att välja en delmängd av kolumner att transformera eller använda i modellering. De omvandlade eller borttagna kolumnerna kan enkelt återanslutas till den ursprungliga data uppsättningen med hjälp av modulen [Lägg till kolumner](./add-columns.md) .  

- Använd [partition och exempel](./partition-and-sample.md) för att dela upp data uppsättningen, utföra sampling eller hämta de översta n raderna.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
