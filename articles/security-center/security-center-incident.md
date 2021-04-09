---
title: Hantera säkerhets incidenter i Azure Security Center | Microsoft Docs
description: Det här dokumentet hjälper dig att använda Azure Security Center för att hantera säkerhets incidenter.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 722a508679c74f9d62df07575ffa1006528f4398
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652114"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Hantera säkerhets incidenter i Azure Security Center

Sorterar och undersöker säkerhets aviseringar kan ta lång tid för även de mest erfarna säkerhetsanalytikerna. För många är det svårt att veta var du ska börja. 

Security Center använder [analyser](./security-center-alerts-overview.md) för att ansluta informationen mellan olika [säkerhets aviseringar](security-center-managing-and-responding-alerts.md). Med dessa anslutningar kan Security Center tillhandahålla en enda vy över en angrepps kampanj och dess relaterade aviseringar för att hjälpa dig att förstå angriparens åtgärder och de resurser som påverkas.

Den här sidan innehåller en översikt över incidenter i Security Center.

## <a name="what-is-a-security-incident"></a>Vad är en säkerhetsincident?

I Security Center är en säkerhetsincident en sammanställning av alla aviseringar för en resurs som överensstämmer med särskilda [händelsekedjemönster](alerts-reference.md#intentions). Incidenter visas på sidan [säkerhets aviseringar](security-center-managing-and-responding-alerts.md) . Välj en incident om du vill visa relaterade aviseringar och få mer information.

## <a name="managing-security-incidents"></a>Hantera säkerhetsincidenter

1. På sidan aviseringar för Security Center använder du knappen **Lägg till filter** för att filtrera efter aviserings namn till aviserings namn **säkerhets incidenten har identifierats på flera resurser**. 

    :::image type="content" source="media/security-center-incident/locating-incidents.png" alt-text="Hitta incidenter på sidan aviseringar i Azure Security Center":::

    Listan är nu filtrerad så att endast incidenter visas. Observera att säkerhets incidenter har en annan ikon för säkerhets aviseringar.

    :::image type="content" source="media/security-center-incident/incidents-list.png" alt-text="Lista över incidenter på sidan aviseringar i Azure Security Center":::

1. Om du vill visa information om en incident väljer du en i listan. En sido ruta visas med mer information om incidenten.

    :::image type="content" source="media/security-center-incident/incident-quick-peek.png" alt-text="I sidofönstret visas information om incidenten":::

1. Om du vill visa mer information väljer du **Visa fullständig information**.

    [![Svara på säkerhets incidenter i Azure Security Center](media/security-center-incident/incident-details.png)](media/security-center-incident/incident-details.png#lightbox)

    Den vänstra rutan på sidan säkerhets incident visar information på hög nivå om säkerhets incidenten: rubrik, allvarlighets grad, status, aktivitets tid, beskrivning och den berörda resursen. Bredvid resursen som påverkas kan du se relevanta Azure-taggar. Använd de här taggarna för att härleda organisationens organisatoriska kontext vid utredning av aviseringen.

    Den högra rutan innehåller fliken **aviseringar** med de säkerhets aviseringar som korrelerats som en del av den här incidenten. 

    >[!TIP]
    > Om du vill ha mer information om en speciell avisering väljer du den. 

    [![Fliken vidta åtgärd i incidenten](media/security-center-incident/incident-take-action-tab.png)](media/security-center-incident/incident-take-action-tab.png#lightbox)

    Om du vill växla till fliken **vidta åtgärd** väljer du fliken eller knappen längst ned i den högra rutan. Använd den här fliken för att vidta ytterligare åtgärder, till exempel:
    - *Minimera hotet* – ger manuella reparations steg för den här säkerhets incidenten
    - *Förhindra framtida attacker* – ger säkerhets rekommendationer för att minska attack ytan, öka säkerheten position och förhindra framtida attacker
    - *Utlös automatiserat svar* – ger möjlighet att utlösa en Logic app som ett svar på denna säkerhets incident
    - *Ignorera liknande aviseringar* – ger möjlighet att ignorera framtida aviseringar med liknande egenskaper om aviseringen inte är relevant för din organisation 

   > [!NOTE]
   > Samma avisering kan finnas som en del av en incident, och visas som en fristående avisering.

1. Om du vill åtgärda hoten i incidenten följer du de åtgärds steg som medföljer varje avisering.


## <a name="next-steps"></a>Nästa steg

Den här sidan förklaras säkerhets incident funktionerna i Security Center. Relaterad information finns på följande sidor:

- [Säkerhets aviseringar i Security Center](security-center-alerts-overview.md)
- [Hantera och åtgärda säkerhetsaviseringar](security-center-managing-and-responding-alerts.md)