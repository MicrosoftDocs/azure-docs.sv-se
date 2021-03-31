---
title: Så identifierar genomsökningar borttagna tillgångar
description: Den här artikeln förklarar hur ett Azure avdelningens kontroll-konto identifierar borttagna till gångar under genomsökningar.
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 9b1515ef00355c831e161c01227678197792cc20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96555387"
---
# <a name="how-scans-detect-deleted-assets"></a>Så identifierar genomsökningar borttagna tillgångar

I den här artikeln beskrivs hur Azure avdelningens kontroll använder genomsöknings resultat för att identifiera borttagna till gångar.

## <a name="background-info"></a>Bakgrunds information

En Azure avdelningens kontroll-katalog är medveten om status för ett data lager endast när den genomsöker den. För katalogen för att veta om en fil, tabell eller behållare har tagits bort, jämför den senaste skanningen med den aktuella sökningen. Anta till exempel att den senaste gången du skannade ett Azure Data Lake Storage Gen2-konto, inkluderade en mapp med namnet *Mapp1*. När samma konto genomsöks igen saknas *Mapp1* . Därför förutsätter katalogen att mappen har tagits bort.

## <a name="detecting-deleted-files"></a>Identifiera borttagna filer

Logiken för att identifiera saknade filer fungerar för flera avsökningar av samma användare och av olika användare. Anta till exempel att en användare kör en eng ång slö inläsning på en Data Lake Storage Gen2 data lager på mapparna A, B och C. Senare kör en annan användare i samma konto en annan enstaka genomsökning på mapparna C, D och E av samma data lager. Eftersom mappar C genomsöks två gånger, kontrollerar katalogen om det finns möjliga borttagningar. Mapparna A, B, D och E genomsöks dock bara en gång och katalogen kontrol leras inte för borttagna till gångar.

Om du vill behålla borttagna filer från katalogen är det viktigt att köra regelbundna genomsökningar. Genomsöknings intervallet är viktigt eftersom katalogen inte kan identifiera borttagna resurser förrän en annan sökning har körts. Om du kör genomsökningar en gång i månaden i en viss butik kan katalogen inte identifiera några borttagna data till gångar i arkivet förrän du kör nästa genomsökning en månad senare.

När du räknar upp stora data lager som Data Lake Storage Gen2 finns det flera sätt (inklusive uppräknings fel och ignorerade händelser) för att sakna information. En viss genomsökning kan sakna en fil som skapats eller tagits bort. Så om katalogen inte är säker på att en fil har tagits bort tas den inte bort från katalogen. Den här strategin innebär att det kan finnas fel när en fil som inte finns i det genomsökta data lagret fortfarande finns i katalogen. I vissa fall kan ett data lager behöva skannas två eller tre gånger innan det fångar vissa borttagna till gångar.

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång med Azure avdelningens kontroll-kataloger finns i [snabb start: skapa ett avdelningens kontroll-konto](create-catalog-portal.md).
