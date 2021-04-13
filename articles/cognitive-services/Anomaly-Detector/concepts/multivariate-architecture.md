---
title: 'Predicative underhålls arkitektur för att använda API: t för avvikelse detektor Multivarierad'
titleSuffix: Azure Cognitive Services
description: 'Referens arkitektur för att använda Multivarierad-API: er i avvikelse detektor för att tillämpa avvikelse identifiering för dina tids serie data för förutsägande underhåll.'
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: avvikelse identifiering, maskin inlärning, algoritmer
ms.openlocfilehash: 4d379cfe01dcbd88531b98a32b45e0b30f6adeef
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315693"
---
# <a name="predictive-maintenance-solution-with-anomaly-detector-multivariate"></a>Förebyggande underhålls lösning med avvikelse detektor multivarierad

Många olika branscher behöver förebyggande underhålls lösningar för att minska riskerna och få användbara insikter genom att bearbeta data från deras utrustning. Förebyggande underhåll utvärderar utrustnings villkor genom att utföra övervakning online. Målet är att utföra underhåll innan utrustningen degraderas eller bryts ned.

Övervakning av hälso status för utrustning kan vara svårt, eftersom varje komponent i utrustningen kan generera dussin tals signaler, till exempel vibration, orientering och rotation.  Detta kan vara ännu mer komplext när dessa signaler har en implicit relation och måste övervakas och analyseras tillsammans. Det kan vara dyrt att definiera olika regler för dessa signaler och korrelera dem med varandra manuellt. Funktionen multivarierad för avvikelse detektor gör det möjligt att:

* Flera korrelerade signaler som ska övervakas tillsammans och sambanden mellan dem redovisas i modellen.
* I varje fångad avvikelse kan bidrags rangordningen för olika signaler hjälpa till med avvikelse förklaringen och analys av incident Rotors Aker.
* Multivarierad avvikelse identifierings modell har skapats på ett ej övervakat sätt. Modeller kan tränas specifikt för olika typer av utrustning.

Här tillhandahåller vi en referens arkitektur för en förebyggande underhålls lösning som baseras på avvikelser i multivarierad.

## <a name="reference-architecture"></a>Referensarkitektur

[![Arkitektur diagram som börjar med sensor data som samlas in på gränsen med en del av industriell utrustning och spårar pipelinen för bearbetning/analys till slut resultatet av en incident avisering som genereras efter att avvikelserna körts. ](../media/multivariate-architecture/multivariate-architecture.png)](../media/multivariate-architecture/multivariate-architecture.png#lightbox)

I ovanstående arkitektur kommer strömmande händelser som kommer från sensor data att lagras i Azure Data Lake och sedan bearbetas av en datatransformering-modul som ska konverteras till ett format för tids serier. Under tiden utlöser strömnings händelsen real tids identifiering med den tränade modellen. I allmänhet finns det en modul för att hantera livs cykeln för multivarierad-modellen, som *Bridge service* i den här arkitekturen.

**Modell träning**: innan du använder Multivarierad avvikelse detektor för att identifiera avvikelser för en komponent eller utrustning. Vi måste träna en modell på vissa signaler (Time-Series) som genereras av den här entiteten. *Tjänsten Bridge* hämtar historiska data och skickar ett utbildnings jobb till avvikelse detektorn och behåller sedan modell-ID: t i *modellens meta* -lagring.

**Modell validering**: inlärnings tiden för en viss modell kan variera beroende på den utbildnings data volym. *Brygga tjänsten* kan fråga om modell status och diagnostisk information regelbundet. Det kan vara nödvändigt att validera modell kvaliteten innan den kan placeras online. Om det finns etiketter i scenariot kan dessa etiketter användas för att kontrol lera modell kvaliteten. Annars kan diagnostikinformation användas för att utvärdera modell kvaliteten, och du kan också utföra identifiering av historiska data med den tränade modellen och utvärdera resultatet för att kontrol lera att modellen är giltig.

**Modellens härledning**: identifiering av online sker med en giltig modell, och resultat-ID: t kan lagras i en *härlednings tabell*. Både inlärnings processen och härlednings processen görs på ett asynkront sätt. I allmänhet kan en identifierings uppgift slutföras inom några sekunder. Signaler som används för identifiering ska vara samma som har använts för utbildning. Om vi till exempel använder vibrationer, orientering och rotation för utbildning ska de tre signalerna inkluderas som inmatade.

**Incident avisering** Identifierings resultaten kan frågas med resultat-ID: n. Varje resultat innehåller allvarlighets graden för varje avvikelse och bidrags rang. Bidrags rang kan användas för att förstå varför den här avvikelsen hände och vilken signal som orsakade denna incident. Olika tröskelvärden kan ställas in på allvarlighets graden för att generera aviseringar och meddelanden som skickas till fält tekniker för att utföra underhålls arbete.

## <a name="next-steps"></a>Nästa steg

- [Snabb starter](../quickstarts/client-libraries-multivariate.md).
- [Bästa praxis](../concepts/best-practices-multivariate.md): den här artikeln innehåller rekommenderade mönster som används med Multivarierad-API: er.