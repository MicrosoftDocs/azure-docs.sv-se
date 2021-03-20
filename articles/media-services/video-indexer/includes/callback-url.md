---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: fbc77b960cac0db2d077345c74d64485bd7ad8bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994509"
---
En URL som används för att meddela kunder (med en POST-begäran) om följande händelser:

- Indexering av tillståndsändring: 
    - Egenskaper:    
    
        |Name|Beskrivning|
        |---|---|
        |id|Video-ID|
        |state|Videotillståndet|  
    - Exempel: https: \/ /test.com/notifyme?projectName=MyProject&ID = 1234abcd&State = bearbetad
- Person som identifierades i videon:
  - Egenskaper
    
      |Name|Beskrivning|
      |---|---|
      |id| Video-ID|
      |faceId|Ansikts-ID som visas i videoindexet|
      |knownPersonId|Person-ID som är unikt inom en ansikts-modell|
      |personName|Namnet på personen|
        
    - Exempel: https: \/ /test.com/notifyme?projectName=MyProject&ID = 1234abcd&FaceID = 12&knownPersonId = CCA84350-89B7-4262-861C-3CAC796542A5&personName = Inigo_Montoya 
