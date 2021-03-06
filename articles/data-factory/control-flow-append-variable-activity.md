---
title: Lägg till variabel aktivitet i Azure Data Factory
description: Lär dig hur du ställer in aktiviteten Lägg till variabel för att lägga till ett värde till en befintlig mat ris variabel som definieras i en Data Factory pipeline
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.date: 10/09/2018
ms.openlocfilehash: 1ca58fc208bb02d137b977e0b18857e8c87a5440
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783854"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Lägg till variabel aktivitet i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Använd aktiviteten Lägg till variabel för att lägga till ett värde till en befintlig mat ris variabel som definierats i en Data Factory pipeline.

## <a name="type-properties"></a>Typ egenskaper

Egenskap | Beskrivning | Obligatorisk
-------- | ----------- | --------
name | Namn på aktiviteten i pipeline | Ja
beskrivning | Text som beskriver vad aktiviteten gör | nej
typ | Aktivitets typen är AppendVariable | ja
värde | Sträng tecken eller uttrycks objekt värde som används för att lägga till angiven variabel | ja
variableName | Namnet på variabeln som ska ändras av aktivitet, variabeln måste vara av typen matris | ja

## <a name="next-steps"></a>Nästa steg
Lär dig mer om en relaterad kontroll flödes aktivitet som stöds av Data Factory: 

- [Ange variabelaktivitet](control-flow-set-variable-activity.md)
