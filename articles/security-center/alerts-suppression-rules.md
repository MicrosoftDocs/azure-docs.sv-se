---
title: Använd undervisnings regler för varningar för att förhindra falska positiva identifieringar eller andra oönskade säkerhets varningar i Azure Security Center.
description: Den här artikeln förklarar hur du använder Azure Security Centers undertrycks regler för att dölja oönskade säkerhets varningar
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 02/17/2021
ms.service: security-center
ms.topic: how-to
ms.openlocfilehash: 646495597565bbb033ac3adaa15f3754f33e8fd6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100634178"
---
# <a name="suppress-alerts-from-azure-defender"></a>Ignorera aviseringar från Azure Defender

På den här sidan förklaras hur du kan använda undertrycks regler för varningar för att förhindra falska positiva identifieringar eller andra oönskade säkerhets varningar från Azure Defender.

## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmän tillgänglighet (GA)|
|Priset|Kostnadsfri<br>(De flesta säkerhets aviseringar är bara tillgängliga med Azure Defender)|
|Nödvändiga roller och behörigheter:|**Säkerhets administratör** och **ägare** kan skapa/ta bort regler.<br>**Säkerhets läsare** och **läsare** kan visa regler.|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Ja](./media/icons/yes-icon.png) National/suverän (US Gov, Kina gov, andra gov)|
|||


## <a name="what-are-suppression-rules"></a>Vad är undertrycks regler?

De olika Azure Defender-planerna identifierar hot i alla områden i miljön och genererar säkerhets aviseringar.

När en enskild avisering inte är intressant eller relevant kan du stänga den manuellt. Du kan också använda funktionen undertrycks regler för att automatiskt stänga liknande aviseringar i framtiden. Normalt använder du en undertrycks regel för att:

- Ignorera aviseringar som du har identifierat som falska positiva identifieringar

- Ignorera aviseringar som utlöses för ofta för att vara användbara

Dina undertrycks regler definierar kriterierna för vilka aviseringar automatiskt ska avvisas.

> [!CAUTION]
> Om du undertrycker säkerhets aviseringar minskas skydds effektiviteten i Azure Defender. Du bör noggrant kontrol lera den potentiella effekten av en undertrycks regel och övervaka den över tid.

:::image type="content" source="./media/alerts-suppression-rules/create-suppression-rule.gif" alt-text="Skapa regel för att utelämna varningar":::

## <a name="create-a-suppression-rule"></a>Skapa undertryckningsregel

Du kan skapa regler för att undertrycka oönskade säkerhetsaviseringar på några olika sätt:

- Om du vill undertrycka aviseringar på hanteringsgruppsnivå använder du Azure Policy
- Om du vill undertrycka aviseringar på prenumerationsnivå kan du använda Azure-portalen eller REST-API:et på det sätt som beskrivs nedan

Undertrycks regler kan bara ignorera aviseringar som redan har utlösts av de valda prenumerationerna.

Så här skapar du en regel direkt i Azure Portal:

1. Gå till sidan för säkerhetsvarningar i Security Center:

    - Välj den avisering som du inte vill se längre och välj **vidta åtgärd** i informations fönstret.

    - Eller Välj länken **undertrycks regler** överst på sidan och välj **Skapa ny undertrycks regel** på sidan undertrycks regler:

        ![Skapa ny undertrycks regel * * knapp](media/alerts-suppression-rules/create-new-suppression-rule.png)

1. Ange information om den nya regeln i fönstret ny undertrycks regel.
    - Din regel kan ignorera aviseringen för **alla resurser** så att du inte får några aviseringar som den här i framtiden.     
    - Din regel kan ignorera aviseringen **om vissa villkor** – när den relaterar till en speciell IP-adress, process namn, användar konto, Azure-resurs eller plats.

    > [!TIP]
    > Om du har öppnat den nya regel sidan från en speciell avisering konfigureras aviseringen och prenumerationen automatiskt i den nya regeln. Om du använde länken **Skapa ny undertrycks regel** kommer de valda prenumerationerna att matcha det aktuella filtret i portalen.

    [![Fönstret Skapa undertrycks regel](media/alerts-suppression-rules/new-suppression-rule-pane.png)](media/alerts-suppression-rules/new-suppression-rule-pane.png#lightbox)
1. Ange information om regeln:
    - **Namn** – ett namn för regeln. Regelnamn måste börja med en bokstav eller en siffra, innehålla mellan 2 och 50 tecken och de får inte innehålla några andra symboler än bindestreck (-) och understreck (_). 
    - **State** -aktive rad eller inaktive rad.
    - **Orsak** – Välj en av de inbyggda orsakerna eller "andra" om de inte uppfyller dina behov.
    - **Utgångs datum** – slutdatum och slut tid för regeln. Regler kan köras i upp till sex månader.
1. Du kan också testa regeln med knappen **simulera** för att se hur många aviseringar som skulle ha stängts om regeln hade varit aktiv.
1. Spara regeln. 


## <a name="edit-a-suppression-rule"></a>Redigera en undertrycks regel

Om du vill redigera en regel som du har skapat använder du sidan undertrycks regler.

1. På sidan säkerhets aviseringar för Security Center väljer du länken **undertrycks regler** högst upp på sidan.
1. Sidan undertrycks regler öppnas med alla regler för de valda prenumerationerna.

    [![Lista över undertrycks regler](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. Om du vill redigera en enskild regel öppnar du menyn med tre punkter (...) för regeln och väljer **Redigera**.
1. Gör nödvändiga ändringar och välj **Använd**. 

## <a name="delete-a-suppression-rule"></a>Ta bort en undertrycks regel

Om du vill ta bort en eller flera regler som du har skapat använder du sidan undertrycks regler.

1. På sidan säkerhets aviseringar för Security Center väljer du länken **undertrycks regler** högst upp på sidan.
1. Sidan undertrycks regler öppnas med alla regler för de valda prenumerationerna.
1. Om du vill ta bort en enskild regel öppnar du menyn med tre punkter (...) för regeln och väljer **ta bort**.
1. Markera kryss rutorna för de regler som ska tas bort och välj **ta bort** om du vill ta bort flera regler.
    ![Ta bort en eller flera undertrycks regler](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="create-and-manage-suppression-rules-with-the-api"></a>Skapa och hantera undertrycks regler med API: et

Du kan skapa, Visa eller ta bort undervisnings regler för aviseringar via Security Center REST API. 

Relevanta HTTP-metoder för undertrycks regler i REST API är:

- **Lägg** till: om du vill skapa eller uppdatera en undertrycks regel i en angiven prenumeration.

- **Hämta**:

    - För att visa en lista med alla regler som kon figurer ATS för en angiven prenumeration. Den här metoden returnerar en matris med tillämpliga regler.

    - För att hämta information om en specifik regel för en angiven prenumeration. Den här metoden returnerar en undertrycks regel.

    - För att simulera effekten av en undertrycks regel fortfarande i Design fasen. Det här anropet identifierar vilken av dina befintliga aviseringar som skulle ha stängts om regeln hade varit aktiv.

- **Ta bort**: tar bort en befintlig regel (men ändrar inte statusen för aviseringar som redan har avslagits av den).

Fullständig information och användnings exempel finns i [API-dokumentationen](/rest/api/securitycenter/). 


## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs undertrycks reglerna i Azure Security Center som automatiskt ignorerar oönskade aviseringar.

Mer information om Azure Defender säkerhets aviseringar finns i följande sidor:

- [Säkerhets aviseringar och avsikten med Kill-kedjan](alerts-reference.md) – en referens guide till säkerhets aviseringar som du kan få från Azure Defender.