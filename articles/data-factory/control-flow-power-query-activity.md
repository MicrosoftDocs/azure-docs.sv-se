---
title: Power Query aktivitet i Azure Data Factory
description: Lär dig hur du använder Power Query-aktivitet för data datatransformering-funktioner i en Data Factory pipeline
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: fc4f20db55f8e7e0b2f92cb8309c1c128b235089
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385463"
---
# <a name="power-query-activity-in-data-factory"></a>Power Query-aktivitet i Data Factory

Med Power Query-aktiviteten kan du bygga och köra Power Query kombinera-UPS för att köra data datatransformering i stor skala i en Data Factory pipeline. Du kan skapa en ny Power Query kombinera från meny alternativet nya resurser eller genom att lägga till en energi aktivitet till din pipeline.

![Skärm bild som visar Power Query i fönstret fabriks resurser.](media/data-flow/power-query-wrangling.png)

Tidigare skrevs data datatransformering i Azure Data Factory från meny alternativet data flöde. Detta har ändrats till redigering från en ny Power Query-aktivitet. Du kan arbeta direkt i Power Query kombinera-redigeraren för att utföra interaktiva data utforskningar och sedan spara ditt arbete. När du är klar kan du ta din Power Query-aktivitet och lägga till den i en pipeline. Azure Data Factory skalar automatiskt ut det och operationalisera dina data datatransformering med hjälp av Azure Data Factorys data flöde Spark-miljö.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>Översättning till data flödes skript

För att uppnå skalning med din Power Query aktivitet, Azure Data Factory översätter ```M``` skriptet till ett data flödes skript så att du kan köra Power Query i skala med Azure Data Factory Data Flow Spark-miljö. Redigera ditt datatransformering-dataflöde med hjälp av kod fri förberedelse av data. En lista över tillgängliga funktioner finns i [omvandlings funktioner](wrangling-functions.md).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om data datatransformering-koncept med [Power Query i Azure Data Factory](wrangling-tutorial.md)
