---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 72588ccfd9ad00305cbdeaf8132999731d86b9b1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92164938"
---
En *logg nivå* tilldelas till varje logg. Värdet är ett heltal som anger relativ prioritet:

|Loggnivå    |Kod| Beskrivning |
|------------|---|--------------|
|Spårning       | 0 |Loggar som innehåller de mest detaljerade meddelandena. Dessa meddelanden kan innehålla känsliga program data. Dessa meddelanden är inaktiverade som standard och ska aldrig aktive ras i en produktions miljö.|
|Felsöka       | 1 | Loggar som används för interaktiv undersökning under utvecklingen. Dessa loggar bör främst innehålla information som är användbar för fel sökning och som inte har något långsiktigt värde. |
|Information | 2 | Loggar som spårar programmets allmänna flöde. De här loggarna bör ha långsiktigt värde. |
|Varning     | 3 | Loggar som markerar en onormal eller oväntad händelse i program flödet, men inte på annat sätt gör att program körningen stoppas. |
|Fel       | 4 | Loggar som markerar när det aktuella körnings flödet stoppas på grund av ett haveri. Felen bör tyda på ett fel i den aktuella aktiviteten, inte ett program som inte är större. |
|Kritiskt    | 5 | Loggar som beskriver ett oåterkalleligt program eller en system krasch, eller ett oåterkalleligt fel som kräver omedelbar uppmärksamhet. |
|Inget        | 6 | Inaktiverar loggning för den angivna kategorin. |

[ *host.jsi* fil](../articles/azure-functions/functions-host-json.md) konfigurationen avgör hur mycket loggning av en Functions-app som skickas till Application Insights.  
