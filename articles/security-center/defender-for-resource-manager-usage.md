---
title: Så här svarar du på Azure Defender för Resource Manager-aviseringar
description: Lär dig mer om de steg som krävs för att svara på aviseringar från Azure Defender för Resource Manager
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 54790795aab8aac247e17198159130d7139dd38c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754745"
---
# <a name="respond-to-azure-defender-for-resource-manager-alerts"></a>Svara på aviseringar från Azure Defender för Resource Manager

När du får en avisering från Azure Defender för Resource Manager rekommenderar vi att du undersöker och svarar på aviseringen enligt beskrivningen nedan. Azure Defender för Resource Manager skyddar alla anslutna resurser, så även om du är van vid det program eller den användare som utlöste aviseringen, är det viktigt att kontrol lera att situationen omger varje avisering.  


## <a name="step-1-contact"></a>Steg 1. Kontakt

1. Kontakta resursens ägare för att avgöra om beteendet förväntades eller avsiktligt.
1. Om aktiviteten förväntas stänger du av aviseringen.
1. Om aktiviteten är oväntad kan du behandla relaterade användar konton, prenumerationer och virtuella datorer som komprometterade och minimeras enligt beskrivningen i följande steg.

## <a name="step-2-immediate-mitigation"></a>Steg 2. Omedelbar minskning 

1. Åtgärda komprometterade användar konton:
    - Om de är okända kan du ta bort dem eftersom de kan ha skapats av en hot aktör
    - Om de är bekanta ändrar du autentiseringsuppgifterna för autentisering
    - Använd Azures aktivitets loggar för att granska alla aktiviteter som utförs av användaren och identifiera eventuella som är misstänkta

1. Åtgärda komprometterade prenumerationer:
    - Ta bort eventuella okända Runbooks från det komprometterade Automation-kontot
    - Granska IAM-behörigheter för prenumerationen och ta bort behörigheter för alla okända användar konton
    - Granska alla Azure-resurser i prenumerationen och ta bort alla som är okända
    - Granska och undersök eventuella säkerhets aviseringar för prenumerationen i Azure Security Center
    - Använd Azures aktivitets loggar för att granska alla aktiviteter som utförts i prenumerationen och identifiera eventuella som är misstänkta

1. Åtgärda de virtuella datorerna som påverkas
    - Ändra lösen orden för alla användare
    - Kör en fullständig genomsökning av skadlig kod på datorn
    - Återställa avbildningen av datorerna från en kostnads fri källa


## <a name="next-steps"></a>Nästa steg

På den här sidan förklaras processen att svara på en avisering från Azure Defender för Resource Manager. För relaterad information, se följande sidor:

- [Introduktion till Azure Defender för Resource Manager](defender-for-resource-manager-introduction.md)
- [Ignorera aviseringar från Azure Defender](alerts-suppression-rules.md)
- [Exportera Security Center data kontinuerligt](continuous-export.md)