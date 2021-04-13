---
author: baanders
description: ta med fil för Azure Digitals flätade – sätt att hantera instanser
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 1f66101fc1231be2e5ce36dc348b1ca850113867
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303772"
---
I den här artikeln beskrivs hur du slutför olika hanterings åtgärder med [Azure Digitals .net- **SDK**(C#)](/dotnet/api/overview/azure/digitaltwins/management). Du kan också använda samma hanterings samtal med de andra språk-SDK: erna som beskrivs i [*anvisningar: använda Azures digitala dubbla API: er och SDK: er*](../articles/digital-twins/how-to-use-apis-sdks.md).

> [!TIP] 
> Kom ihåg att alla SDK-metoder ingår i synkrona och asynkrona versioner. För växlings anrop returneras asynkrona metoder `AsyncPageable<T>` när de synkrona versionerna returneras `Pageable<T>` .

Ett annat hanterings alternativ är att anropa Azure Digitals [**REST-API: er**](/rest/api/azure-digitaltwins/) för det här avsnittet direkt, via en rest-klient som Postman. Instruktioner för hur du gör detta finns i [*How-to: göra förfrågningar med Postman*](../articles/digital-twins/how-to-use-postman.md).

Slutligen kan du slutföra samma hanterings åtgärder med hjälp av Azure Digitals flätade **CLI**. Mer information om hur du använder CLI finns i [*How-to: använda Azure Digitals flätade CLI*](../articles/digital-twins/how-to-use-cli.md).