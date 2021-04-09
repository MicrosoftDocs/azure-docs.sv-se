---
title: Så här svarar du på Azure Defender för DNS-aviseringar
description: Lär dig mer om de steg som krävs för att svara på aviseringar från Azure Defender för DNS
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 375a81127a000741ca5e0397642800794610bcda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754772"
---
# <a name="respond-to-azure-defender-for-dns-alerts"></a>Svara på aviseringar från Azure Defender för DNS

När du får en avisering från Azure Defender för DNS rekommenderar vi att du undersöker och svarar på aviseringen enligt beskrivningen nedan. Azure Defender för DNS skyddar alla anslutna resurser, så även om du är van vid det program eller den användare som utlöste aviseringen är det viktigt att kontrol lera att situationen omger varje avisering.  


## <a name="step-1-contact"></a>Steg 1. Kontakt

1. Kontakta resursens ägare för att avgöra om beteendet förväntades eller avsiktligt.
1. Om aktiviteten förväntas stänger du av aviseringen.
1. Om aktiviteten är oväntad kan du behandla resursen som potentiellt komprometterad och minimera enligt beskrivningen i nästa steg.

## <a name="step-2-immediate-mitigation"></a>Steg 2. Omedelbar minskning 

1. Isolera resursen från nätverket för att förhindra lateral förflyttning.
1. Kör en fullständig genomsökning av program mot skadlig kod på resursen med hjälp av eventuella reparations råd.
1. Granska installerad och pågående program vara på resursen och ta bort alla okända eller oönskade paket.
1. Återställa datorn till ett känt fungerande tillstånd, installera om operativ systemet om det behövs, och Återställ program vara från en verifierad skadlig kod-kostnads fri källa.
1. Lös eventuella Azure Security Center rekommendationer för datorn, och åtgärda markerade säkerhets problem för att förhindra framtida överträdelser.


## <a name="next-steps"></a>Nästa steg

På den här sidan förklaras processen att svara på en avisering från Azure Defender för DNS. För relaterad information, se följande sidor:

- [Introduktion till Azure Defender för DNS](defender-for-dns-introduction.md)
- [Ignorera aviseringar från Azure Defender](alerts-suppression-rules.md)
- [Exportera Security Center data kontinuerligt](continuous-export.md)