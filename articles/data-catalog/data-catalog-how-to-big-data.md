---
title: Så här katalogiserar du Big data i Azure Data Catalog
description: Instruktions artikel som markerar mönster för att använda Azure Data Catalog med data källor med Big data, inklusive Azure Blob Storage, Azure Data Lake och Hadoop HDFS.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 3e898542c25f6ed1a9e6c811ada47220733abb12
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674673"
---
# <a name="how-to-catalog-big-data-in-azure-data-catalog"></a>Så här katalogiserar du Big data i Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Introduktion

**Microsoft Azure Data Catalog** är en fullständigt hanterad moln tjänst som fungerar som ett system för registrering och identifierings system för företags data källor. Det är allt som hjälper människor att upptäcka, förstå och använda data källor och hjälpa organisationer att få mer värde än befintliga data källor, inklusive big data.

**Azure Data Catalog** stöder registrering av Azure Storage-blobbar och kataloger samt Hadoop HDFS-filer och kataloger. Den delvis strukturerade typen av data källor ger stor flexibilitet. Men för att få ut det mesta värdet för att registrera dem med **Azure Data Catalog** måste användarna fundera över hur data källorna är ordnade.

## <a name="directories-as-logical-data-sets"></a>Kataloger som logiska data uppsättningar

Ett vanligt mönster för att ordna stora data källor är att behandla kataloger som logiska data uppsättningar. Kataloger på den översta nivån används för att definiera en data uppsättning, medan undermappar definierar partitioner och de filer som de innehåller lagrar själva data.

Ett exempel på det här mönstret kan vara:

```text
    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...
```

I det här exemplet representerar vehicle_maintenance_events och location_tracking_events logiska data uppsättningar. Var och en av dessa mappar innehåller datafiler som organiseras efter år och månad i undermappar. Var och en av dessa mappar kan eventuellt innehålla hundratals eller tusentals filer.

I det här mönstret är det förmodligen ingen mening att registrera enskilda filer med **Azure Data Catalog** . Registrera i stället de kataloger som representerar de data uppsättningar som är meningsfulla för de användare som arbetar med data.

## <a name="reference-data-files"></a>Referens data filer

Ett kompletterande mönster är att lagra referens data uppsättningar som enskilda filer. Dessa data uppsättningar kan betraktas som "små" sidor av Big data och är ofta likartade med dimensioner i en analys data modell. Referens data filen innehåller poster som används för att tillhandahålla kontext för de datafiler som lagras någon annan stans i stor data lagret.

Ett exempel på det här mönstret kan vara:

```text
    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv
```

När ett analytiker eller en data expert arbetar med de data som finns i de större katalog strukturerna kan data i dessa referenser användas för att ge mer detaljerad information om entiteter som endast refereras till av namn eller ID i den större data uppsättningen.

I det här mönstret är det klokt att registrera de enskilda referens data filerna med **Azure Data Catalog**. Varje fil representerar en data uppsättning och var och en kan vara kommenterad och identifierad individuellt.

## <a name="alternate-patterns"></a>Alternativa mönster

Mönstren som beskrivs i föregående avsnitt är bara två möjliga sätt som ett stort data lager kan organiseras, men varje implementering är annorlunda. Oavsett hur dina data källor är strukturerade, när du registrerar stora data källor med **Azure Data Catalog**, fokuserar vi på att registrera de filer och kataloger som representerar de data uppsättningar som är av värde för andra inom din organisation. Att registrera alla filer och kataloger kan göra katalogen rörig, vilket gör det svårare för användarna att hitta vad de behöver.

## <a name="summary"></a>Sammanfattning

Genom att registrera data källor med **Azure Data Catalog** blir det enklare att identifiera och förstå. Genom att registrera och kommentera Big datafiler och kataloger som representerar logiska data uppsättningar kan du hjälpa användarna att hitta och använda de stora data källor som de behöver.
