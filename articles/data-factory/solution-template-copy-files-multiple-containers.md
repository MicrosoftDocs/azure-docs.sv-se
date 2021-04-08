---
title: Kopiera filer från flera containrar
description: Lär dig hur du använder en lösnings mall för att kopiera filer från flera behållare med hjälp av Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: ec7af1e81e0b295491420597636c8443f4d36512
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376096"
---
# <a name="copy-multiple-folders-with-azure-data-factory"></a>Kopiera flera mappar med Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln beskriver en lösnings mall som du kan använda flera kopierings aktiviteter för att kopiera behållare eller mappar mellan filbaserade butiker, där varje kopierings aktivitet ska kopiera en enskild behållare eller mapp. 

> [!NOTE]
> Om du vill kopiera filer från en enda behållare är det mer effektivt att använda [Kopiera data-verktyget](copy-data-tool.md) för att skapa en pipeline med en enda kopierings aktivitet. Mallen i den här artikeln är mer än du behöver för det enkla scenariot.

## <a name="about-this-solution-template"></a>Om den här lösnings mal len

Den här mallen räknar upp mappar från en specifik överordnad mapp i din käll lagrings lagring. Sedan kopieras varje mapp till mål lagret.

Mallen innehåller tre aktiviteter:
- **GetMetaData** genomsöker ditt käll lagrings lager och hämtar undermappen från en specifik överordnad mapp.
- Med **början får du** undermapplistan från **getMetaData** -aktiviteten och itererar sedan över listan och skickar varje mapp till kopierings aktiviteten.
- **Kopiera** kopierar varje mapp från käll lagrings lagret till mål arkivet.

Mallen definierar följande parametrar:
- *SourceFileFolder* är en del av sökvägen till den överordnade mappen för data käll arkivet: *SourceFileFolder/SourceFileDirectory*, där du kan hämta en lista över undermapparna. 
- *SourceFileDirectory* är en del av sökvägen till den överordnade mappen för data käll arkivet: *SourceFileFolder/SourceFileDirectory*, där du kan hämta en lista över undermapparna. 
- *DestinationFileFolder* är en del av sökvägen till den överordnade mappen: *DestinationFileFolder/DestinationFileDirectory* där filerna ska kopieras till mål lagret. 
- *DestinationFileDirectory* är en del av sökvägen till den överordnade mappen: *DestinationFileFolder/DestinationFileDirectory* där filerna ska kopieras till mål lagret. 

Om du vill kopiera flera behållare under rot-mappar mellan lagrings lager, kan du mata in alla fyra parametrarna som */* . Genom att göra det kommer du att replikera allt mellan lagrings lager.

## <a name="how-to-use-this-solution-template"></a>Så här använder du den här lösnings mal len

1. Gå till mallen **Kopiera flera filer mellan fil Arkiv** . Skapa en **ny** anslutning till ditt käll lagrings lager. Käll lagrings platsen är den plats där du vill kopiera filer från flera behållare från.

    ![Skapa en ny anslutning till källan](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Skapa en **ny** anslutning till mål lagrings platsen.

    ![Skapa en ny anslutning till målet](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Välj **Använd den här mallen**.

    ![Använd den här mallen](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Du ser pipelinen, som i följande exempel:

    ![Visa pipelinen](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Välj **Felsök**, ange **parametrarna** och välj sedan **Slutför**.

    ![Köra en pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Granska resultatet.

    ![Granska resultatet](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Nästa steg

- [Mass kopiering från en databas med hjälp av en kontroll tabell med Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Kopiera filer från flera behållare med Azure Data Factory](solution-template-copy-files-multiple-containers.md)
